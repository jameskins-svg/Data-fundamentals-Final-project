# Security Implementation Notes

## Overview
This document explains the security architecture of the Clinic Database System, detailing how roles, policies, and Row Level Security (RLS) are implemented to protect sensitive medical data.

## 🔐 Security Architecture

### User Roles
The system implements two primary roles:

1. **Patients** (`patient` role)
   - Read-only access to their own medical records
   - Can view personal appointments and treatments
   - Cannot modify any data

2. **Doctors** (`doctor` role) 
   - Full read/write access to all medical records
   - Can manage appointments and treatments for all patients
   - Can view all patient information

## 🛡️ Row Level Security (RLS) Implementation

### Table Security Policies

#### Patients Table
```sql
-- Patients can only view their own profile
CREATE POLICY "Patients can view own profile" ON Patients
FOR SELECT USING (auth_id = auth.uid());

-- Doctors can view all patients
CREATE POLICY "Doctors can view all patients" ON Patients
FOR SELECT USING (
  EXISTS (SELECT 1 FROM Doctors WHERE auth_id = auth.uid())
);
```

#### Doctors Table
```sql
-- Patients can view all doctors (for appointment booking)
CREATE POLICY "Patients can view doctors" ON Doctors
FOR SELECT USING (true);

-- Doctors can view all doctors  
CREATE POLICY "Doctors can view all doctors" ON Doctors
FOR SELECT USING (true);
```

#### Appointments Table
```sql
-- Patients can view only their own appointments (read-only)
CREATE POLICY "Patients can view own appointments" ON Appointments
FOR SELECT USING (
  patient_id IN (SELECT patient_id FROM Patients WHERE auth_id = auth.uid())
);

-- Doctors have full CRUD access to all appointments
CREATE POLICY "Doctors full access to appointments" ON Appointments
FOR ALL USING (
  EXISTS (SELECT 1 FROM Doctors WHERE auth_id = auth.uid())
);
```

#### Treatments Table
```sql
-- Patients can view only their own treatments (read-only)
CREATE POLICY "Patients can view own treatments" ON Treatments
FOR SELECT USING (
  appointment_id IN (
    SELECT appointment_id FROM Appointments 
    WHERE patient_id IN (SELECT patient_id FROM Patients WHERE auth_id = auth.uid())
  )
);

-- Doctors have full CRUD access to all treatments
CREATE POLICY "Doctors full access to treatments" ON Treatments
FOR ALL USING (
  EXISTS (SELECT 1 FROM Doctors WHERE auth_id = auth.uid())
);
```

## 🔗 Authentication Integration

### User Identification
- Each user (patient or doctor) has a Supabase Auth account
- The `auth_id` column in Patients/Doctors tables links to `auth.users.id`
- Policies use `auth.uid()` to identify the currently authenticated user

### Role Determination
- Role is determined by presence in the respective table:
  - If user exists in `Patients` table → Patient role
  - If user exists in `Doctors` table → Doctor role
- No explicit role column needed - role is inferred from table membership

## 🧪 Security Testing

### Patient Access Test
```sql
-- Test as patient (should only see own data)
SET ROLE authenticated;
SET "request.jwt.claim.sub" TO 'patient-auth-uuid';
SELECT * FROM Appointments;  -- Should return only patient's appointments
SELECT * FROM Treatments;    -- Should return only patient's treatments
```

### Doctor Access Test  
```sql
-- Test as doctor (should see all data)
SET ROLE authenticated;
SET "request.jwt.claim.sub" TO 'doctor-auth-uuid';
SELECT * FROM Appointments;  -- Should return all appointments
SELECT * FROM Treatments;    -- Should return all treatments
```

### Permission Test
```sql
-- Test patient cannot edit (should fail)
SET "request.jwt.claim.sub" TO 'patient-auth-uuid';
UPDATE Appointments SET status = 'Completed' WHERE appointment_id = 'A001';

-- Test doctor can edit (should succeed)
SET "request.jwt.claim.sub" TO 'doctor-auth-uuid'; 
UPDATE Appointments SET status = 'Completed' WHERE appointment_id = 'A001';
```

## 📋 Policy Verification

Check active RLS policies:
```sql
SELECT schemaname, tablename, policyname, permissive, roles, cmd, qual
FROM pg_policies 
WHERE schemaname = 'public'
ORDER BY tablename, policyname;
```

Verify RLS is enabled:
```sql
SELECT tablename, rowsecurity 
FROM pg_tables 
WHERE schemaname = 'public' 
AND tablename IN ('patients', 'doctors', 'appointments', 'treatments');
```

## 🚨 Security Considerations

### Data Privacy
- Patient data is automatically filtered by RLS
- No application-level filtering required
- Database-level security ensures compliance

### Access Control
- Principle of least privilege enforced
- Patients cannot access other patients' data
- Doctors have broad access for comprehensive care

### Audit Trail
Consider adding for production:
- Audit logs for data access
- Track who viewed/modified medical records
- Compliance with healthcare regulations (HIPAA)

## 🛠️ Maintenance

### Adding New Users
1. Create user in Supabase Auth
2. Insert record in appropriate table (Patients/Doctors)
3. Set `auth_id` to match Auth user ID
4. Security policies automatically apply

### Policy Updates
- Modify policies through SQL ALTER POLICY statements
- Test thoroughly after policy changes
- Maintain backup of policy definitions

## ✅ Compliance Notes

This security model supports:
- **Patient confidentiality** through data isolation
- **Healthcare provider access** for treatment purposes  
- **Audit readiness** with proper logging
- **Regulatory compliance** foundations
