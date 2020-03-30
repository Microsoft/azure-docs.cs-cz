---
title: Aktualizace a restartování uzlů Linuxu s kured ve službě Azure Kubernetes Service (AKS)
description: Naučte se aktualizovat linuxové uzly a automaticky je restartovat pomocí služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 74b12c1bc6e2a88582cc357c8091b5590e6bf3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191278"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Použití aktualizací zabezpečení a jádra u uzlů Linuxu ve službě Azure Kubernetes Service (AKS)

Z důvodu ochrany clusterů se aktualizace zabezpečení automaticky použijí na uzly Linuxu v AKS. Tyto aktualizace zahrnují opravy zabezpečení operačního systému nebo aktualizace jádra. Některé z těchto aktualizací vyžadují restartování uzlu k dokončení procesu. AKS automaticky nerestartuje tyto uzly Linuxu k dokončení procesu aktualizace.

Proces aktualizace uzlů Windows Server (aktuálně ve verzi Preview v AKS) je trochu jiný. Uzly systému Windows Server nedostávají denní aktualizace. Místo toho provedete upgrade AKS, který nasazuje nové uzly s nejnovější základní bitovou kopii a záplatami systému Window Server. Clustery AKS, které používají uzly Systému Windows Server, naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

Tento článek ukazuje, jak používat open-source [kured (KUbernetes REboot Daemon)][kured] sledovat linuxové uzly, které vyžadují restartování, pak automaticky zpracovat přeplánování spuštěných podů a proces uzamykání uzlů.

> [!NOTE]
> `Kured`je open-source projekt společnosti Weaveworks. Podpora tohoto projektu v AKS je poskytována na základě maximálního úsilí. Další podporu naleznete v kanálu #weave komunity.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Principy prostředí pro aktualizaci uzlů AKS

V clusteru AKS vaše uzly Kubernetes běží jako virtuální počítače Azure (VM). Tyto virtuální počítače založené na Linuxu používají bitovou kopii Ubuntu, přičemž operační systém je nakonfigurován tak, aby každou noc automaticky kontroloval aktualizace. Pokud jsou k dispozici aktualizace zabezpečení nebo jádra, jsou automaticky staženy a nainstalovány.

![AKS uzel aktualizovat a restartovat proces s kured](media/node-updates-kured/node-reboot-process.png)

Některé aktualizace zabezpečení, například aktualizace jádra, vyžadují k dokončení procesu restartování uzlu. Uzel Linux, který vyžaduje restartování, vytvoří soubor s názvem */var/run/reboot-required*. Tento proces restartování se neděje automaticky.

Můžete použít vlastní pracovní postupy a procesy ke zpracování `kured` restartování uzlu nebo použít k orchestraci procesu. S `kured`, [DaemonSet][DaemonSet] je nasazen, který spouští pod na každém uzlu Linux v clusteru. Tyto pody v DaemonSet hodinky pro existenci */var/run/reboot-required* soubor a potom zahájit proces restartování uzlů.

### <a name="node-upgrades"></a>Upgrady uzlů

V AKS existuje další proces, který umožňuje *inovovat* cluster. Upgrade je obvykle přesunout na novější verzi Kubernetes, nikoli pouze použít aktualizace zabezpečení uzlu. Upgrade AKS provádí následující akce:

* Nový uzel je nasazen s nejnovějšími aktualizacemi zabezpečení a použitou verzí Kubernetes.
* Starý uzel je uzavřen a vyprázdněn.
* Pody jsou naplánovány na nový uzel.
* Starý uzel je odstraněn.

Během upgradu nemůžete zůstat ve stejné verzi Kubernetes. Je nutné zadat novější verzi Kubernetes. Chcete-li upgradovat na nejnovější verzi kubernetes, můžete [upgradovat cluster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Nasazení kured v clusteru AKS

Chcete-li `kured` nasadit sadu DaemonSet, nainstalujte následující oficiální graf Kured Helm. Tím se vytvoří role role a clusteru, vazby a účet služby, `kured`pak nasadí DaemonSet pomocí .

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Můžete také nakonfigurovat další `kured`parametry pro , jako je například integrace s Prometheus nebo Slack. Další informace o dalších konfiguračních parametrech naleznete v [grafu kured Helm][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizace uzlů clusteru

Ve výchozím nastavení linuxové uzly v AKS kontrolují aktualizace každý večer. Pokud nechcete čekat, můžete ručně provést aktualizaci a `kured` zkontrolovat, zda běží správně. Nejprve postupujte podle pokynů s [ssh na jeden z uzlů AKS][aks-ssh]. Jakmile budete mít připojení SSH k uzlu Linux, zkontrolujte aktualizace a aplikujte je takto:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Pokud byly použity aktualizace, které vyžadují restartování uzlu, soubor je zapsán do */var/run/reboot-required*. `Kured`zkontroluje uzly, které vyžadují restartování každých 60 minut ve výchozím nastavení.

## <a name="monitor-and-review-reboot-process"></a>Sledování a kontrola procesu restartování

Pokud jedna z replik v DaemonSet zjistila, že je vyžadováno restartování uzlu, je na uzlu prostřednictvím rozhraní API Kubernetes umístěn zámek. Tento zámek zabraňuje další pody jsou naplánovány na uzlu. Zámek také označuje, že pouze jeden uzel by měl být restartován současně. Při uzavření uzlu jsou spuštěné pody vyprázdněny z uzlu a uzel je restartován.

Můžete sledovat stav uzlů pomocí [příkazu kubectl get uzly.][kubectl-get-nodes] Následující ukázkový výstup ukazuje uzel se stavem *SchedulingDisabled,* protože uzel se připravuje na proces restartování:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Po dokončení procesu aktualizace můžete zobrazit stav uzlů pomocí příkazu [kubectl get nodes][kubectl-get-nodes] s parametrem. `--output wide` Tento další výstup umožňuje zobrazit rozdíl v *KERNEL-VERSION* podkladových uzlů, jak je znázorněno v následujícím příkladu výstupu. *Aks-nodepool1-28993262-0* byl aktualizován v předchozím kroku a zobrazuje verzi jádra *4.15.0-1039-azure*. Uzel *aks-nodepool1-28993262-1,* který nebyl aktualizován, zobrazuje verzi jádra *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Další kroky

Tento článek podrobně `kured` popisuje, jak použít k automatickému restartování uzlů Linuxu jako součástprocesu aktualizace zabezpečení. Chcete-li upgradovat na nejnovější verzi kubernetes, můžete [upgradovat cluster AKS][aks-upgrade].

Clustery AKS, které používají uzly Systému Windows Server, naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
