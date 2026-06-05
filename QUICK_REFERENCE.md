# Quick Reference Guide

## Fast Track to Deployment

### 📋 For the Impatient

Want to get running quickly? Follow this order:

1. **Local Testing (5 minutes)**
   ```bash
   # Terminal 1: Backend
   cd backend && python3 -m venv venv && source venv/bin/activate
   pip install -r requirements.txt
   python -m uvicorn app.main:app --reload
   
   # Terminal 2: Frontend
   cd frontend && npm install && npm run dev
   
   # Visit http://localhost:5173
   ```

2. **AWS Setup (30 minutes)**
   - Read: `docs/aws/AWS_SETUP.md`
   - Create: Secrets Manager secret
   - Create: S3 bucket with KMS
   - Create: IAM role

3. **Deploy to EC2 (1 hour)**
   - Read: `docs/deployment/DEPLOYMENT.md`
   - Launch 3 EC2 instances
   - Run deployment scripts
   - Verify services

4. **Test (15 minutes)**
   - Read: `docs/TESTING.md`
   - Run CURL examples
   - Use Postman collection

---

## 🔧 Most Common Commands

### Backend

```bash
# Start development
python -m uvicorn app.main:app --reload

# Run migrations
alembic upgrade head

# Create migration
alembic revision --autogenerate -m "message"

# Install dependencies
pip install -r requirements.txt
```

### Frontend

```bash
# Start development
npm run dev

# Build production
npm run build

# Install dependencies
npm install
```

### Database

```bash
# Connect
mysql -h [host] -u patient_app -p patient_db

# Create backup
mysqldump -h [host] -u patient_app -p patient_db > backup.sql

# Restore backup
mysql -h [host] -u patient_app -p patient_db < backup.sql
```

### AWS

```bash
# Get secrets
aws secretsmanager get-secret-value --secret-id patient-management-secrets

# Test S3 access
aws s3 ls

# List KMS keys
aws kms list-keys
```

### Nginx

```bash
# Test config
sudo nginx -t

# Reload
sudo systemctl reload nginx

# View logs
sudo tail -f /var/log/nginx/error.log
```

### Systemd

```bash
# Start service
sudo systemctl start patient-app

# Check status
sudo systemctl status patient-app

# View logs
sudo journalctl -u patient-app -f

# Restart
sudo systemctl restart patient-app
```

---

## 📁 File Reference

### Key Backend Files

| File | Purpose |
|------|---------|
| `app/main.py` | FastAPI app entry point |
| `app/api/auth.py` | Login/register endpoints |
| `app/api/patients.py` | Patient CRUD endpoints |
| `app/models/__init__.py` | Database tables |
| `app/services/` | Business logic |
| `requirements.txt` | Python dependencies |

### Key Frontend Files

| File | Purpose |
|------|---------|
| `src/App.jsx` | Main app with routing |
| `src/pages/` | Page components |
| `src/services/` | API calls |
| `src/context/AuthContext.jsx` | Auth state |
| `package.json` | Node dependencies |

### Key Config Files

| File | Purpose |
|------|---------|
| `.env.example` | Environment template |
| `docker-compose.yml` | Local dev setup |
| `nginx.conf` | Nginx config |
| `patient-app.service` | Systemd service |

### Key Documentation

| File | Purpose |
|------|---------|
| `README.md` | Project overview |
| `docs/aws/AWS_SETUP.md` | AWS infrastructure setup |
| `docs/deployment/DEPLOYMENT.md` | EC2 deployment guide |
| `docs/TESTING.md` | Testing guide |

---

## 🚀 API Endpoints Quick Reference

### Authentication

```
POST   /api/auth/register
POST   /api/auth/login
```

### Patients (all require JWT)

```
GET    /api/patients
POST   /api/patients
GET    /api/patients/{id}
PUT    /api/patients/{id}
DELETE /api/patients/{id}
POST   /api/patients/{id}/upload-image
```

### Health

```
GET    /health
```

---

## 🔐 Environment Variables

### Backend `.env`

```env
DATABASE_URL=mysql+pymysql://user:password@host/db_name
JWT_SECRET_KEY=your-secret-key
JWT_EXPIRATION_HOURS=24
AWS_REGION=us-east-1
S3_BUCKET_NAME=your-bucket
```

### Frontend `.env`

```env
VITE_API_URL=http://localhost:8000/api
```

---

## 🐛 Troubleshooting Quick Fixes

### Backend won't start
```bash
# Check logs
journalctl -u patient-app -n 50

# Check port
sudo lsof -i :8000

# Check secrets accessible
aws secretsmanager get-secret-value --secret-id patient-management-secrets
```

### Database connection failed
```bash
# Test connection
mysql -h [host] -u patient_app -p patient_db -e "SELECT 1"

# Check security group allows port 3306
# Check bind-address in /etc/mysql/mysql.conf.d/mysqld.cnf
```

### Frontend can't reach backend
```bash
# Check VITE_API_URL in .env
# Check backend is running
curl http://localhost:8000/health

# Check CORS is configured
# Check security group allows traffic
```

### S3 upload fails
```bash
# Check IAM role attached
aws ec2 describe-instances --instance-ids [id]

# Check S3 bucket exists and permissions
aws s3 ls

# Check KMS key accessible
aws kms list-keys
```

---

## 📊 Architecture at a Glance

```
[Browser] 
    ↓
[Frontend EC2] (React + Nginx)
    ↓ HTTP
[Backend EC2] (FastAPI + Nginx)
    ↓ MySQL
[Database EC2] (MySQL 8)

    ↓ S3 Upload
[S3 Bucket] 
    ↓ Auto-encrypted
[KMS]

[Secrets Manager] ← Backend reads on startup
[IAM Roles] ← EC2 uses for auth
```

---

## 💰 Estimated AWS Costs

- **EC2**: $25/month (3 small instances)
- **Storage**: $5-10/month (EBS + S3)
- **Services**: $2/month (Secrets Manager, KMS)
- **Transfer**: $0-5/month
- **TOTAL**: ~$30-50/month

---

## ✅ Pre-Deployment Checklist

- [ ] Read AWS_SETUP.md
- [ ] Read DEPLOYMENT.md
- [ ] Created Secrets Manager secret
- [ ] Created S3 bucket
- [ ] Created IAM role
- [ ] Created 3 EC2 instances
- [ ] Modified security groups
- [ ] Updated .env files
- [ ] Run migrations
- [ ] All services running
- [ ] API endpoints respond
- [ ] Frontend loads
- [ ] Can login/register
- [ ] Can create patient
- [ ] Can upload image

---

## 📞 When Things Break

1. **Check logs first**
   ```bash
   sudo journalctl -u patient-app -f
   sudo tail -f /var/log/nginx/error.log
   ```

2. **Verify connectivity**
   ```bash
   curl http://localhost:8000/health
   curl http://localhost/
   ```

3. **Check services**
   ```bash
   sudo systemctl status patient-app
   sudo systemctl status nginx
   sudo systemctl status mysql
   ```

4. **Consult TESTING.md**
   - Has common error responses
   - Has debugging commands
   - Has solutions

5. **Review logs in order**
   - Application logs first
   - Nginx logs second
   - Database logs third
   - AWS logs last

---

## 📚 Documentation Map

```
Start here:
  README.md → Project overview

For AWS:
  docs/aws/AWS_SETUP.md → Setup AWS
  docs/aws/ARCHITECTURE.md → Understand design

For Deployment:
  docs/deployment/DEPLOYMENT.md → Deploy to EC2

For Testing:
  docs/TESTING.md → Test everything

For Code:
  Backend: app/main.py
  Frontend: src/App.jsx
  Database: alembic/versions/001_initial.py

For Issues:
  DEPLOYMENT.md → Troubleshooting section
  TESTING.md → Debugging section
```

---

## 🎯 Success Indicators

- ✅ Backend responds to `/health`
- ✅ Frontend loads without errors
- ✅ Can register new account
- ✅ Can login with email/password
- ✅ Can view dashboard
- ✅ Can create patient
- ✅ Can view patients list
- ✅ Can edit patient
- ✅ Can upload patient image
- ✅ Can delete patient
- ✅ Can logout
- ✅ Protected routes work

---

## 🚨 Emergency Commands

```bash
# Kill a stuck service
sudo systemctl kill -s 9 patient-app

# View MySQL error log
sudo tail -f /var/log/mysql/error.log

# Check disk space
df -h

# Check memory usage
free -h

# Restart all services
sudo systemctl restart patient-app nginx mysql

# Clear database (be careful!)
mysql -u patient_app -p patient_db -e "DROP TABLE IF EXISTS patients; DROP TABLE IF EXISTS users;"

# Re-run migrations
alembic downgrade base
alembic upgrade head
```

---

## 💡 Pro Tips

1. **Save your tokens**
   - Store in `TOKEN` env var for testing
   - `export TOKEN="your-token-here"`
   - Use in CURL: `-H "Authorization: Bearer $TOKEN"`

2. **Monitor logs in real-time**
   - `sudo journalctl -u patient-app -f`
   - Keep open in separate terminal
   - See errors as they happen

3. **Test with Postman**
   - Import `postman-collection.json`
   - Set `base_url` variable
   - Set `token` after login

4. **Use security groups wisely**
   - Restrict ports to what's needed
   - Use security group IDs instead of IPs
   - Document rule purposes

5. **Backup before changes**
   - `mysqldump` before schema changes
   - `git commit` before code changes
   - `aws s3 sync` before S3 changes

---

## 🎓 Learning Path

1. Read `README.md` - Understand project
2. Review `DELIVERY_SUMMARY.md` - See what's included
3. Read `docs/aws/ARCHITECTURE.md` - Learn design
4. Follow `docs/aws/AWS_SETUP.md` - Setup AWS
5. Follow `docs/deployment/DEPLOYMENT.md` - Deploy
6. Study `docs/TESTING.md` - Learn APIs
7. Review code - Understand implementation
8. Extend - Add your own features

---

**Version**: 1.0.0
**Last Updated**: January 2024
**Status**: Production Ready ✅
