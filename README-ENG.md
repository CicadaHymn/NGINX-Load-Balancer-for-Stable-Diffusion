# NGINX Load Balancer for Stable Diffusion
This repository provides an NGINX configuration for load balancing between multiple instances of Stable Diffusion, ensuring fault tolerance and even distribution of requests.

## Requirements

- Installed NGINX
- Running instances of Stable Diffusion

## Installation and Configuration

### 1. NGINX Configuration

1. Copy the configuration file from this repository to /etc/nginx/nginx.conf.
2. In the `upstream stable_diffusion_servers` block, replace the server address `11.11.11` with your server's IP address or domain (e.g., `MyDomain.ngrok-free.dev`). Example configuration:

    upstream stable_diffusion_servers {
        server MyDomain.ngrok-free.dev:7860 max_fails=1 fail_timeout=6s weight=1;
        server 342.268.3.31:7861 max_fails=1 fail_timeout=6s weight=1;
    }

### 2. Running Stable Diffusion

For Stable Diffusion to work correctly with NGINX, you need to enable listening for external requests and start SD on the desired port:

 When launching Stable Diffusion via Python, add the `--listen -port ####`.
 Example:

    python stable_diffusion.py --listen --port 7680

 If you are using `webui-user.bat` to launch, add the parameter to the file::

    set COMMANDLINE_ARGS=--listen --port 7680

For the second instance, use a different port, for example, `7861`.

### 3. Local Hosting
For successful local hosting of NGINX and SD, you must follow the previous steps, but in the `upstream stable_diffusion_servers` block, use your IP address instead of the usual local address "127.0.0.1:7680".
Thus, the upstream block for local hosting will look like this [IP]:[PORT]

Example:
    upstream stable_diffusion_servers {
        server 123.321.2.23:7860 max_fails=1 fail_timeout=6s weight=1;
        server 123.321.2.23:7861 max_fails=1 fail_timeout=6s weight=1;
    }

### Notes
Check NGINX files, in addition to nginx.conf, which can intercept the request, and as a result, the balancer will not work, for correct operation you will need to determine which file intercepts the request and disable the file or resolve the conflict, everything depends on the situation.