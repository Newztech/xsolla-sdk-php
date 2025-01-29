# Xsolla SDK for PHP

[![Latest Stable Version](https://poser.pugx.org/xsolla/xsolla-sdk-php/v/stable.png)](https://packagist.org/packages/xsolla/xsolla-sdk-php)
[![Build Status](https://travis-ci.org/xsolla/xsolla-sdk-php.png?branch=master)](https://travis-ci.org/xsolla/xsolla-sdk-php)
[![Code Coverage](https://scrutinizer-ci.com/g/xsolla/xsolla-sdk-php/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/xsolla/xsolla-sdk-php/?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/xsolla/xsolla-sdk-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/xsolla/xsolla-sdk-php/?branch=master)
[![Downloads](https://poser.pugx.org/xsolla/xsolla-sdk-php/d/total.png)](https://packagist.org/packages/xsolla/xsolla-sdk-php)
[![Join the chat at https://gitter.im/xsolla/xsolla-sdk-php](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/xsolla/xsolla-sdk-php?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/Luamtech/xsolla-sdk-php/main/LICENSE)

An official PHP SDK for seamless integration with [Xsolla API](https://developers.xsolla.com/api/). This SDK streamlines the implementation of payment processing, user authentication, and webhook handling in your PHP applications.

![Payment UI screenshot](http://xsolla.cachefly.net/img/ps3_github2.png)

## Core Features

* **Complete Payment UI Customization**: Flexible token-based system for full control over the payment interface
* **Comprehensive API Integration**: Easy-to-use client for all Xsolla API methods, including:
  * Virtual currency management
  * Item and subscription handling
  * User balance operations
  * Financial reporting through Report API
* **Advanced Webhook Processing**:
  * Simple single-callback implementation
  * Built-in security features (signature authentication and IP whitelisting)
  * Customizable notification handling logic
* **Robust Architecture**: Built on Guzzle v3, featuring:
  * Persistent connections
  * Parallel request processing
  * Event-driven architecture
  * Service descriptions
  * Comprehensive logging
  * Request caching
  * Flexible batching
  * Automatic retry mechanism

## System Requirements

* PHP ^7.3 or ^8.0
* Required PHP extensions:
  * curl
  * json

## Getting Started

1. Register your [Publisher Account](https://publisher.xsolla.com/signup)
2. Create a new project
3. Obtain the following credentials from your [Company Profile](https://publisher.xsolla.com/company) and [Project Settings](https://publisher.xsolla.com/projects):
   * MERCHANT_ID
   * API_KEY
   * PROJECT_ID
   * PROJECT_KEY

## Installation Options

### 1. Docker Installation (Recommended)

The SDK includes Docker support for easy deployment and development. To get started:

1. Clone the repository:
   ```bash
   git clone https://github.com/Luamtech/xsolla-sdk-php.git
   cd xsolla-sdk-php
   ```

2. Configure your environment variables:
   * Copy the example environment file:
     ```bash
     cp .env.example .env
     ```
   * Update the following variables in .env:
     ```
     MERCHANT_ID=your_merchant_id
     API_KEY=your_api_key
     PROJECT_SECRET_KEY=your_project_secret_key
     ```

3. Build and run with Docker Compose:
   ```bash
   docker-compose up -d
   ```

The SDK will be available at `http://localhost:9000`.

You can check the container status using:
```bash
docker ps
```

Example output:
```
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                            PORTS      NAMES
91b72b0574c7   xsolla-sdk-php   "docker-php-entrypoi…"   12 seconds ago   Up 4 seconds (health: starting)   9000/tcp   xsolla-sdk-php
```

The health status indicators help you monitor the container's state:
* `(health: starting)`: Container is initializing
* `(healthy)`: Container is running properly
* `(unhealthy)`: Container has failed health checks

### Healthcheck Benefits

The implemented healthcheck mechanism provides several advantages:
* Automatic monitoring of PHP-FPM service status
* Early detection of service failures
* Prevention of traffic routing to malfunctioning containers
* Facilitates automatic recovery and failover
* Enables container orchestration platforms to make informed scheduling decisions
* Provides visibility into container health without manual intervention

Docker environment features:
* PHP-FPM 7.3 (configurable via build args)
* Built-in health monitoring
* Volume mounting for development
* Automatic restart policy
* Optimized multi-stage build

### 2. Composer Installation

```bash
composer require xsolla/xsolla-sdk-php
```

Then include the autoloader:
```php
require 'vendor/autoload.php';
```

### 3. Manual Installation

* [Download the phar archive](https://github.com/Luamtech/xsolla-sdk-php/releases)
* [Download the zip file](https://github.com/Luamtech/xsolla-sdk-php/releases)

## Usage Examples

### Integrating Payment UI

```php
<?php

use Xsolla\SDK\API\XsollaClient;

$client = XsollaClient::factory([
    'merchant_id' => MERCHANT_ID,
    'api_key' => API_KEY
]);

$paymentUIToken = $client->createCommonPaymentUIToken(
    PROJECT_ID,
    USER_ID,
    $sandboxMode = true
);
```

Implement in your HTML:
```php
<html>
<head lang="en">
    <meta charset="UTF-8">
</head>
<body>
    <button data-xpaystation-widget-open>Buy Credits</button>
    <?php \Xsolla\SDK\API\PaymentUI\PaymentUIScriptRenderer::send($paymentUIToken, $isSandbox = true); ?>
</body>
</html>
```

### Processing Webhooks

```php
<?php

use Xsolla\SDK\Webhook\WebhookServer;
use Xsolla\SDK\Webhook\Message\Message;
use Xsolla\SDK\Webhook\Message\NotificationTypeDictionary;
use Xsolla\SDK\Exception\Webhook\XsollaWebhookException;

$callback = function (Message $message) {
    switch ($message->getNotificationType()) {
        case NotificationTypeDictionary::USER_VALIDATION:
            // Handle user validation
            break;
        case NotificationTypeDictionary::PAYMENT:
            // Process payment
            break;
        case NotificationTypeDictionary::REFUND:
            // Handle refund
            break;
        default:
            throw new XsollaWebhookException('Notification type not implemented');
    }
};

$webhookServer = WebhookServer::create($callback, PROJECT_KEY);
$webhookServer->start();
```

## Development and Testing

When using Docker, you can run tests and development tools:

```bash
# Run tests
docker-compose exec xsolla-sdk-php vendor/bin/phpunit

# Install development dependencies
docker-compose exec xsolla-sdk-php composer install --dev

# Run code style checks
docker-compose exec xsolla-sdk-php vendor/bin/phpcs
```

## Troubleshooting

For common issues and solutions, please refer to our [troubleshooting guide](https://developers.xsolla.com/doc/sdk/#php_sdk_troubleshooting).

## Contributing

Please read our [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## Resources

* [Official Website](http://xsolla.com)
* [Developer Documentation](http://developers.xsolla.com)
* [System Status](http://status.xsolla.com)
* [Support](mailto:integration@xsolla.com)

## Nginx Configuration with Reverse Proxy

To expose your API at `grindinggear.api.luam.tech` through Nginx and forward traffic to the Docker container running on port **9000**, follow these steps:

### 1. Install Nginx and Certbot
```bash
sudo apt install nginx
sudo apt install certbot python3-certbot-nginx
```

### 2. Create the Nginx site configuration
```bash
sudo nano /etc/nginx/sites-available/api
```

### 3. Add the following configuration:
```nginx
server {
    listen 80;
    server_name grindinggear.api.luam.tech;
    
    location / {
        proxy_pass http://localhost:9000;
        proxy_set_header HOST $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 4. Enable the configuration and restart Nginx
```bash
sudo ln -s /etc/nginx/sites-available/api /etc/nginx/sites-enabled/api
sudo systemctl restart nginx
```

### 5. Verify the Nginx configuration
```bash
sudo nginx -t
```
If everything is correct, you should see:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 6. Obtain and renew the SSL certificate
```bash
sudo certbot --nginx -d grindinggear.api.luam.tech
```

### 7. Verify that the container is running
```bash
docker ps
```
If the status appears as **(unhealthy)**, check the container logs:
```bash
docker logs xsolla-sdk-php
```

With this configuration, Nginx will act as a reverse proxy for your API, making it accessible through HTTP.
