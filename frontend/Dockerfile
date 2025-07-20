# Stage 1: Build React App
FROM node:18 AS builder
WORKDIR /app

# Accept build-time environment variables
ARG REACT_APP_USER_SERVICE_URL
ARG REACT_APP_PRODUCT_SERVICE_URL
ARG REACT_APP_CART_SERVICE_URL
ARG REACT_APP_ORDER_SERVICE_URL

ENV REACT_APP_USER_SERVICE_URL=$REACT_APP_USER_SERVICE_URL
ENV REACT_APP_PRODUCT_SERVICE_URL=$REACT_APP_PRODUCT_SERVICE_URL
ENV REACT_APP_CART_SERVICE_URL=$REACT_APP_CART_SERVICE_URL
ENV REACT_APP_ORDER_SERVICE_URL=$REACT_APP_ORDER_SERVICE_URL

# Install dependencies
COPY package*.json ./
RUN npm install --legacy-peer-deps

# Copy source code
COPY . .

# Fix permission issue for react-scripts
RUN chmod +x node_modules/.bin/react-scripts

# Build the app
RUN npm run build

# Stage 2: Serve using NGINX
FROM nginx:alpine

# Clean default static files
RUN rm -rf /usr/share/nginx/html/*

# Copy build output from builder
COPY --from=builder /app/build /usr/share/nginx/html

# Optional: custom nginx config
# COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
