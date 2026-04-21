# Digitic E-Commerce Server (Backend API)

## 1. Project Overview

The **Digitic Server** is a RESTful API backend that powers the Digitic e-commerce platform. It handles authentication, product management, orders, payments (Stripe), image uploads (Cloudinary), and email notifications.

**Target Users:** Developers integrating with the Digitic platform, Admin/Client frontends

---

## 2. Tech Stack

| Category         | Technology             |
| ------------------| ------------------------|
| Runtime          | Node.js                |
| Framework        | Express.js 4.19        |
| Database         | MongoDB (Mongoose 8.5) |
| Authentication   | JWT (jsonwebtoken)     |
| Password Hashing | bcrypt                 |
| Payments         | Stripe SDK             |
| File Upload      | Multer, Cloudinary     |
| Email            | Nodemailer             |
| Image Processing | Sharp                  |
| Logging          | Morgan                 |
| Environment      | dotenv                 |

---

## 3. Features

### Authentication & Authorization
- User registration and login
- Admin login with role verification
- JWT access tokens (1-day expiry)
- Refresh tokens (3-day expiry, httpOnly cookies)
- Password reset via email tokens
- Protected routes with middleware

### User Management
- Create/update user profiles
- Block/unblock users (admin only)
- Address management
- Wishlist functionality

### Product Catalog
- CRUD operations for products
- Product categories and brands
- Color and size variants
- Product ratings and reviews
- Image upload to Cloudinary
- Slug-based URLs

### Shopping Cart
- Add/remove items
- Update quantities
- Empty cart
- Cart persistence per user

### Order Management
- Create orders from cart
- Order status tracking
- View order history
- Admin order management
- Monthly/annual sales reports

### Payment Processing
- Stripe PaymentIntent integration
- Payment verification
- Secure checkout flow

### Content Management
- Blog posts with rich content
- Blog categories
- Contact form handling (enquiries)

### Coupon System
- Discount coupon creation
- Coupon validation

### Email Notifications
- Password reset emails
- Order confirmations (extensible)

---

## 4. Project Structure

```
server/
├── config/
│   ├── dbConnection.js      # MongoDB connection
│   ├── jwtToken.js          # JWT generation
│   └── refreshToken.js      # Refresh token generation
├── controllers/
│   ├── userController.js    # Auth, users, orders, cart
│   ├── productController.js # Products, ratings, wishlist
│   ├── blogController.js    # Blog posts
│   ├── blogCategoryController.js
│   ├── productCategoryController.js
│   ├── brandController.js
│   ├── colorController.js
│   ├── couponController.js
│   ├── enquiryController.js
│   ├── uploadController.js  # Cloudinary uploads
│   ├── paymentController.js # Stripe payments
│   └── emailController.js   # Nodemailer setup
├── middlewares/
│   ├── authMiddleware.js    # JWT verification
│   └── errorHandler.js      # Global error handling
├── models/
│   ├── userModel.js
│   ├── productModel.js
│   ├── cartModel.js
│   ├── orderModel.js
│   ├── blogModel.js
│   ├── blogCategoryModel.js
│   ├── productCategoryModel.js
│   ├── brandModel.js
│   ├── colorModel.js
│   ├── couponModel.js
│   └── enquiryModel.js
├── routes/
│   ├── authRoutes.js        # User/auth endpoints
│   ├── productRoutes.js
│   ├── blogRoutes.js
│   ├── blogCategoryRoutes.js
│   ├── productCategoryRoutes.js
│   ├── brandRoutes.js
│   ├── colorRoutes.js
│   ├── couponRoutes.js
│   ├── enquiryRoutes.js
│   └── uploadRoutes.js
├── utils/
│   ├── cloudinary.js        # Cloudinary config
│   └── validateMongoDBId.js # ID validation
├── index.js                 # Entry point
├── .env                     # Environment variables (gitignored)
└── package.json
```

---

## 5. Setup Instructions

### Prerequisites
- Node.js 18+ installed
- MongoDB database (local or Atlas)
- Cloudinary account (for image uploads)
- Stripe account (for payments)
- Gmail/Email service (for nodemailer)

### Installation

```bash
# Navigate to server directory
cd server

# Install dependencies
npm install
```

### Environment Variables

Create a `.env` file in the `server/` root:

```env
# Server
PORT=5000

# Database
MONGODB_URL=mongodb+srv://username:password@cluster.mongodb.net/digitic

# JWT
JWT_SECRET=your_super_secret_jwt_key

# Cloudinary
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Stripe
STRIPE_SECRET_SECRET=sk_test_your_stripe_secret_key

# Email (Gmail SMTP)
NODEMAILER_USER=your_email@gmail.com
NODEMAILER_PASSWORD=your_app_password
```

### Running the Project

```bash
# Development (with nodemon auto-restart)
npm run dev

# Production
npm start
```

Default server: `http://localhost:5000`

---

## 6. API Routes

### Authentication & Users (`/api/users`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/register` | Create new user | No |
| POST | `/login` | User login | No |
| POST | `/admin-login` | Admin login | No |
| GET | `/all-users` | List all users | Admin |
| GET | `/refresh` | Refresh access token | No (cookie) |
| GET | `/logout` | Logout user | Yes |
| PUT | `/password` | Change password | Yes |
| POST | `/forgot-password-token` | Request reset token | No |
| PUT | `/reset-password/:token` | Reset password | No |
| PUT | `/edit-user` | Update profile | Yes |
| GET | `/wishlist` | Get wishlist | Yes |
| PUT | `/address` | Save address | Yes |
| GET | `/:id` | Get single user | Yes |
| PUT | `/block-user/:id` | Block user | Admin |
| PUT | `/unblock-user/:id` | Unblock user | Admin |
| DELETE | `/:id` | Delete user | Yes |

### Cart (`/api/users`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/cart` | Add to cart |
| GET | `/cart` | Get user cart |
| DELETE | `/cart/:cartId` | Remove item |
| PUT | `/cart/:cartId/:newQuantity` | Update quantity |
| DELETE | `/empty-cart` | Clear cart |

### Orders (`/api/users`)

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/orders` | Create order | Yes |
| GET | `/orders` | Get my orders | Yes |
| GET | `/all-orders` | Get all orders | Admin |
| GET | `/orders/:id` | Get single order | Admin |
| PUT | `/orders/:id` | Update order status | Admin |
| POST | `/order/checkout` | Stripe checkout | Yes |
| POST | `/order/payment-verification` | Verify payment | Yes |
| GET | `/monthly-orders` | Monthly stats | Admin |
| GET | `/annual-orders` | Annual stats | Admin |

### Products (`/api/products`)

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/` | List all products | No |
| POST | `/` | Create product | Admin |
| GET | `/:id` | Get single product | No |
| PUT | `/:id` | Update product | Admin |
| DELETE | `/:id` | Delete product | Admin |
| PUT | `/wishlist` | Toggle wishlist | Yes |
| PUT | `/rating` | Add rating | Yes |

### Categories (`/api/product-categories`, `/api/blog-categories`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | List categories |
| POST | `/` | Create category |
| GET | `/:id` | Get category |
| PUT | `/:id` | Update category |
| DELETE | `/:id` | Delete category |

### Blogs (`/api/blogs`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | List blogs |
| POST | `/` | Create blog |
| GET | `/:id` | Get blog |
| PUT | `/:id` | Update blog |
| DELETE | `/:id` | Delete blog |

### Brands (`/api/brands`), Colors (`/api/colors`), Coupons (`/api/coupons`)

Similar CRUD operations as categories.

### Enquiries (`/api/enquiries`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/` | Submit enquiry |
| GET | `/` | List enquiries |
| GET | `/:id` | Get enquiry |
| PUT | `/:id` | Update enquiry |
| DELETE | `/:id` | Delete enquiry |

### Uploads (`/api/uploads`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/` | Upload image to Cloudinary |
| DELETE | `/:id` | Delete image |

---

## 7. Scripts

| Script | Command | Purpose |
|--------|---------|---------|
| start | `node index.js` | Run production server |
| dev | `nodemon index.js` | Development with auto-restart |

---

## 8. Key Dependencies Explained

### Security
- **bcrypt**: Password hashing
- **jsonwebtoken**: JWT generation/verification
- **cookie-parser**: HTTP-only cookies for refresh tokens

### Data
- **mongoose**: MongoDB ODM with schema validation
- **express-async-handler**: Async error catching

### File Handling
- **multer**: Multipart form data handling
- **sharp**: Image resizing/processing
- **cloudinary**: Cloud image storage

### Payment
- **stripe**: Payment processing SDK

### Communication
- **nodemailer**: SMTP email sending

---

## 9. Deployment Notes

### Platform Recommendations
- **Render**: Easy Node.js deployment with environment variables
- **Railway**: Simple deployment with MongoDB integration
- **Heroku**: Traditional PaaS option
- **AWS EC2**: Full control for larger scale

### Production Checklist
1. Set `NODE_ENV=production`
2. Use strong, unique JWT_SECRET
3. Configure CORS for your frontend domain
4. Set up MongoDB Atlas with IP whitelist
5. Configure Cloudinary with proper transformations
6. Use Stripe live keys (not test)
7. Set up email service with proper SPF/DKIM

### Security Considerations
- Always use HTTPS in production
- Store sensitive keys in environment variables
- Implement rate limiting (consider `express-rate-limit`)
- Validate all inputs (already handled via Mongoose)
- Use helmet.js for security headers

### CORS Configuration
Edit `index.js` for production:
```javascript
app.use(cors({
  origin: 'https://your-frontend-domain.com',
  credentials: true
}));
```
