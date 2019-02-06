---
title: Použití automatického škálování clusteru ve službě Azure Kubernetes Service (AKS)
description: Další informace o použití automatického škálování clusteru pro automatické škálování clusteru splňovat požadavky na aplikace v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: bfdea1d5380750ec23964cd8564db9b3a9539f15
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754637"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Automatické škálování clusteru, které splňují požadavky aplikace ve službě Azure Kubernetes Service (AKS)

Jak držet krok s požadavky na aplikace ve službě Azure Kubernetes Service (AKS), budete muset upravit počet uzlů, na kterých běží vaše úlohy. Součást automatického škálování clusteru můžete sledovat podů v clusteru, který se kvůli omezení prostředků se nedá naplánovat. Když se zjistí problémy, se zvýší počet uzlů pro splnění požadavků aplikace. Uzly jsou také pravidelně kontroluje nedostatku s podů, počet uzlů, pak sníží podle potřeby. Tato schopnost automaticky vertikálně navyšovat nebo snižovat počet uzlů v clusteru AKS vám umožní spustit cluster efektivní a cenově výhodný.

V tomto článku se dozvíte, jak povolit a spravovat automatického škálování clusteru v clusteru AKS.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Před zahájením

Tento článek vyžaduje použití Azure CLI verze 2.0.55 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

AKS clustery, které podporují automatického škálování clusteru musí používat škálovací sady virtuálních počítačů a verzí Kubernetes *1.12.4* nebo novější. Tato podpora škálovací sada je ve verzi preview. Pokud chcete vyjádřit výslovný souhlas a vytvářet clustery, které používají škálovací sady, nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az] [ az-extension-add] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Při instalaci *aks ve verzi preview* rozšíření, každý cluster AKS, vytvoříte používá model nasazení škálovací sady ve verzi preview. Chcete-li odhlásit se a vytvářet pravidelné a plně podporované clustery, odeberte pomocí rozšíření `az extension remove --name aks-preview`.

## <a name="about-the-cluster-autoscaler"></a>O automatického škálování clusteru

Chcete-li upravit na měnící se požadavky aplikace, jako je mezi workday a večer nebo o víkendu clustery často potřebují způsob, jak automatické škálování. Clustery AKS je možné škálovat dvěma způsoby:

* **Clusteru bylo** hodinky pro podů, které se nedají naplánovat na uzly z důvodu omezení prostředku. Cluster automaticky potom zvýší počet uzlů.
* **Automatického škálování podů vodorovné** používá metrik serveru v clusteru Kubernetes pro monitorování prostředků poptávku pody. Pokud služba potřebuje více prostředků, se automaticky zvýší počet podů pro splnění těchto požadavků.

![Automatického škálování clusteru a automatického škálování podů vodorovné často pracují společně pro podporu požadavků požadovanou aplikaci](media/autoscaler/cluster-autoscaler.png)

Na vodorovné pod automatického škálování a automatického škálování clusteru také pak můžete snížit počet podů a uzlů podle potřeby. Automatického škálování clusteru snižuje počet uzlů, když bude zjištěna nevyužitou kapacitu pro určitou dobu. Podů na uzlu odeberou pomocí automatického škálování clusteru bezpečně naplánováno jinde v clusteru. Automatického škálování clusteru můžou být nelze můžete vertikálně snížit kapacitu, pokud nelze přesunout podů, například v těchto situacích:

* Pod přímo vytvořeny a není podporovaný službou sadu objektu, takové nasazení nebo repliky řadiče.
* Rozpočet pod přerušení (PDB) je příliš omezující a neumožňuje počet podů klesne pod určitou prahovou hodnotu.
* Pod používá selektory uzlu nebo proti spřažení, který nemůže dodržet, pokud naplánováno na jiném uzlu.

Další informace o tom, jak může být automatického škálování clusteru nelze můžete vertikálně snížit kapacitu, najdete v části [jaké druhy lusků může zabránit odebrání uzlu automatického škálování clusteru?][autoscaler-scaledown]

Automatického škálování clusteru používá parametry spuštění pro takové věci, jako je časových intervalů mezi škálovací události a prahové hodnoty prostředků. Tyto parametry jsou definované platformou Azure a nejsou přístupné aktuálně si můžete upravit. Další informace o jaké parametry se používá automatického škálování clusteru naleznete v tématu [co jsou parametry automatického škálování clusteru?] [autoscaler-parameters].

Dvě autoscalers vzájemně spolupracují a často nasazených v clusteru. V kombinaci, automatického škálování podů vodorovné se zaměřuje na spuštění počet podů, požadováno pro splnění požadavků aplikace. Automatického škálování clusteru se zaměřuje na spuštění z počtu uzlů, které jsou potřebné k podpoře naplánované podů.

> [!NOTE]
> Při použití automatického škálování clusteru je ruční škálování zakázané. Umožní automatického škálování clusteru určit požadovaný počet uzlů. Pokud chcete ručně škálování clusteru, [zakázání automatického škálování clusteru](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Vytvoření clusteru AKS a povolení automatického škálování clusteru

Pokud potřebujete k vytvoření clusteru AKS, použijte [az aks vytvořit] [ az-aks-create] příkazu. Zadejte *verze kubernetes –* , který splňuje nebo překračuje počet minimální verze vyžaduje, jak je uvedeno v předchozím [před zahájením](#before-you-begin) oddílu. Chcete-li povolit a konfigurovat automatického škálování clusteru, použijte *--enable clusteru-automatického škálování* parametr a určit uzel *– minimální počet* a *– maximální počet*.

> [!IMPORTANT]
> Automatického škálování clusteru je součástí Kubernetes. I když clusteru AKS používá virtuálního počítače škálovací sady uzlů, není ručně povolit nebo upravit nastavení pro automatické škálování škálovací sady na webu Azure portal nebo pomocí rozhraní příkazového řádku Azure. Umožní automatického škálování clusteru Kubernetes, spravovat škálování požadované nastavení. Další informace najdete v tématu [můžete upravit AKS prostředky ve skupině prostředků MC_?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group)

Následující příklad vytvoří AKS cluster pomocí škálovací sady virtuálních počítačů a automatického škálování clusteru povolena a používá minimálně *1* a maximální počet *3* uzly:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.4 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Trvá několik minut vytvořit cluster a konfigurovat nastavení automatického škálování clusteru.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Povolení automatického škálování clusteru v existujícím clusteru AKS

Můžete povolit v existujícím clusteru AKS, který splňuje požadavky, jak je uvedeno v předchozím automatického škálování clusteru [před zahájením](#before-you-begin) oddílu. Použití [az aks aktualizovat] [ az-aks-update] příkazů a zvolit *--enable clusteru-automatického škálování*, zadejte uzel *– minimální počet* a *– maximální počet*. Následující příklad povolí automatického škálování clusteru v existujícím clusteru, který používá minimálně *1* a maximální počet *3* uzly:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Pokud uzel minimální počet je větší než stávající počet uzlů v clusteru, trvá několik minut vytvořit další uzly.

## <a name="change-the-cluster-autoscaler-settings"></a>Změnit nastavení automatického škálování clusteru

V předchozím kroku, a vytvořit nebo aktualizovat existující cluster AKS, byl nastaven clusteru bylo minimálního počtu uzlů na *1*, a maximální počet uzlů byla nastavena na *3*. Jako vaši aplikaci se změní požadavky, může být potřeba upravit počet uzlů clusteru automatického škálování.

Chcete-li změnit počet uzlů, použijte [az aks aktualizovat] [ az-aks-update] příkaz a zadejte minimální a maximální hodnotou. Následující příklad nastaví *– minimální počet* k *1* a *– maximální počet* k *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Ve verzi preview nelze nastavit vyšší počet minimální uzlů, než je aktuálně nastavený pro cluster. Například, pokud aktuálně máte minimální počet nastavena na *1*, nejde aktualizovat počet minut na *3*.

Monitorování výkonu aplikací a služeb a upravte počet uzlu clusteru automatického škálování tak, aby odpovídaly potřebný výkon.

## <a name="disable-the-cluster-autoscaler"></a>Zakázání automatického škálování clusteru

Pokud chcete už pomocí automatického škálování clusteru, můžete zakázat pomocí [az aks aktualizovat] [ az-aks-update] příkazu. Uzly se neodeberou, pokud je zakázán automatického škálování clusteru.

Chcete-li odebrat automatického škálování clusteru, zadejte *--disable clusteru-automatického škálování* parametru, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Můžete ručně škálovat cluster pomocí [az aks škálování] [ az-aks-scale] příkazu. Pokud používáte automatického škálování podů vodorovné, tuto funkci nadále běží s clusteru bylo zakázáno, ale podů uvíznout nemůže být naplánováno, pokud uzel prostředky se všechny používají.

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak automaticky škálovat počet uzlů AKS. Můžete také horizontální pod automatického škálování má automaticky upravovat počet podů, na kterých běží vaše aplikace. Pokyny týkající se použití automatického škálování podů vodorovné, najdete v článku [škálování aplikací ve službě AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
