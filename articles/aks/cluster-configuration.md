---
title: Konfigurace clusteru ve službě Azure Kubernetes Services (AKS)
description: Informace o tom, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775865"
---
# <a name="configure-an-aks-cluster"></a>Konfigurace clusteru AKS

V rámci vytváření clusteru AKS možná budete muset přizpůsobit konfiguraci clusteru tak, aby vyhovovala vašim potřebám. Tento článek představuje několik možností přizpůsobení clusteru AKS.

## <a name="os-configuration"></a>Konfigurace operačního systému

AKS teď podporuje Ubuntu 18,04 jako výchozí operační systém pro clustery (GA) ve všeobecné dostupnosti (GA) pro clustery ve verzích Kubernetes vyšší než 1,18 pro verze nižší než 1,18, AKS Ubuntu 16,04 je stále výchozí základní image. Od Kubernetes v 1.18 a vyšší je výchozí základ AKS Ubuntu 18,04.

> [!IMPORTANT]
> Fondy uzlů vytvořené ve výchozím nastavení Kubernetes v 1.18 nebo vyšší pro `AKS Ubuntu 18.04` Image Node. Fondy uzlů na podporované verzi Kubernetes, která je menší než 1,18, se přijímají `AKS Ubuntu 16.04` jako image uzlu, ale `AKS Ubuntu 18.04` po aktualizaci verze Kubernetes fondu uzlů na verzi v 1.18 nebo vyšší.
> 
> Před použitím clusterů na 1,18 nebo novějším se doporučuje testovat vaše úlohy na fondech uzlů AKS Ubuntu 18,04.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Použití AKS Ubuntu 18,04 (GA) v nových clusterech

Clustery vytvořené ve výchozím nastavení Kubernetes v 1.18 nebo vyšší pro `AKS Ubuntu 18.04` Image Node. Fondy uzlů v podporované verzi Kubernetes, která je menší než 1,18, se budou dál přijímat `AKS Ubuntu 16.04` jako image uzlu, ale `AKS Ubuntu 18.04` po aktualizaci verze Kubernetes fondu clusteru nebo uzlu na verzi v 1.18 nebo novějším se aktualizuje.

Před použitím clusterů na 1,18 nebo novějším se doporučuje testovat vaše úlohy na fondech uzlů AKS Ubuntu 18,04.

Pokud chcete vytvořit cluster pomocí `AKS Ubuntu 18.04` Image uzlu, stačí vytvořit cluster se systémem Kubernetes v 1.18 nebo vyšší, jak je znázorněno níže.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Použití AKS Ubuntu 18,04 (GA) na existujících clusterech

Clustery vytvořené ve výchozím nastavení Kubernetes v 1.18 nebo vyšší pro `AKS Ubuntu 18.04` Image Node. Fondy uzlů v podporované verzi Kubernetes, která je menší než 1,18, se budou dál přijímat `AKS Ubuntu 16.04` jako image uzlu, ale `AKS Ubuntu 18.04` po aktualizaci verze Kubernetes fondu clusteru nebo uzlu na verzi v 1.18 nebo novějším se aktualizuje.

Před použitím clusterů na 1,18 nebo novějším se doporučuje testovat vaše úlohy na fondech uzlů AKS Ubuntu 18,04.

Pokud jsou clustery nebo fondy uzlů připravené k `AKS Ubuntu 18.04` imagi uzlů, můžete je jednoduše upgradovat na verzi v 1.18 nebo vyšší.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Pokud chcete pouze upgradovat pouze jeden fond uzlů:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Test AKS Ubuntu 18,04 (GA) na existujících clusterech

Fondy uzlů vytvořené ve výchozím nastavení Kubernetes v 1.18 nebo vyšší pro `AKS Ubuntu 18.04` Image Node. Fondy uzlů na podporované verzi Kubernetes, která je menší než 1,18, se budou i nadále přijímat `AKS Ubuntu 16.04` jako image uzlu, ale `AKS Ubuntu 18.04` po aktualizaci verze Kubernetes fondu uzlů na hodnotu v 1.18 nebo novějším se aktualizují.

Před upgradem fondů vašich produkčních uzlů se důrazně doporučuje testovat úlohy ve fondech uzlů AKS Ubuntu 18,04.

Pokud chcete vytvořit fond uzlů pomocí `AKS Ubuntu 18.04` Image uzlu, stačí vytvořit fond uzlů se systémem Kubernetes v 1.18 nebo novějším. Vaše řídicí plocha clusteru musí být aspoň v 1.18 nebo vyšší, ale ostatní fondy uzlů můžou zůstat na starší verzi Kubernetes.
Dál je potřeba upgradovat rovinu ovládacího prvku a pak vytvořit nový fond uzlů s v 1.18, který obdrží novou verzi operačního systému pro Image uzlu.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Konfigurace modulu runtime kontejneru

Modul runtime kontejneru je software, který spouští kontejnery a spravuje image kontejneru na uzlu. Modul runtime pomáhá abstraktním funkcím sys-Call nebo Operating System (OS) spouštět kontejnery v systému Linux nebo Windows. Clustery AKS s využitím fondů uzlů Kubernetes verze 1,19 a větším využitím `containerd` jako modul runtime kontejneru. Clustery AKS využívající Kubernetes před a v 1.19 pro fondy uzlů používají jako svůj modul runtime kontejneru [Moby](https://mobyproject.org/) (nadřazený Docker).

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) je modul runtime základního kontejneru kompatibilního s rozhraním [OCI](https://opencontainers.org/) (Open container Initiative), který poskytuje minimální sadu požadovaných funkcí pro spouštění kontejnerů a správu imagí na uzlu. V březnu [2017 byl tento](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) Cloud Native COMPUTE Foundation (CNCF). Aktuální verze Moby, kterou používá AKS, již využívá a je postavená nad `containerd` , jak je uvedeno výše.

V `containerd` případě fondů uzlů a uzlů se `dockershim` kubelet bude komunikovat přímo s `containerd` modulem plug-in CRI (rozhraní Container Runtime) a odebrat nadbytečné segmenty směrování v toku ve srovnání s implementací Docker CRI. V takovém případě se zobrazí lepší latence při spuštění a menší využití prostředků (CPU a paměti).

Díky použití `containerd` pro uzly AKS zlepšuje latence při spuštění a snižuje spotřebu prostředků v modulu runtime kontejneru. Tato vylepšení jsou povolená touto novou architekturou, ve které kubelet mluví přímo s modulem `containerd` Plug-in CRI v době, kdy se v architektuře Moby/Docker kubelet `dockershim` před dosažením domluví k modulu a Docker `containerd` , takže bude mít další směrování v toku.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funguje na všech Kubernetes verze GA v AKS a v každé nadřazené Kubernetes verzi výše v 1.19 a podporuje všechny funkce Kubernetes a AKS.

> [!IMPORTANT]
> Clustery s fondy uzlů vytvořenými ve výchozím nastavení Kubernetes v 1.19 nebo vyšší `containerd` pro modul runtime kontejneru. Clustery s fondy uzlů v podporované verzi Kubernetes, která je nižší než 1,19 `Moby` , obdrží pro svůj modul runtime kontejneru, ale `ContainerD` po aktualizaci verze Kubernetes fondu uzlů na hodnotu v 1.19 nebo novějším se aktualizují. `Moby`Fondy uzlů a clustery můžete stále používat ve starších podporovaných verzích, dokud tyto nespadají do podpory.
> 
> Důrazně doporučujeme testovat úlohy ve fondech uzlů AKS s `containerD` před použitím clusterů na 1,19 nebo vyšší.

### <a name="containerd-limitationsdifferences"></a>`Containerd` omezení/rozdíly

* Pokud chcete použít `containerd` jako modul runtime kontejneru, musíte jako základní image operačního systému použít AKS Ubuntu 18,04.
* I když je sada nástrojů Docker stále přítomna v uzlech, Kubernetes používá `containerd` jako modul runtime kontejneru. Proto vzhledem k tomu, že Moby/Docker nespravuje kontejnery vytvořené Kubernetes na uzlech, nemůžete zobrazit ani s nimi pracovat pomocí příkazů Docker (jako `docker ps` ) nebo rozhraní Docker API.
* `containerd`V případě nástroje doporučujeme použít [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) jako náhradní příkaz CLI místo Docker CLI pro **řešení potíží s** podmnožinami, kontejnery a imagemi kontejnerů na uzlech Kubernetes (například `crictl ps` ). 
   * Neposkytuje kompletní funkce rozhraní příkazového řádku Docker. Je určený jenom pro odstraňování potíží.
   * `crictl` nabízí více kubernetesch zobrazení kontejnerů, s koncepty, jako jsou lusky atd.
* `Containerd` Nastaví protokolování pomocí standardizovaného `cri` formátu protokolování (to se liší od toho, co aktuálně pochází z ovladače rozhraní JSON Docker). Vaše řešení protokolování vyžaduje podporu `cri` formátu protokolování (například [Azure monitor pro kontejnery](../azure-monitor/containers/container-insights-enable-new-cluster.md)).
* Už nemůžete získat přístup k modulu Docker, `/var/run/docker.sock` ani použít Docker-in-Docker (DinD).
  * Pokud v současné době extrahujete protokoly aplikací nebo monitorovaná data z modulu Docker, použijte místo toho něco jako [Azure monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md) . AKS navíc nepodporuje spouštění jakýchkoli příkazů mimo pásmo na uzlech agentů, které by mohly způsobit nestabilitu.
  * I když používáte Moby/Docker, sestavování imagí a přímé využití modulu Docker prostřednictvím výše uvedených metod se důrazně nedoporučuje. Kubernetes není plně vědoma těch spotřebovaných prostředků a tyto přístupy obsahují řadu problémů, které jsou [zde](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) popsané, a [tady](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), například.
* Vytváření imagí – stávající pracovní postup sestavení Docker můžete dál používat jako normální, pokud nevytváříte image v rámci clusteru AKS. V takovém případě zvažte přechod na doporučený postup pro vytváření imagí pomocí [ACR úloh](../container-registry/container-registry-quickstart-task-cli.md)nebo bezpečnější možnost v clusteru, jako je [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Virtuální počítače 2. generace

Azure podporuje [virtuální počítače generace 2 (Gen2) (VM](../virtual-machines/generation-2.md)). Virtuální počítače 2. generace podporují klíčové funkce, které nejsou podporované na virtuálních počítačích generace 1 (Gen1). Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM).

Virtuální počítače generace 2 používají novou architekturu na bázi rozhraní UEFI namísto architektury založené na systému BIOS používané virtuálními počítači 1. generace.
Virtuální počítače Gen2 podporují jenom určité SKU a velikosti. Zkontrolujte [seznam podporovaných velikostí](../virtual-machines/generation-2.md#generation-2-vm-sizes), abyste viděli, jestli vaše SKU podporuje nebo vyžaduje Gen2.

Kromě toho ne všechny image virtuálních počítačů podporují Gen2, na virtuálních počítačích s AKS Gen2 použije nový [Obrázek AKS Ubuntu 18,04](#os-configuration). Tento obrázek podporuje všechny SKU a velikosti Gen2.

## <a name="ephemeral-os"></a>Dočasný operační systém

Ve výchozím nastavení Azure automaticky replikuje disk operačního systému pro virtuální počítač do služby Azure Storage, aby nedošlo ke ztrátě dat, aby bylo možné virtuální počítač přeumístit na jiného hostitele. Vzhledem k tomu, že kontejnery nejsou navržené tak, aby měly trvalý místní stav, toto chování nabízí omezené hodnoty a zároveň poskytuje některé nevýhody, včetně pomalejšího zřizování uzlů a vyšší latence čtení a zápisu.

Naproti tomu jsou dočasné disky s operačním systémem uložené jenom na hostitelském počítači, stejně jako na dočasném disku. To zajišťuje nižší latenci čtení a zápisu společně s rychlejším škálováním uzlů a upgrady clusteru.

Podobně jako na dočasném disku je dočasný disk s operačním systémem zahrnutý do ceny virtuálního počítače, takže nebudete mít k dispozici žádné další náklady na úložiště.

> [!IMPORTANT]
>Pokud uživatel výslovně nepožaduje pro daný operační systém spravované disky, AKS pro danou konfiguraci nodepool nastaví jako výchozí dočasný operační systém.

Při použití dočasného operačního systému se musí disk s operačním systémem vejít do mezipaměti virtuálních počítačů. Velikosti mezipaměti virtuálních počítačů jsou dostupné v dokumentaci k [Azure](../virtual-machines/dv3-dsv3-series.md) v závorkách vedle vstupně-výstupních propustností ("velikost mezipaměti v GIB").

Když použijete AKS výchozí velikost virtuálního počítače Standard_DS2_v2 s výchozí velikostí disku operačního systému 100 GB jako příklad, tato velikost virtuálního počítače podporuje dočasný operační systém, ale má jenom 86GB velikosti mezipaměti. Tato konfigurace bude ve výchozím nastavení pro spravované disky, pokud uživatel nezadá explicitně. Pokud uživatel výslovně požadoval dočasný operační systém, došlo k chybě ověření.

Pokud uživatel požádá o stejnou Standard_DS2_v2 s diskem s operačním systémem 60GB, bude tato konfigurace ve výchozím nastavení dočasný operační systém: požadovaná velikost 60GB je menší než maximální velikost mezipaměti 86GB.

Při použití Standard_D8s_v3 s diskem s operačním systémem 100 GB podporuje tato velikost virtuálního počítače dočasný operační systém a má 200 GB místo v mezipaměti. Pokud uživatel neurčí typ disku operačního systému, nodepool by ve výchozím nastavení dostal dočasný operační systém. 

Dočasný operační systém vyžaduje minimálně 2.15.0 verze Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Použít dočasný operační systém v nových clusterech

Nakonfigurujte cluster, aby při vytvoření clusteru používal dočasné disky s operačním systémem. Pomocí `--node-osdisk-type` příznaku nastavte dočasný operační systém jako typ disku operačního systému pro nový cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Pokud chcete vytvořit běžný cluster pomocí disků s operačním systémem připojeného k síti, můžete to udělat tak, že zadáte `--node-osdisk-type=Managed` . Můžete se také rozhodnout přidat další dočasné fondy uzlů operačního systému podle níže uvedených pokynů.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Použití dočasného operačního systému na existujících clusterech
Nakonfigurujte nový fond uzlů, aby používal dočasné disky s operačním systémem. Pomocí `--node-osdisk-type` příznaku pro tento fond uzlů nastavte jako typ disku s operačním systémem jako typ disku s operačním systémem.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> S dočasným operačním systémem můžete nasadit image virtuálních počítačů a instancí až do velikosti mezipaměti virtuálních počítačů. V případě AKS používá výchozí konfigurace disku s operačním systémem Node 128 GB, což znamená, že potřebujete velikost virtuálního počítače, která má mezipaměť větší než 128 GB. Výchozí Standard_DS2_v2 má velikost mezipaměti 86GB, která není dostatečně velká. Standard_DS3_v2 má velikost mezipaměti 172GB, která je dostatečně velká. Výchozí velikost disku s operačním systémem můžete také snížit pomocí `--node-osdisk-size` . Minimální velikost pro Image AKS je 30 GB. 

Pokud chcete vytvořit fondy uzlů s disky s operačním systémem připojené k síti, můžete to udělat tak, že zadáte `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Název vlastní skupiny prostředků

Když v Azure nasadíte cluster služby Azure Kubernetes, vytvoří se pro pracovní uzly druhá skupina prostředků. Ve výchozím nastavení pojmenuje skupinu prostředků uzlu AKS `MC_resourcegroupname_clustername_location` , ale můžete zadat i vlastní název.

Pokud chcete zadat vlastní název skupiny prostředků, nainstalujte rozšíření Azure CLI AKS-Preview verze 0.3.2 nebo novější. Pomocí rozhraní příkazového řádku Azure použijte `--node-resource-group` parametr `az aks create` příkazu k zadání vlastního názvu pro skupinu prostředků. Pokud k nasazení clusteru AKS použijete šablonu Azure Resource Manager, můžete definovat název skupiny prostředků pomocí `nodeResourceGroup` Vlastnosti.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Sekundární skupinu prostředků automaticky vytvoří poskytovatel prostředků Azure ve vlastním předplatném. Název vlastní skupiny prostředků můžete zadat jenom při vytvoření clusteru. 

Při práci s skupinou prostředků uzlu Pamatujte na to, že nemůžete:

- Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
- Zadejte jiné předplatné pro skupinu prostředků uzlu.
- Po vytvoření clusteru změňte název skupiny prostředků uzlu.
- Zadejte názvy spravovaných prostředků v rámci skupiny prostředků uzlu.
- Upravte nebo odstraňte značky spravovaných prostředků vytvořené v Azure v rámci skupiny prostředků uzlu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [upgradovat image uzlů](node-image-upgrade.md) v clusteru.
- Přečtěte si téma [upgrade clusteru Azure Kubernetes Service (AKS)](upgrade-cluster.md) , kde se dozvíte, jak upgradovat cluster na nejnovější verzi Kubernetes.
- Přečtěte si další informace o [ `containerd` a Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- V seznamu [nejčastějších dotazů k AKS](faq.md) najdete odpovědi na některé běžné otázky týkající se AKS.
- Přečtěte si další informace o [dočasných discích s operačním systémem](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
