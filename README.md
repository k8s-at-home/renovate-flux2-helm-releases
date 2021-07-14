# Renovate Helm Releases
<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-5-orange.svg?style=flat-square)](#contributors-)
<!-- ALL-CONTRIBUTORS-BADGE:END -->

A script / action that configures [Flux2](https://github.com/fluxcd/flux2) `HelmRelease`'s for automated updates using [Renovate](https://github.com/renovatebot/renovate).

A common way to get started on a GitHub repository is:

- Configure `Renovate` for your flux git repository. See [Renovate Docs: GitHub App Installation](https://docs.renovatebot.com/install-github-app/).
- Install this script as a [Github Action](https://docs.github.com/en/actions/quickstart) using the [Workflow example usage](#workflow-example-usage) below. This will add an annotation to any `HelmRelease` and Helm chart, required by `Renovate`.
- Add a `regexManager` in the `Renovate` config to allow `Renovate` to pick up newer versions of Helm charts. See [Renovate Docs: Configuration Options](https://docs.renovatebot.com/configuration-options/) for more details.
Combined with a `regexManager` in the `Renovate` config will allow `Renovate` to pick up newer versions of Helm charts.

# Example HelmRelease with annotation

This is an example of the annotation this script adds to the helm chart spec of a `HelmRelease`.

```yaml
---
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: ingress-nginx-1
  namespace: default
spec:
  interval: 5m
  chart:
    spec:
      # renovate: registryUrl=https://kubernetes.github.io/ingress-nginx
      chart: ingress-nginx
      version: 3.23.0
      sourceRef:
        kind: HelmRepository
        name: ingress-nginx-charts
        namespace: flux-system
      interval: 5m
```

## Workflow example usage

A common approach is to schedule a cron job workflow to invoke this script an update any annotations and send a pull request. See example clusters in https://github.com/k8s-at-home/awesome-home-kubernetes in particular `.github/workflows` for an end to end example.

```yaml
uses: k8s-at-home/renovate-helm-releases@v1
with:
  # Path to the folder containing your Flux2 Helm Repositories and Helm Releases
  cluster-path: './cluster'
  # Folders to exclude such as those use includes (Example: Ansible Vault)
  exclude-folders: './cluster/ansible','./cluster/docs'
  # Turn on debug logging
  debug: 'no'
  # Do not alter Helm Release files
  dry-run: 'no'
  # Tolerate yaml files with errors instead of aborting on errors
  tolerate-yaml-errors: 'no'
```

## Script usage example

This script will only work with Python 3

```bash
# install python dependencies
pip install -U -r requirements.txt
# run the script
./renovate.py --cluster-path="./cluster"
```

## Renovate configuration example

Something like the following is needed in order for `Renovate` to pick up `HelmReposistory`'s and `HelmRelease`'s

```jsonc
  "regexManagers": [
    // regexManager to read and process helm repositories
    {
      // tell renovatebot to parse only helm releases
      "fileMatch": ["cluster/.+helm-release\\.yaml$"],
      // tell renovatebot to match the following pattern in helm release files
      "matchStrings": [
        "registryUrl=(?<registryUrl>.*?)\n *chart: (?<depName>.*?)\n *version: (?<currentValue>.*)\n"
      ],
      // tell renovatebot to search helm repositories
      "datasourceTemplate": "helm"
    },
```

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://github.com/bjw-s"><img src="https://avatars.githubusercontent.com/u/6213398?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Bᴇʀɴᴅ Sᴄʜᴏʀɢᴇʀs</b></sub></a><br /><a href="https://github.com/k8s-at-home/renovate-helm-releases/commits?author=bjw-s" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/allenporter"><img src="https://avatars.githubusercontent.com/u/6026418?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Allen Porter</b></sub></a><br /><a href="https://github.com/k8s-at-home/renovate-helm-releases/commits?author=allenporter" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/adtennant"><img src="https://avatars.githubusercontent.com/u/2420216?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Alex Tennant</b></sub></a><br /><a href="https://github.com/k8s-at-home/renovate-helm-releases/commits?author=adtennant" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/angelnu"><img src="https://avatars.githubusercontent.com/u/4406403?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Angel Nunez Mencias</b></sub></a><br /><a href="https://github.com/k8s-at-home/renovate-helm-releases/commits?author=angelnu" title="Code">💻</a></td>
    <td align="center"><a href="https://github.com/jmmaloney4"><img src="https://avatars.githubusercontent.com/u/5781547?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jack Maloney</b></sub></a><br /><a href="https://github.com/k8s-at-home/renovate-helm-releases/commits?author=jmmaloney4" title="Code">💻</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
