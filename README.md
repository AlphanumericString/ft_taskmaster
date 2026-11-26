# ft_taskmaster

**Un clone minimaliste de [supervisor](https://supervisord.org/) écrit en Rust.**
Il lance, surveille et redémarre des processus définis dans un fichier de
configuration, et se pilote depuis un client en ligne de commande.

[![Rust](https://img.shields.io/badge/Rust-000?logo=rust&logoColor=white)](https://www.rust-lang.org/)
[![42 Paris](https://img.shields.io/badge/42-Paris-000)](https://42.fr/)
[![Licence : GPL v3](https://img.shields.io/badge/Licence-GPLv3-blue.svg)](LICENSE)

> Projet du cursus 42 Paris — en cours de développement.

## Aperçu

`ft_taskmaster` reprend les fonctions essentielles d'un gestionnaire de
processus UNIX :

- lecture d'un fichier de configuration décrivant les programmes à lancer ;
- démarrage, arrêt, redémarrage et supervision de plusieurs processus ;
- redémarrage automatique selon le code de sortie ;
- redirection de `stdout` et `stderr` vers des fichiers de log ;
- contrôle à chaud via `taskmasterctl` et un socket UNIX ;
- rechargement de la configuration sans redémarrer le démon.

## Architecture

```text
        ┌───────────────┐        socket UNIX        ┌───────────────┐
        │ taskmasterctl │ ◄───────────────────────► │   taskmaster  │
        └───────────────┘                           └───────┬───────┘
                                                            │ fork / exec
                                                  ┌─────────┼─────────┐
                                                  ▼         ▼         ▼
                                                prog 1    prog 2    prog 3
```

Le démon est responsable du cycle de vie des processus ; le client se contente
d'envoyer des commandes (`status`, `start`, `stop`, `restart`, `reload`,
`shutdown`) et d'afficher la réponse.

## Configuration

Le fichier de configuration permettra de régler, pour chaque programme :

| Option | Rôle |
| --- | --- |
| `cmd` | commande à exécuter |
| `numprocs` | nombre d'instances |
| `autostart` | démarrage automatique |
| `autorestart` | redémarrage automatique (`always`, `never`, `unexpected`) |
| `exitcodes` | codes de sortie considérés comme normaux |
| `starttime` / `startretries` | délai et nombre d'essais au démarrage |
| `stopsignal` / `stopwaitsecs` | signal et délai d'arrêt |
| `stdout` / `stderr` | fichiers de log |
| `workingdir`, `umask`, `user`, `environment` | contexte d'exécution |

> Le format exact du fichier sera documenté avec l'implémentation.

## Compilation

Une fois le code ajouté :

```sh
cargo build --release
```

## Statut

- [x] Dépôt, licence et documentation
- [ ] Parseur et validation de la configuration
- [ ] Démon et supervision des processus
- [ ] Client `taskmasterctl`
- [ ] Rechargement à chaud
- [ ] Tests d'intégration

## Licence

Distribué sous licence [GPL-3.0](LICENSE).
