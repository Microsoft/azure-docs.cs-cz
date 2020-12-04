---
title: Konfigurace clusteru ve službě Azure Kubernetes Services (AKS)
description: Informace o tom, jak nakonfigurovat cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: dcd9ec52992da8037bb498dc09701879c4c94667
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572571"
---
# <a name="configure-an-aks-cluster"></a>Konfigurace clusteru AKS

V rámci vytváření clusteru AKS možná budete muset přizpůsobit konfiguraci clusteru tak, aby vyhovovala vašim potřebám. Tento článek představuje několik možností přizpůsobení clusteru AKS.

## <a name="os-configuration"></a>Konfigurace operačního systému

AKS nyní podporuje Ubuntu 18,04 jako operační systém Node (OS) ve všeobecné dostupnosti pro clustery ve verzích Kubernetes vyšších než 1.18.8. Pro verze nižší než 1.18. x je výchozí základní bitová kopie stále výchozí AKS Ubuntu 16,04. Od Kubernetes v 1.18. x a dalších je výchozí základ AKS Ubuntu 18,04.

> [!IMPORTANT]
> Fondy uzlů vytvořené ve výchozím nastavení Kubernetes v 1.18 nebo vyšší pro `AKS Ubuntu 18.04` Image Node. Fondy uzlů na podporované verzi Kubernetes, která je menší než 1,18, se přijímají `AKS Ubuntu 16.04` jako image uzlu, ale `AKS Ubuntu 18.04` po aktualizaci verze Kubernetes fondu uzlů na verzi v 1.18 nebo vyšší.
> 
> Před použitím clusterů na 1,18 nebo novějším se doporučuje testovat vaše úlohy na fondech uzlů AKS Ubuntu 18,04. Přečtěte si informace o [testování fondů uzlů Ubuntu 18,04](#use-aks-ubuntu-1804-existing-clusters-preview).

V následující části se dozvíte, jak používat a testovat AKS Ubuntu 18,04 na clusterech, které ještě nepoužívají Kubernetes verze 1.18. x nebo vyšší, nebo které byly vytvořené před tím, než byla tato funkce všeobecně dostupná, pomocí verze Preview konfigurace operačního systému.

Musíte mít nainstalované následující zdroje:

- [Rozhraní příkazového řádku Azure][azure-cli-install], verze 2.2.0 nebo novější
- Rozšíření AKS-Preview 0.4.35

Pokud chcete nainstalovat rozšíření AKS-Preview 0.4.35 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Zaregistrujte `UseCustomizedUbuntuPreview` funkci:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Pro nové clustery použijte AKS Ubuntu 18,04 (Preview).

Nakonfigurujte cluster tak, aby při vytvoření clusteru používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit clustery s imagí AKS Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Použití existujících clusterů AKS Ubuntu 18,04 (Preview)

Nakonfigurujte nový fond uzlů tak, aby používal Ubuntu 18,04. Pomocí `--aks-custom-headers` příznaku nastavte Ubuntu 18,04 jako výchozí operační systém pro tento fond uzlů.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Pokud chcete vytvořit fondy uzlů s imagí AKS Ubuntu 16,04, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

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

V následující části se dozvíte, jak můžete používat a testovat AKS s `containerD` clustery, které ještě nepoužívají Kubernetes verze 1,19 nebo vyšší, nebo byly vytvořené před tím, než se tato funkce všeobecně k dispozici, pomocí ukázky konfigurace modulu runtime kontejneru.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Použijte `containerd` jako modul runtime kontejneru (Preview)

Musíte mít následující požadavky:

- Je nainstalovaná verze rozhraní příkazového [řádku Azure CLI][azure-cli-install]2.8.0 nebo novější.
- Rozšíření AKS-Preview verze 0.4.53 nebo novější
- `UseCustomizedContainerRuntime`Příznak funkce je zaregistrovaný.
- `UseCustomizedUbuntuPreview`Příznak funkce je zaregistrovaný.

Pokud chcete nainstalovat rozšíření AKS-Preview 0.4.53 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Zaregistrujte `UseCustomizedContainerRuntime` `UseCustomizedUbuntuPreview` funkce a:

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Použít `containerd` pro nové clustery (Preview)

Nakonfigurujte cluster, který se použije `containerd` při vytváření clusteru. Pomocí `--aks-custom-headers` příznaku nastavte `containerd` jako modul runtime kontejneru.

> [!NOTE]
> `containerd`Modul runtime je podporován pouze v uzlech a fondech uzlů pomocí Image AKS Ubuntu 18,04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Pokud chcete vytvořit clustery s modulem runtime Moby (Docker), můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.

### <a name="use-containerd-on-existing-clusters-preview"></a>Použít `containerd` pro existující clustery (Preview)

Nakonfigurujte nový fond uzlů, který se má použít `containerd` . Pomocí `--aks-custom-headers` příznaku nastavte `containerd` modul runtime pro tento fond uzlů.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Pokud chcete vytvořit fondy uzlů s modulem runtime Moby (Docker), můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.


### <a name="containerd-limitationsdifferences"></a>`Containerd` omezení/rozdíly

* Pokud chcete použít `containerd` jako modul runtime kontejneru, musíte jako základní image operačního systému použít AKS Ubuntu 18,04.
* I když je sada nástrojů Docker stále přítomna v uzlech, Kubernetes používá `containerd` jako modul runtime kontejneru. Proto vzhledem k tomu, že Moby/Docker nespravuje kontejnery vytvořené Kubernetes na uzlech, nemůžete zobrazit ani s nimi pracovat pomocí příkazů Docker (jako `docker ps` ) nebo rozhraní Docker API.
* `containerd`V případě nástroje doporučujeme použít [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) jako náhradní příkaz CLI místo Docker CLI pro **řešení potíží s** podmnožinami, kontejnery a imagemi kontejnerů na uzlech Kubernetes (například `crictl ps` ). 
   * Neposkytuje kompletní funkce rozhraní příkazového řádku Docker. Je určený jenom pro odstraňování potíží.
   * `crictl` nabízí více kubernetesch zobrazení kontejnerů, s koncepty, jako jsou lusky atd.
* `Containerd` Nastaví protokolování pomocí standardizovaného `cri` formátu protokolování (to se liší od toho, co aktuálně pochází z ovladače rozhraní JSON Docker). Vaše řešení protokolování vyžaduje podporu `cri` formátu protokolování (například [Azure monitor pro kontejnery](../azure-monitor/insights/container-insights-enable-new-cluster.md)).
* Už nemůžete získat přístup k modulu Docker, `/var/run/docker.sock` ani použít Docker-in-Docker (DinD).
  * Pokud v současné době extrahujete protokoly aplikací nebo monitorovaná data z modulu Docker, použijte místo toho něco jako [Azure monitor for Containers](../azure-monitor/insights/container-insights-enable-new-cluster.md) . AKS navíc nepodporuje spouštění jakýchkoli příkazů mimo pásmo na uzlech agentů, které by mohly způsobit nestabilitu.
  * I když používáte Moby/Docker, sestavování imagí a přímé využití modulu Docker prostřednictvím výše uvedených metod se důrazně nedoporučuje. Kubernetes není plně vědoma těch spotřebovaných prostředků a tyto přístupy obsahují řadu problémů, které jsou [zde](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) popsané, a [tady](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), například.
* Vytváření imagí – doporučený postup pro vytváření imagí je použití [úloh ACR](../container-registry/container-registry-quickstart-task-cli.md). Alternativním řešením je použití bezpečnějších možností v clusteru jako [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Virtuální počítače generace 2 (Preview)

Azure podporuje [virtuální počítače generace 2 (Gen2) (VM](../virtual-machines/generation-2.md)). Virtuální počítače 2. generace podporují klíčové funkce, které nejsou podporované na virtuálních počítačích generace 1 (Gen1). Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM).

Virtuální počítače generace 2 používají novou architekturu na bázi rozhraní UEFI namísto architektury založené na systému BIOS používané virtuálními počítači 1. generace.
Virtuální počítače Gen2 podporují jenom určité SKU a velikosti. Zkontrolujte [seznam podporovaných velikostí](../virtual-machines/generation-2.md#generation-2-vm-sizes), abyste viděli, jestli vaše SKU podporuje nebo vyžaduje Gen2.

Kromě toho ne všechny image virtuálních počítačů podporují Gen2, na virtuálních počítačích s AKS Gen2 použije nový [Obrázek AKS Ubuntu 18,04](#os-configuration). Tento obrázek podporuje všechny SKU a velikosti Gen2.

Pokud chcete ve verzi Preview používat virtuální počítače s Gen2, budete potřebovat:
- `aks-preview`Rozšíření CLI je nainstalované.
- `Gen2VMPreview`Příznak funkce je zaregistrován.

Zaregistrujte `Gen2VMPreview` funkci:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

K instalaci rozšíření AKS-Preview rozhraní příkazového řádku použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
az extension add --name aks-preview
```

Pokud chcete aktualizovat rozšíření CLI AKS-Preview, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Použití virtuálních počítačů s Gen2 pro nové clustery (Preview)
Nakonfigurujte cluster, aby při vytváření clusteru používal virtuální počítače s Gen2 pro vybranou SKU. Pomocí `--aks-custom-headers` příznaku nastavte Gen2 jako generování virtuálního počítače na novém clusteru.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Pokud chcete vytvořit běžný cluster pomocí virtuálních počítačů generace 1 (Gen1), můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku. Můžete také přidat další virtuální počítače s Gen1 nebo Gen2, jak je uvedeno níže.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Použití virtuálních počítačů s Gen2 pro existující clustery (Preview)
Nakonfigurujte nový fond uzlů tak, aby používal virtuální počítače s Gen2. Pomocí `--aks-custom-headers` příznaku nastavte Gen2 jako generování virtuálního počítače pro tento fond uzlů.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Pokud chcete vytvořit regulární fondy uzlů Gen1, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` značku.


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
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
