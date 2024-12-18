# StreamingXL Ansible
Ce projet utilise Ansible pour automatiser le déploiement de services de streaming et VPN :
- **Traefik** : Proxy inverse pour gérer HTTPS et le routage.
- **Jellyfin** : Serveur de streaming multimédia.
- **OpenVPN** : VPN pour sécuriser l'accès aux services.

## Structure
- `roles/traefik` : Installation et configuration de Traefik.
- `roles/jellyfin` : Installation et configuration de Jellyfin.
- `roles/openvpn` : Installation et configuration d'OpenVPN.

## Utilisation
1. Configurez l'inventaire dans `inventory`.
2. Lancez le playbook :
   ```bash
   ansible-playbook -i inventory playbook.yml
