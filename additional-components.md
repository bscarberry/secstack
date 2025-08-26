# Additional Component Files to Create

## 1. src/main.jsx
```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './styles/index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

## 2. src/styles/index.css
```css
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;600;700&display=swap');
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  html {
    @apply scroll-smooth;
  }
  
  body {
    @apply antialiased;
  }
}

@layer components {
  .hover-lift {
    @apply transition-all duration-300 ease-in-out;
  }
  
  .hover-lift:hover {
    @apply -translate-y-1 shadow-xl;
  }
  
  .terminal-cursor::after {
    content: '_';
    @apply animate-blink;
  }
  
  .code-comment {
    @apply text-green-600 dark:text-green-500;
  }
  
  .kql-keyword {
    @apply text-blue-500;
  }
  
  .kql-operator {
    @apply text-gray-300;
  }
}

@layer utilities {
  .animate-blink {
    animation: blink 1s infinite;
  }
  
  @keyframes blink {
    0%, 50% { opacity: 1; }
    51%, 100% { opacity: 0; }
  }
}
```

## 3. src/components/Footer.jsx
```jsx
import React from 'react';
import { Link } from 'react-router-dom';

const Footer = () => {
  return (
    <footer className="bg-gray-900/50 border-t border-gray-800 mt-20 py-8 px-6">
      <div className="max-w-7xl mx-auto">
        <div className="flex flex-col md:flex-row items-center justify-between">
          <div className="text-sm text-gray-500 mb-4 md:mb-0">
            <span className="code-comment">/* © 2025 Secure Stack Insights */</span>
          </div>
          <div className="flex items-center space-x-6 text-sm">
            <a 
              href="https://github.com" 
              target="_blank" 
              rel="noopener noreferrer"
              className="text-gray-400 hover:text-green-400 transition-colors"
            >
              GitHub
            </a>
            <Link to="/rss" className="text-gray-400 hover:text-green-400 transition-colors">
              RSS
            </Link>
            <a 
              href="https://twitter.com" 
              target="_blank" 
              rel="noopener noreferrer"
              className="text-gray-400 hover:text-green-400 transition-colors"
            >
              Twitter
            </a>
          </div>
        </div>
      </div>
    </footer>
  );
};

export default Footer;
```

## 4. src/components/CodeSnippet.jsx
```jsx
import React from 'react';

const CodeSnippet = () => {
  return (
    <div className="border-t border-b border-gray-800 bg-gray-900/30 py-8 px-6">
      <div className="max-w-7xl mx-auto">
        <pre className="text-xs text-gray-500 overflow-x-auto">
          <span className="code-comment">// Latest security insights query</span>{'\n'}
          <span className="kql-keyword">SecurityEvents</span>{'\n'}
          <span className="kql-operator">|</span> <span className="text-gray-300">where</span> TimeGenerated <span className="kql-operator">&gt;</span> ago(<span className="text-orange-400">7d</span>){'\n'}
          <span className="kql-operator">|</span> <span className="text-gray-300">summarize</span> Count = count() <span className="text-gray-300">by</span> EventID, Computer{'\n'}
          <span className="kql-operator">|</span> <span className="text-gray-300">top</span> <span className="text-orange-400">10</span> <span className="text-gray-300">by</span> Count <span className="text-gray-300">desc</span>
        </pre>
      </div>
    </div>
  );
};

export default CodeSnippet;
```

## 5. src/pages/BlogPost.jsx
```jsx
import React, { useState, useEffect } from 'react';
import { useParams, Link } from 'react-router-dom';
import { motion } from 'framer-motion';
import { format } from 'date-fns';
import { ArrowLeftIcon } from '@heroicons/react/24/outline';

const BlogPost = () => {
  const { slug } = useParams();
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchPost();
  }, [slug]);

  const fetchPost = async () => {
    try {
      const response = await fetch(`/api/posts/${slug}`);
      const data = await response.json();
      setPost(data);
    } catch (error) {
      console.error('Error fetching post:', error);
    } finally {
      setLoading(false);
    }
  };

  if (loading) {
    return (
      <div className="flex items-center justify-center min-h-screen">
        <div className="text-green-400 animate-pulse">Loading post...</div>
      </div>
    );
  }

  if (!post) {
    return (
      <div className="flex items-center justify-center min-h-screen">
        <div className="text-red-400">Post not found</div>
      </div>
    );
  }

  return (
    <article className="max-w-4xl mx-auto px-6 py-12">
      <motion.div
        initial={{ opacity: 0, y: 20 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ duration: 0.6 }}
      >
        <Link 
          to="/" 
          className="inline-flex items-center space-x-2 text-gray-400 hover:text-green-400 transition-colors mb-8"
        >
          <ArrowLeftIcon className="h-4 w-4" />
          <span>Back to posts</span>
        </Link>

        <div className="mb-8">
          <div className="text-sm text-gray-500 mb-2">
            <span className="code-comment">// {format(new Date(post.date), 'yyyy-MM-dd')}</span>
          </div>
          <h1 className="text-4xl font-bold mb-4">{post.title}</h1>
          <div className="flex items-center space-x-4 text-sm text-gray-400">
            <span>@{post.author}</span>
            <span>•</span>
            <span className="text-cyan-400">#{post.category}</span>
            {post.tags?.map((tag, i) => (
              <span key={i}>#{tag}</span>
            ))}
          </div>
        </div>

        <div 
          className="prose prose-invert prose-lg max-w-none"
          dangerouslySetInnerHTML={{ __html: post.content }}
        />
      </motion.div>
    </article>
  );
};

export default BlogPost;
```

## 6. src/pages/Category.jsx
```jsx
import React, { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';
import { motion } from 'framer-motion';
import PostCard from '../components/PostCard';

const Category = () => {
  const { category } = useParams();
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchCategoryPosts();
  }, [category]);

  const fetchCategoryPosts = async () => {
    try {
      const response = await fetch(`/api/categories/${category}`);
      const data = await response.json();
      setPosts(data);
    } catch (error) {
      console.error('Error fetching category posts:', error);
    } finally {
      setLoading(false);
    }
  };

  const categoryTitle = category.split('-').map(word => 
    word.charAt(0).toUpperCase() + word.slice(1)
  ).join(' ');

  return (
    <div className="min-h-screen px-6 py-12">
      <div className="max-w-7xl mx-auto">
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.6 }}
          className="mb-12"
        >
          <h1 className="text-4xl font-bold mb-4">
            <span className="text-gray-400">Category:</span>{' '}
            <span className="text-green-400">{categoryTitle}</span>
          </h1>
          <p className="text-gray-400">
            All posts tagged with #{category}
          </p>
        </motion.div>

        {loading ? (
          <div className="text-center text-green-400 animate-pulse">
            Loading posts...
          </div>
        ) : (
          <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
            {posts.map((post, index) => (
              <PostCard key={post.slug} post={post} index={index} />
            ))}
          </div>
        )}
      </div>
    </div>
  );
};

export default Category;
```

## 7. src/pages/About.jsx
```jsx
import React from 'react';
import { motion } from 'framer-motion';

const About = () => {
  return (
    <div className="min-h-screen px-6 py-12">
      <div className="max-w-4xl mx-auto">
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.6 }}
        >
          <h1 className="text-4xl font-bold mb-8">
            <span className="text-gray-400">About</span>{' '}
            <span className="text-green-400">SecureStack.Insights</span>
          </h1>
          
          <div className="prose prose-invert prose-lg">
            <p className="text-gray-300">
              Welcome to Secure Stack Insights, a technical blog dedicated to deep dives 
              into Microsoft's security ecosystem, with a focus on Entra ID, Microsoft 
              Defender, and the powerful Kusto Query Language (KQL).
            </p>
            
            <h2 className="text-2xl font-semibold mt-8 mb-4 text-gray-100">Our Mission</h2>
            <p className="text-gray-300">
              We believe in empowering security professionals with practical, actionable 
              knowledge. Our content bridges the gap between documentation and real-world 
              implementation, providing hands-on examples and battle-tested techniques.
            </p>
            
            <h2 className="text-2xl font-semibold mt-8 mb-4 text-gray-100">What We Cover</h2>
            <ul className="space-y-2 text-gray-300">
              <li>• Advanced KQL queries for threat hunting and investigation</li>
              <li>• Microsoft Entra ID (Azure AD) security configurations</li>
              <li>• Microsoft Defender suite implementation and optimization</li>
              <li>• Zero Trust architecture patterns</li>
              <li>• Security automation and orchestration</li>
              <li>• Incident response playbooks</li>
            </ul>
            
            <h2 className="text-2xl font-semibold mt-8 mb-4 text-gray-100">Contributing</h2>
            <p className="text-gray-300">
              We welcome contributions from the security community. If you have insights, 
              queries, or techniques you'd like to share, please reach out through our 
              contact page or submit a pull request to our GitHub repository.
            </p>
          </div>
        </motion.div>
      </div>
    </div>
  );
};

export default About;
```

## 8. src/pages/Contact.jsx
```jsx
import React, { useState } from 'react';
import { motion } from 'framer-motion';

const Contact = () => {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    subject: '',
    message: ''
  });

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Handle form submission here
    console.log('Form submitted:', formData);
  };

  return (
    <div className="min-h-screen px-6 py-12">
      <div className="max-w-2xl mx-auto">
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.6 }}
        >
          <h1 className="text-4xl font-bold mb-8">
            <span className="text-gray-400">Contact</span>{' '}
            <span className="text-green-400">Us</span>
          </h1>
          
          <form onSubmit={handleSubmit} className="space-y-6">
            <div>
              <label htmlFor="name" className="block text-sm text-gray-400 mb-2">
                // name
              </label>
              <input
                type="text"
                id="name"
                name="name"
                value={formData.name}
                onChange={handleChange}
                className="w-full px-4 py-2 bg-gray-900 border border-gray-800 rounded-md text-gray-100 focus:outline-none focus:border-green-500"
                required
              />
            </div>
            
            <div>
              <label htmlFor="email" className="block text-sm text-gray-400 mb-2">
                // email
              </label>
              <input
                type="email"
                id="email"
                name="email"
                value={formData.email}
                onChange={handleChange}
                className="w-full px-4 py-2 bg-gray-900 border border-gray-800 rounded-md text-gray-100 focus:outline-none focus:border-green-500"
                required
              />
            </div>
            
            <div>
              <label htmlFor="subject" className="block text-sm text-gray-400 mb-2">
                // subject
              </label>
              <input
                type="text"
                id="subject"
                name="subject"
                value={formData.subject}
                onChange={handleChange}
                className="w-full px-4 py-2 bg-gray-900 border border-gray-800 rounded-md text-gray-100 focus:outline-none focus:border-green-500"
                required
              />
            </div>
            
            <div>
              <label htmlFor="message" className="block text-sm text-gray-400 mb-2">
                // message
              </label>
              <textarea
                id="message"
                name="message"
                value={formData.message}
                onChange={handleChange}
                rows="6"
                className="w-full px-4 py-2 bg-gray-900 border border-gray-800 rounded-md text-gray-100 focus:outline-none focus:border-green-500"
                required
              />
            </div>
            
            <button
              type="submit"
              className="px-6 py-2 bg-green-500 text-gray-900 font-semibold rounded-md hover:bg-green-400 transition-colors"
            >
              send_message()
            </button>
          </form>
        </motion.div>
      </div>
    </div>
  );
};

export default Contact;
```

## 9. index.html
```html
<!doctype html>
<html lang="en" class="dark">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content="Technical deep dives into Microsoft Entra ID, Defender, and KQL" />
    <title>Secure Stack Insights - Security Blog</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

## 10. postcss.config.js
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## 11. .gitignore
```
# Dependencies
node_modules/
.pnp
.pnp.js

# Production
dist/
build/

# Misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

# Logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*

# Editor directories
.idea/
.vscode/
*.swp
*.swo

# OS files
Thumbs.db
```

## 12. .env.example
```
NODE_ENV=development
PORT=5000
```