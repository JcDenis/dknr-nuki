# README

[![Release](https://img.shields.io/docker/v/jcpd/dknr-nuki?sort=semver)](https://img.shields.io/docker/v/jcpd/dknr-nuki?sort=semver)
[![Pull](https://img.shields.io/docker/pulls/jcpd/dknr-nuki)](https://img.shields.io/docker/pulls/jcpd/dknr-nuki)
[![Issue](https://img.shields.io/github/issues/jcdenis/dknr-nuki)](https://img.shields.io/github/issues/jcdenis/dknr-nuki)
[![License](https://img.shields.io/github/license/jcdenis/dknr-nuki)](https://github.com/JcDenis/dknr-nuki/blob/master/LICENSE)


## A propos

**dknr-nuki** est un projet consacré au pilotage, à la récupération et à l'exploitation des données des appareils Nuki en local. Ce projet n'utilise pas le cloud Nuki (Nuki Web) mais attaque directement l'API HTTP des bridges Nuki et, en option, l'API MQTT native des Smart Lock récentes.

Toutes les informations sont regroupées sur votre serveur MQTT, dans la même arborescence que les autres projets DKNR.


## Fonctionnalités

- **Support de plusieurs bridges Nuki** (bridge matériel ou logiciel) et de tous leurs appareils appairés.
- **Smart Lock 1.0/2.0/3.0/4.0, Smart Door et Opener**.
- **Appairage simplifié** : sans jeton, le projet le demande au bridge, il suffit d'appuyer sur son bouton.
- **Jeton chiffré** (hashed token) pour ne pas faire circuler le jeton en clair sur le réseau.
- **Mises à jour instantanées** grâce au callback HTTP du bridge, enregistré automatiquement.
- **Import des Smart Lock en MQTT natif** (Smart Lock 3.0 Pro / 4.0) publiant sur `nuki/...`.
- **Pilotage** : verrouiller, déverrouiller, ouvrir, Lock'n'Go, verrouillage complet, Ring to Open, mode continu.
- **Support Home-Assistant Discovery** en lecture et pilotage des appareils (entité serrure, sélecteur d'action, batterie, capteur de porte, sonnette...).


## Matériel requis

Le matériel exact dépend de l'installation, mais l'utilisation du projet nécessite généralement :

* un ou plusieurs appareils Nuki : Smart Lock, Smart Door ou Opener.
* un bridge Nuki avec l'**API HTTP activée** dans l'application Nuki (Bridge > Gérer le bridge > API HTTP), **ou** une Smart Lock avec MQTT natif configuré sur le même serveur MQTT.
* une machine exécutant le logiciel docker et connectée au même réseau que les bridges.


## Installation

Des images Docker prêtes à l'emploi sont disponibles. 
Utilisez le fichier docker-compose.yaml à la racine du dépôt en fournissant les variables d'environnement suivantes :

* **DKNR_USERNAME** : login de l'interface node-red.
* **DKNR_PASSWORD** : mot de passe de l'interface node-red.
* **DKNR_SECRET**   : un mot secret pour encrypter les credits.

Le tableau de bord est disponible sur `http://<ip du serveur docker>:1880/` et l'éditeur Node-RED sur `http://<ip du serveur docker>:1880/admin`.


## Configuration

### Ajouter un bridge

Dans la page **Ajouter** :

* **Adresse IP** et **port** du bridge (8080 par défaut).
* **Jeton API** : laissez le champ vide puis appuyez sur le bouton du bridge dans les 30 secondes pour obtenir un jeton automatiquement, ou recopiez le jeton affiché dans l'application Nuki.
* **Jeton chiffré** : recommandé avec un bridge matériel. L'horloge du serveur docker doit être à l'heure. A décocher pour un bridge logiciel.
* **URL de callback** (facultatif) : adresse à laquelle le bridge peut joindre ce projet, par exemple `http://192.168.0.20:1880/nuki/callback`. Le callback est enregistré automatiquement sur le bridge (3 callbacks maximum par bridge).

Les appareils du bridge apparaissent ensuite dans la page **Piloter**. Leur état est relu toutes les 30 secondes, et immédiatement à chaque changement si le callback est configuré.

### Smart Lock en MQTT natif

Dans la page **Configurer**, cochez **Importer les Smart Lock connectés nativement en MQTT**. Les serrures configurées dans l'application Nuki pour publier sur le même serveur MQTT (`nuki/<id>/...`) sont alors reprises dans l'arborescence DKNR et pilotables depuis le projet.


## MQTT

Avec le topic `dknr` par défaut, l'identifiant d'un appareil est son Nuki ID en hexadécimal (tel qu'imprimé sur l'appareil) :

* `dknr/get/nuki/<id>/<entité>` : valeurs publiées (retenues) à chaque changement.
* `dknr/set/nuki/<id>/lock` : `LOCK`, `UNLOCK` ou `OPEN`.
* `dknr/set/nuki/<id>/action` : nom de l'action (ex : `Verrouiller`, `Lock'n'Go`) ou son numéro Nuki.
* `dknr/discovery/nuki/<id>/<entité>` : description des entités.
* `homeassistant/<type>/nuki/<id>_<entité>/config` : Home Assistant Discovery.

Entités Smart Lock : `lock`, `action`, `state`, `mode`, `battery`, `batteryCritical`, `batteryCharging`, `keypadBatteryCritical`, `doorsensorState`, `door`, `doorsensorBatteryCritical`, `lastAction`, `timestamp`, `firmware`.

Entités Opener : `lock`, `action`, `state`, `mode`, `batteryCritical`, `ring`, `ringTimestamp`, `lastAction`, `timestamp`, `firmware`.

Seules les entités réellement fournies par l'appareil sont publiées.


## A faire

* **Mode découverte** pour rechercher automatiquement les bridges sur le réseau local.
* Statistiques de batterie.


## Contribuer

Ce projet est **open-source**, vous pouvez participer en créant des [tickets](https://github.com/JcDenis/dknr-nuki/issues) ou en proposant de [requêtes](https://github.com/JcDenis/dknr-nuki/pulls) sur le dépôt github du projet.


## Voir aussi

- [DKNR Teleinfo](https://github.com/JcDenis/dknr-teleinfo)
- [DKNR APSystems](https://github.com/JcDenis/dknr-apsystems)
- [DKNR Daikin](https://github.com/JcDenis/dknr-daikin)
- [DKNR Zendure](https://github.com/JcDenis/dknr-zendure)
- [DKNR RFXCom](https://github.com/JcDenis/dknr-rfxcom)
- [Nuki Bridge HTTP API](https://developer.nuki.io/t/bridge-http-api/26)
- [Nuki MQTT API](https://developer.nuki.io/t/mqtt-api-specification-v1-3/17626)
