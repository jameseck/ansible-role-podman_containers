Role Name
=========

This role manages podman containers.

Requirements
------------

None

Role Variables
--------------

Available variables are listed below along with default values (`defaults/main.yml`):

    podman_containers: {}
  A dict describing the containers you wish to manage

  Each item in the podman_containers dict uses the following variables:

    name
  The name of the podman container
    image
  The image (e.g. docker.io/helloworld)
    image_tag
  The image tag (e.g. latest)
    params
  A list of params to pass to podman run
    host_dirs
  A dict of host dirs to manage - each item uses the same params as an ansible file object
    templates
  A dict of template files to manage - each item uses the same params as an ansible template object
  These templates should reside in one of the mapped host_dirs
    volumes
  A list of volumes to map into the container


Dependencies
------------

None

Example Playbook
----------------

Below shows an example of using this to manage a Caddy container.  It shows how to use host_dirs and templates

    - hosts: server
      roles:
         - role: jameseck.podman_containers
           podman_containers:
           - name: caddy
             image: localhost/mycaddy
             image_tag: latest
             params:
             - "--net={{ podman_network_name }}"
             - -p 80:80/tcp
             - -p 443:443/tcp
             - "-e CLOUDFLARE_EMAIL={{ dns_cloudflare_email }}"
             - "-e CLOUDFLARE_API_TOKEN={{ dns_cloudflare_api_token }}"
             - -e ACME_AGREE=true
             volumes:
             - /etc/caddy/data:/data
             - /etc/caddy/config:/config
             - /etc/caddy/Caddyfile:/etc/caddy/Caddyfile
             host_dirs:
             - path: /etc/caddy
               state: directory
               owner: root
               group: root
               mode: "0770"
             - path: /etc/caddy/config
               state: directory
               owner: root
               group: root
               mode: "0700"
             - path: /etc/caddy/data
               state: directory
               owner: root
               group: root
               mode: "0700"
             templates:
             - dest: /etc/caddy/Caddyfile
               src: "{{ playbook_dir }}/templates/Caddyfile.j2"
               owner: root
               group: root
               mode: "0600"


License
-------

BSD

Author Information
------------------

James Eckersall <james.eckersall@gmail.com>
