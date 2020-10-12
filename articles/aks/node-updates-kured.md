---
title: Obsluha restartování uzlu Linux pomocí kured
titleSuffix: Azure Kubernetes Service
description: Naučte se aktualizovat uzly Linux a automaticky je restartovat pomocí kured ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88164236"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)

V zájmu ochrany clusterů se aktualizace zabezpečení automaticky aplikují na uzly Linux v AKS. Tyto aktualizace zahrnují opravy zabezpečení operačního systému nebo aktualizace jádra. Některé z těchto aktualizací vyžadují k dokončení procesu restart uzlu. AKS neprovede automatické restartování těchto uzlů Linux k dokončení procesu aktualizace.

Proces udržování uzlů Windows serveru v aktuálním stavu je malý rozdíl. Uzly Windows serveru neobdrží každodenní aktualizace. Místo toho provedete upgrade AKS, který nasadí nové uzly s nejnovější imagí a opravami základního serveru systému Windows. Clustery AKS, které používají uzly Windows serveru, najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

V tomto článku se dozvíte, jak používat open source [kured (KUbernetes restart Daemon)][kured] ke sledování uzlů pro Linux, které vyžadují restart, a pak automaticky zpracuje přeplánování běžícího procesu lusků a restartování uzlu.

> [!NOTE]
> `Kured` je open source projekt od Weaveworks. Podpora pro tento projekt v AKS je poskytována na základě optimálního úsilí. Další podporu najdete v kanálu rezervy #weave-Community.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Principy prostředí aktualizace uzlů AKS

V clusteru AKS se uzly Kubernetes spouštějí jako virtuální počítače Azure. Tyto virtuální počítače se systémem Linux používají image Ubuntu s operačním systémem nakonfigurovaným k automatickému vyhledávání aktualizací v každé noci. Pokud jsou k dispozici aktualizace zabezpečení nebo jádra, automaticky se stáhnou a nainstalují.

![Proces aktualizace a restartování uzlu AKS pomocí kured](media/node-updates-kured/node-reboot-process.png)

Některé aktualizace zabezpečení, jako jsou například aktualizace jádra, vyžadují k dokončení procesu restart uzlu. Uzel Linux, který vyžaduje restart, vytvoří soubor s názvem */var/run/reboot-Required*. Tento proces restartování neproběhne automaticky.

Můžete použít vlastní pracovní postupy a procesy ke zpracování restartování uzlu nebo `kured` k orchestraci tohoto procesu. V systému `kured` je nasazený [DaemonSet][DaemonSet] , který spouští pod na každém uzlu Linux v clusteru. Tyto lusky v DaemonSet Sledujte existenci souboru */var/run/reboot-Required* a potom zahájí proces restartování uzlů.

### <a name="node-upgrades"></a>Upgrady uzlů

V AKS je další proces, který umožňuje *upgradovat* cluster. Upgrade se obvykle přesouvá na novější verzi Kubernetes, ne jenom k aktualizaci zabezpečení uzlů. Upgrade AKS provádí následující akce:

* Nový uzel je nasazen s nejnovějšími aktualizacemi zabezpečení a použitou verzí Kubernetes.
* Starý uzel je uzavřené a vyprázdněn.
* Lusky jsou plánovány na novém uzlu.
* Starý uzel je odstraněný.

Během události upgradu nemůžete zůstat ve stejné verzi Kubernetes. Je nutné zadat novější verzi Kubernetes. Pokud chcete upgradovat na nejnovější verzi Kubernetes, můžete [upgradovat cluster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Nasazení kured v clusteru AKS

Pokud chcete nasadit `kured` DaemonSet, nainstalujte následující oficiální graf helmu Kured. Tím se vytvoří role a role clusteru, vazby a účet služby a pak se nasadí DaemonSet pomocí `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Můžete také nakonfigurovat další parametry pro `kured` , jako je například integrace s Prometheus nebo časovou rezervou. Další informace o dalších parametrech konfigurace najdete v [grafu Kured Helm][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizace uzlů clusteru

Ve výchozím nastavení se uzly Linux v AKS kontrolují aktualizace každé večer. Pokud nechcete čekat, můžete ručně provést aktualizaci, abyste zkontrolovali, jestli `kured` funguje správně. Nejdřív použijte postup [SSH na jeden z vašich AKS uzlů][aks-ssh]. Jakmile budete mít připojení SSH k uzlu Linux, vyhledejte aktualizace a použijte je následujícím způsobem:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Pokud byly aplikovány aktualizace, které vyžadují restartování uzlu, soubor je zapsán do */var/run/reboot-Required*. `Kured` kontroluje uzly, které ve výchozím nastavení vyžadují restartování každých 60 minut.

## <a name="monitor-and-review-reboot-process"></a>Monitorování a Kontrola procesu restartování

Když jedna z replik v DaemonSet zjistila, že se vyžaduje restart uzlu, na uzel se přes rozhraní Kubernetes API umístí zámek. Tento zámek zabraňuje naplánování dalších lusků na uzlu. Zámek také indikuje, že by mělo být restartován pouze jeden uzel v jednom okamžiku. Když je uzel uzavřené vypnutý, spuštěné lusky se z uzlu vyprázdní a uzel se restartuje.

Stav uzlů můžete monitorovat pomocí příkazu [kubectl Get Nodes][kubectl-get-nodes] . Následující příklad výstupu ukazuje uzel se stavem *SchedulingDisabled* , protože uzel připravuje proces restartování:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Po dokončení procesu aktualizace můžete zobrazit stav uzlů pomocí příkazu [kubectl Get Nodes][kubectl-get-nodes] s `--output wide` parametrem. Tento dodatečný výstup vám umožní zobrazit rozdíl v *jádru verze* podkladových uzlů, jak je znázorněno v následujícím příkladu výstupu. *AKS-nodepool1-28993262-0* se aktualizoval v předchozím kroku a zobrazuje jádro verze *4.15.0-1039-Azure*. Uzel *AKS-nodepool1-28993262-1* , který se neaktualizoval, zobrazuje verzi jádra *4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Další kroky

Tento článek podrobně popisuje, jak použít `kured` k automatickému restartování uzlů Linux v rámci procesu aktualizace zabezpečení. Pokud chcete upgradovat na nejnovější verzi Kubernetes, můžete [upgradovat cluster AKS][aks-upgrade].

Clustery AKS, které používají uzly Windows serveru, najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
