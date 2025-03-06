# Nginx-config-files-Loz  
Config files for: Proxmox, Wiki.js, Hedgedoc, PfSense, OpenVPN Access Server, Visual Studio Code Server  
### Warning!    
**I host these services for my own needs, my services are not accessible to the public.**  
**I use CloudFlare WAF, CloudFlare Zero Trust, CloudFlare reverse proxy, router firewalls, and host firewalls to keep my systems safe.**  
**My point is I have not done much research into securing Nginx for use on publicly accessible infrastructure.**  
**These configs work for me, they might work for you.**
#### I am open to suggestions an how to improve them!  
Thank you. 
## SSL/TLS    
#### This cert/key combo is Cloudflare Edge Certificates     
ssl_certificate /your/path/to/namecloudflare-tls.pem;                                 
ssl_certificate_key /your/path/to/namecloudflare-tls.key;  
**Settings:**  
Total TLS - not applied  
Always Use HTTPS - applied  
HTTP Strict Transport Security (HSTS) - Status: On - Max-Age: 6 months (Recommended) - Include subdomains: On - Preload: On  
Minimum TLS Version - TLS 1.0 (default)  
Opportunistic Encryption - applied  
TLS 1.3 - applied  
Automatic HTTPS Rewrites - applied  
Certificate Transparency Monitoring - applied  
Disable Universal SSL - not applied  
#### This cert is Cloudfalare Origin Certificates   
ssl_client_certificate /your/path/to/namecloudflare-ca.pem;       
**Setting**    
Authenticated Origin Pulls - applied  
If you do not have this setting, remove this line:  
ssl_verify_client on;  
#### One note about SSL/TLS   
Traffic flow: CloudFlare -> Router -> Nginx -> Proxy Pass to Internal Service    
It does not matter if your internal service is HTTP or HTTPS.  
proxy_ssl_verify off; - It does not even check.  
The the SSL/TLS negotiation and encryption is happening between Nginx and Cloudflare, not your internal service and CloudFlare.  
## general.purpose.com.conf
This is my go-to copy and paste configuration.      
The only lines you will have to change for each service is:
- server_name
- proxy_pass (Can pass to both HTTP and HTTPS)
- And if you change the domain you will have to change the Edge Certs as well.      
#### I personally use this configuration for:     
- ProxmoxVE Web Interface
- Hosting Wiki.js
- PfSense Web Interface
- OpenVPN Access Server Web Interface    
## visualstudio.codeserver.com.conf   
How is this configuration any different than general.purpose.com.conf?   
The only difference is these two lines:    
**proxy_set_header Connection upgrade;**       
**proxy_set_header Accept-Encoding gzip;**       
If you do not add these lines, you will be able to access your VScode web page, however you will get the error:   
"An unexpected error occurred that requires a reload of this page. The workbench failed to connect to the server (Error: WebSocket close with status code 1006)"   
## hedgedoc.domain.com.conf     
The main difference between this config file and the general purpose config file is:     
location /socket.io/ block   
## Firewall   
Not really necessary because I have like three other firewalls that traffic will have to pass through before it hits the Nginx rules. But more safety for minimal effort seems like a good choice to me.  
The rules are:   
Allow CloudFlare IP's  
Deny everything else
## Contact: contact@gistyr.dev         
