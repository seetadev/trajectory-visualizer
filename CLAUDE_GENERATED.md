# Claude Generated Code

**Task**: Audit the entire codebase and identify improvements in:

🔐 Security
Detect insecure patterns (e.g. unvalidated inputs, unsafe data handling)

Suggest best practices (e.g. input sanitization, environment variable usage).

🛠️ Maintainability
Flag complex or duplicate code, unclear logic, poor naming.

Recommend modularization and better structure.

🚀 Performance
Spot inefficient loops, redundant logic, unnecessary API calls.

Suggest caching or optimization where needed.

🧹 Clean-up
Find dead code, unused variables/functions/imports.

List outdated or unnecessary dependencies.



**Generated on**: Mon Jul 28 15:46:43 UTC 2025

## Generated Content

```
# Codebase Audit and Improvements

## 🔐 Security Issues Found

### Critical Issues:
1. **CORS Configuration Too Permissive** in `vite.config.ts`
2. **Missing Input Validation** throughout the application
3. **No Environment Variable Usage** for sensitive configurations
4. **Unsafe HTML Rendering** potential in markdown components

### Medium Issues:
1. **No Content Security Policy** headers
2. **Missing Authentication/Authorization** patterns
3. **Unsafe File Upload Handling**

## 🛠️ Maintainability Issues

### Major Issues:
1. **Type Duplication** - Multiple type files with overlapping definitions
2. **Poor Component Structure** - Missing clear separation of concerns
3. **No Consistent Error Handling** patterns
4. **Missing Configuration Management**

## 🚀 Performance Issues

### Critical Issues:
1. **No Caching Strategy** for API calls
2. **Potential Memory Leaks** in file processing
3. **No Code Splitting** implementation
4. **Missing Optimization** for large data sets

## 🧹 Clean-up Issues

### Found Issues:
1. **Unused Configuration Files** (`craco.config.js` for Vite project)
2. **Inconsistent Testing Setup** (both jest and vitest configs)
3. **Missing TypeScript Strict Mode** enforcement
4. **Unused Dependencies** potential

---

## Implementation Plan

## FILES_TO_CREATE

```
src/
├── config/
│   ├── environment.ts
│   ├── security.ts
│   └── api-config.ts
├── hooks/
│   ├── useApiCache.ts
│   ├── useErrorHandler.ts
│   └── useFileValidation.ts
├── utils/
│   ├── validation.ts
│   ├── sanitization.ts
│   └── cache.ts
├── types/
│   └── common.ts (consolidated types)
└── constants/
    └── app-constants.ts
```

## FILES_TO_MODIFY

1. `vite.config.ts` - Security improvements
2. `src/App.tsx` - Add error boundaries and security headers
3. `src/types/index.ts` - Consolidate and improve types
4. `package.json` - Remove unused dependencies
5. `tsconfig.json` - Stricter configuration

## CODE_CHANGES

### 1. Create Security Configuration

**FILES_TO_CREATE: `src/config/environment.ts`**
```typescript
// Environment configuration with validation
export interface AppEnvironment {
  API_BASE_URL: string;
  GITHUB_CLIENT_ID?: string;
  MAX_FILE_SIZE: number;
  ALLOWED_FILE_TYPES: string[];
  ENABLE_ANALYTICS: boolean;
}

const validateEnvironment = (): AppEnvironment => {
  const env = {
    API_BASE_URL: import.meta.env.VITE_API_BASE_URL || 'http://localhost:3000',
    GITHUB_CLIENT_ID: import.meta.env.VITE_GITHUB_CLIENT_ID,
    MAX_FILE_SIZE: parseInt(import.meta.env.VITE_MAX_FILE_SIZE || '10485760'), // 10MB
    ALLOWED_FILE_TYPES: (import.meta.env.VITE_ALLOWED_FILE_TYPES || 'json,jsonl,zip').split(','),
    ENABLE_ANALYTICS: import.meta.env.VITE_ENABLE_ANALYTICS === 'true'
  };

  // Validate required environment variables
  if (!env.API_BASE_URL) {
    throw new Error('VITE_API_BASE_URL is required');
  }

  return env;
};

export const environment = validateEnvironment();
```

**FILES_TO_CREATE: `src/config/security.ts`**
```typescript
// Security configuration and utilities
export const SECURITY_CONFIG = {
  CSP_POLICY: {
    'default-src': ["'self'"],
    'script-src': ["'self'", "'unsafe-inline'", "'unsafe-eval'"],
    'style-src': ["'self'", "'unsafe-inline'"],
    'img-src': ["'self'", "data:", "https:"],
    'connect-src': ["'self'", "https://api.github.com"],
    'font-src': ["'self'"],
    'object-src': ["'none'"],
    'media-src': ["'self'"],
    'frame-src': ["'none'"]
  },
  
  CORS_CONFIG: {
    allowedOrigins: [
      'http://localhost:3000',
      'http://localhost:12000',
      process.env.NODE_ENV === 'production' ? 'https://your-domain.com' : ''
    ].filter(Boolean),
    allowedMethods: ['GET', 'POST', 'PUT', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
    credentials: false // Set to true only if needed
  },

  RATE_LIMITING: {
    windowMs: 15 * 60 * 1000, // 15 minutes
    maxRequests: 100 // limit each IP to 100 requests per windowMs
  }
};

export const generateCSPHeader = (): string => {
  return Object.entries(SECURITY_CONFIG.CSP_POLICY)
    .map(([directive, sources]) => `${directive} ${sources.join(' ')}`)
    .join('; ');
};
```

**FILES_TO_CREATE: `src/utils/validation.ts`**
```typescript
// Input validation utilities
import DOMPurify from 'dompurify';

export interface ValidationResult {
  isValid: boolean;
  errors: string[];
  sanitizedValue?: any;
}

export class ValidationError extends Error {
  constructor(public errors: string[]) {
    super(`Validation failed: ${errors.join(', ')}`);
    this.name = 'ValidationError';
  }
}

// File validation
export const validateFile = (file: File): ValidationResult => {
  const errors: string[] = [];
  const maxSize = 10 * 1024 * 1024; // 10MB
  const allowedTypes = ['application/json', 'application/x-jsonl', 'application/zip'];

  if (file.size > maxSize) {
    errors.push(`File size exceeds ${maxSize / 1024 / 1024}MB limit`);
  }

  if (!allowedTypes.includes(file.type) && !file.name.match(/\.(json|jsonl|zip)$/i)) {
    errors.push(`File type not allowed. Allowed types: ${allowedTypes.join(', ')}`);
  }

  return {
    isValid: errors.length === 0,
    errors
  };
};

// String validation with sanitization
export const validateAndSanitizeString = (
  input: string, 
  maxLength: number = 1000,
  allowHtml: boolean = false
): ValidationResult => {
  const errors: string[] = [];

  if (typeof input !== 'string') {
    errors.push('Input must be a string');
    return { isValid: false, errors };
  }

  if (input.length > maxLength) {
    errors.push(`Input exceeds maximum length of ${maxLength} characters`);
  }

  let sanitizedValue = input.trim();
  
  if (!allowHtml) {
    sanitizedValue = DOMPurify.sanitize(sanitizedValue, { ALLOWED_TAGS: [] });
  } else {
    sanitizedValue = DOMPurify.sanitize(sanitizedValue);
  }

  return {
    isValid: errors.length === 0,
    errors,
    sanitizedValue
  };
};

// JSON validation
export const validateJson = (jsonString: string): ValidationResult => {
  const errors: string[] = [];
  let parsedData;

  try {
    parsedData = JSON.parse(jsonString);
  } catch (error) {
    errors.push('Invalid JSON format');
    return { isValid: false, errors };
  }

  // Additional JSON structure validation can be added here
  
  return {
    isValid: true,
    errors: [],
    sanitizedValue: parsedData
  };
};

// URL validation
export const validateUrl = (url: string): ValidationResult => {
  const errors: string[] = [];

  try {
    const urlObject = new URL(url);
    
    // Only allow HTTP and HTTPS protocols
    if (!['http:', 'https:'].includes(urlObject.protocol)) {
      errors.push('URL must use HTTP or HTTPS protocol');
    }

    // Prevent localhost in production
    if (process.env.NODE_ENV === 'production' && 
        (urlObject.hostname === 'localhost' || urlObject.hostname === '127.0.0.1')) {
      errors.push('Localhost URLs not allowed in production');
    }

  } catch (error) {
    errors.push('Invalid URL format');
  }

  return {
    isValid: errors.length === 0,
    errors,
    sanitizedValue: url
  };
};
```

**FILES_TO_CREATE: `src/utils/cache.ts`**
```typescript
// Caching utilities
interface CacheEntry<T> {
  data: T;
  timestamp: number;
  expiresAt: number;
}

interface CacheOptions {
  ttl?: number; // Time to live in milliseconds
  maxSize?: number; // Maximum number of entries
}

export class MemoryCache<T> {
  private cache = new Map<string, CacheEntry<T>>();
  private readonly ttl: number;
  private readonly maxSize: number;

  constructor(options: CacheOptions = {}) {
    this.ttl = options.ttl || 5 * 60 * 1000; // 5 minutes default
    this.maxSize = options.maxSize || 100; // 100 entries default
  }

  set(key: string, data: T, customTtl?: number): void {
    const now = Date.now();
    const expiresAt = now + (customTtl || this.ttl);

    // Remove oldest entries if cache is full
    if (this.cache.size >= this.maxSize) {
      const oldestKey = this.cache.keys().next().value;
      this.cache.delete(oldestKey);
    }

    this.cache.set(key, {
      data,
      timestamp: now,
      expiresAt
    });
  }

  get(key: string): T | null {
    const entry = this.cache.get(key);
    
    if (!entry) {
      return null;
    }

    // Check if expired
    if (Date.now() > entry.expiresAt) {
      this.cache.delete(key);
      return null;
    }

    return entry.data;
  }

  delete(key: string): boolean {
    return this.cache.delete(key);
  }

  clear(): void {
    this.cache.clear();
  }

  // Clean expired entries
  cleanup(): void {
    const now = Date.now();
    for (const [key, entry] of this.cache.entries()) {
      if (now > entry.expiresAt) {
        this.cache.delete(key);
      }
    }
  }

  size(): number {
    return this.cache.size;
  }
}

// Global cache instances
export const apiCache = new MemoryCache<any>({ ttl: 5 * 60 * 1000, maxSize: 50 });
export const fileCache = new MemoryCache<any>({ ttl: 10 * 60 * 1000, maxSize: 20 });
```

**FILES_TO_CREATE: `src/hooks/useErrorHandler.ts`**
```typescript
// Error handling hook
import { useState, useCallback } from 'react';

export interface AppError {
  id: string;
  message: string;
  type: 'error' | 'warning' | 'info';
  timestamp: number;
  details?: any;
}

export const useErrorHandler = () => {
  const [errors, setErrors] = useState<AppError[]>([]);

  const addError = useCallback((
    message: string, 
    type: AppError['type'] = 'error',
    details?: any
  ) => {
    const error: AppError = {
      id: `${Date.now()}-${Math.random()}`,
      message,
      type,
      timestamp: Date.now(),
      details
    };

    setErrors(prev => [...prev, error]);

    // Auto-remove after 5 seconds for non-error types
    if (type !== 'error') {
      setTimeout(() => {
        setErrors(prev => prev.filter(e => e.id !== error.id));
      }, 5000);
    }
  }, []);

  const removeError = useCallback((id: string) => {
    setErrors(prev => prev.filter(error => error.id !== id));
  }, []);

  const clearErrors = useCallback(() => {
    setErrors([]);
  }, []);

  const handleAsyncError = useCallback(async <T>(
    asyncFn: () => Promise<T>,
    errorMessage = 'An error occurred'
  ): Promise<T | null> => {
    try {
      return await asyncFn();
    } catch (error) {
      const message = error instanceof Error ? error.message : errorMessage;
      addError(message, 'error', error);
      return null;
    }
  }, [addError]);

  return {
    errors,
    addError,
    removeError,
    clearErrors,
    handleAsyncError
  };
};
```

**FILES_TO_CREATE: `src/hooks/useApiCache.ts`**
```typescript
// API caching hook
import { useState, useEffect, useCallback } from 'react';
import { apiCache } from '../utils/cache';
import { useErrorHandler } from './useErrorHandler';

interface ApiCacheOptions {
  ttl?: number;
  forceRefresh?: boolean;
  enabled?: boolean;
}

export const useApiCache = <T>(
  key: string,
  fetcher: () => Promise<T>,
  options: ApiCacheOptions = {}
) => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);
  const { addError } = useErrorHandler();

  const {
    ttl,
    forceRefresh = false,
    enabled = true
  } = options;

  const fetchData = useCallback(async (force = false) => {
    if (!enabled) return;

    // Try to get from cache first
    if (!force && !forceRefresh) {
      const cachedData = apiCache.get(key);
      if (cachedData) {
        setData(cachedData);
        return cachedData;
      }
    }

    setLoading(true);
    setError(null);

    try {
      const result = await fetcher();
      apiCache.set(key, result, ttl);
      setData(result);
      return result;
    } catch (err) {
      const error = err instanceof Error ? err : new Error('Unknown error');
      setError(error);
      addError(`Failed to fetch data: ${error.message}`);
      return null;
    } finally {
      setLoading(false);
    }
  }, [key, fetcher, ttl, forceRefresh, enabled, addError]);

  const refresh = useCallback(() => {
    return fetchData(true);
  }, [fetchData]);

  const invalidate = useCallback(() => {
    apiCache.delete(key);
  }, [key]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return {
    data,
    loading,
    error,
    refresh,
    invalidate
  };
};
```

### 2. Update Vite Configuration for Security

**FILES_TO_MODIFY: `vite.config.ts`**
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import viteTsconfigPaths from 'vite-tsconfig-paths';
import { SECURITY_CONFIG } from './src/config/security';

export default defineConfig({
  plugins: [react(), viteTsconfigPaths()],
  server: {
    port: 12000,
    host: 'localhost', // More secure than 0.0.0.0
    headers: {
      'X-Content-Type-Options': 'nosniff',
      'X-Frame-Options': 'DENY',
      'X-XSS-Protection': '1; mode=block',
      'Referrer-Policy': 'strict-origin-when-cross-origin',
      'Permissions-Policy': 'geolocation=(), microphone=(), camera=()',
    },
    cors: {
      origin: SECURITY_CONFIG.CORS_CONFIG.allowedOrigins,
      methods: SECURITY_CONFIG.CORS_CONFIG.allowedMethods,
      allowedHeaders: SECURITY_CONFIG.CORS_CONFIG.allowedHeaders,
      credentials: SECURITY_CONFIG.CORS_CONFIG.credentials
    }
  },
  build: {
    outDir: 'build',
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          markdown: ['react-markdown'],
          utils: ['axios', 'jszip']
        }
      }
    }
  },
  define: {
    'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV || 'development')
  }
});
```

### 3. Consolidate and Improve Types

**FILES_TO_MODIFY: `src/types/index.ts`**
```typescript
// Consolidated and improved type definitions

// Base types
export interface BaseEntity {
  id: string | number;
  created_at?: string;
  updated_at?: string;
}

// API Response types
export interface ApiResponse<T> {
  data: T;
  status: 'success' | 'error';
  message?: string;
  pagination?: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

export interface ApiError {
  status: number;
  message: string;
  code?: string;
  details?: Record<string, any>;
}

// Repository type (improved)
export interface Repository extends BaseEntity {
  name: string;
  full_name: string;
  owner: {
    login: string;
    avatar_url: string;
  };
  description: string | null;
  html_url: string;
  private: boolean;
  fork: boolean;
  archived: boolean;
  disabled: boolean;
  language?: string;
  stargazers_count: number;
  forks_count: number;
}

// Workflow Run type (improved)
export interface WorkflowRun extends BaseEntity {
  name: string | null;
  head_branch: string;
  head_sha: string;
  run_number: number;
  event: string;
  status: 'queued' | 'in_progress' | 'completed';
  conclusion: 'success' | 'failure' | 'neutral' | 'cancelled' | 'timed_out' | 'action_required' | null;
  workflow_id: number;
  url: string;
  html_url: string;
  run_attempt: number;
  run_started_at: string;
  jobs_url: string;
  logs_url: string;
  check_suite_url: string;
  artifacts_url: string;
  cancel_url: string;
  rerun_url: string;
}

// Artifact type (improved)
export interface Artifact extends BaseEntity {
  name: string;
  size_in_bytes: number;
  url: string;
  archive_download_url: string;
  expired: boolean;
  expires_at: string;
  workflow_run: {
    id: number;
    repository_id: number;
    head_repository_id: number;
    head_branch: string;
    head_sha: string;
  };
}

// File processing types
export interface ProcessedFile {
  name: string;
  content: string | ArrayBuffer;
  type: string;
  size: number;
  lastModified: number;
  processing: {
    status: 'pending' | 'processing' | 'completed' | 'failed';
    progress?: number;
    error?: string;
  };
}

// UI State types
export interface LoadingState {
  isLoading: boolean;
  message?: string;
  progress?: number;
}

export interface ErrorState {
  hasError: boolean;
  message?: string;
  details?: any;
  retryable?: boolean;
}

// Form types
export interface FormState<T> {
  values: T;
  errors: Partial<Record<keyof T, string>>;
  touched: Partial<Record<keyof T, boolean>>;
  isSubmitting: boolean;
  isValid: boolean;
}

// Theme types
export type ThemeMode = 'light' | 'dark' | 'system';

export interface ThemeConfig {
  mode: ThemeMode;
  primaryColor?: string;
  secondaryColor?: string;
}
```

### 4. Update TypeScript Configuration

**FILES_TO_MODIFY: `tsconfig.json`**
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    
    // Strict type checking options
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    
    // Additional checks
    "allowJs": false,
    "forceConsistentCasingInFileNames": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "alwaysStrict": true,
    
    "baseUrl": "./src",
    "paths": {
      "@/*": ["*"],
      "@/components/*": ["components/*"],
      "@/utils/*": ["utils/*"],
      "@/types/*": ["types/*"],
      "@/hooks/*": ["hooks/*"],
      "@/config/*": ["config/*"]
    }
  },
  "include": ["src/**/*", "vite.config.ts", "vitest.config.ts"],
  "exclude": ["node_modules", "build", "dist"]
}
```

### 5. Update Package.json

**FILES_TO_MODIFY: `package.json`**
```json
{
  "name": "trajectory-visualizer",
  "version": "0.0.1",
  "author": {
    "name": "Pontus Espe",
    "email": "pontus.espe@devpteam.com"
  },
  "private": true,
  "type": "module",
  "dependencies": {
    "axios": "^1.6.8",
    "clsx": "^2.1.1",
    "diff": "^7.0.0",
    "dompurify": "^3.0.5",
    "jszip": "^3.10.1",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-dropzone": "^14.3.8",
    "react-error-boundary": "^4.0.11",
    "react-icons": "^5.5.0",
    "react-markdown": "^9.1.0",
    "react-router-dom": "^6.22.3",
    "react-syntax-highlighter": "^15.6.1",
    "remark-gfm": "^4.0.1",
    "web-vitals": "^4.2.4"
  },
  "scripts": {
    "start": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "lint": "eslint src --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint src --ext ts,tsx --fix",
    "type-check": "tsc --noEmit",
    "pre-commit": "./pre-commit-check.sh"
  },
  "devDependencies": {
    "@tailwindcss/typography": "^0.5.16",
    "@testing-library/jest-dom": "^6.6.3",
    "@testing-library/react": "^16.2.0",
    "@types/dompurify": "^3.0.5",
    "@types/jest": "^29.5.14",
    "@types/node": "^20.11.30",
    "@types/react": "^18.2.67",
    "@types/react-dom": "^18.2.22",
    "@types/react-syntax-highlighter": "^15.5.13",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.18",
    "eslint": "^8.45.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.3",
    "jsdom": "^26.0.0",
    "postcss": "^8.4.35",
    "tailwind-scrollbar": "^3.0.5",
    "tailwindcss": "^3.4.1",
    "typescript": "^5.0.2",
    "vite": "^5.1.4",
    "vite-tsconfig-paths": "^4.3.1",
    "vitest": "^1.3.1"
  }
}
```

## INSTRUCTIONS

### 1. Security Setup
1. Install new dependencies: `npm install dompurify react-error-boundary @types/dompurify`
2. Create environment file `.env.local` with necessary variables
3. Update CORS configuration based on your actual domains
4. Implement CSP headers in your deployment configuration

### 2. Remove Unused Files
1. Delete `craco.config.js` (not needed for Vite)
2. Remove unused configuration from old CRA setup
3. Clean up duplicate type definitions

### 3. Performance Optimizations
1. Implement the caching layer in your API calls
2. Add code splitting for large components
3. Consider implementing virtual scrolling for large lists

### 4. Error Handling
1. Wrap your App component with ErrorBoundary
2. Implement consistent error handling throughout components
3. Add logging service for production error tracking

### 5. Testing Updates
1. Update tests to use the new validation utilities
2. Add tests for security features
3. Implement integration tests for critical user flows

### 6. Deployment Considerations
1. Set up proper environment variables in your deployment platform
2. Configure CSP headers in your web server
3. Enable security headers in production
4. Set up monitoring for security incidents

This audit addresses the major security, maintainability, performance, and cleanup issues in your codebase. The implementations follow TypeScript and React best practices while maintaining consistency with your existing code style.
```
