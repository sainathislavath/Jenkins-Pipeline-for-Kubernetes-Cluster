FROM node:18 AS builder
WORKDIR /app

# Accept build-time environment variables
ARG REACT_APP_USER_SERVICE_URL
ARG REACT_APP_PRODUCT_SERVICE_URL
ARG REACT_APP_CART_SERVICE_URL
ARG REACT_APP_ORDER_SERVICE_URL

# Set them as environment variables for the build process
ENV REACT_APP_USER_SERVICE_URL=$REACT_APP_USER_SERVICE_URL
ENV REACT_APP_PRODUCT_SERVICE_URL=$REACT_APP_PRODUCT_SERVICE_URL
ENV REACT_APP_CART_SERVICE_URL=$REACT_APP_CART_SERVICE_URL
ENV REACT_APP_ORDER_SERVICE_URL=$REACT_APP_ORDER_SERVICE_URL

# Install dependencies and build the app
COPY package*.json ./
ENV NODE_ENV=development
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
