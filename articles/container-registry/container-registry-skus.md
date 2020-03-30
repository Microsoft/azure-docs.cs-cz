---
title: Úrovně služeb a sloky
description: Další informace o funkcích a omezeních v základních, standardních a prémiových úrovních služeb (SKU) v Registru kontejnerů Azure.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456267"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKUs

Azure Container Registry (ACR) je k dispozici ve více úrovních služeb, označované jako SKU. Tyto sku poskytují předvídatelné ceny a několik možností pro zarovnání kapacity a využití vašeho soukromého registru Dockerv Azure.

| Skladová jednotka (SKU) | Popis |
| --- | ----------- |
| **Basic** | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové funkce jako Standard a Premium (například [integrace ověřování](container-registry-authentication.md#individual-login-with-azure-ad)Azure Active Directory , [odstranění bitových obrázků][container-registry-delete]a [webhooky).][container-registry-webhook] Zahrnuté úložiště a propustnost bitové kopie jsou však nejvhodnější pro scénáře nižší využití. |
| **Standard** | Standardní registry nabízejí stejné funkce jako Basic, se zvýšenou zahrnutou propustností úložiště a image. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Prémiové registry poskytují nejvyšší množství zahrnutého úložiště a souběžných operací, což umožňuje scénáře s velkým objemem. Kromě vyšší propustnosti bitových obrázků přidává premium funkce, jako je [geografická replikace][container-registry-geo-replication] pro správu jednoho registru ve více oblastech, [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek bitových označení, brány firewall a virtuální [sítě (náhled)](container-registry-vnet.md) pro omezení přístupu k registru. |

Základní, standardní a premium sloky poskytují stejné programové funkce. Všechny také využívají [úložiště bitových obrázků][container-registry-storage] spravovaného výhradně azurem. Výběr skladové položky vyšší úrovně poskytuje vyšší výkon a škálování. S více úrovněmi služeb můžete začít s Basic, pak převést na standardní a premium jako vaše využití registru zvyšuje.

## <a name="sku-features-and-limits"></a>Funkce a limity skladové položky

V následující tabulce jsou podrobně uvedeny funkce a omezení úrovní služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna SKUs

Skladovou položku registru můžete změnit pomocí příkazového příkazu k příkazu Azure nebo na webu Azure Portal. Mezi skladovými položkami se můžete volně pohybovat, dokud má skladová položka, na kterou přepínáte, požadovanou maximální kapacitu úložiště. 

### <a name="azure-cli"></a>Azure CLI

Chcete-li přejít mezi SKU v příkazovém příkazu k onomu Azure CLI, použijte příkaz [az acr update.][az-acr-update] Chcete-li například přepnout na premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V registru kontejneru **Přehled** na portálu Azure vyberte **Aktualizovat**a pak vrozené nové **skladové položky** z rozbalovací položky skladové položky.

![Aktualizovat skladovou položku registru kontejneru na webu Azure Portal][update-registry-sku]

## <a name="pricing"></a>Ceny

Informace o cenách ke každému sumu registru kontejnerů Azure najdete v [tématu Ceny registru kontejnerů][container-registry-pricing].

Podrobnosti o cenách za přenos dat naleznete v [tématu Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Další kroky

**Plán registru kontejnerů Azure**

Informace o nadcházejících funkcích služby najdete v [plánu ACR][acr-roadmap] na GitHubu.

**Azure Container Registry UserVoice**

Odeslat a hlasovat o nových funkcích v [ACR UserVoice][container-registry-uservoice].

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
