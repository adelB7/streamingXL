# StreamingXL Ansible

Ce projet utilise Ansible pour automatiser le déploiement de services de streaming et VPN :
- **Traefik** : Proxy inverse pour gérer HTTPS et le routage.
- **Jellyfin** : Serveur de streaming multimédia.
- **OpenVPN** : VPN pour sécuriser l'accès aux services.

---

## Prérequis

Avant de commencer, assurez-vous que les éléments suivants sont configurés :

1. **Logiciels installés** :
   - Docker : [Installation Docker](https://docs.docker.com/get-docker/)
   - Ansible : [Installation Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

2. **Nom de domaine** :
   - Un nom de domaine doit pointer vers votre **adresse IP publique** (exemple : `streameurxl.adxl697.ovh`).

3. **Redirection des ports** :
   - Configurez votre routeur pour rediriger les ports suivants vers votre serveur :
     - **80** : HTTP
     - **443** : HTTPS
     - **1194** : VPN (UDP)

4. **Configuration réseau sur le serveur (si Ubuntu)** :
   - Autorisez les ports nécessaires dans le pare-feu :
     ```bash
     sudo ufw allow 80
     sudo ufw allow 443
     sudo ufw allow 1194
     ```
   - Vérifiez que l'interface TUN/TAP est active :
     ```bash
     ls -l /dev/net/tun
     ```
     Si le périphérique n'existe pas, créez-le :
     ```bash
     sudo chmod 600 /dev/net/tun
     sudo modprobe tun
     ```

---

## Configuration des Fichiers Multimédias

Pour que **Jellyfin** puisse accéder à vos fichiers multimédias, modifiez le chemin du volume dans le rôle `jellyfin` pour qu’il corresponde à l’emplacement où vos fichiers sont stockés sur votre serveur.

1. **Chemin par défaut dans le rôle** :
   Dans `roles/jellyfin/tasks/main.yml`, le volume de fichiers multimédias est configuré par défaut comme suit :
   ```yaml
   volumes:
     - /media/sf_Film:/media

---

## Structure

- `roles/traefik` : Installation et configuration de Traefik.
- `roles/jellyfin` : Installation et configuration de Jellyfin.
- `roles/openvpn` : Installation et configuration d'OpenVPN.

---

## Configuration des Noms de Domaine

### **1. Jellyfin**
Pour utiliser votre propre nom de domaine avec Jellyfin, modifiez le rôle **Traefik** dans le fichier suivant :
- `roles/jellyfin/tasks/main.yml`

Recherchez la configuration des **labels** et remplacez le domaine par le vôtre :
```yaml
labels:
  traefik.enable: "true"
  traefik.http.routers.jellyfin.entrypoints: "websecure"
  traefik.http.routers.jellyfin.rule: "Host(\"votre-domaine-jellyfin.com\")"
  traefik.http.routers.jellyfin.tls.certresolver: "myresolver"
  traefik.http.services.jellyfin.loadbalancer.server.port: "8096"
  ```
### **2. OpenVPN**

Pour utiliser votre propre nom de domaine avec OpenVPN, modifiez le rôle **OpenVPN** dans le fichier suivant :
- `roles/openvpn/tasks/main.yml`

Recherchez la commande de configuration et remplacez le domaine par le vôtre :
```yaml
- name: Initialiser la configuration OpenVPN
  shell: |
    docker run -v /ServeurXL/openvpn-data:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://votre-domaine-vpn.com
  args:
    executable: /bin/bash
  ```
---

## Utilisation

1. Configurez l'inventaire dans le fichier `inventory` en ajoutant les informations de votre serveur.

2. Lancez le playbook avec la commande suivante :
   ```bash
   sudo ansible-playbook -i inventory playbook.yml
   ```
3. Récuperer le fichier client **UsersXL** et le configurer dans un client OpenVPN.

---

## Enjoy
