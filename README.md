# Clinic Database System <a name="about-project"></a>

> A secure and scalable medical database system built with Supabase that manages patient records, doctor information, appointments, and treatments with robust role-based security.

**Clinic Database System** is a comprehensive healthcare management solution that provides secure data storage and access control for medical facilities. It ensures patient privacy through Row Level Security while giving healthcare providers efficient tools to manage medical records.

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>

<details>
  <summary>Backend</summary>
  <ul>
    <li><a href="https://supabase.com/">Supabase</a></li>
  </ul>
</details>

<details>
<summary>Database</summary>
  <ul>
    <li><a href="https://www.postgresql.org/">PostgreSQL</a></li>
  </ul>
</details>

<details>
<summary>Security</summary>
  <ul>
    <li>Row Level Security (RLS)</li>
    <li>Supabase Authentication</li>
  </ul>
</details>

<!-- Features -->

### Key Features <a name="key-features"></a>

- **[Secure Patient Data Management]** - Implements healthcare privacy standards with Row Level Security
- **[Role-Based Access Control]** - Patients can view their records while doctors have full medical access
- **[Comprehensive Medical Tracking]** - Manages appointments, treatments, and patient-doctor relationships

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->

## 💻 Getting Started <a name="getting-started"></a>

To set up your own clinic database system, follow these steps.

### Prerequisites

- A Supabase account ([sign up here](https://supabase.com/))
- Basic knowledge of SQL and database concepts

### Setup

1. **Create a Supabase Project**
   - Go to [Supabase Dashboard](https://app.supabase.com/)
   - Create a new project and wait for initialization

2. **Execute the Database Schema**
   - Navigate to the SQL Editor in your Supabase project
   - Copy and paste the entire schema from `schema.sql`
   - Run the SQL commands to create tables and security policies

### Install

1. **Verify Table Creation**
   - Check the Table Editor to ensure these tables exist:
     - Patients
     - Doctors  
     - Appointments
     - Treatments

2. **Configure Authentication**
   - Go to Authentication → Settings
   - Enable Email/Password authentication
   - Set up your authorized redirect URLs

### Usage

**For Patients:**
- Sign up through your application using Supabase Auth
- View personal medical appointments and treatment history
- Access is read-only and restricted to own records

**For Doctors:**
- Access all patient records and medical data
- Create and manage appointments
- Update treatment records and medical information

### Run tests

Use the test queries included in the schema to verify security:

```sql
-- Test patient access (should see only their data)
SET ROLE authenticated;
SET "request.jwt.claim.sub" TO 'patient-auth-uuid';
SELECT * FROM Appointments;

-- Test doctor access (should see all data)  
SET "request.jwt.claim.sub" TO 'doctor-auth-uuid';
SELECT * FROM Appointments;
```

### Deployment

This project is deployed on Supabase. To deploy your own instance:

1. Create a new Supabase project
2. Execute the provided schema.sql
3. Configure authentication settings
4. Connect your frontend application to the Supabase API

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- AUTHORS -->

## 👥 Authors <a name="authors"></a>

👤 **James Mwai**

- GitHub: [@jameskins_svg](https://github.com/jameskins_svg)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- FUTURE FEATURES -->

## 🔭 Future Features <a name="future-features"></a>

- [ ] **Medical Billing Integration** - Connect with payment processing systems
- [ ] **Prescription Management** - Digital prescription tracking and renewal
- [ ] **Lab Results Portal** - Integration with laboratory systems for test results
- [ ] **Telemedicine Features** - Virtual consultations and remote patient monitoring

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](../../issues/).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- SUPPORT -->

## ⭐️ Show your support <a name="support"></a>

If you find this clinic database system useful for your healthcare application, please give it a star! Your support helps improve medical data management for everyone.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGEMENTS -->

## 🙏 Acknowledgments <a name="acknowledgements"></a>

I would like to thank the Supabase team for providing an excellent platform that makes secure database management accessible for healthcare applications.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- FAQ (optional) -->

## ❓ FAQ <a name="faq"></a>

- **How do patients access their medical records?**
  - Patients sign up through your application, which connects to Supabase Auth. The RLS policies automatically filter data to show only their records.

- **Can doctors from different specialties access all patient data?**
  - Yes, the current security model allows all doctors full access to ensure comprehensive patient care, but this can be customized for specific privacy requirements.

- **How do I add new patients to the system?**
  - When a patient registers through your application, create a record in the Patients table with their Supabase auth UID to link their authentication with their medical data.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->

## 📝 License <a name="license"></a>

This project is [MIT](./LICENSE) licensed.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
