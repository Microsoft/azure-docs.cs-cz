---
title: Aktualizovat a restartovat uzly s Linuxem pomocí kured ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak aktualizovat uzly s Linuxem a automaticky je restartovat s kured ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 1702d9558e27452006a2f015fd3312ac19362871
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849871"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Použít zabezpečení a aktualizace jádra na uzly s Linuxem ve službě Azure Kubernetes Service (AKS)

K ochraně vašich clusterů, jsou aktualizace zabezpečení automaticky použít pro uzly s Linuxem ve službě AKS. Tyto aktualizace zahrnují oprav chyb zabezpečení operačního systému nebo aktualizace jádra. Některé z těchto aktualizací vyžadují restartování uzlu dokončete proces. AKS nebude automatickému restartování tyto uzly s Linuxem k dokončení procesu aktualizace.

Proces zajištění aktuálnosti uzly Windows serveru (aktuálně ve verzi preview ve službě AKS) je trochu jiné. Windows Server uzly nepřijímají denní aktualizace. Místo toho upgradujete AKS, který se nasazuje nových uzlů s opravy a nejnovější základní image Windows serveru. AKS clustery, které používají uzly Windows serveru, naleznete v tématu [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].

V tomto článku se dozvíte, jak používat open source [kured (KUbernetes restartování démona)] [ kured] sledovat pro uzly s Linuxem, které vyžadují restartování, pak automaticky zpracovává přeplánování spuštěných podů a uzlu Restartujte proces.

> [!NOTE]
> `Kured` je open source projekt podle Weaveworks. Na základě best effort poskytuje podporu pro tento projekt ve službě AKS. Další podporu najdete v # úřady weave slackový kanál.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.59 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Popis prostředí aktualizace uzlů AKS

V clusteru AKS uzly Kubernetes spouštět jako virtuální počítače Azure (VM). Tyto virtuální počítače s linuxem pomocí Ubuntu image s operačním systémem nakonfigurovaný tak, aby automaticky vyhledávat aktualizace každou noc. Pokud jsou k dispozici zabezpečení nebo aktualizace jádra, že se automaticky stahují a instalují.

![Uzlů AKS aktualizaci nebo restartování procesu s kured](media/node-updates-kured/node-reboot-process.png)

Některé aktualizace zabezpečení, jako je například jádra aktualizace vyžadují restartování uzlu pro dokončení procesu. Uzel systému Linux, který vyžaduje restartování počítače vytvoří soubor s názvem */var/run/reboot-required*. Tento proces restartováním počítače nedojde automaticky.

Můžete použít vlastní pracovní postupy a procesy a zpracovat restartování uzlu, nebo použít `kured` k orchestraci procesu. S `kured`, [DaemonSet] [ DaemonSet] nasazení, který spustí pod v každém uzlu clusteru systému Linux. Podívejte se na tyto pody v DaemonSet existenci */var/run/reboot-required* souboru a potom zahájí proces restartování uzlů.

### <a name="node-upgrades"></a>Upgrady uzlů

Je další proces ve službě AKS, která vám umožní *upgradovat* clusteru. Chcete-li přesunout na novější verzi Kubernetes, nejen včasnou aktualizaci zabezpečení uzlu je obvykle upgrade. AKS upgrade provede následující akce:

* Nový uzel se nasazuje s nejnovější aktualizací zabezpečení a verzi Kubernetes.
* Původní uzel je uzavřené a Vyprázdněné.
* Podů jsou naplánovány na novém uzlu.
* Původní uzel je odstraněn.

Během upgradu události nelze nadále využívat stejnou verzi Kubernetes. Musíte zadat novější verzi Kubernetes. Chcete-li upgradovat na nejnovější verzi Kubernetes, můžete [upgrade clusteru AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Nasazení kured v clusteru AKS

K nasazení `kured` DaemonSet, použijte následující ukázku YAML manifest z jejich stránce projektu na Githubu. Tento manifest vytvoří roli a roli clusteru, vazby a účet služby a pak nasadí DaemonSet pomocí `kured` verzi 1.1.0, která podporuje clustery AKS 1.9 nebo novější.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml

You can also configure additional parameters for `kured`, such as integration with Prometheus or Slack. For more information about additional configuration parameters, see the [kured installation docs][kured-install].

## Update cluster nodes

By default, Linux nodes in AKS check for updates every evening. If you don't want to wait, you can manually perform an update to check that `kured` runs correctly. First, follow the steps to [SSH to one of your AKS nodes][aks-ssh]. Once you have an SSH connection to the Linux node, check for updates and apply them as follows:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Pokud bylo nasazení aktualizací, které vyžadují restartování uzlu, je soubor zapsán do */var/run/reboot-required*. `Kured` Vyhledá uzly, které vyžadují restartování každých 60 minut ve výchozím nastavení.

## <a name="monitor-and-review-reboot-process"></a>Sledování a restartování proces kontroly

Pokud jeden z replik ve DaemonSet program zjistí, že je vyžadováno restartování uzlu, je uzamknout uzlu prostřednictvím rozhraní Kubernetes API. Tento zámek je chrání dalších podů naplánované na uzlu. Zámek také určuje, že by měl být restartuje pouze jeden uzel. Uzel kapacity jsou spuštěné podů Vyprázdněné z uzlu a po restartování uzlu.

Můžete monitorovat stav uzlů pomocí [kubectl get uzly] [ kubectl-get-nodes] příkazu. Následující příklad výstupu ukazuje uzlu se stavem *SchedulingDisabled* jako uzel připraví pro restartování procesu:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Po dokončení procesu aktualizace můžete zobrazit stav uzlů pomocí [kubectl get uzly] [ kubectl-get-nodes] příkazů `--output wide` parametru. Další výstup vám umožňují vidět rozdíl v *verze jádra* základní uzlů, jak je znázorněno v následujícím příkladu výstupu. *Aks nodepool1 28993262 0* byla aktualizována v předchozím kroku a verze jádra ukazuje *4.15.0-1039-azure*. Uzel *aks nodepool1 28993262 1* , který se zobrazí aktualizovaná verze jádra *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Další postup

Tento článek podrobně popsané použití `kured` restartování uzlů s Linuxem automaticky jako součást procesu aktualizace zabezpečení. Chcete-li upgradovat na nejnovější verzi Kubernetes, můžete [upgrade clusteru AKS][aks-upgrade].

AKS clustery, které používají uzly Windows serveru, naleznete v tématu [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
