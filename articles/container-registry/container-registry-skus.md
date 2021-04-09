---
title: Úrovně a funkce služby registru
description: Přečtěte si o funkcích a omezeních (kvótách) na úrovních služeb Basic, Standard a Premium (SKU) Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b55fc16dcd6dcb544ed4597ce4bdc6ba17b52646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015656"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry úrovní služeb

Azure Container Registry je k dispozici ve více úrovních služby (označované také jako SKU). Tyto úrovně poskytují předvídatelné ceny a několik možností pro zarovnávání se vzory kapacity a využití vašeho privátního registru Docker v Azure.

| Úroveň | Description |
| --- | ----------- |
| **Basic** | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové možnosti jako standard a Premium (jako je Azure Active Directory [integrace ověřování](container-registry-authentication.md#individual-login-with-azure-ad), [odstraňování imagí][container-registry-delete]a [Webhooky][container-registry-webhook]). Zahrnutá propustnost úložiště a imagí je ale nejvhodnější pro scénáře s nižším využitím. |
| **Standard** | Registry úrovně Standard nabízejí stejné možnosti jako základní a zvyšují propustnost úložiště a imagí. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Registry úrovně Premium poskytují nejvyšší objem zahrnutých úložišť a souběžných operací, což umožňuje použití scénářů s velkými objemy. Kromě propustnosti vyšších imagí přináší Premium navíc funkce, jako je [geografická replikace][container-registry-geo-replication] pro správu jednoho registru napříč několika oblastmi, [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek obrázků, [soukromé propojení s privátními koncovými body](container-registry-private-link.md) , aby se omezil přístup k registru. |

Úrovně Basic, Standard a Premium poskytují stejné programové funkce. Všechny mají i výhody [úložiště imagí][container-registry-storage] spravované výhradně Azure. Výběr úrovně vyšší úrovně poskytuje vyšší výkon a škálování. S více úrovněmi služeb můžete začít používat základní a pak po zvýšení využití registru převést na standard a Premium.

## <a name="service-tier-features-and-limits"></a>Funkce a omezení úrovně služby

Následující tabulka obsahuje podrobnosti o funkcích a omezeních úrovně služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Změna úrovní

Vrstvu služby registru můžete změnit pomocí rozhraní příkazového řádku Azure CLI nebo v Azure Portal. Mezi vrstvami se můžete pohybovat volně, pokud úroveň, na kterou přepínáte, má požadovanou maximální kapacitu úložiště. 

Při přesunu mezi úrovněmi služeb nedochází k žádným výpadkům registru ani vlivům na operace registru.

### <a name="azure-cli"></a>Azure CLI

Pokud se chcete pohybovat mezi úrovněmi služeb v Azure CLI, použijte příkaz [AZ ACR Update][az-acr-update] . Například pro přepnutí na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V **přehledu** registru kontejnerů v Azure Portal vyberte **aktualizovat** a potom z rozevíracího seznamu SKU vyberte novou **skladovou** položku.

![Aktualizace SKU registru kontejneru v Azure Portal][update-registry-sku]

## <a name="pricing"></a>Ceny

Informace o cenách na jednotlivých úrovních Azure Container Registry služby najdete v článku [Container Registry ceny][container-registry-pricing].

Podrobnosti o cenách přenosů dat najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Další kroky

**Azure Container Registry plán**

Informace o nadcházejících funkcích ve službě najdete v [ACR][acr-roadmap] plánu na GitHubu.

**Azure Container Registry UserVoice**

Posílejte na nové návrhy funkcí v [ACR UserVoice][container-registry-uservoice]a hlasovat o nich.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md