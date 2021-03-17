---
title: Přidání fondu spotových uzlů do clusteru služby Azure Kubernetes Service (AKS)
description: Naučte se přidat fond uzlů s přímým odkazem do clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181121"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Přidání fondu spotových uzlů do clusteru služby Azure Kubernetes Service (AKS)

Fond uzlů s přímým odkazem je fond uzlů zálohovaný pomocí [škálované sady škálování virtuálních počítačů][vmss-spot]. Použití přímých virtuálních počítačů pro uzly s clusterem AKS vám umožní využít výhod nevyužité kapacity v Azure, a to s významnou úsporou nákladů. Množství dostupné nevyužité kapacity se bude lišit v závislosti na mnoha faktorech, včetně velikosti uzlů, oblasti a denního času.

Když nasadíte fond uzlů s přímým uzlem, Azure přidělí uzly bodu, pokud je dostupná kapacita. Ale pro uzly na místě neexistuje žádná smlouva SLA. Sada škálování s přímým nastavením, která vrací fond uzlů pro obnovení, je nasazená v jedné doméně selhání a nenabízí žádné záruky vysoké dostupnosti. V případě, že Azure potřebuje kapacitu zpátky, vyřadí se uzly na místě v infrastruktuře Azure.

Uzly s přímým ovládáním jsou skvělé pro úlohy, které mohou zpracovávat přerušení, předčasné ukončení nebo vyřazení. Například úlohy, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, můžou být vhodnými kandidáty pro naplánování na fond uzlů s přímým zatížením.

V tomto článku přidáte fond sekundárních uzlů do stávajícího clusteru Azure Kubernetes Service (AKS).

Tento článek předpokládá základní znalost konceptů Kubernetes a Azure Load Balancer. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Když vytvoříte cluster pro použití fondu uzlů s přímým použitím, musí tento cluster také používat Virtual Machine Scale Sets pro fondy uzlů a *standardní* SKU pro vyrovnávání zatížení. Po vytvoření clusteru musíte také přidat další fond uzlů, aby bylo možné použít fond uzlů s přímým použitím. Přidání dalšího fondu uzlů je zahrnuto v pozdějším kroku.

Tento článek vyžaduje, abyste spustili Azure CLI verze 2,14 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS s přímým fondem uzlů platí následující omezení:

* Fond uzlů s přímým nastavením nemůže být výchozím fondem uzlů clusteru. Fond uzlů s přímým použitím se dá použít jenom pro sekundární fond.
* Nemůžete upgradovat fond uzlů s přímým vztahem, protože fondy přímých uzlů nemůžou zaručit Cordon a vyprázdnění. Stávající fond uzlů s přímým použitím je nutné nahradit novým operací, jako je například upgrade verze Kubernetes. Chcete-li nahradit fond uzlů typu bodový, vytvořte nový fond uzlů s jinou verzí Kubernetes, počkejte *na dokončení jeho stavu a pak* Odeberte starý fond uzlů.
* Rovina ovládacího prvku a fondy uzlů nelze upgradovat současně. Chcete-li upgradovat rovinu ovládacího prvku a zbývající fondy uzlů současně, je nutné je upgradovat samostatně nebo odebrat fond uzlů s přímým plánem.
* Fond uzlů s přímým použitím musí Virtual Machine Scale Sets.
* Po vytvoření nelze změnit ScaleSetPriority ani SpotMaxPrice.
* Při nastavování SpotMaxPrice musí být hodnota-1 nebo kladná hodnota s až pěti desetinnými místy.
* Fond uzlů s přímým popisem bude mít *Kubernetes.Azure.com/scalesetpriority:spot* jmenovku, *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* a lusky v systému budou mít proti spřažení.
* Je nutné přidat [odpovídající tolerování][spot-toleration] pro plánování úloh ve fondu uzlů s přímým cílem.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Přidání fondu spotových uzlů do clusteru AKS

Do existujícího clusteru, který má povolené více fondů uzlů, je nutné přidat fond bodových uzlů. Další podrobnosti o vytváření clusteru AKS s více fondy uzlů jsou k dispozici [zde][use-multiple-node-pools].

Vytvořte fond uzlů pomocí direktivy [AZ AKS nodepool Add][az-aks-nodepool-add].
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

Ve výchozím nastavení vytvoříte fond uzlů s *prioritou* *Regular* v clusteru AKS při vytváření clusteru s více fondy uzlů. Výše uvedený příkaz přidá pomocný fond uzlů do stávajícího clusteru AKS s *prioritou* *bodu*. *Priorita* *bodu* nastaví fond uzlů jako fond uzlů. Parametr *vyřazení-Policy* je v předchozím příkladu nastaven na hodnotu *Odstranit* , což je výchozí hodnota. Když nastavíte [zásadu vyřazení][eviction-policy] na hodnotu *Odstranit*, uzly v podkladové sadě škálování fondu uzlů budou při jejich vyřazení odstraněny. Můžete také nastavit zásadu vyřazení k *navrácení*. Když nastavíte zásadu vyřazení k uvolnění *, uzly* v podkladové sadě škálování budou po vyřazení nastaveny na stav zastaveno (přidělení zrušeno). Uzly v počtu zastavených a nepřidělených stavů oproti vaší kvótě COMPUTE a můžou způsobit problémy s škálováním nebo upgradem clusteru. Hodnoty zásad *priority* a *vyřazení* lze nastavit pouze během vytváření fondu uzlů. Tyto hodnoty se nedají aktualizovat později.

Příkaz také umožňuje [Automatické škálování clusteru][cluster-autoscaler], které se doporučuje používat s fondy uzlů s přímým použitím. V závislosti na zatíženích spuštěných ve vašem clusteru škáluje automatické škálování clusteru a škáluje počet uzlů ve fondu uzlů. V případě fondů uzlů s přímým škálováním bude po vyřazení škálovat počet uzlů, pokud je potřeba další uzly. Pokud změníte maximální počet uzlů, které může fond uzlů mít, je také nutné upravit `maxCount` hodnotu přidruženou k automatickému škálování clusteru. Pokud automatické škálování clusteru nepoužíváte při vyřazení, fond na místě se nakonec sníží na nulu a bude vyžadovat ruční operaci pro příjem jakýchkoli dalších uzlů.

> [!Important]
> Naplánujte pouze úlohy ve fondech uzlů, které mohou zpracovávat přerušení, například úlohy dávkového zpracování a testovací prostředí. Doporučuje se nastavit [příchuti a tolerovány][taints-tolerations] ve fondu uzlů s přímým ovládáním, abyste zajistili, že pouze úlohy, které mohou zpracovávat vyřazení uzlů, budou naplánovány na fond uzlů s přímým použitím. Například výše uvedený příkaz ve výchozím nastavení přidá chuti *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* , takže v tomto uzlu je naplánováno pouze lusky s odpovídajícími tolerováními.

## <a name="verify-the-spot-node-pool"></a>Ověřit fond uzlů pro přímý uzel

Pokud chcete ověřit, že fond uzlů byl přidán jako fond uzlů s přímým označením:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Potvrďte, že *scaleSetPriority* je na *místě*.

Chcete-li naplánovat spuštění pod uzlem na místě, přidejte tolerování, které odpovídá chuti, aplikovanému na váš uzel na místě. Následující příklad ukazuje část souboru YAML, která definuje tolerovánost, která odpovídá *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* chuti použité v předchozím kroku.

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

Když se nasadí uzel pod touto tolerování, může Kubernetes úspěšně naplánovat pole na uzlech s aplikovanou příchuti.

## <a name="max-price-for-a-spot-pool"></a>Maximální cena pro fond přímých adres
[Ceny pro instance přímých instancí jsou proměnné][pricing-spot]na základě oblastí a SKU. Další informace najdete v tématu ceny pro [Linux][pricing-linux] a [Windows][pricing-windows].

S proměnnými cenami máte možnost nastavit maximální cenu v USD (USD), která používá až 5 desetinných míst. Například hodnota *0,98765* by byla maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu na hodnotu *-1*, instance nebude vyřazení na základě ceny. Cena za instanci bude aktuální cena za cenu nebo cena standardní instance, podle toho, která je menší, pokud je dostupná kapacita a kvóta.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak přidat do clusteru AKS fond uzlů typu bodový. Další informace o tom, jak ovládat lusky napříč fondy uzlů, najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
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