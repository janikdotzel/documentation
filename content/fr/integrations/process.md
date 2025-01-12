---
assets:
  configuration:
    spec: assets/configuration/spec.yaml
  dashboards: {}
  logs: {}
  metrics_metadata: metadata.csv
  monitors: {}
  service_checks: assets/service_checks.json
categories:
- os & system
creates_events: false
ddtype: check
dependencies:
- https://github.com/DataDog/integrations-core/blob/master/process/README.md
display_name: Process
draft: false
git_integration_title: process
guid: 1675eced-b435-464a-8f84-f65e438f838e
integration_id: system
integration_title: Process
integration_version: 2.3.0
is_public: true
kind: integration
maintainer: help@datadoghq.com
manifest_version: 1.0.0
metric_prefix: system.
metric_to_check: system.processes.cpu.pct
name: process
public_title: Intégration Datadog/Process
short_description: Recueillez des métriques et surveillez le statut des processus
  en cours d'exécution.
support: core
supported_os:
- linux
- mac_os
- windows
---



## Présentation

Le check Process possède plusieurs applications.

- Il vous permet de recueillir des métriques d'utilisation des ressources pour des processus en cours d'exécution spécifiques sur n'importe quel host (processeur, mémoire, E/S, nombre de threads, etc.).
- Il vous aide à tirer parti des [monitors de processus][1] : vous pouvez configurer des seuils pour le nombre d'instances d'un processus spécifique à exécuter et recevoir des alertes lorsque les seuils ne sont pas atteints (voir la section **Checks de service** ci-dessous).

## Configuration

### Installation

Le check Process est inclus avec le package de l'[Agent Datadog][2] : vous n'avez donc rien d'autre à installer sur votre serveur.

### Configuration

Contrairement à de nombreux checks, le check Process ne surveille aucun processus utile par défaut. Vous devez configurer les processus que vous souhaitez surveiller et la méthode à employer.


1. Bien qu'il n'y ait pas de configuration standard par défaut, voici un exemple de configuration `process.d/conf.yaml` qui permet de surveiller les processus SSH/SSHD. Consultez le [fichier d'exemple process.d/conf.yaml][3] pour découvrir toutes les options de configuration disponibles :

```yaml
  init_config:

  instances:

  ## @param name - chaîne, obligatoire
  ## Permet d'identifier de façon unique vos métriques en leur appliquant un tag correspondant à ce nom dans Datadog.
  #
        - name: ssh

  ## @param search_string - liste de chaînes, facultatif
  ## Si l'un des éléments de la liste est recherché, le nombre
  ## total de processus correspondant parfaitement à la chaîne est renvoyé par défaut. Pour modifier ce comportement, utilisez le
  ## paramètre `exact_match: false`.
  ##
  ## Remarque : vous devez définir un seul des paramètres suivants pour chaque instance : search_string, pid ou pid_file.
  #
          search_string:
            - ssh
            - sshd
```

    Pour récupérer certaines métriques de processus, le Collector Datadog doit être exécuté avec le même utilisateur que celui du processus surveillé ou disposer des droits d'accès adéquats. Si la première option n'est pas envisageable, et que vous ne souhaitez pas exécuter le Collector Datadog en tant que `root`, l'option `try_sudo` permet au check Process d'utiliser `sudo` pour recueillir cette métrique. Pour l'instant, seule la métrique `open_file_descriptors` sur les plateformes Unix accepte ce paramètre. Attention, cette option ne fonctionne que si les règles sudoers appropriées ont été configurées :

   ```text
   dd-agent ALL=NOPASSWD: /bin/ls /proc/*/fd/
   ```

2. [Redémarrez l'Agent][4].

### Validation

Lancez la [sous-commande status de l'Agent][5] et cherchez `process` dans la section Checks.

### Précisions sur les métriques

**Remarque** : certaines métriques ne sont pas disponibles sous Linux ou OSX :

- Les métriques E/S de processus ne sont **pas** disponibles sous Linux ou OSX. En effet, seul le propriétaire du processus peut lire les fichiers que l'Agent doit lire (`/proc//io`). Pour en savoir plus, [consultez la FAQ sur l'Agent][6].
- `system.cpu.iowait` n'est pas disponible sous Windows.

Toutes les métriques sont envoyées pour chaque `instance` configurée dans process.yaml et reçoivent le tag `nom_processus:<nom_instance>`.

La métrique `system.processes.cpu.pct` envoyée par ce check est uniquement fiable pour les processus actifs pendant plus de 30 secondes. Sa valeur ne doit pas être considérée comme exacte pour les processus actifs sur de plus courtes périodes.

Pour obtenir la liste des métriques, consultez la section [Métriques](#metriques).

## Données collectées

### Métriques
{{< get-metrics-from-git "process" >}}


### Événements

Le check Process n'inclut aucun événement.

### Checks de service
{{< get-service-checks-from-git "process" >}}


## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][9].

## Pour aller plus loin

Pour mieux comprendre comment (ou pourquoi) surveiller l'utilisation des ressources de processus avec Datadog, lisez la [série d'articles de blog][10] de Datadog à ce sujet.

[1]: https://docs.datadoghq.com/fr/monitors/create/types/process_check/?tab=checkalert
[2]: https://app.datadoghq.com/account/settings#agent
[3]: https://github.com/DataDog/integrations-core/blob/master/process/datadog_checks/process/data/conf.yaml.example
[4]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/#start-stop-and-restart-the-agent
[5]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/#agent-status-and-information
[6]: https://docs.datadoghq.com/fr/agent/faq/why-don-t-i-see-the-system-processes-open-file-descriptors-metric/
[7]: https://github.com/DataDog/integrations-core/blob/master/process/metadata.csv
[8]: https://github.com/DataDog/integrations-core/blob/master/process/assets/service_checks.json
[9]: https://docs.datadoghq.com/fr/help/
[10]: https://www.datadoghq.com/blog/process-check-monitoring