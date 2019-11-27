---
title: Aktualizovat skupinu kontejnerů
description: Naučte se aktualizovat spuštěné kontejnery ve vašich Azure Container Instancesch skupinách kontejnerů.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533307"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizace kontejnerů v Azure Container Instances

Během normálního provozu instancí kontejnerů může být nutné aktualizovat běžící kontejnery ve [skupině kontejnerů](container-instances-container-groups.md). Například můžete chtít aktualizovat verzi image, změnit název DNS, aktualizovat proměnné prostředí nebo aktualizovat stav kontejneru, u kterého došlo k chybě aplikace.

> [!NOTE]
> Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile se skupina kontejnerů ukončí (je v úspěšném nebo neúspěšném stavu) nebo se odstranila, musí být skupina nasazená jako nová.

## <a name="update-a-container-group"></a>Aktualizace skupiny kontejnerů

Aktualizujte kontejnery ve spuštěné skupině kontejnerů tak, že znovu nasadíte stávající skupinu s aspoň jednou upravenou vlastností. Když aktualizujete skupinu kontejnerů, všechny spuštěné kontejnery ve skupině se restartují místně, obvykle na stejném podkladovém hostiteli kontejneru.

Znovu nasaďte existující skupinu kontejnerů vyvoláním příkazu CREATE (nebo použijte Azure Portal) a zadejte název existující skupiny. Upravte alespoň jednu platnou vlastnost skupiny při vydávání příkazu CREATE pro aktivaci opětovného nasazení a nechejte zbývající vlastnosti beze změny (nebo pokračujte v používání výchozích hodnot). Ne všechny vlastnosti skupiny kontejnerů jsou platné pro opětovné nasazení. Seznam nepodporovaných vlastností najdete v tématu [vlastnosti, které vyžadují odstranění](#properties-that-require-container-delete) .

Následující příklad Azure CLI aktualizuje skupinu kontejnerů pomocí nového popisku názvu DNS. Vzhledem k tomu, že vlastnost jmenovky názvu DNS skupiny je ta, kterou je možné aktualizovat, skupina kontejnerů se znovu nasadí a její kontejnery se restartují.

Počáteční nasazení s popiskem názvu DNS *MyApplication – Příprava*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aktualizujte skupinu kontejnerů pomocí nového popisku názvu DNS, *MyApplication*a nechejte zbývající vlastnosti beze změny:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Aktualizovat výhody

Hlavní výhodou aktualizace stávající skupiny kontejnerů je rychlejší nasazení. Při opětovném nasazení existující skupiny kontejnerů se z nich z uložených vrstev imagí kontejnerů nainstalují předchozí nasazení. Místo toho, aby se všechny vrstvy imagí z registru stáhly s novými nasazeními, se vyžádaly jenom změněné vrstvy (pokud nějaké jsou).

Aplikace založené na větších imagích kontejnerů, jako je Windows Server Core, můžou při aktualizaci místo odstranění a nasazení nové zobrazit výrazné zlepšení rychlosti nasazení.

## <a name="limitations"></a>Omezení

Ne všechny vlastnosti skupiny kontejnerů podporují aktualizace. Chcete-li změnit některé vlastnosti skupiny kontejnerů, je třeba nejprve odstranit a znovu nasadit skupinu. Podrobnosti najdete v tématu [vlastnosti, které vyžadují odstranění kontejneru](#properties-that-require-container-delete).

Všechny kontejnery ve skupině kontejnerů se po aktualizaci skupiny kontejnerů restartují. V rámci skupiny více kontejnerů se nedá provést aktualizace nebo místní restartování určitého kontejneru.

IP adresa kontejneru se obvykle nemění mezi aktualizacemi, ale nezaručujeme, že zůstane stejná. Pokud je skupina kontejnerů nasazená do stejného základního hostitele, skupina kontejnerů uchová svou IP adresu. I když je to zřídka a i když Azure Container Instances provádí opětovné nasazení na stejného hostitele, existují některé události interní v Azure, které můžou způsobit opětovné nasazení na jiného hostitele. Pokud chcete tento problém zmírnit, vždycky pro své instance kontejneru použijte popisek názvu DNS.

Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile je skupina kontejnerů zastavena (je v *ukončeném* stavu) nebo byla odstraněna, skupina je nasazena jako nová.

## <a name="properties-that-require-container-delete"></a>Vlastnosti, které vyžadují odstranění kontejneru

Jak bylo zmíněno dříve, ne všechny vlastnosti skupiny kontejnerů lze aktualizovat. Chcete-li například změnit porty nebo restartovat zásady kontejneru, je nutné nejprve odstranit skupinu kontejnerů a pak ji znovu vytvořit.

Tyto vlastnosti vyžadují před znovu nasazením odstranění skupiny kontejnerů:

* Typ operačního systému
* Procesor
* Paměť
* Restartovat zásadu
* Porty

Když skupinu kontejnerů odstraníte a znovu vytvoříte, nebude se znovu nasazovat, ale vytvořila se nová. Všechny vrstvy imagí jsou z registru načítány z mezipaměti, nikoli z těch, které jsou uložené v mezipaměti v předchozím nasazení. IP adresa kontejneru se může také změnit, protože se nasazuje na jiného základního hostitele.

## <a name="next-steps"></a>Další kroky

V tomto článku je uvedeno několik případů, kde je **Skupina kontejnerů**. Každý kontejner ve Azure Container Instances je nasazený ve skupině kontejnerů a skupiny kontejnerů můžou obsahovat více než jeden kontejner.

[Skupiny kontejnerů ve službě Azure Container Instances](container-instances-container-groups.md)

[Nasazení skupiny s více kontejnery](container-instances-multi-container-group.md)

[Ruční zastavení nebo spuštění kontejnerů v Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
