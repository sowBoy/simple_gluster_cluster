**GLUSTERFS CLUSTER SETUP USING ANSIBLE**

Ceci est un moyen simple de mettre en place un cluster glusterFS en utilisant ansible.
Ce stack est designé pour un cluster de 3 VMs avec l'OS DEBIAN 10 .Commencer par clonner ce répository et se placer dans le dossier.

<code>git clone https://github.com/sowBoy/simple_gluster_cluster.git</code>
<code>cd simple_gluster_cluster</code>

<h3>Prérequis</h3>
<ul>
  <li>Ansible la version la plus récente de préference</li>
  <li>Trois VMs avec DEBIAN 10 comme systeme d'exploitation</li>
</ul>

<h4>NB:</h4>

Le fichier hosts contient la liste des trois VMs

<code>storageOne nom d'hosts de votre server N°1 qui a pour IP XX.XX.XX.XX </code>

<code>storageTwo nom d'hosts de votre server N°2 qui a pour IP YY.YY.YY.YY</code>

<code>storageThree nom d'hosts de votre server N°3 qui a pour IP ZZ.ZZ.ZZ.ZZ</code>

<h3>Étape1: Installation de quelques utilités pour DEBIAN 10</h3>
Lancer ceci :

<code>ansible-playbook -i hosts initial.yml</code>

<h3>Étape2: Création de bricks, formatage et montage</h3>

<code>ansible-playbook -i hosts format-mount-bricks.yml</h3>

<h3>Étape3: Installation de glusterFS et démarage du daemon glusterd</h3>

<code>ansible-playbook -i hosts glusterfs-install.yml</code>

<h3>Étape4: Configuration du firewall</h3>

Chaque node doit accepter tout les flux venant des autres nodes du cluster 

<code>ansible-playbook -i hosts firewall-and-trusted-pool-config.yml --flush-cache --extra-vars "storage_one=XX.XX.XX.XX storage_two=YY.YY.YY.YY storage_three=ZZ.ZZ.ZZ.ZZ"</code>


