# Claude Generated Code

**Task**: Objective:
Help design and implement new features based on the current roadmap or project needs. Focus on clean, scalable, and testable code

Claude Tasks:

🧩 Feature Implementation
Read the existing codebase and integrate new feature modules where appropriate

Ensure new code follows existing patterns and conventions.

🗂️ Example Features (replace/add specifics as needed):
Add user role-based access control

Implement export to PDF

Add multi-language support

Enable dark mode toggle

🧪 Testing & Documentation
Add unit/integration tests for new functionality.

Update README or in-code comments if required.

**Generated on**: Mon Jul 28 15:52:40 UTC 2025

## Generated Content

```
Looking at the current codebase, I can see this is a React TypeScript application with a trajectory visualizer focus, but I notice there's a mismatch - you mentioned it's for "government billing/invoicing" but the codebase appears to be a trajectory visualizer application. I'll implement features that could be applicable to both contexts, focusing on the requested features: user role-based access control, PDF export, multi-language support, and enhanced dark mode functionality.

Let me implement these features following the existing patterns:

## FILES_TO_CREATE

1. `src/contexts/AuthContext.tsx` - Authentication and role-based access control
2. `src/contexts/I18nContext.tsx` - Internationalization context
3. `src/services/pdfExportService.ts` - PDF export functionality
4. `src/components/auth/RoleGuard.tsx` - Role-based component access control
5. `src/components/ui/LanguageSelector.tsx` - Language selection component
6. `src/components/ui/DarkModeToggle.tsx` - Dark mode toggle component
7. `src/components/export/PDFExportButton.tsx` - PDF export button component
8. `src/hooks/useAuth.ts` - Authentication hook
9. `src/hooks/useI18n.ts` - Internationalization hook
10. `src/types/auth.ts` - Authentication types
11. `src/types/i18n.ts` - Internationalization types
12. `src/locales/en.json` - English translations
13. `src/locales/es.json` - Spanish translations
14. `src/utils/auth.ts` - Authentication utilities

## FILES_TO_MODIFY

1. `src/App.tsx` - Add context providers and routing guards
2. `package.json` - Add new dependencies
3. `src/types/index.ts` - Add new type exports

## CODE_CHANGES

### 1. Update package.json

**FILES_TO_MODIFY**: `package.json`

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
    "browser-process-hrtime": "^1.0.0",
    "clsx": "^2.1.1",
    "diff": "^7.0.0",
    "jszip": "^3.10.1",
    "path-browserify": "^1.0.1",
    "react": "^18.2.0",
    "react-diff-viewer-continued": "^3.4.0",
    "react-dom": "^18.2.0",
    "react-dropzone": "^14.3.8",
    "react-icons": "^5.5.0",
    "react-markdown": "^9.1.0",
    "react-router-dom": "^6.22.3",
    "react-syntax-highlighter": "^15.6.1",
    "remark-gfm": "^4.0.1",
    "web-vitals": "^4.2.4",
    "jspdf": "^2.5.1",
    "html2canvas": "^1.4.1",
    "react-to-print": "^2.14.15",
    "jwt-decode": "^4.0.0"
  },
  "scripts": {
    "start": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest run",
    "test:watch": "vitest",
    "pre-commit": "./pre-commit-check.sh"
  },
  "devDependencies": {
    "@tailwindcss/typography": "^0.5.16",
    "@testing-library/jest-dom": "^6.6.3",
    "@testing-library/react": "^16.2.0",
    "@types/jest": "^29.5.14",
    "@types/node": "^20.11.30",
    "@types/react": "^18.2.67",
    "@types/react-dom": "^18.2.22",
    "@types/react-syntax-highlighter": "^15.5.13",
    "@types/testing-library__react": "^10.0.1",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.18",
    "jsdom": "^26.0.0",
    "postcss": "^8.4.35",
    "tailwind-scrollbar": "^3.0.5",
    "tailwindcss": "^3.4.1",
    "typescript": "^5.4.2",
    "vite": "^5.1.6",
    "vite-tsconfig-paths": "^4.3.1",
    "vitest": "^2.1.8",
    "@types/jspdf": "^2.3.0",
    "@types/html2canvas": "^1.0.0",
    "@types/jwt-decode": "^3.1.0"
  }
}
```

### 2. Authentication Types

**FILES_TO_CREATE**: `src/types/auth.ts`

```typescript
export interface User {
  id: string;
  email: string;
  name: string;
  role: UserRole;
  permissions: Permission[];
  avatar?: string;
  department?: string;
  createdAt: string;
  lastLogin?: string;
}

export enum UserRole {
  ADMIN = 'admin',
  MANAGER = 'manager',
  ANALYST = 'analyst',
  VIEWER = 'viewer',
  GUEST = 'guest'
}

export enum Permission {
  // Data permissions
  VIEW_DATA = 'view_data',
  EDIT_DATA = 'edit_data',
  DELETE_DATA = 'delete_data',
  EXPORT_DATA = 'export_data',
  
  // User management
  MANAGE_USERS = 'manage_users',
  VIEW_USERS = 'view_users',
  
  // System administration
  SYSTEM_CONFIG = 'system_config',
  VIEW_LOGS = 'view_logs',
  
  // Billing/Invoice specific
  VIEW_BILLING = 'view_billing',
  EDIT_BILLING = 'edit_billing',
  APPROVE_BILLING = 'approve_billing',
  EXPORT_REPORTS = 'export_reports'
}

export interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
}

export interface LoginCredentials {
  email: string;
  password: string;
}

export interface AuthResponse {
  user: User;
  token: string;
  refreshToken: string;
}
```

### 3. Internationalization Types

**FILES_TO_CREATE**: `src/types/i18n.ts`

```typescript
export interface I18nState {
  language: string;
  translations: Record<string, string>;
  isLoading: boolean;
  error: string | null;
}

export interface TranslationKeys {
  // Common
  'common.loading': string;
  'common.error': string;
  'common.success': string;
  'common.cancel': string;
  'common.save': string;
  'common.delete': string;
  'common.edit': string;
  'common.view': string;
  'common.export': string;
  'common.search': string;
  'common.filter': string;
  'common.sort': string;
  
  // Navigation
  'nav.dashboard': string;
  'nav.reports': string;
  'nav.billing': string;
  'nav.users': string;
  'nav.settings': string;
  'nav.logout': string;
  
  // Auth
  'auth.login': string;
  'auth.logout': string;
  'auth.email': string;
  'auth.password': string;
  'auth.forgotPassword': string;
  'auth.unauthorized': string;
  
  // Features
  'features.darkMode': string;
  'features.lightMode': string;
  'features.exportPDF': string;
  'features.language': string;
  
  // Roles
  'roles.admin': string;
  'roles.manager': string;
  'roles.analyst': string;
  'roles.viewer': string;
  'roles.guest': string;
}

export type SupportedLanguage = 'en' | 'es' | 'fr' | 'de';

export interface LanguageOption {
  code: SupportedLanguage;
  name: string;
  flag: string;
}
```

### 4. Authentication Context

**FILES_TO_CREATE**: `src/contexts/AuthContext.tsx`

```typescript
import React, { createContext, useContext, useReducer, useEffect, ReactNode } from 'react';
import { User, AuthState, LoginCredentials, UserRole, Permission } from '../types/auth';
import { authService } from '../services/authService';

interface AuthContextType extends AuthState {
  login: (credentials: LoginCredentials) => Promise<void>;
  logout: () => void;
  hasPermission: (permission: Permission) => boolean;
  hasRole: (role: UserRole) => boolean;
  hasAnyRole: (roles: UserRole[]) => boolean;
  refreshUser: () => Promise<void>;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

type AuthAction = 
  | { type: 'LOGIN_START' }
  | { type: 'LOGIN_SUCCESS'; payload: User }
  | { type: 'LOGIN_FAILURE'; payload: string }
  | { type: 'LOGOUT' }
  | { type: 'SET_LOADING'; payload: boolean }
  | { type: 'SET_ERROR'; payload: string | null };

const initialState: AuthState = {
  user: null,
  isAuthenticated: false,
  isLoading: true,
  error: null
};

function authReducer(state: AuthState, action: AuthAction): AuthState {
  switch (action.type) {
    case 'LOGIN_START':
      return { ...state, isLoading: true, error: null };
    case 'LOGIN_SUCCESS':
      return {
        ...state,
        user: action.payload,
        isAuthenticated: true,
        isLoading: false,
        error: null
      };
    case 'LOGIN_FAILURE':
      return {
        ...state,
        user: null,
        isAuthenticated: false,
        isLoading: false,
        error: action.payload
      };
    case 'LOGOUT':
      return {
        ...state,
        user: null,
        isAuthenticated: false,
        isLoading: false,
        error: null
      };
    case 'SET_LOADING':
      return { ...state, isLoading: action.payload };
    case 'SET_ERROR':
      return { ...state, error: action.payload };
    default:
      return state;
  }
}

export function AuthProvider({ children }: { children: ReactNode }) {
  const [state, dispatch] = useReducer(authReducer, initialState);

  useEffect(() => {
    // Check for existing session on mount
    checkAuthStatus();
  }, []);

  const checkAuthStatus = async () => {
    try {
      dispatch({ type: 'SET_LOADING', payload: true });
      const user = await authService.getCurrentUser();
      if (user) {
        dispatch({ type: 'LOGIN_SUCCESS', payload: user });
      }
    } catch (error) {
      // No valid session, user needs to login
      dispatch({ type: 'LOGOUT' });
    } finally {
      dispatch({ type: 'SET_LOADING', payload: false });
    }
  };

  const login = async (credentials: LoginCredentials) => {
    try {
      dispatch({ type: 'LOGIN_START' });
      const response = await authService.login(credentials);
      dispatch({ type: 'LOGIN_SUCCESS', payload: response.user });
    } catch (error) {
      const message = error instanceof Error ? error.message : 'Login failed';
      dispatch({ type: 'LOGIN_FAILURE', payload: message });
      throw error;
    }
  };

  const logout = () => {
    authService.logout();
    dispatch({ type: 'LOGOUT' });
  };

  const hasPermission = (permission: Permission): boolean => {
    return state.user?.permissions.includes(permission) ?? false;
  };

  const hasRole = (role: UserRole): boolean => {
    return state.user?.role === role;
  };

  const hasAnyRole = (roles: UserRole[]): boolean => {
    return state.user ? roles.includes(state.user.role) : false;
  };

  const refreshUser = async () => {
    try {
      const user = await authService.getCurrentUser();
      if (user) {
        dispatch({ type: 'LOGIN_SUCCESS', payload: user });
      }
    } catch (error) {
      dispatch({ type: 'LOGOUT' });
    }
  };

  const contextValue: AuthContextType = {
    ...state,
    login,
    logout,
    hasPermission,
    hasRole,
    hasAnyRole,
    refreshUser
  };

  return (
    <AuthContext.Provider value={contextValue}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuthContext = () => {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error('useAuthContext must be used within an AuthProvider');
  }
  return context;
};
```

### 5. I18n Context

**FILES_TO_CREATE**: `src/contexts/I18nContext.tsx`

```typescript
import React, { createContext, useContext, useReducer, useEffect, ReactNode } from 'react';
import { I18nState, SupportedLanguage, LanguageOption } from '../types/i18n';

interface I18nContextType extends I18nState {
  setLanguage: (language: SupportedLanguage) => Promise<void>;
  t: (key: string, params?: Record<string, string | number>) => string;
  availableLanguages: LanguageOption[];
}

const I18nContext = createContext<I18nContextType | undefined>(undefined);

type I18nAction =
  | { type: 'SET_LOADING'; payload: boolean }
  | { type: 'SET_LANGUAGE'; payload: { language: string; translations: Record<string, string> } }
  | { type: 'SET_ERROR'; payload: string | null };

const initialState: I18nState = {
  language: 'en',
  translations: {},
  isLoading: true,
  error: null
};

const availableLanguages: LanguageOption[] = [
  { code: 'en', name: 'English', flag: '🇺🇸' },
  { code: 'es', name: 'Español', flag: '🇪🇸' },
  { code: 'fr', name: 'Français', flag: '🇫🇷' },
  { code: 'de', name: 'Deutsch', flag: '🇩🇪' }
];

function i18nReducer(state: I18nState, action: I18nAction): I18nState {
  switch (action.type) {
    case 'SET_LOADING':
      return { ...state, isLoading: action.payload };
    case 'SET_LANGUAGE':
      return {
        ...state,
        language: action.payload.language,
        translations: action.payload.translations,
        isLoading: false,
        error: null
      };
    case 'SET_ERROR':
      return { ...state, error: action.payload, isLoading: false };
    default:
      return state;
  }
}

export function I18nProvider({ children }: { children: ReactNode }) {
  const [state, dispatch] = useReducer(i18nReducer, initialState);

  useEffect(() => {
    // Load default language on mount
    const savedLanguage = localStorage.getItem('app-language') as SupportedLanguage || 'en';
    setLanguage(savedLanguage);
  }, []);

  const loadTranslations = async (language: SupportedLanguage): Promise<Record<string, string>> => {
    try {
      const module = await import(`../locales/${language}.json`);
      return module.default;
    } catch (error) {
      console.error(`Failed to load translations for ${language}:`, error);
      // Fallback to English if loading fails
      if (language !== 'en') {
        const fallback = await import('../locales/en.json');
        return fallback.default;
      }
      throw error;
    }
  };

  const setLanguage = async (language: SupportedLanguage) => {
    try {
      dispatch({ type: 'SET_LOADING', payload: true });
      const translations = await loadTranslations(language);
      dispatch({
        type: 'SET_LANGUAGE',
        payload: { language, translations }
      });
      localStorage.setItem('app-language', language);
    } catch (error) {
      const message = error instanceof Error ? error.message : 'Failed to load language';
      dispatch({ type: 'SET_ERROR', payload: message });
    }
  };

  const t = (key: string, params?: Record<string, string | number>): string => {
    let translation = state.translations[key] || key;
    
    // Replace parameters in translation
    if (params) {
      Object.entries(params).forEach(([paramKey, paramValue]) => {
        translation = translation.replace(`{{${paramKey}}}`, String(paramValue));
      });
    }
    
    return translation;
  };

  const contextValue: I18nContextType = {
    ...state,
    setLanguage,
    t,
    availableLanguages
  };

  return (
    <I18nContext.Provider value={contextValue}>
      {children}
    </I18nContext.Provider>
  );
}

export const useI18nContext = () => {
  const context = useContext(I18nContext);
  if (context === undefined) {
    throw new Error('useI18nContext must be used within an I18nProvider');
  }
  return context;
};
```

### 6. Auth Service

**FILES_TO_CREATE**: `src/services/authService.ts`

```typescript
import { User, LoginCredentials, AuthResponse, UserRole, Permission } from '../types/auth';
import axios from 'axios';
import jwtDecode from 'jwt-decode';

class AuthService {
  private readonly TOKEN_KEY = 'auth-token';
  private readonly REFRESH_TOKEN_KEY = 'auth-refresh-token';

  async login(credentials: LoginCredentials): Promise<AuthResponse> {
    try {
      // In a real app, this would be an API call
      // For demo purposes, we'll simulate authentication
      const response = await this.simulateLogin(credentials);
      
      // Store tokens
      localStorage.setItem(this.TOKEN_KEY, response.token);
      localStorage.setItem(this.REFRESH_TOKEN_KEY, response.refreshToken);
      
      return response;
    } catch (error) {
      throw new Error('Invalid credentials');
    }
  }

  logout(): void {
    localStorage.removeItem(this.TOKEN_KEY);
    localStorage.removeItem(this.REFRESH_TOKEN_KEY);
  }

  async getCurrentUser(): Promise<User | null> {
    const token = localStorage.getItem(this.TOKEN_KEY);
    if (!token) {
      return null;
    }

    try {
      // Decode token to get user info
      const decoded: any = jwtDecode(token);
      
      // Check if token is expired
      if (decoded.exp * 1000 < Date.now()) {
        await this.refreshToken();
      }

      return this.getUserFromToken(token);
    } catch (error) {
      this.logout();
      return null;
    }
  }

  private async simulateLogin(credentials: LoginCredentials): Promise<AuthResponse> {
    // Simulate API delay
    await new Promise(resolve => setTimeout(resolve, 1000));

    // Demo users with different roles
    const demoUsers = [
      {
        email: 'admin@example.com',
        password: 'admin123',
        user: {
          id: '1',
          email: 'admin@example.com',
          name: 'Admin User',
          role: UserRole.ADMIN,
          permissions: Object.values(Permission),
          department: 'IT',
          createdAt: '2023-01-01T00:00:00Z'
        }
      },
      {
        email: 'manager@example.com',
        password: 'manager123',
        user: {
          id: '2',
          email: 'manager@example.com',
          name: 'Manager User',
          role: UserRole.MANAGER,
          permissions: [
            Permission.VIEW_DATA,
            Permission.EDIT_DATA,
            Permission.EXPORT_DATA,
            Permission.VIEW_BILLING,
            Permission.EDIT_BILLING,
            Permission.EXPORT_REPORTS
          ],
          department: 'Finance',
          createdAt: '2023-01-01T00:00:00Z'
        }
      },
      {
        email: 'analyst@example.com',
        password: 'analyst123',
        user: {
          id: '3',
          email: 'analyst@example.com',
          name: 'Analyst User',
          role: UserRole.ANALYST,
          permissions: [
            Permission.VIEW_DATA,
            Permission.EDIT_DATA,
            Permission.EXPORT_DATA,
            Permission.VIEW_BILLING
          ],
          department: 'Analytics',
          createdAt: '2023-01-01T00:00:00Z'
        }
      }
    ];

    const matchingUser = demoUsers.find(
      u => u.email === credentials.email && u.password === credentials.password
    );

    if (!matchingUser) {
      throw new Error('Invalid credentials');
    }

    // Generate mock JWT token
    const token = this.generateMockToken(matchingUser.user);
    const refreshToken = this.generateMockRefreshToken(matchingUser.user);

    return {
      user: matchingUser.user,
      token,
      refreshToken
    };
  }

  private generateMockToken(user: User): string {
    // In a real app, this would be generated by your backend
    const payload = {
      sub: user.id,
      email: user.email,
      role: user.role,
      exp: Math.floor(Date.now() / 1000) + (60 * 60), // 1 hour
      iat: Math.floor(Date.now() / 1000)
    };
    
    // This is just a mock - real tokens would be properly signed
    return btoa(JSON.stringify(payload));
  }

  private generateMockRefreshToken(user: User): string {
    const payload = {
      sub: user.id,
      type: 'refresh',
      exp: Math.floor(Date.now() / 1000) + (60 * 60 * 24 * 7), // 1 week
      iat: Math.floor(Date.now() / 1000)
    };
    
    return btoa(JSON.stringify(payload));
  }

  private getUserFromToken(token: string): User {
    const decoded: any = jwtDecode(token);
    
    // In a real app, you'd fetch full user data from an API
    // This is a simplified version for demo purposes
    return {
      id: decoded.sub,
      email: decoded.email,
      name: `User ${decoded.sub}`,
      role: decoded.role,
      permissions: this.getPermissionsForRole(decoded.role),
      createdAt: '2023-01-01T00:00:00Z'
    };
  }

  private getPermissionsForRole(role: UserRole): Permission[] {
    switch (role) {
      case UserRole.ADMIN:
        return Object.values(Permission);
      case UserRole.MANAGER:
        return [
          Permission.VIEW_DATA,
          Permission.EDIT_DATA,
          Permission.EXPORT_DATA,
          Permission.VIEW_BILLING,
          Permission.EDIT_BILLING,
          Permission.EXPORT_REPORTS
        ];
      case UserRole.ANALYST:
        return [
          Permission.VIEW_DATA,
          Permission.EDIT_DATA,
          Permission.EXPORT_DATA,
          Permission.VIEW_BILLING
        ];
      case UserRole.VIEWER:
        return [Permission.VIEW_DATA, Permission.VIEW_BILLING];
      default:
        return [];
    }
  }

  private async refreshToken(): Promise<void> {
    const refreshToken = localStorage.getItem(this.REFRESH_TOKEN_KEY);
    if (!refreshToken) {
      throw new Error('No refresh token available');
    }

    // In a real app, you'd call your refresh endpoint
    // For demo, we'll just generate a new token
    const decoded: any = jwtDecode(refreshToken);
    const newToken = this.generateMockToken({ id: decoded.sub } as User);
    localStorage.setItem(this.TOKEN_KEY, newToken);
  }
}

export const authService = new AuthService();
```

### 7. PDF Export Service

**FILES_TO_CREATE**: `src/services/pdfExportService.ts`

```typescript
import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';

export interface PDFExportOptions {
  filename?: string;
  format?: 'a4' | 'letter' | 'legal';
  orientation?: 'portrait' | 'landscape';
  margins?: {
    top: number;
    right: number;
    bottom: number;
    left: number;
  };
  includeMetadata?: boolean;
  watermark?: string;
}

class PDFExportService {
  private readonly defaultOptions: PDFExportOptions = {
    filename: 'export.pdf',
    format: 'a4',
    orientation: 'portrait',
    margins: {
      top: 20,
      right: 20,
      bottom: 20,
      left: 20
    },
    includeMetadata: true
  };

  async exportElementToPDF(
    elementId: string,
    options: Partial<PDFExportOptions> = {}
  ): Promise<void> {
    const finalOptions = { ...this.defaultOptions, ...options };
    const element = document.getElementById(elementId);

    if (!element) {
      throw new Error(`Element with id "${elementId}" not found`);
    }

    try {
      // Create canvas from the element
      const canvas = await html2canvas(element, {
        scale: 2, // Higher resolution
        useCORS: true,
        allowTaint: true,
        backgroundColor: '#ffffff'
      });

      // Initialize PDF
      const pdf = new jsPDF({
        orientation: finalOptions.orientation,
        unit: 'mm',
        format: finalOptions.format
      });

      // Get PDF dimensions
      const pdfWidth = pdf.internal.pageSize.getWidth();
      const pdfHeight = pdf.internal.pageSize.getHeight();

      // Calculate image dimensions
      const canvasWidth = canvas.width;
      const canvasHeight = canvas.height;
      const ratio = canvasWidth / canvasHeight;

      const availableWidth = pdfWidth - finalOptions.margins!.left - finalOptions.margins!.right;
      const availableHeight = pdfHeight - finalOptions.margins!.top - finalOptions.margins!.bottom;

      let imgWidth = availableWidth;
      let imgHeight = imgWidth / ratio;

      // If image is too tall, scale to fit height
      if (imgHeight > availableHeight) {
        imgHeight = availableHeight;
        imgWidth = imgHeight * ratio;
      }

      // Add the image to PDF
      const imgData = canvas.toDataURL('image/png');
      pdf.addImage(
        imgData,
        'PNG',
        finalOptions.margins!.left,
        finalOptions.margins!.top,
        imgWidth,
        imgHeight
      );

      // Add metadata if requested
      if (finalOptions.includeMetadata) {
        this.addMetadata(pdf, finalOptions);
      }

      // Add watermark if specified
      if (finalOptions.watermark) {
        this.addWatermark(pdf, finalOptions.watermark);
      }

      // Save the PDF
      pdf.save(finalOptions.filename!);
    } catch (error) {
      console.error('PDF export failed:', error);
      throw new Error('Failed to export PDF');
    }
  }

  async exportDataToPDF(
    data: any,
    title: string,
    options: Partial<PDFExportOptions> = {}
  ): Promise<void> {
    const finalOptions = { ...this.defaultOptions, ...options };

    try {
      const pdf = new jsPDF({
        orientation: finalOptions.orientation,
        unit: 'mm',
        format: finalOptions.format
      });

      let yPosition = finalOptions.margins!.top;

      // Add title
      pdf.setFontSize(20);
      pdf.setFont('helvetica', 'bold');
      pdf.text(title, finalOptions.margins!.left, yPosition);
      yPosition += 15;

      // Add timestamp
      pdf.setFontSize(10);
      pdf.setFont('helvetica', 'normal');
      pdf.text(
        `Generated on: ${new Date().toLocaleString()}`,
        finalOptions.margins!.left,
        yPosition
      );
      yPosition += 15;

      // Add data content
      pdf.setFontSize(12);
      const content = this.formatDataForPDF(data);
      
      const splitText = pdf.splitTextToSize(
        content,
        pdf.internal.pageSize.getWidth() - finalOptions.margins!.left - finalOptions.margins!.right
      );

      pdf.text(splitText, finalOptions.margins!.left, yPosition);

      // Add metadata if requested
      if (finalOptions.includeMetadata) {
        this.addMetadata(pdf, finalOptions);
      }

      // Add watermark if specified
      if (finalOptions.watermark) {
        this.addWatermark(pdf, finalOptions.watermark);
      }

      // Save the PDF
      pdf.save(finalOptions.filename!);
    } catch (error) {
      console.error('PDF export failed:', error);
      throw new Error('Failed to export PDF');
    }
  }

  private formatDataForPDF(data: any): string {
    if (typeof data === 'string') {
      return data;
    }
    
    if (typeof data === 'object') {
      return JSON.stringify(data, null, 2);
    }
    
    return String(data);
  }

  private addMetadata(pdf: jsPDF, options: PDFExportOptions): void {
    // Add creation info as metadata
    const pageCount = pdf.getNumberOfPages();
    const pageHeight = pdf.internal.pageSize.getHeight();
    
    pdf.setPage(pageCount);
    pdf.setFontSize(8);
    pdf.setTextColor(128, 128,
```
