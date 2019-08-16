---
title: Použití automatického škálování clusteru ve službě Azure Kubernetes Service (AKS)
description: Naučte se, jak pomocí automatického škálování clusteru automaticky škálovat cluster tak, aby splňoval požadavky aplikace v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 6ed50380b47040793e9826b64297bacf6ab12c71
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533590"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Preview – automaticky škáluje cluster tak, aby splňoval požadavky aplikace ve službě Azure Kubernetes (AKS).

Aby se zajistilo splnění požadavků aplikace ve službě Azure Kubernetes Service (AKS), možná budete muset upravit počet uzlů, které spouštějí vaše úlohy. Součást automatického škálování clusteru může ve vašem clusteru sledovat lusky, které není možné naplánovat z důvodu omezení prostředků. Při zjištění problémů se počet uzlů ve fondu uzlů zvyšuje, aby splňoval požadavek na aplikaci. Uzly jsou také pravidelně zkontrolovány na chybějící běžící lusky s počtem uzlů, které se pak v případě potřeby snížily. Díky této možnosti horizontálního navýšení nebo snížení počtu uzlů v clusteru AKS můžete spustit efektivní a cenově výhodné cluster.

V tomto článku se dozvíte, jak povolit a spravovat automatické škálování clusteru v clusteru AKS. Automatické škálování clusteru by se mělo testovat jenom ve verzi Preview v clusterech AKS.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Pro použití automatického škálování clusteru potřebujete rozšíření *AKS-Preview* CLI verze 0.4.4 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli nejsou dostupné aktualizace, pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Registrovat poskytovatele funkcí sady škálování

Pokud chcete vytvořit AKS, který používá sady škálování, musíte ve svém předplatném taky povolit příznak funkce. Chcete-li zaregistrovat příznak funkce *VMSSPreview* , použijte příkaz [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které používají automatické škálování clusteru, platí následující omezení:

* Nelze použít doplněk směrování aplikace HTTP.

## <a name="about-the-cluster-autoscaler"></a>O automatickém škálování clusteru

Pro úpravu požadavků aplikace, například mezi Workday a večer nebo na víkendu, clustery často potřebují způsob automatického škálování. Clustery AKS se můžou škálovat jedním ze dvou způsobů:

* **Automatické škálování clusteru** sleduje lusky, které není možné naplánovat na uzlech z důvodu omezení prostředků. Cluster pak automaticky zvýší počet uzlů.
* Horizontální navýšení **pod AutoScale** používá server metrik v clusteru Kubernetes k monitorování požadavků na prostředky v luskech. Pokud služba potřebuje více prostředků, počet lusků se automaticky zvýší, aby splňovala požadavky.

![Automatické škálování clusteru a horizontální navýšení se často spolupracuje na podporu požadovaných požadavků aplikace.](media/autoscaler/cluster-autoscaler.png)

Automatické škálování pod a automatického škálováním na clusteru může také snížit počet lusků a uzlů podle potřeby. Automatické škálování clusteru snižuje počet uzlů v případě nevyužité kapacity v časovém intervalu. Lusky na uzlu, který se má odebrat pomocí automatického škálování clusteru, se bezpečně naplánovaly jinde v clusteru. V případě, že se nedá přesunout lusky, například v následujících situacích, není možné automatické škálování clusteru zmenšit.

* Objekt, který je přímo vytvořený, a není zálohovaný objektem kontroleru, jako je například nasazení nebo sada replik.
* Rozpočet přerušení pod přerušením (PDB) je příliš omezující a neumožňuje, aby počet lusků klesl pod určitou prahovou hodnotu.
* Uzel pod používá selektory uzlů nebo spřažení, které nelze akceptovat, pokud je naplánováno na jiném uzlu.

Další informace o tom, jak se automatické škálování clusteru nedá škálovat, najdete v tématu [Jaké typy lusků můžou zabránit automatickému škálování clusteru v odebírání uzlu][autoscaler-scaledown] .

Automatické škálování clusteru používá parametry spouštění pro věci, jako jsou časové intervaly mezi událostmi škálování a mezními hodnotami prostředků. Tyto parametry jsou definované platformou Azure a momentálně se nezveřejňují, abyste je mohli upravovat. Další informace o tom, jaké parametry používá nástroj pro automatické škálování clusteru, najdete v tématu [co jsou parametry automatického škálování clusteru?][autoscaler-parameters].

Cluster a horizontální funkce autoškálovatelnosti můžou spolupracovat společně a často se nasazují společně v clusteru. V kombinaci se horizontální automatické škálování pod ním zaměřuje na spouštění počtu lusků potřebných pro splnění požadavků aplikace. Automatické škálování clusteru se zaměřuje na spouštění počtu uzlů potřebných k podpoře naplánovaných lusků.

> [!NOTE]
> Ruční škálování je při použití automatického škálování clusteru zakázané. Určete požadovaný počet uzlů, aby měl automatické škálování clusteru. Pokud chcete cluster ručně škálovat, [zakažte automatické škálování clusteru](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Vytvoření clusteru AKS a povolení automatického škálování clusteru

Pokud potřebujete vytvořit cluster AKS, použijte příkaz [AZ AKS Create][az-aks-create] . Pokud chcete zapnout a nakonfigurovat automatické škálování clusteru ve fondu uzlů pro cluster, použijte parametr *--Enable-cluster-* autoscaleer a určete uzel *--min-Count* a *--Max-Count*.

> [!IMPORTANT]
> Automatické škálování clusteru je komponenta Kubernetes. I když cluster AKS používá pro uzly sadu škálování virtuálního počítače, nepovolujte ručně ani neupravujte nastavení automatického škálování sady škálování v Azure Portal nebo pomocí Azure CLI. Umožněte, aby modul automatického škálování clusteru Kubernetes spravoval požadovaná nastavení škálování. Další informace najdete v tématu [Změna prostředků AKS ve skupině prostředků uzlu?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

Následující příklad vytvoří cluster AKS s jediným fondem uzlů zálohovaným sadou škálování virtuálního počítače. Povoluje taky automatické škálování clusteru ve fondu uzlů pro cluster a nastavuje minimálně *1* a maximálně *3* uzly:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Pokud při spuštění `az aks create`zadáte *--Kubernetes-Version* , musí tato verze splňovat minimální číslo verze, které je nutné, jak je uvedeno v předchozí části, [než začnete](#before-you-begin) .

Vytvoření clusteru a konfigurace nastavení automatického škálování clusteru trvá několik minut.

## <a name="change-the-cluster-autoscaler-settings"></a>Změna nastavení automatického škálování clusteru

> [!IMPORTANT]
> Pokud máte ve svém předplatném povolenou funkci *více fondů agentů* , přejděte do [části Automatické škálování s více fondy agentů](##use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Clustery s povoleným více fondy agentů vyžadují `az aks nodepool` použití sady příkazů ke změně vlastností specifických pro fond uzlů `az aks`místo. Níže uvedené pokyny předpokládají, že jste nepovolili více fondů uzlů. Pokud chcete zjistit, jestli je povolená, `az feature  list -o table` spusťte příkaz a `Microsoft.ContainerService/multiagentpoolpreview`vyhledejte ho.

Pokud jste v předchozím kroku vytvořili cluster AKS nebo aktualizovali existující fond uzlů, byl minimální počet uzlů pro automatické škálování clusteru nastavený na *1*a maximální počet uzlů byl nastavený na *3*. Jak vaše aplikace vyžaduje změnu, možná budete muset upravit počet uzlů automatického škálování clusteru.

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
> Během období Preview nemůžete nastavit vyšší minimální počet uzlů, než je aktuálně nastaveno pro fond uzlů. Pokud máte například v současné době minimální počet minut nastavenou na hodnotu *1*, nelze aktualizovat minimální počet na *3*.

Monitorujte výkon svých aplikací a služeb a upravte počty uzlů automatického škálování clusteru tak, aby odpovídaly požadovanému výkonu.

## <a name="disable-the-cluster-autoscaler"></a>Zakázat automatické škálování clusteru

Pokud už nechcete používat automatické škálování clusteru, můžete ho zakázat pomocí příkazu [AZ AKS Update][az-aks-update] a zadat parametr *--Disable-cluster-* autoscaleer. Když je automatické škálování clusteru zakázané, uzly se neodeberou.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Po zakázání automatického škálování clusteru můžete ručně škálovat cluster pomocí příkazu [AZ AKS Scale][az-aks-scale] . Použijete-li automatické škálování vodorovně pod, tato funkce bude nadále běžet se zakázaným nástrojem pro automatické škálování clusteru, ale v případě, že se všechny prostředky uzlů používají, může dojít k neočekávanému ukončení lusků.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Opětovné povolení automatického škálování zakázaného clusteru

Pokud chcete znovu povolit automatické škálování clusteru v existujícím clusteru, můžete ho znovu povolit pomocí příkazu [AZ AKS Update][az-aks-update] , který určuje parametr *--Enable-cluster-* autoscaleer.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Použití automatického škálování clusteru s povolenými fondy více uzly

Automatické škálování clusteru je možné použít společně s povolenou [funkcí Preview fondů více uzlů](use-multiple-node-pools.md) . Pomocí tohoto dokumentu se dozvíte, jak povolit více fondů uzlů a přidat další fondy uzlů do existujícího clusteru. Při použití obou funkcí současně povolíte automatické škálování clusteru pro každý fond jednotlivých uzlů v clusteru a můžete každému z nich předat jedinečná pravidla automatického škálování.

V níže uvedeném příkazu se předpokládá, že jste provedli [počáteční pokyny](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) dříve v tomto dokumentu a chcete aktualizovat Max-Count fondu uzlů z *3* na *5*. Pomocí příkazu [AZ AKS nodepool Update][az-aks-nodepool-update] aktualizujte nastavení existujícího fondu uzlů.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Automatické škálování clusteru je možné zakázat pomocí [AZ AKS nodepool Update][az-aks-nodepool-update] a předáním `--disable-cluster-autoscaler` parametru.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak automaticky škálovat počet uzlů AKS. K automatickému nastavení počtu lusků, ve kterých běží vaše aplikace, můžete také použít horizontální automatické škálování pod automatickým příchodem. Postup při použití automatického škálování pod horizontálního navýšení najdete v tématu [škálování aplikací v AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
