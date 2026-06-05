# Patient Management Application

A simple, production-ready Patient Management System built with React, FastAPI, and MySQL.

## Overview

This is a monolithic, full-stack application for managing patient records with profile images stored in AWS S3 with KMS encryption.

**Key Features:**
- User authentication with JWT
- Complete CRUD operations for patients
- Patient profile image upload to S3
- AWS Secrets Manager integration
- AWS KMS encryption for S3 objects
- Responsive UI with Tailwind CSS
- Secure password hashing with bcrypt

**Architecture:**
```
Frontend (React) → Backend (FastAPI) → Database (MySQL)
                        ↓
                    S3 (Images)
                        ↓
                    KMS (Encryption)
```

---

## Technology Stack

### Frontend
- React 18
- Vite
- React Router
- Axios
- Tailwind CSS

### Backend
- Python 3.13
- FastAPI
- SQLAlchemy
- Alembic
- Gunicorn
- Nginx

### Database
- MySQL 8

### AWS Services
- EC2 (Compute)
- S3 (Object Storage)
- Secrets Manager (Credentials)
- KMS (Encryption)
- IAM (Authentication)

---

## Project Structure

```
kms-secret-project/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── auth.py (Authentication routes)
│   │   │   └── patients.py (Patient CRUD routes)
│   │   ├── core/
│   │   │   ├── config.py (Configuration)
│   │   │   ├── security.py (Password & JWT)
│   │   │   └── dependencies.py (Auth dependency)
│   │   ├── database/
│   │   │   └── __init__.py (Database session)
│   │   ├── models/
│   │   │   └── __init__.py (SQLAlchemy models)
│   │   ├── schemas/
│   │   │   └── __init__.py (Pydantic validators)
│   │   ├── services/
│   │   │   ├── auth.py (Auth business logic)
│   │   │   └── patient.py (Patient business logic)
│   │   ├── utils/
│   │   │   ├── aws.py (S3 & Secrets Manager)
│   │   │   └── validators.py (File validation)
│   │   ├── main.py (FastAPI app)
│   │   └── __init__.py
│   ├── alembic/
│   │   ├── versions/ (Database migrations)
│   │   ├── env.py (Alembic config)
│   │   └── script.mako.py (Migration template)
│   ├── requirements.txt
│   ├── alembic.ini
│   ├── .env.example
│   └── README.md
│
├── frontend/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── LoginPage.jsx
│   │   │   ├── RegisterPage.jsx
│   │   │   ├── DashboardPage.jsx
│   │   │   ├── PatientsPage.jsx
│   │   │   ├── AddPatientPage.jsx
│   │   │   └── EditPatientPage.jsx
│   │   ├── components/ (Reusable components)
│   │   ├── services/
│   │   │   ├── api.js (Axios instance)
│   │   │   ├── authService.js
│   │   │   └── patientService.js
│   │   ├── context/
│   │   │   └── AuthContext.jsx (Auth state)
│   │   ├── routes/
│   │   │   └── PrivateRoute.jsx (Protected routes)
│   │   ├── App.jsx (Main component)
│   │   ├── main.jsx (Entry point)
│   │   └── index.css
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   ├── .env.example
│   └── README.md
│
├── docs/
│   ├── aws/
│   │   └── AWS_SETUP.md (Complete AWS setup)
│   ├── deployment/
│   │   └── DEPLOYMENT.md (EC2 deployment guide)
│   ├── TESTING.md (API testing guide)
│   ├── postman-collection.json (Postman collection)
│   └── README.md
│
└── README.md (This file)
```

---

## Quick Start (Local Development)

### Prerequisites
- Python 3.13
- Node.js 18+
- MySQL 8
- pip and npm

### Setup Backend

```bash
cd backend

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp .env.example .env

# Edit .env with your MySQL credentials
nano .env

# Run migrations
alembic upgrade head

# Start server
python -m uvicorn app.main:app --reload
```

Backend runs at: `http://localhost:8000`

### Setup Frontend

```bash
cd frontend

# Install dependencies
npm install

# Copy environment file
cp .env.example .env

# Edit .env with your API URL
nano .env

# Start development server
npm run dev
```

Frontend runs at: `http://localhost:5173`

### Access Application

1. Open browser: `http://localhost:5173`
2. Register new account
3. Login
4. Add patients
5. Upload images

---

## API Documentation

### Base URL (Development)
```
http://localhost:8000/api
```

### Authentication Endpoints

#### Register
```
POST /auth/register
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "SecurePassword123"
}
```

#### Login
```
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "SecurePassword123"
}

Response:
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "user_id": 1
}
```

### Patient Endpoints (Require JWT Token)

#### Create Patient
```
POST /patients
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "John Smith",
  "age": 35,
  "gender": "Male",
  "blood_group": "O+",
  "phone": "+1-555-123-4567",
  "address": "123 Main St"
}
```

#### Get All Patients
```
GET /patients
Authorization: Bearer {token}
```

#### Get Patient
```
GET /patients/{id}
Authorization: Bearer {token}
```

#### Update Patient
```
PUT /patients/{id}
Authorization: Bearer {token}
Content-Type: application/json

{
  "age": 36,
  "phone": "+1-555-987-6543"
}
```

#### Upload Image
```
POST /patients/{id}/upload-image
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: [image file]
```

#### Delete Patient
```
DELETE /patients/{id}
Authorization: Bearer {token}
```

---

## Database Schema

### Users Table
```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  hashed_password VARCHAR(255) NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Patients Table
```sql
CREATE TABLE patients (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  age INT NOT NULL,
  gender VARCHAR(50) NOT NULL,
  blood_group VARCHAR(10) NOT NULL,
  phone VARCHAR(20) NOT NULL,
  address VARCHAR(500) NOT NULL,
  image_url VARCHAR(500),
  user_id INT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  INDEX (user_id)
);
```

---

## Environment Configuration

### Backend (.env)
```
APP_NAME=Patient Management API
DEBUG=False
API_PREFIX=/api

DATABASE_URL=mysql+pymysql://user:password@localhost/patient_db
JWT_SECRET_KEY=your-secret-key-here
JWT_ALGORITHM=HS256
JWT_EXPIRATION_HOURS=24

AWS_REGION=us-east-1
S3_BUCKET_NAME=patient-images-bucket
```

### Frontend (.env)
```
VITE_API_URL=http://localhost:8000/api
```

---

## AWS Deployment

### Three EC2 Instances

1. **Frontend EC2**
   - Ubuntu 22.04 LTS
   - t3.small
   - Node.js + React app
   - Nginx web server

2. **Backend EC2**
   - Ubuntu 22.04 LTS
   - t3.medium
   - Python 3.13 + FastAPI
   - Gunicorn + Nginx

3. **Database EC2**
   - Ubuntu 22.04 LTS
   - t3.small
   - MySQL 8

### AWS Services

- **Secrets Manager**: Store database credentials and JWT secret
- **S3**: Store patient profile images
- **KMS**: Encrypt S3 objects
- **IAM Roles**: Secure EC2 authentication

### Complete Setup

See [AWS_SETUP.md](docs/aws/AWS_SETUP.md) and [DEPLOYMENT.md](docs/deployment/DEPLOYMENT.md)

---

## Testing

### Run API Tests

```bash
# See docs/TESTING.md for complete testing guide

# Register user
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@example.com","password":"password"}'

# Login
TOKEN=$(curl -s -X POST http://localhost:8000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password"}' \
  | jq -r '.access_token')

# Get patients
curl -X GET http://localhost:8000/api/patients \
  -H "Authorization: Bearer $TOKEN"
```

### Postman Collection

Import `docs/postman-collection.json` into Postman for complete API testing.

---

## Security Features

✓ Password hashing with bcrypt
✓ JWT authentication
✓ Protected routes
✓ Input validation with Pydantic
✓ CORS configuration
✓ AWS IAM roles (no hardcoded credentials)
✓ Secrets Manager integration
✓ S3 encryption with KMS
✓ Secure password requirements

---

## Production Checklist

- [ ] Enable HTTPS/SSL certificates
- [ ] Set `DEBUG=False` in backend
- [ ] Use strong JWT secret
- [ ] Configure security groups properly
- [ ] Enable CloudWatch monitoring
- [ ] Set up database backups
- [ ] Configure log aggregation
- [ ] Enable VPC Flow Logs
- [ ] Review IAM policies
- [ ] Enable S3 versioning
- [ ] Monitor costs

---

## Troubleshooting

### Backend won't start
```bash
# Check logs
sudo journalctl -u patient-app -n 50

# Verify secrets are accessible
aws secretsmanager get-secret-value --secret-id patient-management-secrets
```

### Database connection failed
```bash
# Test connection
mysql -h [db-host] -u patient_app -p patient_db -e "SELECT 1"

# Check security group
# (Verify port 3306 is open between instances)
```

### S3 upload fails
```bash
# Check IAM role is attached
aws ec2 describe-instances --instance-ids [instance-id] | grep 'Arn'

# Test S3 access
aws s3 ls
```

---

## Performance Optimization

- Database connection pooling enabled
- Nginx reverse proxy caching
- Static asset caching (1 year)
- JWT token validation on each request
- Database indexes on frequently queried columns

---

## Cost Estimation (AWS)

- **3x EC2 instances (t3.small/medium)**: ~$25/month
- **S3 storage**: ~$0.023/GB
- **Secrets Manager**: $0.40/secret/month
- **KMS**: ~$1/month
- **Data transfer**: ~$0.09/GB (outbound)

**Total Estimated: $30-50/month**

---

## License

MIT License - Feel free to use this project for educational and commercial purposes.

---

## Support

For issues and questions:
1. Check [TESTING.md](docs/TESTING.md) for API testing examples
2. Review [DEPLOYMENT.md](docs/deployment/DEPLOYMENT.md) for setup issues
3. Check [AWS_SETUP.md](docs/aws/AWS_SETUP.md) for AWS configuration

---

## Next Steps

1. Complete AWS setup (see [AWS_SETUP.md](docs/aws/AWS_SETUP.md))
2. Deploy to EC2 instances (see [DEPLOYMENT.md](docs/deployment/DEPLOYMENT.md))
3. Run tests (see [TESTING.md](docs/TESTING.md))
4. Configure monitoring
5. Set up backups
6. Monitor application performance

---

## Architecture Decisions

### Why Monolithic?
- Simple deployment
- Easier debugging
- Reduced operational complexity
- Perfect for small teams

### Why Not?
- Microservices, Docker, Kubernetes, Redis, RabbitMQ, CQRS, DDD
- These add unnecessary complexity for this project
- Keep it simple and maintainable

### Technology Choices
- **FastAPI**: Modern, fast, easy to learn
- **React**: Popular, good ecosystem
- **MySQL**: Reliable, familiar
- **AWS**: Scalable infrastructure
- **Tailwind CSS**: Modern, utility-first styling
- **JWT**: Stateless authentication

---

## Contributing

1. Follow the existing code structure
2. Keep components simple and focused
3. Add tests for new features
4. Update documentation
5. Use descriptive commit messages

---

## Changelog

### Version 1.0.0 (Initial Release)
- User authentication (Register/Login)
- Patient CRUD operations
- Profile image upload to S3
- AWS Secrets Manager integration
- KMS encryption for S3 objects
- React frontend with Tailwind CSS
- FastAPI backend
- MySQL database
- Complete deployment guide

---

## Additional Resources

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [React Documentation](https://react.dev/)
- [AWS EC2 Guide](https://docs.aws.amazon.com/ec2/)
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc7519)

---

**Last Updated:** January 2024
**Status:** Production Ready
