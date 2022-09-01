
![Panda](/panda.jpg)

# Helm3 Plugin `helm-release`
[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/helm-release)](https://artifacthub.io/packages/helm-plugin/helm-release/release)
[![GitHub license](https://img.shields.io/github/license/JovianX/helm-release-plugin)](https://github.com/JovianX/helm-release-plugin)
![GitHub contributors](https://img.shields.io/github/contributors/JovianX/helm-release-plugin)
[![GitHub stars](https://img.shields.io/github/stars/JovianX/helm-release-plugin)](https://github.com/JovianX/helm-release-plugin/stargazers)  >>  **Please star ⭐ the repo if you find it useful.**


`helm-release` is a Helm 3 plugin that allows running operations on Helm releases (deployed Helm charts).

Features:

 * Pull (re-create) Helm charts from a deployed helm release.
 * Update values of a deployed release, without providing the chart used for release deployment.

Common use-Cases:
 * Redeploy a release on another cluster or namespace with the same helm chart.
 * Update values of a release, when you are not sure what exact chart version was used, or you don't have access to the original helm chart (Contrary to the `helm upgrade` command which requires the chart).

## Getting started
### Installation
To install the plugin:
```shell
helm plugin install https://github.com/JovianX/helm-release-plugin
```

>
> Dependencies: `helm-release` plugin depends on:
>>      jq - a lightweight and flexible command-line JSON processor.
>>             Install: https://stedolan.github.io/jq/download/
>

Update to the latest version:
```shell
$ helm plugin update release
```
Verify it's been installed:
```shell
$ helm plugin list
NAME    VERSION DESCRIPTION
...
release 0.3.2   Update values of a release, pull charts from releases
...
```


### Usage

```
$ helm release
usage: helm release [ pull | upgrade ]
```
Available Commands:
* __pull__ - Pulls (re-create) the Helm chart from a deployed Helm release.
* __upgrade__ - Updates Helm release vlaues without the Helm chart.
* __ttl__ - Sets release time to live(TTL) to schedule release automatic delete.



### `helm release pull`

Pulls (re-create) a Helm chart from a deployed Helm release.

```
$ helm release pull
usage: helm release pull <RELEASE NAME> [-d | --destination <TARGET CHART DIRECTORY>] [-o | --output [yaml | json | text]]
```

Example:
```
$ helm --namespace nginx release pull nginx --destination /home/me/helm-charts
Chart saved to nginx-ingress-0.13.2

$ ls /home/me/helm-charts/nginx-ingress-0.13.2/
Chart.yaml  crds  README.md  templates  values-icp.yaml  values-nsm.yaml  values-plus.yaml  values.yaml
```
>
>Note: Setting namespace`[-n | --namespace ]` or context `[--context]` name as needed) 
>
### `helm release upgrade`

Update the Helm release values, without specifying the helm chart. The `helm release upgrade` command accepts the same parameters as `helm upgrade` without specifying the helm chart. `--destination` is an optional parameter to set the directory where the chart is saved, defaults to `/tmp`. After the release is updated the chart is deleted. We recommend setting the `--reuse-values` flag to keep exsiting values and provide only the values you would like to change.
```
$ helm release upgrade
Update release values without specifying the Helm Chart. Usage: helm release upgrade [RELEASE NAME] [-d | --destination <TARGET CHART DIRECTORY>] [helm upgrade arguments]
```

Example:
```
helm release upgrade rabbitmq --namespace=rabbitmq --set=key1=value1 --reuse-values
...
... standard helm upgrade output ...
Update Complete. ⎈Happy Helming!⎈
```


### `helm release ttl`
Sets release time-to-live(TTL) to schedule atuomatic release uninstall. `release ttl` uses Kubernetes CronJob to schedule automatic uninstallation of releases. Helm release TTL supports actions: **set**, **unset** and **get** TTL for a helm release.

#### GET TTL
To get the current TTL of a release pass the `<RELEASE NAME>`. 
```
helm release ttl <RELEASE NAME>
```

Supproted outputs: `text`(defaul), `yaml` and `json`. 
For example, to see when `redis` release scheduled for deletion, run:
```
helm release ttl redis
Scheduled release removal date: Tue Aug 30 20:12:00 EEST 2022
```
Same request with `json` output:
```
helm release ttl redis --output=json
{"scheduled_date": "2022-08-30 17:12"}
```
Same request with `yaml` output:
```
helm release ttl redis --output=yaml
scheduled_date: 2022-08-30 17:12
```

| Note: Dates returned in cluster timezone (default UTC). |
| --- |


#### SET TTL

Sets the TTL of a release, afterwhich the release is deleted. Provide `<RELEASE-NAME>` and  `--set` to set the release TTL time using `date` fromat.
```
helm release ttl <RELEASE NAME> --set <DATE>
```

 For example to schedule deletion of a release in `five minutes`, run:
```
helm release ttl redis --set='5 minutes'
cronjob.batch/redis-ttl created
```
> Refer to complete `<DATA>` [documentation](https://www.gnu.org/software/ coreutils/manual/html_node/Relative-items-in-date-strings.html) for
> detailed description of possible time delta options.

If TTL is configued (CronJob exists) and the `--set` command is executed again the TTL(CronJob) will be rescheduled:
```
helm release ttl redis --set='5 minutes'
cronjob.batch/redis-ttl configured
```

#### UNSET TTL
To remove release TTL pass `<RELEASE NAME>` and `--unset` flag. 
```
helm release ttl <RELEASE NAME> --unset
```


For example, to remove `redis` release TTL run:
```
helm release ttl redis --unset
cronjob.batch "redis-ttl" deleted
```



## Contributing
We love your input! We want to make contributing to this project as easy and transparent as possible, whether it's:
- Reporting a bug
- Submitting a fix
- Proposing new features
- All pull requests are welcome.

Please see the [CONTRIBUTING](CONTRIBUTING.md) guide.


## Contributors
<a href = "https://github.com/JovianX/helm-release-plugin/graphs/contributors">
  <img src = "https://contrib.rocks/image?repo=JovianX/helm-release-plugin"/>
</a>

## License
 Copyright 2022 JovianX Ltd.

 Licensed under the Apache License, Version 2.0 (the "[LICENSE](https://github.com/JovianX/helm-release-plugin/blob/main/LICENSE)")

<a href="https://jovianx.com">
    <img src=https://jovianx.com/wp-content/uploads/2021/05/Logo2-2.png  height="50">
</a>

