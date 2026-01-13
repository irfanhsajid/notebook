# Next.js

Next.js is a React framework that provides features like server-side rendering, static site generation, and API routes out of the box.

## Getting Started

### Installation

```bash
# Create a new Next.js app
npx create-next-app@latest my-app
cd my-app
npm run dev
```

### Project Structure

```
my-app/
├── app/                  # App Router (Next.js 13+)
│   ├── layout.js        # Root layout
│   ├── page.js          # Home page
│   └── about/
│       └── page.js      # About page
├── public/              # Static files
├── styles/              # CSS files
└── next.config.js       # Next.js configuration
```

## Routing

### App Router (Next.js 13+)

#### Pages

Create a `page.js` file in a folder to create a route:

```jsx
// app/page.js - Home page (/)
export default function Home() {
  return <h1>Home Page</h1>;
}

// app/about/page.js - About page (/about)
export default function About() {
  return <h1>About Page</h1>;
}
```

#### Dynamic Routes

```jsx
// app/blog/[slug]/page.js
export default function BlogPost({ params }) {
  return <h1>Blog Post: {params.slug}</h1>;
}
```

#### Layouts

```jsx
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <nav>Navigation Bar</nav>
        {children}
        <footer>Footer</footer>
      </body>
    </html>
  );
}
```

### Navigation

```jsx
import Link from 'next/link';

export default function Nav() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog">Blog</Link>
    </nav>
  );
}
```

## Data Fetching

### Server Components (Default)

```jsx
// app/posts/page.js
async function getPosts() {
  const res = await fetch('https://api.example.com/posts');
  return res.json();
}

export default async function Posts() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map((post) => (
        <div key={post.id}>{post.title}</div>
      ))}
    </div>
  );
}
```

### Client Components

```jsx
'use client'; // Mark as client component

import { useState, useEffect } from 'react';

export default function ClientPosts() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch('https://api.example.com/posts')
      .then((res) => res.json())
      .then((data) => setPosts(data));
  }, []);

  return (
    <div>
      {posts.map((post) => (
        <div key={post.id}>{post.title}</div>
      ))}
    </div>
  );
}
```

## API Routes

### Creating API Endpoints

```javascript
// app/api/hello/route.js
export async function GET(request) {
  return Response.json({ message: 'Hello from Next.js!' });
}

export async function POST(request) {
  const body = await request.json();
  // Process the data
  return Response.json({ success: true, data: body });
}
```

### Dynamic API Routes

```javascript
// app/api/users/[id]/route.js
export async function GET(request, { params }) {
  const userId = params.id;
  // Fetch user data
  return Response.json({ userId, name: 'John Doe' });
}
```

## Image Optimization

```jsx
import Image from 'next/image';

export default function Profile() {
  return (
    <Image
      src="/profile.jpg"
      alt="Profile picture"
      width={500}
      height={500}
      priority
    />
  );
}
```

## Metadata and SEO

```jsx
// app/page.js
export const metadata = {
  title: 'My Next.js App',
  description: 'Welcome to my Next.js application',
  keywords: ['Next.js', 'React', 'JavaScript'],
};

export default function Home() {
  return <h1>Home Page</h1>;
}
```

### Dynamic Metadata

```jsx
// app/blog/[slug]/page.js
export async function generateMetadata({ params }) {
  const post = await getPost(params.slug);

  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

## Static Site Generation (SSG)

```jsx
// Generate static pages at build time
export async function generateStaticParams() {
  const posts = await getPosts();

  return posts.map((post) => ({
    slug: post.slug,
  }));
}
```

## Environment Variables

```bash
# .env.local
DATABASE_URL=postgresql://...
API_KEY=your_api_key_here
NEXT_PUBLIC_API_URL=https://api.example.com
```

```javascript
// Usage in server components
const dbUrl = process.env.DATABASE_URL;

// Usage in client components (must start with NEXT_PUBLIC_)
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

## Middleware

```javascript
// middleware.js
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Check authentication, redirect, etc.
  const token = request.cookies.get('token');

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/profile/:path*'],
};
```

## Configuration

### next.config.js

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['example.com'],
  },
  env: {
    CUSTOM_KEY: 'my-value',
  },
};

module.exports = nextConfig;
```

## Deployment

### Vercel (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel
```

### Build for Production

```bash
npm run build
npm start
```

## Useful Packages

- **@vercel/analytics**: Analytics for Vercel
- **next-auth**: Authentication
- **prisma**: Database ORM
- **swr**: Data fetching hooks
- **tailwindcss**: Styling

