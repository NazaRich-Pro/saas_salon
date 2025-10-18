FROM node:20-alpine

WORKDIR /app

# Copy package.json first
COPY package.json ./

# Install ALL dependencies
RUN npm install

# Copy all application files
COPY . .

# Ensure lib directory exists and has the required files
RUN mkdir -p src/lib

# Create utils.ts if it doesn't exist
RUN if [ ! -f src/lib/utils.ts ]; then \
    echo 'import { type ClassValue, clsx } from "clsx";' > src/lib/utils.ts && \
    echo 'import { twMerge } from "tailwind-merge";' >> src/lib/utils.ts && \
    echo '' >> src/lib/utils.ts && \
    echo 'export function cn(...inputs: ClassValue[]) {' >> src/lib/utils.ts && \
    echo '  return twMerge(clsx(inputs));' >> src/lib/utils.ts && \
    echo '}' >> src/lib/utils.ts; \
fi

# Create tenant.ts if it doesn't exist
RUN if [ ! -f src/lib/tenant.ts ]; then \
    echo 'export interface TenantTheme {' > src/lib/tenant.ts && \
    echo '  primary_color?: string;' >> src/lib/tenant.ts && \
    echo '  secondary_color?: string;' >> src/lib/tenant.ts && \
    echo '  logo_url?: string;' >> src/lib/tenant.ts && \
    echo '  font_family?: string;' >> src/lib/tenant.ts && \
    echo '}' >> src/lib/tenant.ts && \
    echo '' >> src/lib/tenant.ts && \
    echo 'export async function getTenantFromHost(host: string) { return null; }' >> src/lib/tenant.ts && \
    echo 'export function applyTenantTheme(theme: TenantTheme) {}' >> src/lib/tenant.ts; \
fi

# Verify files exist
RUN ls -la src/lib/
RUN cat src/lib/utils.ts
RUN cat src/lib/tenant.ts

# Set environment variables
ARG NEXT_PUBLIC_API_URL
ENV NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL:-https://saas.akylman.online/api}
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

# Build the application
RUN npm run build

# Create non-root user
RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nextjs

# Change ownership of the app directory
RUN chown -R nextjs:nodejs /app

# Switch to non-root user
USER nextjs

# Expose port
EXPOSE 3000

ENV PORT=3000
ENV HOSTNAME="0.0.0.0"

# Start the application
CMD ["npm", "start"]