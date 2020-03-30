---
title: Použití automatického škálování clusteru ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak pomocí automatického škálování clusteru automaticky škálovat cluster tak, aby splňoval požadavky aplikací v clusteru Služby Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596245"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatické škálování clusteru tak, aby splňoval požadavky aplikací ve službě Azure Kubernetes Service (AKS)

Chcete-li držet krok s požadavky aplikací ve službě Azure Kubernetes Service (AKS), budete muset upravit počet uzlů, které spouštějí vaše úlohy. Součást automatického škálování clusteru může sledovat pody v clusteru, které nelze naplánovat z důvodu omezení prostředků. Když jsou zjištěny problémy, počet uzlů ve fondu uzlů se zvýší tak, aby splňovaly požadavky aplikace. Uzly jsou také pravidelně kontrolovány z důvodu nedostatku spuštěných podů, přičemž počet uzlů se pak podle potřeby snížil. Tato možnost automatického škálování nahoru nebo dolů počet uzlů v clusteru AKS umožňuje spustit efektivní, nákladově efektivní cluster.

Tento článek ukazuje, jak povolit a spravovat automatické škálování clusteru v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které používají automatický škálovací systém clusteru, platí následující omezení:

* Doplněk směrování aplikace HTTP nelze použít.

## <a name="about-the-cluster-autoscaler"></a>O automatickém škálování clusteru

Chcete-li se přizpůsobit měnícím se požadavkům aplikací, například mezi pracovním dnem a večerem nebo o víkendu, clustery často potřebují způsob, jak automaticky škálovat. Clustery AKS lze škálovat jedním ze dvou způsobů:

* **Automatické škálování clusteru** sleduje pody, které nelze naplánovat na uzlech z důvodu omezení prostředků. Cluster pak automaticky zvýší počet uzlů.
* **Horizontální pod autoscaler** používá server metriky v clusteru Kubernetes ke sledování poptávky po prostředcích podů. Pokud aplikace potřebuje více prostředků, počet podů se automaticky zvýší tak, aby splňovaly požadavky.

![Automatický škálovací modul clusteru a horizontální automatický škálovač podčasto často spolupracují na podpoře požadovaných požadavků na aplikace](media/autoscaler/cluster-autoscaler.png)

Horizontální pod autoscaler a clusteru autoscaler můžete také snížit počet podů a uzlů podle potřeby. Automatický škálovač clusteru snižuje počet uzlů, pokud byla po určitou dobu nevyužitá kapacita. Pody na uzlu, který má být odebrán automatickým škálovačem clusteru, jsou bezpečně naplánovány jinde v clusteru. Automatické škálování clusteru nemusí být možné vertikálně snížit kapacitu, pokud se pody nemohou přesunout, například v následujících situacích:

* Pod je přímo vytvořen a není zálohován objektem řadiče, jako je například nasazení nebo sada replik.
* Rozpočet narušení podu (PDB) je příliš omezující a neumožňuje počet podů, které mají být nižší než určitá prahová hodnota.
* Pod používá voliče uzlů nebo anti-afinity, které nelze dodržet, pokud je naplánováno na jiný uzel.

Další informace o tom, jak automatické škálování clusteru nemusí být možné vertikálně snížit kapacitu, naleznete [v tématu Jaké typy podů může zabránit automatickému škálování clusteru v odebrání uzlu?][autoscaler-scaledown]

Automatický škálovací program clusteru používá parametry spuštění pro věci, jako jsou časové intervaly mezi událostmi škálování a prahovými hodnotami prostředků. Tyto parametry jsou definovány platformou Azure a nejsou aktuálně vystaveny pro úpravu. Další informace o tom, jaké parametry používá automatický škálátor clusteru, naleznete [v tématu Jaké jsou parametry automatického škálování clusteru?][autoscaler-parameters].

Automatické škálování clusteru a horizontálnípod mohou spolupracovat a jsou často nasazeny v clusteru. V kombinaci, horizontální pod autoscaler je zaměřen na spuštění počtu podů potřebných ke splnění poptávky po aplikaci. Automatický škálovací modul clusteru je zaměřen na spuštění počtu uzlů potřebných pro podporu naplánovaných podů.

> [!NOTE]
> Ruční škálování je zakázáno při použití automatického škálování clusteru. Nechte automatické škálování clusteru určit požadovaný počet uzlů. Chcete-li ručně škálovat cluster, [zakažte automatický škálovací program clusteru](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Vytvoření clusteru AKS a povolení automatického škálování clusteru

Pokud potřebujete vytvořit cluster AKS, použijte příkaz [az aks create.][az-aks-create] Chcete-li povolit a nakonfigurovat automatický škálátor clusteru ve fondu uzlů pro cluster, použijte parametr *--enable-cluster-autoscaler* a zadejte uzel *--min-count* a *--max-count*.

> [!IMPORTANT]
> Automatický škálovač clusteru je součástí Kubernetes. Přestože cluster AKS používá škálovací sadu virtuálních strojů pro uzly, nepovolujte ručně ani neupravujte nastavení automatického škálování na webu Azure Portal nebo pomocí příkazového příkazu k nastavení Azure. Umožňuje automatickému škálování clusteru Kubernetes spravovat požadovaná nastavení měřítka. Další informace naleznete v [tématu Lze upravit prostředky AKS ve skupině prostředků uzlu?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

Následující příklad vytvoří cluster AKS s fondem jednoho uzlu podporovanýškálovací sadou virtuálních strojů. Umožňuje také automatické škálování clusteru ve fondu uzlů pro cluster a nastavuje minimálně *1* a maximálně *3* uzly:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Vytvoření clusteru a konfigurace nastavení automatického škálovače clusteru trvá několik minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Změna nastavení automatického škálování clusteru

> [!IMPORTANT]
> Pokud máte v clusteru AKS více fondů uzlů, přejděte k [automatickému škálování s více fondy agentů](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Clustery s více fondy `az aks nodepool` agentů vyžadují použití sady příkazů ke `az aks`změně vlastností specifických pro fond uzlů namísto .

V předchozím kroku k vytvoření clusteru AKS nebo aktualizaci existujícího fondu uzlů byl minimální počet uzlů automatického škálování clusteru nastaven na *1*a maximální počet uzlů byl nastaven na *3*. Jak vaše aplikace vyžaduje změnu, budete muset upravit počet uzlů automatického škálování clusteru.

Chcete-li změnit počet uzlů, použijte příkaz [az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Výše uvedený příklad aktualizuje automatické škálování clusteru ve fondu jednoho uzlu v *myAKSCluster* na minimálně *1* a maximálně *5* uzlů.

> [!NOTE]
> Nelze nastavit vyšší minimální počet uzlů, než je aktuálně nastavena pro fond uzlů. Pokud máte například aktuálně min počet nastaven na *1*, nemůžete aktualizovat počet min na *3*.

Sledujte výkon aplikací a služeb a upravte počty uzlů automatického škálování clusteru tak, aby odpovídaly požadovanému výkonu.

## <a name="using-the-autoscaler-profile"></a>Použití profilu autoscaleru

Podrobnější podrobnosti automatického škálování clusteru můžete také nakonfigurovat změnou výchozích hodnot v profilu automatického škálovače celého clusteru. Například škálování dolů událost se stane po uzly jsou nedostatečně využívány po 10 minutách. Pokud jste měli úlohy, které běžely každých 15 minut, můžete změnit profil automatického škálování na škálování pod využité uzly po 15 nebo 20 minutách. Pokud povolíte automatický škálovací systém clusteru, použije se výchozí profil, pokud nezadáte různá nastavení. Profil automatického škálování clusteru má následující nastavení, která můžete aktualizovat:

| Nastavení                          | Popis                                                                              | Výchozí hodnota |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| interval skenování                    | Jak často je cluster přehodnocen pro škálování nahoru nebo dolů                                    | 10 sekund    |
| škálování dolů-zpoždění po přidání       | Jak dlouho po škálování nahoru, že hodnocení škálování pokračuje                               | 10 minut    |
| škálování dolů-zpoždění po odstranění    | Jak dlouho po odstranění uzlu, které škálování hodnocení pokračuje                          | interval skenování |
| škálování-zpoždění po selhání   | Jak dlouho po selhání škálování, které škálování hodnocení pokračuje                     | 3 minuty     |
| zmenšení nepotřebného času         | Jak dlouho by měl být uzel nepotřebný, než bude mít nárok na zmenšení                  | 10 minut    |
| změna velikosti dolů-nepřipravený čas          | Jak dlouho by měl být nepřipravený uzel nepotřebný, než bude způsobilý pro zmenšení         | 20 minut    |
| prahová hodnota pro sazby | Úroveň využití uzlu, definovaná jako součet požadovaných zdrojů dělených kapacitou, pod kterou lze uzel považovat za zmenšení kapacity | 0,5 |
| max-graceful-termination-sec     | Maximální počet sekund, po které automatický škálovač clusteru čeká na ukončení podu při pokusu o zmenšení stupně platnosti uzlu. | 600 sekund   |

> [!IMPORTANT]
> Profil automatického škálování clusteru ovlivňuje všechny fondy uzlů, které používají automatický škálovací systém clusteru. Nelze nastavit profil automatického škálování na fond uzlů.

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření cli aks-preview

Chcete-li nastavit profil nastavení automatického škálování clusteru, potřebujete rozšíření rozšíření *aks-preview* CLI verze 0.4.30 nebo vyšší. Nainstalujte rozšíření *AKS-preview* Azure CLI pomocí příkazu [az extension add][az-extension-add] a pak zkontrolujte všechny dostupné aktualizace pomocí příkazu [aktualizace rozšíření az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Nastavení profilu automatického škálování clusteru v existujícím clusteru AKS

Pomocí příkazu [az aks update][az-aks-update] s parametrem *profile clusteru autoscaler* nastavte profil automatického škálování clusteru v clusteru. Následující příklad konfiguruje nastavení intervalu skenování jako 30s v profilu.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Pokud povolíte automatické škálování clusteru ve fondech uzlů v clusteru, budou tyto clustery také používat profil automatického škálování clusteru. Například:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Když nastavíte profil automatického škálování clusteru, všechny existující fondy uzlů s povoleným automatickým škálovačem clusteru začnou profil okamžitě používat.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Nastavení profilu automatického škálování clusteru při vytváření clusteru AKS

Při vytváření clusteru můžete také použít parametr *profilu clusteru autoscaler.* Například:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Výše uvedený příkaz vytvoří cluster AKS a definuje interval skenování jako 30 sekund pro profil automatického škálovače celého clusteru. Příkaz také povolí automatické škálování clusteru v počátečním fondu uzlů, nastaví minimální počet uzlů na 1 a maximální počet uzlů na 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Obnovit výchozí hodnoty profilu automatického škálování clusteru na výchozí hodnoty

Pomocí příkazu [az aks update][az-aks-update] obnovte profil automatického škálování clusteru v clusteru.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Zakázání automatického škálování clusteru

Pokud již nechcete používat automatický škálovatel clusteru, můžete jej zakázat pomocí příkazu [az aks update][az-aks-update] a zadat parametr *--disable-cluster-autoscaler.* Uzly nejsou odebrány, pokud je automatický škálovač clusteru zakázán.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Po zakázání automatického škálování clusteru můžete ručně škálovat cluster pomocí příkazu [měřítka az aks.][az-aks-scale] Pokud používáte horizontální pod autoscaler, tato funkce nadále běží s clusteru autoscaler zakázán, ale pody může skončit nelze naplánovat, pokud jsou používány všechny prostředky uzlu.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Opětovné povolení automatického škálování zakázaného clusteru

Pokud chcete znovu povolit automatické škálování clusteru v existujícím clusteru, můžete jej znovu povolit pomocí příkazu [az aks update][az-aks-update] a zadat parametry *--enable-cluster-autoscaler*, *--min-count*a *--max-count.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Načtení protokolů a stavu automatického škálování clusteru

Chcete-li diagnostikovat a ladit události automatického škálování, protokoly a stav lze načíst z doplňku autoscaler.

AKS spravuje automatické škálování clusteru vaším jménem a spouští jej v rovině spravovaného řízení. Protokoly hlavních uzlů musí být nakonfigurovány tak, aby byly zobrazeny jako výsledek.

Chcete-li nakonfigurovat protokoly, které mají být nabízeny z automatického škálování clusteru do log analytics, postupujte takto.

1. Nastavte pravidlo pro diagnostické protokoly pro nabízení protokolů automatického škálování clusteru do analýzy protokolů. [Pokyny jsou podrobně popsány zde](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs) `cluster-autoscaler` , ujistěte se, že políčko při výběru možností pro "Protokoly".
1. Klikněte na část "Protokoly" ve vašem clusteru prostřednictvím portálu Azure.
1. Zadejte následující ukázkový dotaz do analýzy protokolů:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Měli byste vidět protokoly podobné následujícímu příkladu tak dlouho, dokud existují protokoly načíst.

![Protokoly analýzy protokolů](media/autoscaler/autoscaler-logs.png)

Automatický škálovací nástroj clusteru také zapíše stav `cluster-autoscaler-status`do configmap s názvem . Chcete-li tyto protokoly `kubectl` načíst, spusťte následující příkaz. Stav bude hlášen pro každý fond uzlů nakonfigurovaný pomocí automatického škálování clusteru.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Chcete-li se dozvědět více o tom, co je zaznamenáno z autoscaleru, přečtěte si nejčastější dotazy k [projektu Kubernetes/autoscaler GitHub](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Použití automatického škálování clusteru s povoleným více fondy uzlů

Automatické škálování clusteru lze použít společně s [povoleným více fondy uzlů.](use-multiple-node-pools.md) Postupujte podle tohoto dokumentu se dozvíte, jak povolit více fondů uzlů a přidat další fondy uzlů do existujícího clusteru. Při společném používání obou funkcí povolíte automatické škálování clusteru v každém jednotlivém fondu uzlů v clusteru a můžete každému z nich předat jedinečná pravidla automatického škálování.

Níže uvedený příkaz předpokládá, že jste postupovali podle [počátečních pokynů](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) dříve v tomto dokumentu a chcete aktualizovat maximální počet existujícího fondu uzlů od *3* do *5*. Pomocí příkazu [aktualizace uzlu az aks][az-aks-nodepool-update] aktualizujte nastavení existujícího fondu uzlů.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Automatické škálování clusteru lze zakázat s [aktualizací uzlu az aks][az-aks-nodepool-update] a předáním parametru. `--disable-cluster-autoscaler`

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Pokud chcete znovu povolit automatické škálování clusteru v existujícím clusteru, můžete jej znovu povolit pomocí příkazu [aktualizace uzlu aks][az-aks-nodepool-update] a zadat parametry *--enable-cluster-autoscaler*, *--min-count*a *--max-count.*

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak automaticky škálovat počet uzlů AKS. Můžete také použít horizontální pod autoscaler automaticky upravit počet podů, které spouštějí aplikaci. Postup používání automatického škálování vodorovných podů najdete [v tématu Škálování aplikací v AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
