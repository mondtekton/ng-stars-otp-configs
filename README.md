# ng-stars-otp-configs

Dépôt de configuration centralisée pour tous les services du groupe ngstars.

## Rôle

Ce dépôt ne contient pas de code. Il contient uniquement les fichiers de configuration YAML de chaque service. C'est le service `ngstars-config` qui lit ce dépôt et distribue la bonne configuration à chaque service au démarrage.

Chaque fois qu'un service démarre, il contacte `ngstars-config` qui vient lire ici et lui retourne ce dont il a besoin. Aucun service ne lit ce dépôt directement.

## Structure

```
ng-stars-otp-configs/
├── otp.yaml
├── otp-dev.yaml
├── email.yaml
├── email-dev.yaml
├── gateway.yaml
├── gateway-dev.yaml
├── file.yaml
└── file-dev.yaml
```

Chaque service a deux fichiers :

- Le fichier de base (ex: `otp.yaml`) contient la configuration commune à tous les environnements.
- Le fichier `-dev` (ex: `otp-dev.yaml`) est chargé en plus quand le profil `dev` est actif. Il peut ajouter ou écraser des valeurs du fichier de base.

En production, seul le fichier de base est chargé.

## Correspondance service / fichiers

| Service | Fichier de base | Fichier dev |
|---|---|---|
| `ngstars-otp` | `otp.yaml` | `otp-dev.yaml` |
| `ngstars-email` | `email.yaml` | `email-dev.yaml` |
| `ngstars-gateway` | `gateway.yaml` | `gateway-dev.yaml` |
| `ngstars-file` | `file.yaml` | `file-dev.yaml` |

> `ngstars-registry` et `ngstars-config` gèrent leur propre configuration localement et n'ont pas de fichiers ici.

## Comment ça marche avec ngstars-config

`ngstars-config` est configuré pour pointer vers ce dépôt :

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/votre-org/ng-stars-otp-configs
          default-label: main
```

Quand un service démarre avec `spring.application.name: otp` et le profil `dev`, Spring Cloud Config va chercher dans ce dépôt les fichiers `otp.yaml` et `otp-dev.yaml`, les fusionne, et retourne le résultat au service.

## Modifier une configuration

Il suffit de modifier le fichier concerné et de pousser sur `main`. Les services en cours d'exécution ne reçoivent pas automatiquement les changements — il faut les redémarrer, ou utiliser Spring Cloud Bus si c'est mis en place.

> Ne jamais mettre de mots de passe ou de secrets en clair dans ce dépôt si celui-ci est public. Utiliser des variables d'environnement ou un gestionnaire de secrets à la place.
