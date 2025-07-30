# TruLeadAI

> **Unearth your next business opportunity with AI-powered lead discovery**

TruLeadAI is a modern B2B lead generation platform that combines intelligent discovery algorithms with AI-powered insights to help sales teams find and qualify high-potential prospects efficiently.

##  Features

### Core Functionality
- **Daily Lead Discovery**: Unearth up to 100 leads per day with intelligent filtering
- **AI-Powered Insights**: Get personalized sales angles and conversation starters for each lead
- **Advanced Filtering**: Filter leads by location (continent, country, region, city) and industry category
- **Progress Tracking**: Real-time progress meter showing leads remaining for the day
- **Lead Management**: Organize leads with status tracking, notes, and follow-up reminders

### User Experience
- **Animated Discovery**: Staggered fade-in animations for newly discovered leads
- **Responsive Design**: Mobile-first design that works seamlessly across all devices
- **Role-Based Access**: Separate interfaces for users and administrators
- **Request System**: Users can request new lead categories for admin approval

### Analytics & Insights
- **User Dashboard**: Comprehensive analytics with charts and metrics
- **Task Management**: Calendar integration for scheduling follow-ups
- **Request Tracking**: Monitor the status of lead requests from submission to approval
- **Admin Analytics**: AI-powered insights into user behavior and request trends

##  Technology Stack

### Frontend
- **Framework**: Next.js 15.3.3 with React 18
- **Styling**: Tailwind CSS with custom design system
- **UI Components**: shadcn/ui component library
- **Icons**: Lucide React
- **Charts**: Recharts for data visualization
- **Forms**: React Hook Form with Zod validation

### Backend & AI
- **AI Platform**: Google Genkit with Gemini 2.0 Flash
- **Authentication**: Role-based access (User/Admin simulation)
- **Data Storage**: Static JSON files (ready for database migration)
- **Deployment**: Firebase App Hosting

### Development Tools
- **Language**: TypeScript
- **Build Tool**: Turbopack for fast development
- **Linting**: ESLint with Next.js configuration
- **Package Manager**: npm
- **Development Environment**: Firebase Studio with IDX integration

## 📁 Project Structure

```
src/
├── ai/                     # AI integration and flows
│   ├── genkit.ts          # AI configuration
│   └── flows/             # AI processing logic
├── app/                   # Next.js app router pages
│   ├── admin/             # Admin dashboard
│   ├── dashboard/         # User analytics
│   ├── leads/             # Main lead discovery
│   └── request/           # Lead requests
├── components/            # React components
│   ├── dashboard/         # App-specific components
│   └── ui/               # Reusable UI components
├── hooks/                # Custom React hooks
└── lib/                  # Utilities and types
```

##  Getting Started

### Prerequisites
- Node.js 20 or higher
- npm or yarn package manager
- Google AI API key (for Gemini integration)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd trueleadai
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Environment setup**
   ```bash
   cp .env.example .env.local
   # Add your Google AI API key to .env.local
   GOOGLE_API_KEY=your_api_key_here
   ```

4. **Start development server**
   ```bash
   npm run dev
   ```

5. **Access the application**
   - Open [http://localhost:9002](http://localhost:9002)
   - Choose "Login as User" or "Login as Admin"

### Development Commands

```bash
# Start development server with Turbopack
npm run dev

# Start AI development environment
npm run genkit:dev

# Build for production
npm run build

# Start production server
npm start

# Type checking
npm run typecheck

# Linting
npm run lint
```

##  Data Structure

### Current Implementation
The application currently uses static JSON files for data storage:
- **Lead Data**: `public/data/leads.json` - Contains sample lead information
- **User Preferences**: Browser localStorage for user settings and progress
- **Request Management**: In-memory state with localStorage persistence

### Lead Data Schema
```typescript
interface Lead {
  id: string;
  company: string;
  industry: string;
  location: string;
  contactName: string;
  email: string;
  website: string;
  description: string;
  phoneNumber: string;
  linkedin: string | null;
  facebook: string | null;
  postalCode: string;
  foundedOn: string;
  status: 'new' | 'high-potential' | 'follow-up' | 'not-connected';
  browsed: boolean;
  notes?: string;
  nextTask?: string;
  nextTaskDate?: string;
}
```

##  Database Migration to Cosmos DB

### Current Limitations
- **Static Data**: Limited to pre-loaded JSON dataset
- **No Persistence**: User data stored only in browser localStorage
- **Scalability**: Cannot handle dynamic lead generation or multi-user environments
- **Real-time Updates**: No live data synchronization

### Cosmos DB Integration Plan

#### 1. Database Structure
```typescript
// Collections in Cosmos DB
interface CosmosCollections {
  users: {
    id: string;
    email: string;
    role: 'user' | 'admin';
    preferences: UserPreferences;
    createdAt: Date;
  };
  
  leads: {
    id: string;
    ...LeadData;
    source: string;
    lastUpdated: Date;
    assignedTo?: string;
  };
  
  leadRequests: {
    id: string;
    userId: string;
    category: string;
    continent: string;
    status: 'pending' | 'processing' | 'ready' | 'rejected';
    createdAt: Date;
    processedAt?: Date;
    adminNotes?: string;
  };
  
  userProgress: {
    id: string;
    userId: string;
    date: string;
    leadsUnearthed: number;
    leadsViewed: string[];
    quota: number;
  };
}
```

#### 2. Implementation Steps

**Phase 1: Database Setup**
```bash
# Install Cosmos DB SDK
npm install @azure/cosmos

# Create database connection utility
src/lib/cosmosdb.ts
```

**Phase 2: Authentication Integration**
```typescript
// Replace localStorage with Azure AD B2C
// Implement proper user session management
// Add JWT token validation
```

**Phase 3: API Layer**
```typescript
// Create API routes for data operations
src/app/api/
├── leads/
│   ├── route.ts           # GET leads with filters
│   └── [id]/route.ts      # GET/PUT individual lead
├── requests/
│   ├── route.ts           # GET/POST requests
│   └── [id]/route.ts      # PUT request status
└── analytics/
    └── route.ts           # GET user analytics
```

**Phase 4: Real-time Features**
```typescript
// Implement Change Feed for real-time updates
// Add WebSocket connections for live notifications
// Enable collaborative lead management
```

#### 3. Configuration Example

```typescript
// src/lib/cosmosdb.ts
import { CosmosClient } from '@azure/cosmos';

const client = new CosmosClient({
  endpoint: process.env.COSMOS_DB_ENDPOINT!,
  key: process.env.COSMOS_DB_KEY!,
});

export const database = client.database('trueleadai');
export const containers = {
  leads: database.container('leads'),
  users: database.container('users'),
  requests: database.container('leadRequests'),
  progress: database.container('userProgress'),
};
```

#### 4. Environment Variables
```bash
# Add to .env.local
COSMOS_DB_ENDPOINT=https://your-account.documents.azure.com:443/
COSMOS_DB_KEY=your-primary-key
COSMOS_DB_DATABASE=trueleadai
AZURE_AD_CLIENT_ID=your-client-id
AZURE_AD_CLIENT_SECRET=your-client-secret
AZURE_AD_TENANT_ID=your-tenant-id
```

### Migration Benefits
- **Scalability**: Support thousands of concurrent users
- **Real-time Sync**: Live updates across all user sessions
- **Data Persistence**: Reliable data storage with backup and recovery
- **Advanced Querying**: Complex filtering and search capabilities
- **Global Distribution**: Low-latency access worldwide
- **Security**: Enterprise-grade authentication and authorization

##  Design System

### Colors
- **Primary**: Saturated blue (#4285F4) - Trust and discovery
- **Accent**: Vibrant violet (#8E44AD) - Key actions and highlights
- **Background**: Light blue (#E3F2FD) - Clean, uncluttered feel
- **Typography**: Inter font family for modern readability

### Key UI Principles
- **Mobile-responsive**: Seamless experience across all devices
- **Staggered animations**: Smooth, engaging interactions
- **Clear iconography**: Intuitive navigation and usability
- **Accessible design**: WCAG compliant color contrast and navigation

##  Security Considerations

### Current Implementation
- **Role Simulation**: Basic localStorage-based role management
- **Client-side Storage**: All data stored in browser storage
- **No Authentication**: Simplified login for demo purposes

### Production Security Requirements
- **Azure AD B2C**: Enterprise authentication integration
- **JWT Tokens**: Secure session management
- **API Security**: Rate limiting and input validation
- **Data Encryption**: Encrypt sensitive lead information
- **Audit Logging**: Track all user actions and data access

##  Performance Optimizations

### Current Optimizations
- **Turbopack**: Fast development builds
- **Static Generation**: Optimized build output
- **Component Lazy Loading**: Efficient React rendering
- **Tailwind Purging**: Minimal CSS bundle size

### Recommended Enhancements
- **CDN Integration**: Azure CDN for global content delivery
- **Image Optimization**: Next.js automatic image optimization
- **Caching Strategy**: Redis cache for frequently accessed data
- **Bundle Analysis**: Regular bundle size monitoring

##  Testing Strategy

### Recommended Testing Setup
```bash
# Add testing dependencies
npm install --save-dev jest @testing-library/react @testing-library/jest-dom

# Testing structure
src/
├── __tests__/           # Unit tests
├── components/__tests__/ # Component tests
└── cypress/            # E2E tests
```

##  Deployment

### Firebase App Hosting
The application is configured for Firebase App Hosting with:
- **Auto-scaling**: Handles traffic spikes automatically
- **Global CDN**: Fast content delivery worldwide
- **SSL/TLS**: Automatic HTTPS encryption
- **Custom Domain**: Support for custom domain mapping

### Production Checklist
- [ ] Environment variables configured
- [ ] Cosmos DB instance provisioned
- [ ] Azure AD B2C configured
- [ ] Domain and SSL certificates
- [ ] Monitoring and logging setup
- [ ] Backup and recovery procedures

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.


**TruLeadAI** - Transforming B2B lead generation with intelligent discovery and AI-powered insights.
