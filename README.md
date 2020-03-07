**GLUSTERFS CLUSTER SETUP USING ANSIBLE**

Ceci est un moyen simple de mettre en place un cluster glusterFS en utilisant ansible.
Ce stack est conçu pour un cluster de 3 VMs avec l'OS DEBIAN 10 .Commencer par clonner ce répository et se placer dans le dossier simple_gluster_cluster.

<code>git clone https://github.com/sowBoy/simple_gluster_cluster.git</code>
<code>cd simple_gluster_cluster</code>

<h3>Prérequis</h3>
<ul>
  <li>Ansible la version la plus récente de préférence</li>
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

<h3>Étape4: Configuration du firewall du pool de storage</h3>

Chaque node doit accepter tout les flux venant des autres nodes du cluster 

<code>ansible-playbook -i hosts firewall-and-trusted-pool-config.yml --extra-vars "storage_one=XX.XX.XX.XX storage_two=YY.YY.YY.YY storage_three=ZZ.ZZ.ZZ.ZZ"</code>

<h3>Étape5: Création d'un volume glusterFS de type Distributed Replicated</h3>

Le type de volume Distributed Replicated est utilisé lorsqu'une haute disponibilité des données en raison de la redondance et du stockage évolutif est requise.

<code>ansible-playbook -i hosts setup-glusterfs-volume.yml --extra-vars "storage_one=172.16.1.236 storage_two=172.16.1.237 storage_three=172.16.1.239"</code>

Il ne reste plus qu'a tester que le volume est bien crée.Il faut se connecter en ssh sur l'un des serveur par exemple le <code>storageOne</code> et lancer ceci <code>gluster volume info</code>.Si tout s'est bien passé le output devrais etre ceci:

<code>Volume Name: tintabGlusterFsVolume
Type: Distributed-Replicate
Volume ID: 347b185d-39a0-4396-8d30-b2497c2051b4
Status: Started
Snapshot Count: 0
Number of Bricks: 2 x 3 = 6
Transport-type: tcp
Bricks:
Brick1: XX.XX.XX.XX:/data/brick2/gv0
Brick2: XX.XX.XX.XX:/data/brick2/gv1
Brick3: YY.YY.YY.YY:/data/brick2/gv0
Brick4: YY.YY.YY.YY:/data/brick2/gv1
Brick5: ZZ.ZZ.ZZ.ZZ:/data/brick2/gv0
Brick6: ZZ.ZZ.ZZ.ZZ:/data/brick2/gv1
Options Reconfigured:
performance.client-io-threads: off
nfs.disable: on
transport.address-family: inet
</code>

<h3>Conclusion</h3>

Parmis les types de volumes kubernetes figure glusterFS.Donc une fois ce cluster monté vous pourrez le connecter à votre kubernetes.Un point que je voudrais signaler est que ce stack manque de flexbilité quant au nombre de machine du cluster.Il y a cerntainement un moyen de réecrire les playbooks firewall-and-trusted-pool-config.yml et setup-glusterfs-volume.yml afin d'assurer cette flexbilité.Il faut donc concevoir ce travail comme quelque chose qu'il est possible d'améliorer.



