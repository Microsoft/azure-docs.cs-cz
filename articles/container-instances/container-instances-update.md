---
title: Aktualizace kontejnery ve službě Azure Container Instances
description: Zjistěte, jak aktualizovat spuštěné kontejnery ve skupinách kontejnerů Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: cce7befeb7f0f770bf42fcd7926979da97a6a44a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391869"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizace kontejnery ve službě Azure Container Instances

Během normálního provozu vaší instancí kontejneru možná bude nutné aktualizovat kontejnerů ve skupině kontejnerů. Možná budete chtít aktualizovat verzi image, změňte název DNS, aktualizace proměnné prostředí nebo obnovení stavu kontejneru, jehož aplikace došlo k chybě.

## <a name="update-a-container-group"></a>Aktualizace skupiny kontejnerů

Aktualizujte kontejnerů ve skupině kontejnerů pomocí opětovného nasazení existující skupinu s aspoň jeden změněné vlastnosti. Při aktualizaci kontejneru skupiny, restartují se všechny spuštěné kontejnery ve skupině na místě.

Znovu nasadit existující skupinu kontejnerů pomocí příkazu pro vytvoření (nebo použijte na webu Azure portal) a zadejte název existující skupiny. Upravte alespoň jednu platnou vlastnost skupiny při vydání příkazu pro vytvoření aktivuje opětovné nasazení. Ne všechny vlastnosti skupiny kontejnerů jsou platné pro opětovné nasazení. Zobrazit [vlastností, které vyžadují odstranit](#properties-that-require-delete) seznam nepodporované vlastnosti.

Následující příklad rozhraní příkazového řádku Azure aktualizuje skupinu kontejnerů s použitím nového popisku názvu DNS. Vzhledem k tomu, že je změněna vlastnost popisku názvu DNS, skupiny, je znovu nasadit skupinu kontejnerů a jejím kontejnerům restartování.

Počáteční nasazení s popisku názvu DNS *myapplication pracovní*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aktualizovat skupinu kontejnerů s použitím nového popisku názvu DNS, *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Aktualizace výhody

Hlavní výhodou aktualizuje existující skupiny kontejnerů je rychlejší nasazení. Při opětovném nasazování stávající skupinu kontejnerů, jeho vrstvy image kontejneru se berou od těch, které do mezipaměti v předchozím nasazení. Místo potažením všechny vrstvy image čerstvé z registru dělalo u nové nasazení, se berou pouze změněné vrstvy (pokud existuje).

Aplikací založených na větších imagí kontejneru, jako je jádro systému Windows Server můžete zobrazit výrazné zlepšení rychlosti nasazení, když aktualizujete místo odstranění a nového nasazení.

## <a name="limitations"></a>Omezení

Ne všechny vlastnosti skupiny kontejnerů podporují aktualizace. Chcete-li změnit některé vlastnosti skupiny kontejnerů, musíte nejprve odstranit a znovu nasadit skupinu. Podrobnosti najdete v tématu [vlastností, které vyžadují kontejneru odstranit](#properties-that-require-container-delete).

Při aktualizaci kontejneru skupiny, restartují se všechny kontejnery ve skupině kontejnerů. Nelze provést aktualizaci nebo restartování místní konkrétní kontejner v skupiny více kontejnerů.

IP adresu kontejneru se nezmění obvykle mezi aktualizace, ale není zaručena měnit. Tak dlouho, dokud skupiny kontejnerů je nasazen na stejnou základní hostitele, skupiny kontejnerů zachová svou IP adresu. I když je vzácné, a zatímco Azure Container Instances je snaží znovu nasadit do stejného hostitele, existují některé Azure interní události, které může způsobit, že opětovné nasazení na jiného hostitele. Pokud chcete tento problém zmírnit, vždy použijte popisek názvu DNS pro container instances.

Skupin kontejnerů zrušené nebo odstraněné se nedá aktualizovat. Jakmile skupinu kontejnerů se zastavila. (je v *ukončeno* stavu), nebo odstranění skupiny nasazení nových.

## <a name="properties-that-require-container-delete"></a>Vlastnosti, které vyžadují container delete

Jak bylo zmíněno výše, je možné aktualizovat všechny vlastnosti skupiny kontejnerů. Například pokud chcete změnit porty nebo restartovat zásadu kontejneru, musíte nejprve odstranit skupinu kontejnerů a znovu ji vytvořit.

Tyto vlastnosti vyžadovat odstranění skupiny kontejnerů před novým nasazením:

* Typ operačního systému
* Procesor
* Memory (Paměť)
* Zásady restartování
* Porty

Při odstranění skupiny kontejnerů a znovu ho vytvořte ho má není "znovu nasadil", ale byla vytvořena nová. Všechny image vrstvy se berou čerstvé, z registru, ne z mezipaměti předchozí nasazení. Z důvodu se nasazuje na jiného hostitele základní může také změnit IP adresa kontejneru.

## <a name="next-steps"></a>Další postup

Uvedené je několikrát v tomto článku **skupinu kontejnerů**. Každý kontejner ve službě Azure Container Instances se nasadí ve skupině kontejnerů a skupiny kontejnerů může obsahovat více než jednoho kontejneru.

[Skupiny kontejnerů ve službě Azure Container Instances](container-instances-container-groups.md)

[Nasazení skupiny více kontejnerů](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
