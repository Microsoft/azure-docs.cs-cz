---
title: Aktualizovat a restartovat uzly s kured ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak aktualizovat uzly a automaticky je restartovat s kured ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 11/06/2018
ms.author: iainfou
ms.openlocfilehash: 0bcc49df6540b73b8feb5bb1ec4312e680572797
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617871"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Použít zabezpečení a aktualizace jádra na uzly ve službě Azure Kubernetes Service (AKS)

K ochraně vašich clusterů, jsou aktualizace zabezpečení automaticky použít pro uzly ve službě AKS. Tyto aktualizace zahrnují oprav chyb zabezpečení operačního systému nebo aktualizace jádra. Některé z těchto aktualizací vyžadují restartování uzlu dokončete proces. AKS nebude automatickému restartování uzlů pro dokončení procesu aktualizace.

V tomto článku se dozvíte, jak používat open source [kured (KUbernetes restartování démona)] [ kured] sledovat pro uzly, které vyžadují restartování, pak automaticky zpracovat přeplánování spuštěných podů a restartování uzlu proces.

> [!NOTE]
> `Kured` je open source projekt podle Weaveworks. Na základě best effort poskytuje podporu pro tento projekt ve službě AKS. Další podpora lze nalézt v slackový kanál # úřady weave

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.49 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Popis prostředí aktualizace uzlů AKS

V clusteru AKS uzly Kubernetes spouštět jako virtuální počítače Azure (VM). Tyto virtuální počítače s linuxem pomocí Ubuntu image s operačním systémem nakonfigurovaný tak, aby automaticky vyhledávat aktualizace každou noc. Pokud jsou k dispozici zabezpečení nebo aktualizace jádra, že se automaticky stahují a instalují.

![Uzlů AKS aktualizaci nebo restartování procesu s kured](media/node-updates-kured/node-reboot-process.png)

Některé aktualizace zabezpečení, jako je například jádra aktualizace vyžadují restartování uzlu pro dokončení procesu. Uzel, který vyžaduje restart, vytvoří soubor s názvem */var/run/reboot-required*. Tento proces restartováním počítače nedojde automaticky.

Můžete použít vlastní pracovní postupy a procesy a zpracovat restartování uzlu, nebo použít `kured` k orchestraci procesu. S `kured`, [DaemonSet] [ DaemonSet] nasazení, který spustí pod na každém uzlu v clusteru. Podívejte se na tyto pody v DaemonSet existenci */var/run/reboot-required* souboru a potom zahájí proces restartování uzlů.

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
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Můžete také nakonfigurovat další parametry pro `kured`, jako je například integrace s Prometheus nebo Slack. Další informace o další konfigurační parametry, najdete v článku [kured instalace dokumentace][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizovat uzly clusteru

Ve výchozím nastavení uzlů AKS vyhledat aktualizace každý večer. Pokud nechcete čekat, můžete ručně provádět aktualizace zkontroluje, jestli `kured` běží správně. Nejprve, postupujte podle kroků pro [SSH k jednomu z uzlů AKS][aks-ssh]. Jakmile budete mít připojení SSH k uzlu, zkontrolujte aktualizace a použít je následujícím způsobem:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Pokud bylo nasazení aktualizací, které vyžadují restartování uzlu, je soubor zapsán do */var/run/reboot-required*. `Kured` Vyhledá uzly, které vyžadují restartování každých 60 minut ve výchozím nastavení.

## <a name="monitor-and-review-reboot-process"></a>Sledování a restartování proces kontroly

Pokud jeden z replik ve DaemonSet program zjistí, že je vyžadováno restartování uzlu, je uzamknout uzlu prostřednictvím rozhraní Kubernetes API. Tento zámek je chrání dalších podů naplánované na uzlu. Zámek také určuje, že by měl být restartuje pouze jeden uzel. Uzel kapacity jsou spuštěné podů Vyprázdněné z uzlu a po restartování uzlu.

Můžete monitorovat stav uzlů pomocí [kubectl get uzly] [ kubectl-get-nodes] příkazu. Následující příklad výstupu ukazuje uzlu se stavem *SchedulingDisabled* jako uzel připraví pro restartování procesu:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-79590246-2   Ready,SchedulingDisabled   agent     1h        v1.9.11
```

Po dokončení procesu aktualizace můžete zobrazit stav uzlů pomocí [kubectl get uzly] [ kubectl-get-nodes] příkazů `--output wide` parametru. Další výstup vám umožňují vidět rozdíl v *verze jádra* základní uzlů, jak je znázorněno v následujícím příkladu výstupu. *Aks nodepool1 79590246 2* byla aktualizována v předchozím kroku a verze jádra ukazuje *4.15.0-1025-azure*. Uzel *aks nodepool1 79590246 1* , který se zobrazí aktualizovaná verze jádra *4.15.0-1023-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.11   10.240.0.6    <none>        Ubuntu 16.04.5 LTS   4.15.0-1023-azure   docker://1.13.1
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.11   10.240.0.4    <none>        Ubuntu 16.04.5 LTS   4.15.0-1025-azure   docker://1.13.1
```

## <a name="next-steps"></a>Další postup

Tento článek podrobně popsané použití `kured` restartování uzlů automaticky jako součást procesu aktualizace zabezpečení. Chcete-li upgradovat na nejnovější verzi Kubernetes, můžete [upgrade clusteru AKS][aks-upgrade].

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
