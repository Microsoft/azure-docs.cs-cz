---
title: Aktualizovat skupinu kontejnerů
description: Naučte se aktualizovat spuštěné kontejnery ve vašich Azure Container Instancesch skupinách kontejnerů.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786958"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizace kontejnerů ve službě Azure Container Instances

Během normálního provozu instancí kontejnerů může být nutné aktualizovat běžící kontejnery ve [skupině kontejnerů](./container-instances-container-groups.md). Například můžete chtít aktualizovat vlastnost, jako je například verze image, název DNS nebo proměnná prostředí, nebo aktualizovat vlastnost v kontejneru, u kterého došlo k chybě aplikace.

Aktualizujte kontejnery ve spuštěné skupině kontejnerů tak, že znovu nasadíte stávající skupinu s aspoň jednou upravenou vlastností. Když aktualizujete skupinu kontejnerů, všechny spuštěné kontejnery ve skupině se restartují místně, obvykle na stejném podkladovém hostiteli kontejneru.

> [!NOTE]
> Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile se skupina kontejnerů ukončí (je v úspěšném nebo neúspěšném stavu) nebo se odstranila, musí být skupina nasazená jako nová. Podívejte se na další [omezení](#limitations).

## <a name="update-a-container-group"></a>Aktualizace skupiny kontejnerů

Aktualizace existující skupiny kontejnerů:

* Vydejte příkaz Create (nebo použijte Azure Portal) a zadejte název existující skupiny. 
* Upravte nebo přidejte alespoň jednu vlastnost skupiny, která podporuje aktualizaci při opětovném nasazení. Některé vlastnosti [nepodporují aktualizace](#properties-that-require-container-delete).
* Nastavte další vlastnosti pomocí hodnot, které jste zadali dříve. Pokud nenastavíte hodnotu pro vlastnost, vrátí se výchozí hodnota.

> [!TIP]
> [Soubor YAML](./container-instances-container-groups.md#deployment) pomáhá udržovat konfiguraci nasazení skupiny kontejnerů a poskytuje výchozí bod pro nasazení aktualizované skupiny. Pokud jste k vytvoření skupiny použili jinou metodu, můžete tuto konfiguraci exportovat do YAML pomocí [AZ Container export][az-container-export], 

### <a name="example"></a>Příklad

Následující příklad Azure CLI aktualizuje skupinu kontejnerů pomocí nového popisku názvu DNS. Vzhledem k tomu, že vlastnost jmenovky názvu DNS skupiny je ta, kterou je možné aktualizovat, skupina kontejnerů se znovu nasadí a její kontejnery se restartují.

Počáteční nasazení s popiskem názvu DNS *MyApplication – Příprava*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aktualizujte skupinu kontejnerů pomocí nového popisku názvu DNS, *MyApplication* a nastavte zbývající vlastnosti dříve použitými hodnotami:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Aktualizovat výhody

Hlavní výhodou aktualizace stávající skupiny kontejnerů je rychlejší nasazení. Při opětovném nasazení existující skupiny kontejnerů se z nich z uložených vrstev imagí kontejnerů nainstalují předchozí nasazení. Místo toho, aby se všechny vrstvy imagí z registru stáhly s novými nasazeními, se vyžádaly jenom změněné vrstvy (pokud nějaké jsou).

Aplikace založené na větších imagích kontejnerů, jako je Windows Server Core, můžou při aktualizaci místo odstranění a nasazení nové zobrazit výrazné zlepšení rychlosti nasazení.

## <a name="limitations"></a>Omezení

* Ne všechny vlastnosti skupiny kontejnerů podporují aktualizace. Chcete-li změnit některé vlastnosti skupiny kontejnerů, je třeba nejprve odstranit a znovu nasadit skupinu. Viz [vlastnosti, které vyžadují odstranění kontejneru](#properties-that-require-container-delete).
* Všechny kontejnery ve skupině kontejnerů se po aktualizaci skupiny kontejnerů restartují. V rámci skupiny více kontejnerů se nedá provést aktualizace nebo místní restartování určitého kontejneru.
* IP adresa skupiny kontejnerů je obvykle uchována mezi aktualizacemi, ale není zaručena, že zůstane stejná. Pokud je skupina kontejnerů nasazená do stejného základního hostitele, skupina kontejnerů uchová svou IP adresu. I když jsou k dispozici některé události interní pro Azure, které můžou způsobit opětovné nasazení na jiného hostitele, je to i zřídka. Pro zmírnění tohoto problému doporučujeme pro své instance kontejnerů použít popisek názvu DNS.
* Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile je skupina kontejnerů zastavena (je v *ukončeném* stavu) nebo odstraněna, skupina je nasazena jako nová.

## <a name="properties-that-require-container-delete"></a>Vlastnosti, které vyžadují odstranění kontejneru

Ne všechny vlastnosti skupiny kontejnerů se dají aktualizovat. Chcete-li například změnit zásadu restartování kontejneru, je nutné nejprve odstranit skupinu kontejnerů a pak ji znovu vytvořit.

Změny těchto vlastností vyžadují odstranění skupiny kontejnerů před jejich nasazením:

* Typ operačního systému
* Prostředky procesoru, paměti nebo GPU
* Zásady restartování
* Profil sítě

Když skupinu kontejnerů odstraníte a znovu vytvoříte, nebude se znovu nasazovat, ale vytvořila se nová. Všechny vrstvy imagí jsou z registru načítány z mezipaměti, nikoli z těch, které jsou uložené v mezipaměti v předchozím nasazení. IP adresa kontejneru se může také změnit, protože se nasazuje na jiného základního hostitele.

## <a name="next-steps"></a>Další kroky

V tomto článku je uvedeno několik případů, kde je **Skupina kontejnerů**. Každý kontejner ve Azure Container Instances je nasazený ve skupině kontejnerů a skupiny kontejnerů můžou obsahovat více než jeden kontejner.

[Skupiny kontejnerů ve službě Azure Container Instances](./container-instances-container-groups.md)

[Nasazení skupiny více kontejnerů](container-instances-multi-container-group.md)

[Ruční zastavení nebo spuštění kontejnerů ve službě Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
