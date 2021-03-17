---
title: Použití automatického škálování clusteru ve službě Azure Kubernetes Service (AKS)
description: Naučte se, jak pomocí automatického škálování clusteru automaticky škálovat cluster tak, aby splňoval požadavky aplikace v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9caf56545efc6aefae525e28614d39705c00c21e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742564"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatické škálování clusteru pro splnění požadavků aplikace ve službě Azure Kubernetes Service (AKS)

Aby se zajistilo splnění požadavků aplikace ve službě Azure Kubernetes Service (AKS), možná budete muset upravit počet uzlů, které spouštějí vaše úlohy. Součást automatického škálování clusteru může ve vašem clusteru sledovat lusky, které není možné naplánovat z důvodu omezení prostředků. Při zjištění problémů se počet uzlů ve fondu uzlů zvyšuje, aby splňoval požadavek na aplikaci. Uzly jsou také pravidelně zkontrolovány na chybějící běžící lusky s počtem uzlů, které se pak v případě potřeby snížily. Díky této možnosti horizontálního navýšení nebo snížení počtu uzlů v clusteru AKS můžete spustit efektivní a cenově výhodné cluster.

V tomto článku se dozvíte, jak povolit a spravovat automatické škálování clusteru v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>O automatickém škálování clusteru

Pro úpravu požadavků aplikace, například mezi Workday a večer nebo na víkendu, clustery často potřebují způsob automatického škálování. Clustery AKS se můžou škálovat jedním ze dvou způsobů:

* **Automatické škálování clusteru** sleduje lusky, které není možné naplánovat na uzlech z důvodu omezení prostředků. Cluster pak automaticky zvýší počet uzlů.
* Horizontální navýšení **pod AutoScale** používá server metrik v clusteru Kubernetes k monitorování požadavků na prostředky v luskech. Pokud aplikace potřebuje více prostředků, počet lusků se automaticky zvýší, aby splňovala požadavky.

![Automatické škálování clusteru a horizontální navýšení se často spolupracuje na podporu požadovaných požadavků aplikace.](media/autoscaler/cluster-autoscaler.png)

V případě potřeby můžete také snížit počet lusků a uzlů, a to jak horizontální, tak i automatické škálování clusteru. Automatické škálování clusteru snižuje počet uzlů v případě nevyužité kapacity v časovém intervalu. Lusky na uzlu, který se má odebrat pomocí automatického škálování clusteru, se bezpečně naplánovaly jinde v clusteru. V případě, že se nedá přesunout lusky, například v následujících situacích, není možné automatické škálování clusteru zmenšit.

* Objekt pod je přímo vytvořený a není zálohovaný objektem kontroleru, jako je například nasazení nebo sada replik.
* Rozpočet přerušení pod přerušením (PDB) je příliš omezující a neumožňuje, aby počet lusků klesl pod určitou prahovou hodnotu.
* Uzel pod používá selektory uzlů nebo spřažení, které nelze akceptovat, pokud je naplánováno na jiném uzlu.

Další informace o tom, jak se automatické škálování clusteru nedá škálovat, najdete v tématu [Jaké typy lusků můžou zabránit automatickému škálování clusteru v odebírání uzlu][autoscaler-scaledown] .

Automatické škálování clusteru používá parametry spouštění pro věci, jako jsou časové intervaly mezi událostmi škálování a mezními hodnotami prostředků. Další informace o tom, jaké parametry používá nástroj pro automatické škálování clusteru, najdete v tématu [použití profilu automatického škálování](#using-the-autoscaler-profile).

Cluster a horizontální, pod kterými můžou spolupracovníci spolupracovat a často se obě nasazují v clusteru. V kombinaci se horizontální automatické škálování pod ním zaměřuje na spouštění počtu lusků potřebných pro splnění požadavků aplikace. Automatické škálování clusteru se zaměřuje na spouštění počtu uzlů potřebných k podpoře naplánovaných lusků.

> [!NOTE]
> Ruční škálování je při použití automatického škálování clusteru zakázané. Určete požadovaný počet uzlů, aby měl automatické škálování clusteru. Pokud chcete cluster ručně škálovat, [zakažte automatické škálování clusteru](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Vytvoření clusteru AKS a povolení automatického škálování clusteru

Pokud potřebujete vytvořit cluster AKS, použijte příkaz [AZ AKS Create][az-aks-create] . K povolení a konfiguraci automatického škálování clusteru ve fondu uzlů pro cluster použijte `--enable-cluster-autoscaler` parametr a určete uzel `--min-count` a `--max-count` .

> [!IMPORTANT]
> Automatické škálování clusteru je komponenta Kubernetes. I když cluster AKS používá pro uzly sadu škálování virtuálního počítače, nepovolujte ručně ani neupravujte nastavení automatického škálování sady škálování v Azure Portal nebo pomocí Azure CLI. Umožněte, aby modul automatického škálování clusteru Kubernetes spravoval požadovaná nastavení škálování. Další informace najdete v tématu [Změna prostředků AKS ve skupině prostředků uzlu?][aks-faq-node-resource-group]

Následující příklad vytvoří cluster AKS s jedním fondem uzlů, který je zálohovaný sadou škálování virtuálního počítače. Povoluje taky automatické škálování clusteru ve fondu uzlů pro cluster a nastavuje minimálně *1* a maximálně *3* uzly:

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

Vytvoření clusteru a konfigurace nastavení automatického škálování clusteru trvá několik minut.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Aktualizace stávajícího clusteru AKS pro povolení automatického škálování clusteru

Pomocí příkazu [AZ AKS Update][az-aks-update] povolte a nakonfigurujte pro existující cluster automatické škálování clusteru ve fondu uzlů. Použijte `--enable-cluster-autoscaler` parametr a zadejte uzel `--min-count` a `--max-count` .

> [!IMPORTANT]
> Automatické škálování clusteru je komponenta Kubernetes. I když cluster AKS používá pro uzly sadu škálování virtuálního počítače, nepovolujte ručně ani neupravujte nastavení automatického škálování sady škálování v Azure Portal nebo pomocí Azure CLI. Umožněte, aby modul automatického škálování clusteru Kubernetes spravoval požadovaná nastavení škálování. Další informace najdete v tématu [Změna prostředků AKS ve skupině prostředků uzlu?][aks-faq-node-resource-group]

V následujícím příkladu se aktualizuje existující cluster AKS, aby se povolilo automatické škálování clusteru ve fondu uzlů pro cluster, a je nastavené minimum na *1* a maximálně *3* uzly:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Aktualizace clusteru a konfigurace nastavení automatického škálování clusteru trvá několik minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Změna nastavení automatického škálování clusteru

> [!IMPORTANT]
> Pokud máte ve svém clusteru AKS více fondů uzlů, přeskočte do [části Automatické škálování s více fondy agentů](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Clustery s více fondy agentů vyžadují použití `az aks nodepool` sady příkazů ke změně vlastností specifických pro fond uzlů místo `az aks` .

Pokud jste v předchozím kroku vytvořili cluster AKS nebo aktualizovali existující fond uzlů, byl minimální počet uzlů pro automatické škálování clusteru nastavený na *1* a maximální počet uzlů byl nastavený na *3*. Jak vaše aplikace vyžaduje změnu, možná budete muset upravit počet uzlů automatického škálování clusteru.

Chcete-li změnit počet uzlů, použijte příkaz [AZ AKS Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Výše uvedený příklad aktualizuje automatické škálování clusteru ve fondu s jedním uzlem v *myAKSCluster* na minimálně *1* a maximálně *5* uzlů.

> [!NOTE]
> Automatické škálování clusteru provádí rozhodnutí o škálování na základě minimálního a maximálního počtu nastaveného v každém fondu uzlů, ale neuplatňuje je po aktualizaci počtu min nebo max. Například nastavení minimálního počtu na hodnotu 5, pokud je počet aktuálních uzlů na hodnotu 3, nebude fond okamžitě škálovat na 5. Pokud má minimální počet u fondu uzlů hodnotu vyšší, než je aktuální počet uzlů, bude dodrženo nové nastavení min nebo Max, pokud jsou přítomna dostatečná unschedulable lusky, která by vyžadovala 2 nové další uzly a aktivovala událost automatického škálování. Po události škálování jsou dodrženy nové limity počtu.

Monitorujte výkon svých aplikací a služeb a upravte počty uzlů automatického škálování clusteru tak, aby odpovídaly požadovanému výkonu.

## <a name="using-the-autoscaler-profile"></a>Použití profilu automatického škálování

Můžete taky nakonfigurovat podrobnější informace o automatickém škálování clusteru změnou výchozích hodnot v profilu automatického škálování na úrovni clusteru. Například událost horizontálního navýšení kapacity proběhne po využívaném uzlu po 10 minutách. Pokud máte úlohy, které byly spuštěny každých 15 minut, můžete změnit profil automatického škálování tak, aby se v části využívané uzly po 15 nebo 20 minutách změnila velikost. Pokud povolíte automatické škálování clusteru, použije se výchozí profil, pokud neurčíte jiné nastavení. Profil automatického škálování clusteru má následující nastavení, která můžete aktualizovat:

| Nastavení                          | Popis                                                                              | Výchozí hodnota |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| Kontrola – interval                    | Jak často se cluster znovu vyhodnocuje pro horizontální navýšení nebo snížení kapacity                                    | 10 sekund    |
| horizontální navýšení kapacity – zpoždění po přidání       | Jak dlouho po horizontálním navýšení kapacity dotrvají zkušební obnovení                               | 10 minut    |
| horizontální navýšení kapacity – zpoždění po odstranění    | Doba, po kterou se odstraní po odstranění uzlu, které pokračuje ve zkušebním měřítku                          | Kontrola – interval |
| horizontální navýšení kapacity – zpoždění po chybě   | Jak dlouho po selhání horizontálního navýšení kapacity se zkušební verze obnoví                     | 3 minuty     |
| horizontální navýšení kapacity – nepotřebné         | Jak dlouho by měl uzel být nepotřebný, než bude mít nárok na horizontální navýšení kapacity                  | 10 minut    |
| horizontální navýšení kapacity – nečitelný čas          | Doba, po kterou by měl být nečitelný uzel nutný, než bude mít nárok na horizontální navýšení kapacity         | 20 minut    |
| škála-snížení využití – prahová hodnota | Úroveň využití uzlu definovaná jako součet požadovaných prostředků dělený kapacitou, pod kterou je možné uzel zvážit pro horizontální navýšení kapacity | 0,5 |
| Max – řádné – ukončení – s     | Maximální počet sekund, po které bude automatické škálování clusteru čekat po ukončení při pokusu o horizontální navýšení kapacity uzlu | 600 sekund   |
| rovnováha – podobný uzel – skupiny      | Detekuje podobné fondy uzlů a vyrovnává počet uzlů mezi nimi.                 | false (nepravda)         |
| rozbalovací                         | Typ [rozšíření](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) fondu uzlů, které se má použít při horizontálním navýšení kapacity Možné hodnoty: `most-pods` , `random` , `least-waste` , `priority` | vybraných | 
| Přeskočení uzlů – s-Local-Storage    | Pokud ano, nebude automatické škálování clusteru nikdy odstraňovat uzly s lusky s místním úložištěm, například EmptyDir nebo HostPath. | true |
| Skip-Node-with-System-lusky      | Pokud ano, nebude automatické škálování clusteru nikdy odstraňovat uzly s lusky z Kube-System (kromě DaemonSet nebo zrcadlových lusků). | true | 
| Max – prázdné – hromadné odstranění            | Maximální počet prázdných uzlů, které je možné odstranit současně                       | 10 uzlů      |
| nové-pod-škálovat – zpoždění           | U scénářů, jako je například škálování na úrovni shluku/dávky, kdy nechcete, aby certifikační autorita fungovala dříve, než může Plánovač Kubernetes naplánovat všechny lusky, můžete certifikační autoritě sdělit, aby neplánované lusky ignorovaly dřív,                                                                                                                | 0 sekund    |
| Max-Total-unreadal-PERCENTAGE     | Maximální procento nepřečtených uzlů v clusteru Po překročení tohoto procenta ukončí certifikační autorita operace. | 45 % |
| Max-Node-provision-Time          | Maximální doba, po kterou bude automatické škálování čekat na zřízení uzlu                           | 15 minut    |   
| ok – celkem – nepřečtené – počet           | Počet povolených nepřečtených uzlů bez ohledu na maximum-Total-unreadal-PERCENTAGE            | 3 uzly       |

> [!IMPORTANT]
> Profil automatického škálování clusteru má vliv na všechny fondy uzlů, které používají automatické škálování clusteru. Nemůžete nastavit profil automatického škálování na jeden fond uzlů.
>
> Profil automatického škálování clusteru vyžaduje *2.11.1* nebo novější verzi Azure CLI. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Nastavení profilu automatického škálování clusteru v existujícím clusteru AKS

Pomocí příkazu [AZ AKS Update][az-aks-update-preview] s parametrem *cluster-autoscaleer-Profile* nastavte profil automatického škálování clusteru v clusteru. Následující příklad konfiguruje nastavení intervalu kontroly jako 30 s v profilu.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Pokud povolíte automatické škálování clusteru u fondů uzlů v clusteru, budou tyto clustery také používat profil automatického škálování clusteru. Například:

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
> Když nastavíte profil automatického škálování clusteru, začnou být všechny existující fondy uzlů s povoleným nástrojem automatického škálování clusteru hned používat profil.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Nastavení profilu automatického škálování clusteru při vytváření clusteru AKS

Můžete také použít parametr *cluster-autoscaleer-Profile* při vytváření clusteru. Například:

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

Výše uvedený příkaz vytvoří cluster AKS a pro profil automatického škálování pro celé clustery definuje interval kontroly na 30 sekund. Příkaz také umožňuje automatické škálování clusteru v počátečním fondu uzlů, nastavuje minimální počet uzlů na 1 a maximální počet uzlů na 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Resetovat profil automatického škálování clusteru na výchozí hodnoty

Pomocí příkazu [AZ AKS Update][az-aks-update-preview] resetujete profil automatického škálování clusteru v clusteru.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Zakázat automatické škálování clusteru

Pokud už nechcete používat automatické škálování clusteru, můžete ho zakázat pomocí příkazu [AZ AKS Update][az-aks-update-preview] , který specifikuje `--disable-cluster-autoscaler` parametr. Když je automatické škálování clusteru zakázané, uzly se neodeberou.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Po zakázání automatického škálování clusteru můžete ručně škálovat cluster pomocí příkazu [AZ AKS Scale][az-aks-scale] . Použijete-li automatické škálování vodorovně pod, tato funkce bude nadále běžet se zakázaným nástrojem pro automatické škálování clusteru, ale v případě, že se všechny prostředky uzlů používají, může dojít k neočekávanému ukončení lusků.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Opětovné povolení automatického škálování zakázaného clusteru

Pokud chcete znovu povolit automatické škálování clusteru v existujícím clusteru, můžete ho znovu povolit pomocí příkazu [AZ AKS Update][az-aks-update-preview] , a to zadáním `--enable-cluster-autoscaler` `--min-count` parametrů, a `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Načtení protokolů a stavu automatického škálování clusteru

Chcete-li diagnostikovat a ladit události automatického škálování, můžete protokoly a stav načíst z doplňku automatického škálování.

AKS spravuje automatické škálování clusteru vaším jménem a spouští ho v rovině spravovaného ovládacího prvku. Můžete povolit uzel roviny řízení a zobrazit protokoly a operace od certifikační autority.

Pokud chcete nakonfigurovat protokoly, které se budou nabízet z automatického škálování clusteru do Log Analytics, postupujte podle těchto kroků.

1. Nastavte pravidlo pro protokoly prostředků pro nabízení protokolů clusteru – automatického škálování na Log Analytics. [Pokyny najdete tady][aks-view-master-logs], nezapomeňte zaškrtnout políčko `cluster-autoscaler` při výběru možností pro protokoly.
1. Vyberte v clusteru oddíl logs prostřednictvím Azure Portal.
1. Zadejte následující příklad dotazu do Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Měli byste vidět protokoly podobné následujícímu příkladu, pokud existují protokoly, které se mají načíst.

![Protokoly Log Analytics](media/autoscaler/autoscaler-logs.png)

Automatické škálování clusteru také zapisuje stav do `configmap` pojmenovaného stavu `cluster-autoscaler-status` . Chcete-li načíst tyto protokoly, spusťte následující `kubectl` příkaz. Stav bude hlášen pro každý fond uzlů nakonfigurovaný pomocí automatického škálování clusteru.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Pokud se chcete dozvědět víc o tom, co se protokoluje pomocí automatického škálování, přečtěte si nejčastější dotazy k [projektu GitHubu Kubernetes/autoscaleer][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Použití automatického škálování clusteru s povolenými fondy více uzly

Automatické škálování clusteru lze použít společně s povoleným [fondy více uzlů][aks-multiple-node-pools] . Pomocí tohoto dokumentu se dozvíte, jak povolit více fondů uzlů a přidat další fondy uzlů do existujícího clusteru. Při použití obou funkcí současně povolíte automatické škálování clusteru pro každý fond jednotlivých uzlů v clusteru a můžete každému z nich předat jedinečná pravidla automatického škálování.

V níže uvedeném příkazu se předpokládá, že jste provedli [počáteční pokyny](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) dříve v tomto dokumentu a chcete aktualizovat Max-Count fondu uzlů z *3* na *5*. Pomocí příkazu [AZ AKS nodepool Update][az-aks-nodepool-update] aktualizujte nastavení existujícího fondu uzlů.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Automatické škálování clusteru je možné zakázat pomocí [AZ AKS nodepool Update][az-aks-nodepool-update] a předáním `--disable-cluster-autoscaler` parametru.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Pokud chcete znovu povolit automatické škálování clusteru v existujícím clusteru, můžete ho znovu povolit pomocí příkazu [AZ AKS nodepool Update][az-aks-nodepool-update] , a to zadáním `--enable-cluster-autoscaler` `--min-count` parametrů, a `--max-count` .

> [!NOTE]
> Pokud plánujete používat nástroj pro automatické škálování clusteru s nodepools, který je rozložen na více zón, a využíváte funkce plánování týkající se zón, jako je například naplánování škálovatelného plánu, doporučujeme, abyste měli jednu nodepool na zónu a povolili `--balance-similar-node-groups` profil prostřednictvím profilu automatického škálování. Tím zajistíte, že se automatické škálování dokončí úspěšně, a zkusíte a zachováte velikosti nodepools rovnováhy.

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak automaticky škálovat počet uzlů AKS. K automatickému nastavení počtu lusků, ve kterých běží vaše aplikace, můžete také použít horizontální automatické škálování pod automatickým příchodem. Postup při použití automatického škálování pod horizontálního navýšení najdete v tématu [škálování aplikací v AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why