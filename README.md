# 📚 Tutogoggy - Full-Stack Tuition Platform

> A comprehensive, full-stack tuition management and teaching platform with clear separation between frontend and backend services. Built for seamless student-teacher interaction, scheduling, and course management.
---

## ⚡ What makes this project non-trivial

This is not a basic CRUD application. It includes multiple real-world system design problems:

- 🔄 Real-time multi-user chat system with persistent threads (Socket.IO + MongoDB sync)
- 🧠 State-machine based tuition lifecycle (request → approval → active learning session)
- 👥 Multi-role architecture (student / teacher / parent with strict access control)
- 📅 Scheduling system with consensus-based acceptance flow between participants
- 💳 Payment flow tightly coupled with enrollment lifecycle state transitions

---

[![Frontend](https://img.shields.io/badge/Frontend-React%20%2B%20Next.js-blue?logo=react)](https://github.com/Shabbin/teaching-platform)
[![Backend](https://img.shields.io/badge/Backend-Node.js%20%2B%20Express-green?logo=node.js)](https://github.com/Shabbin/tuition-platform)
[![Database](https://img.shields.io/badge/Database-MongoDB-brightgreen?logo=mongodb)](https://www.mongodb.com/)
[![Live Demo](https://img.shields.io/badge/Live%20Demo-Vercel-success)](https://teaching-platform-beige.vercel.app/)

> **Live Demo Note:** The backend may take 30–60 seconds to wake up on the first request because it is hosted on a free-tier server.

---

## 🔗 Project Architecture

The Tutogoggy platform is split into two separate repositories for independent scaling and deployment:

| Component | Repository | Technology |
|-----------|------------|-----------|
| **Frontend** | [teaching-platform](https://github.com/Shabbin/teaching-platform) | React, Next.js 16, TypeScript, Tailwind CSS, Redux |
| **Backend** | [tuition-platform](https://github.com/Shabbin/tuition-platform) | Node.js, Express, MongoDB, Socket.io |
| **Live Demo** | [Vercel Deployment](https://teaching-platform-beige.vercel.app/) | Production Ready |

---

## 🏗️ System Architecture

### Tech Stack

#### Frontend
- **Framework**: Next.js 16.1.3
- **UI Library**: React 19.2.3
- **Styling**: Tailwind CSS 4.1.18, DaisyUI 5.5.14
- **State Management**: Redux Toolkit 2.11.2, Redux Persist
- **Forms**: React Hook Form 7.71.1
- **API Client**: Axios 1.13.2, TanStack Query 5.90.19
- **Real-time**: Socket.io Client 4.8.3
- **Video**: Daily.co, Jit.si
- **Chat**: Stream Chat 9.28.0
- **Rich Text**: React Quill 3.7.0
- **Animations**: Framer Motion 12.26.2
- **Icons**: Lucide React, React Icons
- **State Machines**: XState 5.25.1
- **Validation**: Zod 3.25.74

#### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: MongoDB with Mongoose
- **Real-time**: Socket.io
- **Authentication**: JWT (Cookie-based)
- **Security**: Helmet, CORS, Rate Limiting
- **File Upload**: Cloudinary
- **Video Rooms**: Daily.co API
- **Worker Jobs**: Background routine scheduling

---

## 📊 Entity Relationship Diagram (ERD)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         TUTOGOGGY DATA MODEL                                 │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌──────────────┐
                              │     User     │
                              ├──────────────┤
                              │ _id (PK)     │
                              │ name         │
                              │ email (UQ)   │
                              │ password     │
                              │ role         │◄─────────┐
                              │ profileImage │          │
                              │ hourlyRate   │          │
                              │ topicCredits │          │
                              │ skills[]     │          │
                              │ location     │          │
                              │ availability │          │
                              └──────────────┘          │
                                     ▲                  │
                    ┌────────────────┬┴─────────────┐   │
                    │                │              │   │
            ┌───────┴──────┐   ┌──────┴────┐   ┌────┴──────────────┐
            │ TeacherPost  │   │  Schedule │   │ EnrollmentInvite │
            ├──────────────┤   ├───────────┤   ├──────────────────┤
            │ _id (PK)     │   │ _id (PK)  │   │ _id (PK)         │
            │ teacherId(FK)│──▶│ teacherId │   │ teacherId (FK)   │
            │ title        │   │ postId(FK)│─┐ │ studentId (FK)   │
            │ subject      │   │ studentIds│ │ │ postId (FK)      │
            │ description  │   │ date      │ │ │ routineId (FK)   │
            │ level        │   │ duration  │ │ │ courseTitle      │
            │ hourlyRate   │   │ status    │ │ │ courseFeeTk      │
            │ maxStudents  │   │ type      │ │ │ upfrontDueTk     │
            │ createdAt    │   │ createdAt │ │ │ paymentStatus    │
            └───────┬──────┘   └───────────┘ │ │ status           │
                    │                        │ │ acceptedAt       │
                    │                        │ └──────────────────┘
                    │                        │
            ┌───────┴──────┐         ┌───────┴─────────┐
            │   Routine    │         │   Notification  │
            ├──────────────┤         ├─────────────────┤
            │ _id (PK)     │         │ _id (PK)        │
            │ teacherId(FK)│         │ recipientId(FK) │
            │ postId(FK)   │         │ senderId (FK)   │
            │ studentIds[] │         │ type            │
            │ timezone     │         │ message         │
            │ startDate    │         │ relatedModel    │
            │ endDate      │         │ relatedId       │
            │ slots[]      │         │ isRead          │
            │ status       │         │ createdAt       │
            │ requiresAcceptance     └─────────────────┘
            │ pendingBy[]  │
            │ acceptedBy[] │         ┌──────────────────┐
            └──────────────┘         │   ChatThread     │
                                     ├──────────────────┤
            ┌──────────────┐         │ _id (PK)         │
            │  ChatMessage │         │ participants[]   │
            ├──────────────┤         │ title            │
            │ _id (PK)     │         │ lastMessage      │
            │ threadId(FK) │────────▶│ updatedAt        │
            │ senderId(FK) │         └──────────────────┘
            │ content      │
            │ attachments[]│         ┌──────────────────┐
            │ isEdited     │         │   Payment        │
            │ createdAt    │         ├──────────────────┤
            └──────────────┘         │ _id (PK)         │
                                     │ inviteId(FK)     │
            ┌──────────────┐         │ amount           │
            │   TeacherReq │         │ currency         │
            ├──────────────┤         │ status           │
            │ _id (PK)     │         │ method           │
            │ studentId(FK)│         │ transactionId    │
            │ message      │         │ createdAt        │
            │ status       │         └──────────────────┘
            │ createdAt    │
            └──────────────┘

KEY:
  (PK) = Primary Key
  (FK) = Foreign Key
  (UQ) = Unique
  []   = Array
```

---

## 🔄 State Machine Diagrams

### 1. **Enrollment Invite Lifecycle**

```
┌────────────────────────────────────────────────────────────────┐
│         ENROLLMENT INVITE STATE MACHINE                         │
└────────────────────────────────────────────────────────────────┘

                    ┌─────────────┐
                    │   PENDING   │ ◄─────────────┐
                    │ (Awaiting    │              │
                    │  Student)    │              │
                    └──────┬──────┘              │
                           │                     │
                 ┌─────────┴──────────┐          │
                 │                    │          │
            Accept                Decline    Timeout
                 │                    │          │
                 ▼                    ▼          │
          ┌────────────┐      ┌───────────┐    │
          │ ACCEPTED   │      │ DECLINED  │────┘
          │ (Ready for │      │           │
          │  payment)  │      └───────────┘
          └────┬───────┘
               │
        Payment Setup
               │
               ▼
        ┌──────────────┐      Cancelled
        │ IN_PROGRESS  │◄─────────────────┐
        │ (Collecting  │                  │
        │  Payment)    │                  │
        └────┬─────────┘                  │
             │                            │
      ┌──────┴──────┐                     │
      │             │                     │
   Paid      Expired                      │
      │             │                     │
      ▼             └────────────────────┘
   ┌─────────┐
   │ PAID    │
   │ ACTIVE  │
   └─────────┘
```

### 2. **Routine (Schedule) Lifecycle**

```
┌────────────────────────────────────────────────────────────────┐
│           ROUTINE STATE MACHINE                                 │
└────────────────────────────────────────────────────────────────┘

          ┌──────────────────────────────────┐
          │    CREATE ROUTINE                │
          │    (requiresAcceptance = true)   │
          └──────────────┬───────────────────┘
                         │
                         ▼
                  ┌────────────┐
                  │  PAUSED    │ ◄────────────────┐
                  │ (Awaiting  │                  │
                  │ Agreement) │                  │
                  └────┬───────┘                  │
                       │                         │
        ┌──────────────┬┴──────────────┐         │
        │              │               │         │
    Accept      Partial        Student
    All         Accept         Rejects
        │              │               │         │
        │              ▼               ▼         │
        │         ┌─────────┐   ┌──────────────┘
        │         │ PAUSED  │   │
        │         │(Waiting)│   │
        │         └────┬────┘   │
        │              │        │
        │    All Accept│        │
        │              │        │
        └──────┬───────┘        │
               │                │
               ▼                │
        ┌────────────┐          │
        │   ACTIVE   │          │
        │ (Running   │          │
        │  Routine)  │          │
        └────┬───────┘          │
             │                  │
        ┌────┴──────────┐       │
        │               │       │
      Pause          Expire/   │
        │             Archive   │
        ▼               ▼       │
    ┌─────────┐  ┌──────────┐  │
    │ PAUSED  │  │ ARCHIVED │◄─┘
    └─────────┘  └──────────┘
```

### 3. **Schedule Agreement Flow**

```
┌────────────────────────────────────────────────────────────────┐
│       SCHEDULE AGREEMENT STATE MACHINE                          │
└────────────────────────────────────────────────────────────────┘

              ┌──────────────────┐
              │  PROPOSED        │
              │ (Awaiting        │
              │  Acceptance)     │
              └────────┬─────────┘
                       │
            ┌──────────┴──────────┐
            │                     │
        All Accept          Someone
        Agreement           Declines
            │                     │
            ▼                     ▼
     ┌────────────┐      ┌──────────────┐
     │ SCHEDULED  │      │ CANCELLED    │
     │ (Agreed    │      │              │
     │  & Locked) │      └──────────────┘
     └────┬───────┘
          │
   ┌──────┴──────────┐
   │                 │
Happens          Time Passes
   │                 │
   ▼                 ▼
┌────────────┐  ┌──────────────┐
│ COMPLETED  │  │ COMPLETED    │
│ (Held)     │  │ (Auto-mark)  │
└────────────┘  └──────────────┘
```

### 4. **Payment Flow**

```
┌────────────────────────────────────────────────────────────────┐
│         PAYMENT STATE MACHINE                                   │
└────────────────────────────────────────────────────────────────┘

                  ┌────────────┐
                  │ UNPAID     │ ◄──────────────┐
                  │ (No Amount)│               │
                  └────┬───────┘               │
                       │                      │
                 Initiate Payment             │
                       │                      │
                       ▼                      │
                ┌──────────────┐              │
                │ INITIATED    │              │
                │ (Awaiting    │              │
                │ Gateway)     │              │
                └────┬─────────┘              │
                     │                       │
           ┌─────────┴─────────┐             │
           │                   │             │
        Success            Failure           │
           │                   │             │
           ▼                   ▼             │
      ┌────────┐       ┌──────────────┐    │
      │ PARTIAL│       │ FAILED       │────┘
      │ (Some  │       │ (Retry)      │
      │Amount) │       └──────────────┘
      └────┬───┘
           │
      More Payments
           │
           ▼
      ┌─────────┐
      │  PAID   │
      │ (Full)  │
      └─────────┘
```

---

## ✨ Key Features

### 👨‍🏫 For Teachers
- 📝 Create and manage tutoring posts/courses
- 📅 Set up flexible schedules with recurring routines
- 💰 Track payments and earnings
- 📊 Monitor student progress and engagement
- 📢 Post updates and announcements
- 💬 Direct messaging with students
- 📹 Video session support (Daily.co, Jit.si)
- ⏰ Automated routine scheduling with background workers

### 👨‍🎓 For Students
- 🔍 Discover teachers and courses
- 📅 Book sessions and manage schedules
- 💳 Secure payment processing
- 📖 Access course materials
- 💬 Communicate with teachers
- 📹 Join video sessions
- ⭐ Rate and review teachers
- 📚 Track learning progress

### 🛠️ Platform Features
- 🔐 Secure JWT authentication
- 🔄 Real-time notifications via Socket.io
- 💾 MongoDB for persistent data storage
- ☁️ Cloudinary for media management
- 📱 Fully responsive design
- ⚡ Rate limiting & security headers
- 🔗 RESTful API architecture
- 🎯 Type-safe frontend with TypeScript

---

## 🚀 Quick Start

### Prerequisites
- Node.js 16+
- MongoDB instance or MongoDB Atlas
- Cloudinary account (for media uploads)
- Daily.co or Jit.si API keys (for video)

### Frontend Setup
```bash
cd teaching-platform
npm install
npm run dev
# Open http://localhost:3000
```

### Backend Setup
```bash
cd tuition-platform
npm install
cp .env.example .env
# Edit .env with your credentials
npm start
# Server runs on port 5000
```

---

## 📁 Project Structure

### Frontend (`teaching-platform`)
```
teaching-platform/
├── src/
│   ├── app/          # Next.js app directory
│   ├── components/   # Reusable UI components
│   ├── api/          # API client functions
│   ├── lib/          # Utilities & helpers
│   ├── config/       # Configuration files
│   └── styles/       # Tailwind CSS
├── public/           # Static assets
├── package.json
└── next.config.js
```

### Backend (`tuition-platform`)
```
tuition-platform/
├── models/           # MongoDB schemas
│   ├── user.js
│   ├── schedule.js
│   ├── routine.js
│   ├── post.js
│   ├── enrollmentInvite.js
│   ├── payment.js
│   ├── chatThread.js
│   ├── chatMessage.js
│   └── notification.js
├── routes/           # API endpoints
├── controllers/      # Business logic
├── middleware/       # Authentication, validation
├── services/         # Business operations
├── socketUtils/      # WebSocket handlers
├── config/           # Database config
├── utils/            # Helper functions
├── server.js         # Express server entry
└── package.json
```

---

## 🔌 API Endpoints

### Authentication
- `POST /api/auth/signup` - Register new user
- `POST /api/auth/login` - Login user
- `POST /api/auth/logout` - Logout
- `POST /api/auth/refresh` - Refresh token

### Teachers
- `GET /api/teachers` - List all teachers
- `GET /api/teachers/:id` - Get teacher profile
- `POST /api/posts` - Create teaching post
- `GET /api/posts` - List posts
- `PUT /api/posts/:id` - Update post

### Students
- `GET /api/students/:id` - Get student profile
- `GET /api/students/enrolled` - Get enrolled courses

### Schedules
- `POST /api/schedules` - Create schedule
- `GET /api/schedules` - List schedules
- `PUT /api/schedules/:id` - Update schedule
- `DELETE /api/schedules/:id` - Cancel schedule

### Routines
- `POST /api/routines` - Create routine
- `GET /api/routines` - List routines
- `PUT /api/routines/:id` - Update routine
- `POST /api/routines/:id/accept` - Accept routine proposal

### Chat
- `GET /api/chat/threads` - List chat threads
- `POST /api/chat/threads` - Create thread
- `POST /api/chat/messages` - Send message
- `GET /api/chat/threads/:id/messages` - Get messages

### Payments
- `POST /api/payments/initiate` - Start payment
- `GET /api/payments/:id` - Get payment status
- `POST /api/payments/callback` - Payment webhook

### Notifications
- `GET /api/notifications` - Get user notifications
- `PATCH /api/notifications/:id/read` - Mark as read

---

## 📡 Real-time Features (Socket.io)

- **Message notifications**: Instant message delivery
- **Status updates**: Schedule & routine changes
- **Payment alerts**: Payment status updates
- **Presence**: Online/offline status
- **Notifications**: Push notifications to client

---

## 🔐 Security Features

- **JWT Authentication**: Secure token-based auth
- **CORS Protection**: Strict origin validation
- **Rate Limiting**: 600 req/min globally, stricter on auth
- **Helmet.js**: Security headers
- **Password Validation**: Min 6 characters
- **Email Validation**: Format verification
- **Cloudinary Signed Uploads**: Secure media handling
- **MongoDB Injection Prevention**: Mongoose validation

---

## 📦 Deployment

### Frontend (Vercel)
```bash
npm run build
vercel deploy
```

### Backend (Docker/Heroku)
```bash
docker build -t tutogoggy-api .
docker run -p 5000:5000 tutogoggy-api
```

---

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repositories
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📚 Documentation

- [Frontend README](https://github.com/Shabbin/teaching-platform)
- [Backend README](https://github.com/Shabbin/tuition-platform)
- [API Documentation](https://github.com/Shabbin/tuition-platform/wiki)

---

## 📞 Support

For issues and questions:
- Create an [issue](https://github.com/Shabbin/Tutogoggy/issues)
- Check existing discussions
- Contact: [Your Contact Info]

---

## 📄 License

This project is open source and available under the ISC License.

---

## 👤 Author

**Shabbin**
- GitHub: [@Shabbin](https://github.com/Shabbin)

---

<div align="center">

### ⭐ If you found this helpful, please consider giving it a star!

[Visit Live Demo](https://teaching-platform-beige.vercel.app/) • [Frontend Repo](https://github.com/Shabbin/teaching-platform) • [Backend Repo](https://github.com/Shabbin/tuition-platform)

</div>
