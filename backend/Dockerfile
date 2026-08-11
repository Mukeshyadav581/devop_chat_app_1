# Stage 1: Build
FROM node:18-alpine AS builder

# Set working directory
WORKDIR /app

# Install dependencies
COPY package.json package-lock.json ./
RUN npm ci --only=production

# Copy source code
COPY . .


# Stage 2: Production
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Create non-root user with explicit UID/GID
RUN addgroup -S appgroup -g 1001 && \
    adduser -S appuser -u 1001 -G appgroup

# Copy dependencies from builder
COPY --from=builder /app/node_modules ./node_modules

# Copy application files
COPY --from=builder /app ./

# Set environment variables
ENV NODE_ENV=production

# Give application ownership to non-root user
RUN chown -R appuser:appgroup /app

# Expose application port
EXPOSE 5001

# Switch to non-root user
USER 1001

# Start the server
CMD ["npm", "start"]

# Docker health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:5001/health || exit 1