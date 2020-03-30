---
title: Aktualizovat skupinu kontejnerů
description: Zjistěte, jak aktualizovat spuštěné kontejnery ve skupinách kontejnerů Azure Container Instances.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533307"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizace kontejnerů ve službě Azure Container Instances

Během normálního provozu instancí kontejneru může být nutné aktualizovat spuštěné kontejnery ve [skupině kontejnerů](container-instances-container-groups.md). Můžete například aktualizovat verzi bitové kopie, změnit název DNS, aktualizovat proměnné prostředí nebo aktualizovat stav kontejneru, jehož aplikace havarovala.

> [!NOTE]
> Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile je skupina kontejnerů ukončena (je ve stavu Úspěšné nebo Neúspěšné) nebo byla odstraněna, musí být skupina nasazena jako nová.

## <a name="update-a-container-group"></a>Aktualizace skupiny kontejnerů

Aktualizujte kontejnery ve spuštěné skupině kontejnerů opětovným nasazením existující skupiny s alespoň jednou upravenou vlastností. Při aktualizaci skupiny kontejnerů jsou všechny spuštěné kontejnery ve skupině restartovány na místě, obvykle na stejném podkladovém hostiteli kontejneru.

Znovu nasadit existující skupinu kontejnerů vydáním příkazu create (nebo použijte portál Azure) a zadejte název existující skupiny. Upravte alespoň jednu platnou vlastnost skupiny při vydání příkazu create pro aktivaci opětovného nasazení a ponechte zbývající vlastnosti beze změny (nebo pokračujte v používání výchozích hodnot). Ne všechny vlastnosti skupiny kontejnerů jsou platné pro opětovné nasazení. V části [Vlastnosti, které vyžadují odstranění](#properties-that-require-container-delete) pro seznam nepodporovaných vlastností.

Následující příklad nastavení chování Azure aktualizuje skupinu kontejnerů s novým popiskem názvu DNS. Vzhledem k tomu, že vlastnost popisku názvu DNS skupiny je vlastnost, kterou lze aktualizovat, je skupina kontejnerů znovu nasazena a její kontejnery restartovány.

Počáteční nasazení s popiskem názvu DNS *moje aplikace pracovní*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aktualizujte skupinu kontejnerů novým popiskem názvu DNS, *moje aplikace*a ponechte zbývající vlastnosti beze změny:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Aktualizace výhod

Hlavní výhodou aktualizace existující skupiny kontejnerů je rychlejší nasazení. Když znovu nasadíte existující skupinu kontejnerů, její vrstvy image kontejneru jsou vytaženy z vrstev, které byly uloženy v mezipaměti předchozího nasazení. Namísto vytažení všech vrstev obrazu čerstvé z registru, jak je tomu u nových nasazení, jsou vyžádány pouze upravené vrstvy (pokud existuje).

Aplikace založené na větších ifotkách kontejnerů, jako je Windows Server Core, mohou při aktualizaci vidět významné zlepšení rychlosti nasazení, místo aby byly odstraněny a nasazovat nové.

## <a name="limitations"></a>Omezení

Ne všechny vlastnosti aktualizace skupiny kontejnerů podporují. Chcete-li změnit některé vlastnosti skupiny kontejnerů, musíte nejprve odstranit a potom skupinu znovu nasadit. Podrobnosti naleznete v [tématu Vlastnosti, které vyžadují odstranění kontejneru](#properties-that-require-container-delete).

Všechny kontejnery ve skupině kontejnerů jsou restartovány při aktualizaci skupiny kontejnerů. Nelze provést aktualizaci nebo restartování konkrétního kontejneru na místě ve skupině s více kontejnery.

IP adresa kontejneru se obvykle nezmění mezi aktualizacemi, ale není zaručeno, že zůstane stejná. Dokud je skupina kontejnerů nasazena na stejného základního hostitele, skupina kontejnerů si zachová svou IP adresu. I když vzácné a zatímco Azure Container Instances vynakládá veškeré úsilí k opětovnému nasazení do stejného hostitele, existují některé interní události Azure, které mohou způsobit opětovné nasazení na jiného hostitele. Chcete-li tento problém zmírnit, vždy použijte popisek názvu DNS pro instance kontejneru.

Ukončené nebo odstraněné skupiny kontejnerů nelze aktualizovat. Jakmile je skupina kontejnerů zastavena (je ve stavu *Ukončeno)* nebo byla odstraněna, je skupina nasazena jako nová.

## <a name="properties-that-require-container-delete"></a>Vlastnosti, které vyžadují odstranění kontejneru

Jak již bylo zmíněno dříve, nelze aktualizovat všechny vlastnosti skupiny kontejnerů. Chcete-li například změnit porty nebo restartovat zásady kontejneru, musíte nejprve odstranit skupinu kontejnerů a pak ji znovu vytvořit.

Tyto vlastnosti vyžadují odstranění skupiny kontejnerů před opětovnou nasazením:

* Typ operačního příkazu
* Procesor
* Memory (Paměť)
* Zásady restartování
* Porty

Když odstraníte skupinu kontejnerů a znovu ji vytvoříte, není "znovu nasazena", ale vytvořena nová. Všechny vrstvy bitové kopie jsou vytaženy čerstvé z registru, nikoli z těch, které byly uloženy v mezipaměti předchozího nasazení. IP adresa kontejneru může také změnit z důvodu nasazení na jiného základního hostitele.

## <a name="next-steps"></a>Další kroky

Několikrát je v tomto článku uvedena **skupina kontejnerů**. Každý kontejner v instanci kontejneru Azure se nasadí ve skupině kontejnerů a skupiny kontejnerů mohou obsahovat více než jeden kontejner.

[Skupiny kontejnerů ve službě Azure Container Instances](container-instances-container-groups.md)

[Nasazení skupiny více kontejnerů](container-instances-multi-container-group.md)

[Ruční zastavení nebo spuštění kontejnerů v instanci kontejnerů Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
