Backend Technologies Used
Technology	Purpose
Python 3.x	Programming language for backend logic and automation
Django (REST Framework)	Backend web framework for API development and database interaction
PostgreSQL	Relational database to store users, stores, and orders
AES Encryption (Crypto.Cipher)	Secure file transmission and storage
Firebase	Real-time updates and live tracking of order statuses
Cloudinary	Cloud storage for encrypted documents
Python Script (Store Side)	Automates file download, decryption, and printing

How the Backend Works (Architecture Flow)
1. User Sends Print Request
The React frontend collects file upload, number of copies, print settings, and store selection.

The request is sent to the Django backend via a REST API endpoint.

2. File Encryption and Upload
The file is:

Encrypted with AES (Advanced Encryption Standard).

Uploaded to Cloudinary with a secure URL.

AES key and IV (initialization vector) are stored in the database for decryption at the store.

3. Order Processing and Storage
A PrintOrder is created in the PostgreSQL database with:

User ID

Store ID

File path (Cloudinary URL)

Print type, page size, number of copies

Status = "pending"

4. Store Side Python Script (Automatic Printing)
This script runs on the store's system and:

Polls Cloudinary for new files using API.

Downloads encrypted files.

Decrypts them using AES key.

Sends files to the local printer (via Windows subprocess and WINWORD.EXE).

Updates the order status to "completed" via Django API call.

5. Authentication and Security
JWT (JSON Web Tokens) is used for login/session.

Permissions:

Users can only access their orders.

Store managers can access their own print queue.

Admins have full access.

6. Real-time Updates
Firebase is used to push order status updates (e.g., "Printing", "Completed") to users in real-time without refreshing the page.

 Key Backend Modules and Their Functions
Module	Description
register() and login()	APIs to handle user/store authentication using JWT
upload_file()	Encrypts and uploads the document to Cloudinary
get_orders()	Returns list of orders for user, store, or admin
get_stores()	Returns store data with location for map-based UI
update_payment_status()	Marks order as “Paid” after in-store payment
get_decryption_key()	Returns AES key to decrypt file for printing
encrypt_file() and decrypt_file()	Handles AES logic with IV padding/unpadding
Models	Store, PrintOrder – define data structure and relations

Security Implementations
AES 256-bit Encryption for uploaded documents.

JWT-based Auth for secure API access.

Role-based Access to APIs depending on user type.

HTTPS and Cloudinary Signed URLs for secure file transmission.

Data Flow Summary
pgsql
Copy
Edit
User (React UI)
     ↓
Django API Server (Python)
     ↓
PostgreSQL DB ←→ Cloudinary (Encrypted Files)
     ↓
Store Python Script
     ↓
Local Printer + Order Update via API
     ↓
Firebase → User gets Real-time Update

Why This Backend Architecture Is Effective
Feature	Benefit
REST APIs (Django DRF)	Modular, scalable, and easy to integrate
Cloudinary + AES	Secure and scalable file storage
PostgreSQL	Reliable structured data storage
Python Script Automation	Hands-free printing for store
Firebase	Real-time updates improve user experience
JWT & RBAC	Strong authentication and access control
