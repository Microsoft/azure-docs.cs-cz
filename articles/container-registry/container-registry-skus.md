---
title: Azure Container Registry SKU
description: Porovnejte různé úrovně služeb dostupné v Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311892"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) je k dispozici ve více úrovních služeb, označovaných jako SKU. Tyto SKU poskytují předvídatelné ceny a několik možností pro zarovnávání se vzory kapacity a využití vašeho privátního registru Docker v Azure.

| SKU | Spravovaní | Popis |
| --- | :-------: | ----------- |
| **Basic** | Ano | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové možnosti jako standard a Premium (jako je Azure Active Directory [integrace ověřování](container-registry-authentication.md#individual-login-with-azure-ad), [odstraňování imagí][container-registry-delete]a Webhooky). [][container-registry-webhook] Zahrnutá propustnost úložiště a imagí je ale nejvhodnější pro scénáře s nižším využitím. |
| **Standard** | Ano | Registry úrovně Standard nabízejí stejné možnosti jako základní a zvyšují propustnost úložiště a imagí. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Ano | Registry úrovně Premium poskytují nejvyšší objem zahrnutých úložišť a souběžných operací, což umožňuje použití scénářů s velkými objemy. Kromě propustnosti vyšších imagí přináší Premium navíc funkce, včetně [geografické replikace][container-registry-geo-replication] pro správu jednoho registru napříč několika oblastmi, [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek image a [brány firewall a virtuální sítě (Preview)](container-registry-vnet.md) na. Omezte přístup k registru. |
|  Classic (*není k dispozici po dubnu 2019*) | Ne | Tato SKU povoluje počáteční vydání služby Azure Container Registry v Azure. Klasické Registry jsou založené na účtu úložiště, který Azure vytvoří ve vašem předplatném, což omezuje schopnost ACR poskytovat funkce vyšší úrovně, jako je například vyšší propustnost a geografická replikace. |

> [!IMPORTANT]
> Skladová položka registru Classic jezastaralá a nebude k dispozici po uplynutí **dubna 2019**. Pro všechny nové Registry doporučujeme použít Basic, Standard nebo Premium. Všechny existující Registry Classic by měly být upgradovány před vydáním dubna 2019. Informace o upgradu najdete v tématu [upgrade klasického registru][container-registry-upgrade].

SKU Basic, Standard a Premium (souhrnně označované jako *spravované Registry*) poskytují stejné programové funkce. Všechny mají i výhody [úložiště imagí][container-registry-storage] spravované výhradně Azure. Výběr SKU vyšší úrovně poskytuje vyšší výkon a škálování. S více úrovněmi služeb můžete začít používat základní a pak po zvýšení využití registru převést na standard a Premium.

## <a name="sku-feature-matrix"></a>Matice funkcí SKU

Následující tabulka obsahuje informace o funkcích a omezeních úrovní služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna SKU

SKLADOVOU položku registru můžete změnit pomocí rozhraní příkazového řádku Azure CLI nebo v Azure Portal. Můžete volně přesouvat mezi spravovanými SKU, pokud SKU, na který přecházíte, má požadovanou maximální kapacitu úložiště. Když přepnete na jednu ze spravovaných SKU z klasického režimu, nemůžete přejít zpátky na klasický – jedná se o jednosměrný převod.

### <a name="azure-cli"></a>Azure CLI

Pokud chcete přesouvat mezi SKU v Azure CLI, použijte příkaz [AZ ACR Update][az-acr-update] . Například pro přepnutí na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V **přehledu** registru kontejnerů v Azure Portal vyberte **aktualizovat**a potom z rozevíracího seznamu SKU vyberte novou **skladovou** položku.

![Aktualizace SKU registru kontejneru v Azure Portal][update-registry-sku]

Pokud máte klasický registr, nemůžete v Azure Portal vybrat spravovanou SKLADOVOU položku. Místo toho je nutné nejprve [upgradovat][container-registry-upgrade] na spravovaný registr.

## <a name="pricing"></a>Ceny

Informace o cenách každé z Azure Container Registry SKU najdete v tématu [Container Registry ceny][container-registry-pricing].

Podrobnosti o cenách přenosů dat najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Další postup

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
