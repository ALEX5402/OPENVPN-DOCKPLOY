version: '3.8'

services:
  openvpn:
    image: kylemanna/openvpn
    container_name: openvpn_server
    cap_add:
      - NET_ADMIN
    ports:
      - "1194:1194/udp"
    volumes:
      # Renamed to _v2 to bypass the corrupted storage from earlier attempts
      - openvpn_storage_v2:/etc/openvpn
    restart: always
    command: >
      sh -c "
      if [ ! -f /etc/openvpn/ovpn_env.sh ]; then
        echo 'Initializing OpenVPN configuration...';
        ovpn_genconfig -u udp://vpn.example.com:1194;
        
        echo 'Generating PKI keys...';
        export EASYRSA_BATCH=1;
        export EASYRSA_REQ_CN=OpenVPN-Dokploy;
        ovpn_initpki nopass;
      fi;
      echo 'Starting OpenVPN Server...';
      ovpn_run
      "

volumes:
  openvpn_storage_v2:
