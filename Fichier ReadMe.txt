LAB 1 		Sécurité des systèmes

FICHIER READ.ME POUR INSTALLER LES SERVICES APRES LA CONFIGURATION

Ce travail a été réalisé dans le cadre du cours Administration et Sécurités avec les refferences suivantes :

SCAP : Security Content Automation Protocol
Administration et sécurité Linux et Windows
F-E Goffinet
18 novembre 2022

###DATA STREAM

Data stream
├── xccdf
| 	├── benchmark
|	├── profile
| 	| 	├──rule reference
| 	| 	└──variable
|	├── rule
| 	├── human readable data
|	 	├── oval reference
├── oval 	├── ocil reference
├── ocil 	├── cpe reference
└── cpe 	└── remediation


### 
PROFIL
###

Un profil est un ensemble de règles basées sur une politique de sécurité telle que OSPP, PCI-DSS et Health
Insurance Portability and Accountability Act (HIPAA). Il permet d’auditer le système de manière automatisée
pour vérifier la conformité aux normes de sécurité.


Paramètres de départ de l’exercice

A partir d’une station de contrôle qui est capable de gérer des cibles sur un réseau de gestion, il est proposé
d’auditer les politiques de sécurité d’un hôte RedHat nommé srv1.
• cible: machine srv1
• data stream: ssg-centos7-ds-1.2.xml
• profile: xccdf_org.ssgproject.content_profile_standard


### Connection SSH avec une clé RSA 4096 BIT

En considérant que la cible à auditer est srv1, à partir de la station de contrôle on prépare la connexion vers
la cible :

ssh-keygen -b 4096 -t rsa -f $HOME/.ssh/id_rsa -q -N ""
target="srv1"
ssh-copy-id $target
ssh $target "yum -y install scap-security-guide"


Ces opérations visent à :
• Générer une clé RSA
• La copier sur la cible (un mot de passe sera demandé une dernière fois)
• Installer oscap-scanner sur la cible


###AFFICHER SSG 

Veuillez télécharger localement et décompresser le dernière version du contenu Scap Security Guide (SSG)
v0.1.64 et examiner les fichier que vous trouvez dans le nouveau dossier :
./
├── ansible
├── bash
├── guides
├── kickstart
├── ssg-centos7-*.xml
├── ssg-centos8-*.xml
├── ...
├── ssg-jre-*.xml
├── ssg-rhel7-*.xml
├── ssg-rhel8-*.xml
├── ssg-rhel9-*.xml
├── ssg-ubuntu1604-*.xml
├── ssg-ubuntu1804-*.xml
├── ssg-ubuntu2004-*.xml
└── tables
Le projet SSG construit des contenus de sécurité dans différents formats : XCCDF, OVAL et Source DataStream.
Ces documents peuvent être présentés sous différentes formes et par différentes organisations pour répondre
à leurs besoins en matière de sécurité, d’automatisation et de mise en oeuvre technique.
Pour une utilisation générale, on recommandera les Source DataStreams car ils contiennent toutes les données
dont on a besoin pour évaluer et mettre les machines en conformité.
“Ansible” fait référence aux playbooks Ansible générés à partir des profils de de sécurité. Ceux-ci peuvent être
utilisés à la fois en mode check-mode pour évaluer la conformité, ainsi qu’en mode exécution pour mettre les
machines en conformité.
“Bash fix files” fait référence aux scripts Bash générés à partir des profils de sécurité. Ils sont destinés à être
exécutés sur les machines pour les mettre en conformité. On recommandera l’utilisation d’autres formats mais
il parfaitement compréhensible que pour certains scénarios de déploiement Bash soit la seule option.
Le système de construction combine les fichiers de règles YAML faciles à éditer avec les vérifications OVAL, des
extraits de tâches Ansible, des corrections Bash et d’autres fichiers. La modélisation est fournie à chaque étapeafin d’éviter les modèles passe-partout. Les identifiants de sécurité (CCE, NIST ID, STIG, …) apparaissent dans
tous nos formats de sortie mais proviennent tous de fichiers de règles YAML.
prodtype: rhel7
title: 'Configure The Number of Allowed Simultaneous Requests'
description: |-
The <tt>MaxKeepAliveRequests</tt> directive should be set and configured to
<sub idref="var_max_keepalive_requests" /> or greater by setting the following
in <tt>/etc/httpd/conf/httpd.conf</tt>:
<pre>MaxKeepAliveRequests <sub idref="var_max_keepalive_requests" /></pre>
rationale: |-
Resource exhaustion can occur when an unlimited number of concurrent requests
are allowed on a web site, facilitating a denial of service attack. Mitigating
this kind of attack will include limiting the number of concurrent HTTP/HTTPS
requests per IP address and may include, where feasible, limiting parameter
values associated with keepalive, (i.e., a parameter used to limit the amount of
time a connection may be inactive).
severity: medium
identifiers:
cce: "80551-5"
La page d’accueil du SSG est https://www.open-scap.org/security-policies/scap-security-guide/.
On trouvera aussi des ressources dignes d’intérêt sur :
• SSG User Manual
• SSG Developer Guide
• Compliance As Code Blog

###1.3.6 Tâche 4. Consulter le contenu Scap Security Guide (SSG)

COMMANDE

[root@controller ~]# ls
		cd scap-security-guide-0.1.64-oval-5.10





	### 1.3.7 Tâche 5. Découvrir les binaires oscap

Découvrir les binaires oscap :
=>oscap -h

//// AFFICHE LES COMMANDE LIE A L'OUTIL OSCAP

oscap

OpenSCAP command-line tool

Usage: oscap [options] module operation [operation-options-and-arguments]

oscap options:
   -h --help                     - show this help
   -q --quiet                    - quiet mode
   -V --version                  - print info about supported SCAP versions

Commands:
    ds - DataStream utilities
    oval - Open Vulnerability and Assessment Language
    xccdf - eXtensible Configuration Checklist Description Format
    cvss - Common Vulnerability Scoring System
    cpe - Common Platform Enumeration
    cve - Common Vulnerabilities and Exposures
    cvrf - Common Vulnerability Reporting Framework
    info - info module


==>oscap info -h

Affiche des information lié au profil SSG

==>oscap xccdf -h

Permet de faire une evaluation dirigé par un fichier XCCDF 
oscap -> xccdf

eXtensible Configuration Checklist Description Format

Usage: oscap [options] xccdf command [command-specific-options]

Commands:
    eval - Perform evaluation driven by XCCDF file and use OVAL as checking engine
    resolve - Resolve an XCCDF document
    validate - Validate XCCDF XML content
    validate-xml - Validate XCCDF XML content
    export-oval-variables - Export XCCDF values as OVAL external-variables document(s)
    generate - Convert XCCDF Benchmark to other formats
    remediate - Perform remediation driven by XCCDF TestResult file or ARF.

==> oscap xccdf eval -h

oscap -> xccdf -> eval

Perform evaluation driven by XCCDF file and use OVAL as checking engine

Usage: oscap [options] xccdf eval [options] INPUT_FILE [oval-definitions-files]

==>oscap xccdf generate -h

Genere un benchmark dans un certain format a partir d'un fichier XCCDF

oscap -> xccdf -> generate

Convert XCCDF Benchmark to other formats

Usage: oscap [options] xccdf generate [options] <subcommand> [sub-options] benchmark-file.xml

Generate options:
   --profile <profile-id>        - Apply profile with given ID to the Benchmark before further processing takes place.


==> oscap xccdf generate fix -h

oscap -> xccdf -> generate -> fix

Generate a fix script from an XCCDF file

Usage: oscap [options] xccdf generate [options] fix [options] xccdf-file.xml

==> oscap xccdf generate guide -h

oscap -> xccdf -> generate -> guide

Generate security guide

Usage: oscap [options] xccdf generate [options] guide [options] xccdf-file.xml

Generate options:
   --profile <profile-id>        - Apply profile with given ID to the Benchmark before further processing takes place.

Guide Options:
   --output <file>               - Write the document into file.
   --hide-profile-info           - Do not output additional information about selected profile.
   --benchmark-id <id>           - ID of XCCDF Benchmark in some component in the datastream that should be used.
                                   (only applicable for source datastreams)


==> oscap-ssh -h

oscap-ssh -- Tool for running oscap over SSH and collecting results.

Usage:

$ oscap-ssh user@host 22 info INPUT_CONTENT
$ oscap-ssh user@host 22 xccdf eval [options] INPUT_CONTENT






	### 1.3.8 Tâche 6. Afficher des informations sur un Data Stream (DS) SCAP


La sortie est explicite :
• Document type décrit le format du fichier. Les types courants sont XCCDF, OVAL, “Data stream” source
et “Data stream” résultat.
• Imported est la date à laquelle le fichier a été importé pour être utilisé avec OpenSCAP. Comme OpenSCAP
utilise le système de fichiers local et ne possède pas de format de base de données propriétaire, la
date d’importation est la même que la date de modification du fichier.
• Stream est l’ID du “Data stream”.
• Version est la version de la norme SCAP.
• Checklists liste les listes de contrôle disponibles incorporées dans le “Data stream” que l’on peut utiliser
pour l’attribut de ligne de commande --benchmark-id avec oscap xccdf eval. Les
informations détaillées sont également imprimées pour chaque liste de contrôle.
• Status est le statut du Benchmark XCCDF. Les valeurs courantes incluent accepted”, “draft”, “deprecated”
et “incomplete”. Veuillez vous référer à la spécification XCCDF pour plus de détails.
• Generated date est la date à laquelle le fichier a été créé ou généré. Cette date est indiquée pour les
fichiers XCCDF et les listes de contrôle et provient de l’élément XCCDF “Status”.

data_stream="/usr/share/xml/scap/ssg/content/ssg-centos7-ds-1.2.xml"
oscap info --fetch-remote-resources $data_stream

#################################


Sortie ==>

Document type: Source Data Stream
Imported: 2022-11-20T15:04:54

Stream: scap_org.open-scap_datastream_from_xccdf_ssg-rhel7-xccdf-1.2.xml
Generated: (null)
Version: 1.2
Checklists:
        Ref-Id: scap_org.open-scap_cref_ssg-rhel7-xccdf-1.2.xml
                Status: draft
                Generated: 2022-09-30
                Resolved: true
                Profiles:
                        Title: PCI-DSS v3.2.1 Control Baseline for Red Hat Enterprise Linux 7
                                Id: xccdf_org.ssgproject.content_profile_pci-dss
                        Title: Standard System Security Profile for Red Hat Enterprise Linux 7
                                Id: xccdf_org.ssgproject.content_profile_standard
                Referenced check files:
                        ssg-rhel7-oval.xml
                                system: http://oval.mitre.org/XMLSchema/oval-definitions-5
                        ssg-rhel7-ocil.xml
                                system: http://scap.nist.gov/schema/ocil/2
                        https://access.redhat.com/security/data/oval/com.redhat.rhsa-RHEL7.xml.bz2
                                system: http://oval.mitre.org/XMLSchema/oval-definitions-5
Checks:
        Ref-Id: scap_org.open-scap_cref_ssg-rhel7-oval.xml
        Ref-Id: scap_org.open-scap_cref_ssg-rhel7-ocil.xml
        Ref-Id: scap_org.open-scap_cref_ssg-rhel7-cpe-oval.xml
Dictionaries:
        Ref-Id: scap_org.open-scap_cref_ssg-rhel7-cpe-dictionary.xml


#########
############ 1.3.9 Tâche 7. Scan avec un Data Stream (DS) SCAP


type="$target-std-before"
profile="xccdf_org.ssgproject.content_profile_standard"
oscap-ssh --sudo root@$target 22 xccdf eval \
--fetch-remote-resource \
--profile $profile \
--results $type-results.xml \
--report $type-report.html \
--oval-results \
--cpe $cpe_dict \
$data_stream

///// Ne pas oublier d'installer le scanner sur la cible :

ssh-keygen -b 4096 -t rsa -f $HOME/.ssh/id_rsa -q -N ""
target="srv1"
ssh-copy-id $target
ssh $target "yum -y install scap-security-guide"


Verifier les fichiers créer par le scan sur la cible


[root@controller ~]# ls -l srv1*
-rw-r--r-- 1 root root 1801076 2 déc 12:10 srv1-std-before-report.html
-rw-r--r-- 1 root root 13490472 2 déc 12:10 srv1-std-before-results.xml
[root@controller ~]# ls -l ssg*
-rw-r--r-- 1 root root 153159 2 déc 12:10 ssg-rhel7-cpe-oval.xml.result.xml
-rw-r--r-- 1 root root 5095074 2 déc 12:10 ssg-rhel7-oval.xml.result.xml


#############
1.3.10 Tâche 8. Générer un guide de configuration
oscap xccdf generate guide \
--profile $profile \
--output $type-guide.html \
$type-results.xml


################
1.3.11 Tâche 9. Consulter le rapport et le guide de configuration
Ouvrir le pare-feu et lancer un service Web de fortune :

Commande Pare-feu

firewall-cmd --add-port=8080/tcp
firewall-cmd --reload

Commande pour allumer un serveur HTTP sur le controller (Local)

python3 -m http.server 8080

Exemple de guide : 












