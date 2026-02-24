# NimraCashAndCarry - E-commerce Application

A modern, full-stack e-commerce application with admin dashboard, employee and customer management, inventory, and orders. Built with Next.js, Supabase, Tailwind CSS, and shadcn/ui.

## Features

### Public Features
- 🏠 **Home Page** - Displays featured categories
- 📦 **Products Listing** - Browse all available products
- 🏷️ **Category Pages** - View products by category
- 🔍 **Product Details** - Detailed product information with images

### Admin Dashboard
- 🔐 **Admin Authentication** - Secure login with Supabase Auth
- 📊 **Dashboard** - Overview of categories, products, and orders with pending count and quick actions
- 🏷️ **Category Management** - Full CRUD for categories
- 📦 **Product Management** - Full CRUD for products; image upload; stock and featured flags; product variations
- 📋 **Order Management** - View and manage orders, status updates, order items
- 👥 **Employee Management** - Add, edit, and manage employees (linked to auth)
- 👤 **Customer Management** - Add, edit, and manage customers; approval workflow (pending/approved); employees can create customers for approval
- 📤 **Image Upload** - Product images via Supabase Storage
- 📉 **Inventory** - Product stock tracking with non-negative constraint
- 🛡️ **Protected Routes** - Role-based access (admin vs employee); RLS on all tables

## Tech Stack

- **Framework**: Next.js 16 (App Router)
- **Database**: Supabase (PostgreSQL)
- **Authentication**: Supabase Auth
- **Styling**: Tailwind CSS
- **UI Components**: shadcn/ui
- **Form Validation**: React Hook Form + Zod
- **Language**: TypeScript

## Getting Started

### Prerequisites

- Node.js 18+ and npm
- A Supabase account and project

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd nimracashandcarry
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env.local
   ```
   
   Fill in your Supabase credentials:
   ```env
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   ```

4. **Set up the database**
   - Go to your Supabase project dashboard → SQL Editor
   - Run the scripts in `supabase/sql/` in order: `schema.sql`, then `orders_schema.sql`, `employees_and_customers_schema.sql`, and any migrations (e.g. `customer_approval_migration.sql`, `add_product_variations.sql`) as needed
   - This creates categories, products, orders, order_items, employees, customers, and RLS policies

5. **Set up admin user**
   - In Supabase Dashboard, go to Authentication > Users
   - Create a new user or use an existing one
   - Update the user's metadata to include `role: "admin"`:
     ```json
     {
       "role": "admin"
     }
     ```
   - You can do this via SQL:
     ```sql
     UPDATE auth.users
     SET raw_user_meta_data = jsonb_set(
       COALESCE(raw_user_meta_data, '{}'::jsonb),
       '{role}',
       '"admin"'
     )
     WHERE id = '<user-id>';
     ```

6. **Run the development server**
   ```bash
   npm run dev
   ```

7. **Open your browser**
   Navigate to [http://localhost:3000](http://localhost:3000)

## Project Structure

```
├── src/
│   ├── app/                    # Next.js App Router pages
│   │   ├── admin/              # Admin: dashboard, categories, products, orders, employees, customers
│   │   ├── category/           # Category pages
│   │   ├── products/           # Product pages
│   │   └── layout.tsx          # Root layout
│   ├── components/             # React components
│   │   ├── admin/              # AdminSidebar, ProductTable, OrdersTable, EmployeesTable, CustomersTable, etc.
│   │   └── ui/                 # shadcn/ui components
│   ├── lib/                    # Utilities and server actions
│   │   ├── actions/            # Server actions (products, orders, upload, etc.)
│   │   ├── actions/admin/      # Admin actions (categories, products, orders, employees, customers)
│   │   ├── supabase/           # Supabase clients
│   │   └── utils.ts            # Utility functions
│   └── types/                  # TypeScript types
├── supabase/
│   └── sql/                    # schema, orders, employees/customers, migrations
├── middleware.ts               # Auth and role-based protection
└── components.json             # shadcn/ui configuration
```

## Database Schema

### Categories
- `id`, `name`, `slug`, `created_at`

### Products
- `id`, `name`, `slug`, `description`, `price`, `image_url`, `category_id`, `stock`, `is_featured`, `created_at`  
- Product variations supported via `product_variations` table

### Orders & Order Items
- **orders**: `id`, `customer_name`, `customer_email`, `customer_phone`, `shipping_address`, `city`, `eir`, `vat_number`, `status`, `payment_method`, `total_amount`, `employee_id`, `customer_id`, `created_at`, `updated_at`
- **order_items**: line items per order (product, quantity, price, VAT, variation)

### Employees
- Linked to Supabase Auth; `user_id`, `employee_id`, `name`, `email`, `is_active`, timestamps

### Customers
- `name`, `email`, `phone`, `shipping_address`, `city`, `eir`, `vat_number`, `notes`, `is_active`, `approval_status`, `created_by_employee_id`, timestamps

## Admin Routes

- `/admin` - Dashboard (categories, products, orders counts; quick actions)
- `/admin/login` - Admin login
- `/admin/categories` - Manage categories
- `/admin/categories/new` - Create category
- `/admin/categories/[id]/edit` - Edit category
- `/admin/products` - Manage products (with stock, featured, images, variations)
- `/admin/products/new` - Create product
- `/admin/products/[id]/edit` - Edit product
- `/admin/orders` - View and manage orders (with items)
- `/admin/employees` - Manage employees
- `/admin/employees/new` - Add employee
- `/admin/customers` - Manage customers (approved and pending)
- `/admin/customers/new` - Add customer
- `/admin/customers/[id]/edit` - Edit customer

## Public Routes

- `/` - Home page
- `/products` - All products
- `/products/[slug]` - Product detail page
- `/category/[slug]` - Category page

## Features in Detail

### Authentication & Roles
- Supabase Auth for admin and employee login
- Role-based access: admin (full access) and employee (orders, approved customers, create pending customers)
- Protected admin routes via middleware
- Session management with server and client components

### Data Fetching
- Server Components for initial data loading
- Server Actions for mutations (create, update, delete)
- Automatic revalidation after mutations
- Optimistic updates where applicable

### Form Validation
- Zod schemas for type-safe validation
- React Hook Form for form state management
- Client-side and server-side validation
- User-friendly error messages

### UI/UX
- Responsive design with Tailwind CSS
- shadcn/ui components for consistent UI
- Loading and error states
- Confirmation dialogs for destructive actions

## Development

### Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run start` - Start production server
- `npm run lint` - Run ESLint

### Adding New Components

To add new shadcn/ui components:
```bash
npx shadcn@latest add [component-name]
```

## Deployment

### Vercel (Recommended)

1. Push your code to GitHub
2. Import your repository in Vercel
3. Add environment variables in Vercel dashboard
4. Deploy!

### Environment Variables

Make sure to set these in your deployment platform:
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`

## Security Notes

- Row Level Security (RLS) on all tables (categories, products, orders, order_items, employees, customers)
- Public read for categories and products; admin/employee policies for orders, employees, and customers
- Employees can create customers (pending) and view approved customers; admins approve and manage all
- Admin routes protected by middleware; role checked in middleware and server actions

## More Features

- [ ] Forgot password flow
- [ ] Email setup (SMTP/transactional)
- [ ] Shopping cart and checkout for public
- [ ] Payment integration
- [ ] Search and advanced filtering
- [ ] Pagination on list pages
- [ ] Responsiveness improvements

## License

This project is open source and available under the MIT License.

## Support

For issues and questions, please open an issue on GitHub.

