● NIS | NFS Server with Client ●

➤ AWS: Create 3 Virtual Machines in the same VPC , 1 Server and 2 Clients 

➤ The goal is to store all of a user's data on the server in case of a computer swap continues to have access to your account in another computer with the same data.

➤ ENTASRV (Server, you can choose any name) (NFS Setup)

    ● nano /etc/hosts
    ● Add this line: 172.31.20.30 entasrv.enta.pt entasrv enta.pt (Change the IP)
    ● apt update && apt -y upgrade && apt install nfs-kernel-server
    ● nano /etc/exports
    ● Add: /var/homes *(rw,sync,no_subtree_check,no_root_squash)
    ● mkdir /var/homes
    ● exportfs -a
    ● systemctl enable --now nfs-kernel-server
    ● adduser maria --home /var/homes/maria
    ● echo xfce4-session > /var/homes/maria/.xsession
    ● chown maria:maria /var/homes/maria/.xsession

➤ ENTACLIENT (Client) (NFS Setup)

    ● apt update && apt -y upgrade && apt install nfs-common
    ● nano /etc/hosts
    ● Add this line: 172.31.20.30 entasrv.enta.pt entasrv enta.pt (Change IP)
    ● mkdir /var/homes
    ● mount -t nfs 172.31.20.30:/var/homes /var/homes/  (Change IP)
    ● cat /etc/mtab
    ● Copy the line above paste here ( /etc/fstab ) and add ,nofail
    
  ![Screenshot_2](https://user-images.githubusercontent.com/85712710/150430559-4d44d7bf-9a3b-4ff6-833a-a2d97517fa13.png)
 
➤ ENTASRV (NIS Setup)

    ● apt -y install nis
      - NIS DOMAIN: enta.pt
    ● nano /etc/default/nis
      - NISSERVER=master
    ● nano /etc/ypserv.securenets
      # This line gives access to everybody. PLEASE ADJUST! (change 0.0.0.0 / 0.0.0.0 to mask and private network)
      255.255.255.0         172.31.0.0
      # add to the end: IP range you allow to access
      255.255.255.0         172.31.0.0
    ● nano /var/yp/Makefile
      - MERGE_PASSWD=true
      - MERGE_GROUP=true
    ● Update Database: /usr/lib/yp/ypinit -m
      list, type a <control D>.
        next host to add:  entasrv.enta.pt
        next host to add:     # Ctrl+D key
      The current list of NIS servers looks like this:
    ● systemctl restart nis
    ● after nis restart: (If you added users in local server, apply them to NIS database)
      - cd /var/yp
      - make
    
    
➤ ENTACLIENT (NIS Setup)
   
    ● apt -y install nis
      - NIS DOMAIN: enta.pt
    ● nano /etc/yp.conf
      # ypserver ypserver.network.com
      # add to the end: [domain name] [server] [NIS server's hostname]
      domain enta.pt server entasrv.enta.pt
    ● nano /etc/nsswitch.conf
      - passwd: #add "nis" to the end
      - group: #add "nis" to the end
      - shadow: #add "nis" to the end
      - hosts: #add "nis" to the end
    ● nano /etc/pam.d/common-session
      - #add: session optional        pam_mkhomedir.so skel=/etc/skel umask=077
    ● systemctl restart nis
    ● if you are in root: login
      - User that you have created early.
      - Password.
      
➤ Graphic Interface (NIS Setup) (CLIENT)

    ● Leave root.
    ● sudo apt install -y xfce4 xfce4-goodies
      - lightdm
    ● sudo apt install -y xrdp chromium-browser filezilla
    ● sudo adduser xrdp ssl-cert
    ● echo xfce4-session > ~/.xsession
    ● sudo reboot

  
