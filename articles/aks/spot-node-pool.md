---
title: Náhled – přidání fondu přímých uzlů do clusteru služby Azure Kubernetes Service (AKS)
description: Zjistěte, jak přidat fond přímých uzlů do clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622042"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Náhled – přidání fondu přímých uzlů do clusteru služby Azure Kubernetes Service (AKS)

Fond bodových uzlů je fond uzlů podporovaný [škálovací sadou virtuálních strojů][vmss-spot]. Použití virtuálních počítačů na místě pro uzly s clusterem AKS umožňuje využít nevyužité kapacity v Azure s významnou úsporou nákladů. Množství dostupné nevyužité kapacity se bude lišit v závislosti na mnoha faktorech, včetně velikosti uzlu, oblasti a denní doby.

Při nasazování fondu bodových uzlů Azure přidělí uzly na místě, pokud je k dispozici kapacita. Ale neexistuje žádná SLA pro bodové uzly. Spot škálovací sada, která podporuje fond místních uzlů je nasazena v jedné doméně selhání a nenabízí žádné záruky vysoké dostupnosti. Kdykoli, když Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává uzly spot.

Bodové uzly jsou skvělé pro úlohy, které mohou zpracovávat přerušení, předčasné ukončení nebo vyřazení. Například úlohy, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí a velké výpočetní úlohy, mohou být dobrými kandidáty, které mají být naplánovány ve fondu uzlů na místě.

V tomto článku přidáte sekundární bodový uzel fondu do existujícího clusteru služby Azure Kubernetes Service (AKS).

Tento článek předpokládá základní znalosti kubernetes a Azure Balancer koncepty. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts].

Tato funkce je aktuálně ve verzi Preview.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="before-you-begin"></a>Než začnete

Když vytvoříte cluster pro použití fondu přímých uzlů, musí tento cluster také používat škálovací sady virtuálních strojů pro fondy uzlů a *standardní* nástroj pro vyrovnávání zatížení skladové položky. Po vytvoření clusteru je také nutné přidat další fond uzlů, aby bylo možné použít fond bodových uzlů. Přidání dalšího fondu uzlů je pokryto v pozdějším kroku, ale nejprve musíte povolit funkci náhledu.

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné, opt-in. Jsou poskytovány shromažďovat zpětnou vazbu a chyby z naší komunity. Ve verzi Preview nejsou tyto funkce určeny pro produkční použití. Funkce ve verzi Public Preview spadají pod podporu "nejlepšího úsilí". Pomoc týmů technické podpory AKS je k dispozici pouze v pracovní době tichomořského časového pásma (PST). Další informace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Registrace funkce náhledu spotpoolpreview

Chcete-li vytvořit cluster AKS, který používá fond uzly na místě, musíte povolit příznak funkce *spotpoolpreview* ve vašem předplatném. Tato funkce poskytuje nejnovější sadu vylepšení služeb při konfiguraci clusteru.

> [!CAUTION]
> Když zaregistrujete funkci v předplatném, nelze tuto funkci aktuálně zrušit. Po povolení některých funkcí náhledu mohou být výchozí hodnoty použity pro všechny clustery AKS, které jsou poté vytvořeny v předplatném. Nepovolujte funkce náhledu v produkčních předplatných. Pomocí samostatného předplatného otestujte funkce náhledu a shromážděte zpětnou vazbu.

Zaregistrujte příznak funkce *spotpoolpreview* pomocí příkazu [az feature register,][az-feature-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Trvá několik minut, než se zobrazí stav *Registrováno*. Stav registrace můžete zkontrolovat pomocí příkazu [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft.ContainerService* pomocí příkazu [registrovat zprostředkovatele az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření cli aks-preview

Chcete-li vytvořit cluster AKS, který používá fond bodových uzlů, potřebujete rozšíření rozšíření cli *aks-preview* verze 0.4.32 nebo vyšší. Nainstalujte rozšíření *AKS-preview* Azure CLI pomocí příkazu [az extension add][az-extension-add] a pak zkontrolujte všechny dostupné aktualizace pomocí příkazu [aktualizace rozšíření az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Omezení

Následující omezení platí při vytváření a správě clusterů AKS s fondem přímých uzlů:

* Fond bodových uzlů nemůže být výchozím fondem uzlů clusteru. Fond bodových uzlů lze použít pouze pro sekundární fond.
* Fond bodových uzlů nelze upgradovat, protože bazény s bodovými uzlinami nemohou zaručit kordon a vypouštění. Je nutné nahradit stávající fond bodových uzlů novým, který bude dělat operace, jako je například upgrade verze Kubernetes. Chcete-li nahradit fond bodových uzlů, vytvořte nový fond bodových uzlů s jinou verzí Kubernetes, počkejte, dokud nebude jeho stav *Připraven ,* a pak odeberte fond starých uzlů.
* Fondy roviny ovládacího prvku a uzlů nelze upgradovat současně. Je nutné je upgradovat samostatně nebo odebrat fond bodů uzlu upgradovat rovinu ovládacího prvku a zbývající fondy uzlů ve stejnou dobu.
* Fond bodových uzlů musí používat škálovací sady virtuálních strojů.
* ScaleSetPriority nebo SpotMaxPrice nelze po vytvoření změnit.
* Při nastavování SpotMaxPrice musí být hodnota -1 nebo kladná hodnota s až pěti desetinnými místy.
* Fond bodových uzlů bude mít popisek *kubernetes.azure.com/scalesetpriority:spot*, poskvrnit *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*a systémové pody budou mít anti-afinitu.
* Je nutné přidat [odpovídající tolerance][spot-toleration] naplánovat úlohy na místě uzlu fondu.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Přidání fondu přímých uzlů do clusteru AKS

Do existujícího clusteru, který má povoleno více fondů uzlů, je nutné přidat fond přímých uzlů. Další podrobnosti o vytvoření clusteru AKS s více fondy uzlů jsou k dispozici [zde][use-multiple-node-pools].

Vytvořte fond uzlů pomocí přidání [uzlu az aks][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Ve výchozím nastavení vytvoříte fond uzlů s *prioritou* *Regular* v clusteru AKS při vytváření clusteru s více fondy uzlů. Výše uvedený příkaz přidá fond pomocných uzlů do existujícího clusteru AKS s *prioritou* *Bod*. *Priorita* *Spot* umožňuje fondu uzlů fond bodový uzel. Parametr *zásady vyřazení* je nastaven na *odstranit* ve výše uvedeném příkladu, což je výchozí hodnota. Když nastavíte [zásadu vyřazení][eviction-policy] na *Odstranit*, uzly v základní škálovací sadě fondu uzlů se při vyřazení odstraní. Můžete také nastavit zásady vyřazení na *Navrátit*. Když nastavíte zásadu vyřazení na *Navrátit*, uzly v základní sadě měřítka jsou nastaveny na zastavený stav při vyřazení. Uzly v zastaveném stavu se počítají s vaší výpočetní kvótou a mohou způsobit problémy s škálováním nebo inovací clusteru. Hodnoty *zásad priority* a *vyřazení* lze nastavit pouze během vytváření fondu uzlů. Tyto hodnoty nelze později aktualizovat.

Příkaz také umožňuje [automatické škálování clusteru][cluster-autoscaler], který se doporučuje používat s fondy bodových uzlů. Na základě úloh spuštěných v clusteru se automaticky škálátor clusteru škáluje a škáluje počet uzlů ve fondu uzlů. Pro fondy přímých uzlů automaticky škálátor clusteru navažuje navýšit počet uzlů po vyřazení, pokud jsou stále potřeba další uzly. Pokud změníte maximální počet uzlů, které může fond uzlů mít, `maxCount` budete také muset upravit hodnotu přidruženou k automatickému škálování clusteru. Pokud nepoužíváte automatické škálování clusteru, po vyřazení se fond bodů nakonec sníží na nulu a bude vyžadovat ruční operaci pro příjem dalších uzly na místě.

> [!Important]
> Naplánujte pouze úlohy na fondy uzlů na místě, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování a testovací prostředí. Doporučujeme nastavit skvrny [a tolerance][taints-tolerations] ve fondu uzlů na místě, abyste zajistili, že ve fondu uzlů na místě jsou naplánovány pouze úlohy, které mohou zpracovávat vyřazování uzlů. Například výše uvedený příkaz ny default přidá podobu *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* takže v tomto uzlu jsou naplánovány pouze pody s odpovídající tolerance.

## <a name="verify-the-spot-node-pool"></a>Ověření fondu přímých uzlů

Chcete-li ověřit fond uzlů byl přidán jako fond přímých uzlů:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Potvrdit *scaleSetPriority* je *Spot*.

Chcete-li naplánovat spuštění podu na bodovém uzlu, přidejte toleraci, která odpovídá skvrně aplikované na váš bodový uzel. Následující příklad ukazuje část souboru yaml, která definuje tolerance, která odpovídá *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* poskvrnit použité v předchozím kroku.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Při nasazení pod s touto tolerací kubernetes můžete úspěšně naplánovat pod na uzly s potu použít.

## <a name="max-price-for-a-spot-pool"></a>Maximální cena za bodový bazén
[Ceny pro spotové instance jsou variabilní][pricing-spot], založené na oblasti a skladové jednotce. Další informace naleznete v tématu ceny pro [Linux][pricing-linux] a [Windows][pricing-windows].

S variabilními cenami máte možnost nastavit maximální cenu v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota *0,98765* by byla maximální cena $0.98765 USD za hodinu. Pokud nastavíte maximální cenu na *-1*, instance nebude vyřazena na základě ceny. Cena instance bude aktuální cena spotu nebo cena za standardní instanci, podle toho, která hodnota je nižší, pokud je k dispozici kapacita a kvóta.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak přidat fond bodových uzlů do clusteru AKS. Další informace o tom, jak řídit pody ve fondech uzlů, naleznete [v tématu Doporučené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md