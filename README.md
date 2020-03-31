# Projet 5 Gestion d'un intranet - Infrastructure et SI - Ynov Informatique 2019/2020

## Sommaire

- [Présentation](#Présentation)
    - [Maquette attendue](#Maquette-attendue)
    - [Idées technos possibles](#Idées-technos-possibles)

---

## Présentation

Mise en œuvre d’une gestion simplifiée de l’ensemble de services IT d’une entreprise :
annuaire, gestion de mail, prise de notes, agenda, gestion de fichiers, etc.

- Qui propose des applications internes :
    - Serveur mail, serveur web, gestion d’agenda
- Gestion d’utilisateurs et de groupes avec l’annuaire
    - PAM (pour GNU/LINUX), portail captif, VPN, etc
- La plateforme doit mettre en œuvre un SSO fonctionnel
- Interface « access management » (lien vers les applications internes)
- Accès distant (VPN)

### Maquette attendue

- L’infrastructure est fonctionnelle
- Les services doivent être répartis sur plusieurs hôtes
- Il existe une gestion des utilisateurs avec l’annuaire
- Gestion des logs de connexion des utilisateurs
- Documentation et démonstration sur création/suppression d’un utilisateur
Sauvegarde et monitorer pour pérenniser la solution

### Idées technos possibles

- Partage de fichiers : Samba, Nuxeon Seafile, Alfresco
- Sauvegarde : client Nuxeo, Seafile, etc.
- Annuaire : Windows Active Directory, OpenLDAP
- Portail captif (et access management) : LemonLDAP ; PfSense, etc.

Enjoy :tada: