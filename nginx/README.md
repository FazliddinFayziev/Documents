# **Setting Up NGINX with SSL on Windows for Node.js Application**

This guide will walk you through setting up an NGINX server on **Windows** to serve a Node.js application securely via HTTPS, with SSL certificates, and configuration for Cloudflare.

## **Prerequisites**
1. **NGINX Installed**: Ensure NGINX is installed on your system.
2. **Node.js Application**: A Node.js application running on port `3333`.
3. **SSL Certificates**: Two certificates, a `.key` and a `.pem` file for SSL encryption.
4. **Cloudflare Account**: Configured DNS settings for your domain.

---

## **Step 1: Install NGINX on Windows**

1. Download **NGINX** from the official website: [NGINX Download](https://nginx.org/en/download.html).
   
2. Extract the NGINX files to a directory (e.g., `C:\nginx`).

---

## **Step 2: Configure NGINX**

### **2.1 NGINX Configuration for SSL & Proxy to Node.js**

1. **Navigate to NGINX Configuration**: Open `nginx.conf` located at `C:\nginx\conf\nginx.conf`.

2. **Edit `nginx.conf` to Proxy Requests to Node.js**:
   Update `nginx.conf` with the following configuration:

```nginx
# Global settings
worker_processes 1;
events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    # Logging settings
    access_log  logs/access.log;
    error_log   logs/error.log;

    # SSL Settings
    ssl_certificate     C:/nginx/certificates/your_certificate.pem;
    ssl_certificate_key C:/nginx/certificates/your_certificate.key;

    # Main server block
    server {
        listen 80;
        server_name athenaguard.eton.uz;  # Replace with your domain

        # Redirect all HTTP traffic to HTTPS
        return 301 https://$server_name$request_uri;
    }

    # HTTPS Server Block
    server {
        listen 443 ssl;
        server_name athenaguard.eton.uz;  # Replace with your domain

        # SSL Configurations
        ssl_certificate     C:/nginx/certificates/your_certificate.pem;
        ssl_certificate_key C:/nginx/certificates/your_certificate.key;
        ssl_protocols       TLSv1.2 TLSv1.3;
        ssl_ciphers         HIGH:!aNULL:!MD5;

        # Proxy to Node.js on port 3333
        location / {
            proxy_pass http://localhost:3333;  # Your Node.js application
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # SSL Error Page
        error_page 497 https://$host$request_uri;
    }
}
```

### **2.2 Explanation**
- **HTTP Server Block**: Redirects all HTTP traffic to HTTPS.
- **HTTPS Server Block**: Handles SSL traffic and proxies requests to your Node.js application running on port `3333`.
- **SSL Configuration**: Paths to your SSL `.pem` and `.key` files. Ensure these files are in `C:/nginx/certificates/` or the specified folder.

---

## **Step 3: Allow HTTP & HTTPS Ports in Windows Firewall**

1. Open **Command Prompt as Administrator**.
2. Run the following commands to allow traffic on ports `80` and `443`:

```cmd
netsh advfirewall firewall add rule name="Open Port 80" protocol=TCP dir=in localport=80 action=allow
netsh advfirewall firewall add rule name="Open Port 443" protocol=TCP dir=in localport=443 action=allow
```

---

## **Step 4: Start NGINX Server**

1. Open **Command Prompt** and navigate to the NGINX folder:
   ```cmd
   cd C:\nginx
   ```

2. Start NGINX:
   ```cmd
   start nginx
   ```

3. To restart or stop NGINX:
   - **Restart**:
     ```cmd
     nginx -s reload
     ```
   - **Stop**:
     ```cmd
     nginx -s stop
     ```

---

## **Step 5: Check NGINX Configuration**

1. Test NGINX configuration to ensure there are no errors:
   ```cmd
   nginx -t
   ```
2. If everything is correct, you should see:
   ```
   nginx: configuration file C:/nginx/conf/nginx.conf test is successful
   ```

---

## **Step 6: Test Server Locally**

1. **Local Testing**:
   Open your browser and go to:
   - **http://127.0.0.1** for HTTP (should redirect to HTTPS).
   - **https://127.0.0.1** for HTTPS (Node.js app should appear).

2. **External Testing**:
   Use your domain name (`athenaguard.eton.uz`) in the browser. Ensure your server’s public IP is correctly set in Cloudflare.

---

## **Step 7: Configure Cloudflare**

1. **Login to Cloudflare** and go to the **DNS Settings** for your domain `athenaguard.eton.uz`.
   
2. **Add DNS Record**:
   - **Type**: `A`
   - **Name**: `@` (for root domain)
   - **Value**: Your server's **public IP address**.
   - **Proxy Status**: Set to **Proxied** (orange cloud) to use Cloudflare's security and caching features.

3. **Configure SSL in Cloudflare**:
   - Go to **SSL/TLS** settings in Cloudflare.
   - Choose **Full SSL** (or **Full (Strict)** if you have valid certificates).

4. **Check Cloudflare Settings**:
   - Go to **Firewall > Tools** in Cloudflare and make sure there are no firewall rules blocking your IP.

---

## **Step 8: Test Everything**

1. **Test in Browser**: Visit `https://athenaguard.eton.uz` and ensure it connects to your Node.js app via NGINX.
2. **Check Logs**:
   - NGINX logs: `C:/nginx/logs/access.log` and `C:/nginx/logs/error.log`.
   - Cloudflare logs: In **Cloudflare Dashboard > Analytics**.

---

## **Troubleshooting**

### **1. Cloudflare Error 522** (Connection Timeout):
- **Cause**: NGINX isn't responding in time or there's a network issue.
- **Solution**:
  - Verify NGINX is running on your machine and that it's accessible.
  - Check if the correct ports (`80`, `443`) are open on your firewall.
  - Increase timeout settings on Cloudflare.

### **2. SSL Certificate Issues**:
- **Cause**: SSL certificates might not be correctly configured.
- **Solution**: Ensure both `.pem` and `.key` files are placed correctly and the paths are accurate in the NGINX configuration.

### **3. Firewall Blocked Ports**:
- **Cause**: Ports might be blocked by Windows firewall or external network issues.
- **Solution**: Run the firewall commands to open ports `80` and `443`, and ensure no external firewall blocks those ports.

---

## **Conclusion**

Now, your NGINX server is set up with SSL certificates to securely serve your Node.js application! Your website `athenaguard.eton.uz` is hosted and secured via HTTPS, and Cloudflare handles your traffic with optimal caching and security features.

---

### **Future Maintenance**:
- **To restart NGINX**: `nginx -s reload`.
- **Check for Updates**: Keep NGINX and your SSL certificates up to date.