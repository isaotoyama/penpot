docker compose -p penpot -f docker-compose.yaml up -d
then

http://localhost:9001


stop: docker compose -p penpot -f docker-compose.yaml down


Using the CLI for administrative tasks

docker exec -ti penpot-penpot-backend-1 python3 manage.py create-profile

Update
docker compose -f docker-compose.yaml pull

Example with NGINX#
server {
  listen 80;
  server_name penpot.mycompany.com;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name penpot.mycompany.com;

  # This value should be in sync with the corresponding in the docker-compose.yml
  # PENPOT_HTTP_SERVER_MAX_BODY_SIZE: 31457280
  client_max_body_size 31457280;

  # Logs: Configure your logs following the best practices inside your company
  access_log /path/to/penpot.access.log;
  error_log /path/to/penpot.error.log;

  # TLS: Configure your TLS following the best practices inside your company
  ssl_certificate /path/to/fullchain;
  ssl_certificate_key /path/to/privkey;

  # Websockets
  location /ws/notifications {
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_pass http://localhost:9001/ws/notifications;
  }

  # Proxy pass
  location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Scheme $scheme;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_redirect off;
    proxy_pass http://localhost:9001/;
  }
}
For full documentation, go to the official website

Example with CADDY SERVER#
penpot.mycompany.com {
        reverse_proxy :9001
        tls /path/to/fullchain.pem /path/to/privkey.pem
        log {
            output file /path/to/penpot.log
        }
}