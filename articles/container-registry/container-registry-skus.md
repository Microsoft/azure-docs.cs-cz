---
title: Úrovně služeb a SKU
description: Přečtěte si o funkcích a omezeních úrovní služeb Basic, Standard a Premium (SKU) Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456267"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) je k dispozici ve více úrovních služeb, označovaných jako SKU. Tyto SKU poskytují předvídatelné ceny a několik možností pro zarovnávání se vzory kapacity a využití vašeho privátního registru Docker v Azure.

| Skladová jednotka (SKU) | Popis |
| --- | ----------- |
| **Basic** | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové možnosti jako standard a Premium (jako je Azure Active Directory [integrace ověřování](container-registry-authentication.md#individual-login-with-azure-ad), [odstraňování imagí][container-registry-delete]a [Webhooky][container-registry-webhook]). Zahrnutá propustnost úložiště a imagí je ale nejvhodnější pro scénáře s nižším využitím. |
| **Standard** | Registry úrovně Standard nabízejí stejné možnosti jako základní a zvyšují propustnost úložiště a imagí. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Registry úrovně Premium poskytují nejvyšší objem zahrnutých úložišť a souběžných operací, což umožňuje použití scénářů s velkými objemy. Kromě propustnosti vyšších imagí přináší Premium navíc funkce, jako je [geografická replikace][container-registry-geo-replication] pro správu jednoho registru napříč několika oblastmi, [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek imagí, [brány firewall a virtuální sítě (Preview)](container-registry-vnet.md) do Omezte přístup k registru. |

Všechny SKU Basic, Standard a Premium poskytují stejné programové funkce. Všechny mají i výhody [úložiště imagí][container-registry-storage] spravované výhradně Azure. Výběr SKU vyšší úrovně poskytuje vyšší výkon a škálování. S více úrovněmi služeb můžete začít používat základní a pak po zvýšení využití registru převést na standard a Premium.

## <a name="sku-features-and-limits"></a>Funkce a omezení SKU

Následující tabulka obsahuje informace o funkcích a omezeních úrovní služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna SKU

SKLADOVOU položku registru můžete změnit pomocí rozhraní příkazového řádku Azure CLI nebo v Azure Portal. Můžete volně přesouvat mezi SKU, pokud SKU, na který přecházíte, má požadovanou maximální kapacitu úložiště. 

### <a name="azure-cli"></a>Azure CLI

Pokud chcete přesouvat mezi SKU v Azure CLI, použijte příkaz [AZ ACR Update][az-acr-update] . Například pro přepnutí na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V **přehledu** registru kontejnerů v Azure Portal vyberte **aktualizovat**a potom z rozevíracího seznamu SKU vyberte novou **skladovou** položku.

![Aktualizace SKU registru kontejneru v Azure Portal][update-registry-sku]

## <a name="pricing"></a>Ceny

Informace o cenách každé z Azure Container Registry SKU najdete v tématu [Container Registry ceny][container-registry-pricing].

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
