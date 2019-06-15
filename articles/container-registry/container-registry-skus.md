---
title: Skladové položky registru kontejnerů Azure
description: Porovnání různých úrovních služby dostupné ve službě Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: f36b206ff015511dea7369617febe9220282bbe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069043"
---
# <a name="azure-container-registry-skus"></a>Skladové položky registru kontejnerů Azure

Azure Container Registry (ACR) je k dispozici v několika úrovních služeb, označované jako skladové položky. Tyto skladové položky poskytují předvídatelné ceny a několik možností, zarovnání struktury kapacitu a využití privátního registru Dockeru v Azure.

| Skladová jednotka (SKU) | Spravovaní | Popis |
| --- | :-------: | ----------- |
| **Basic** | Ano | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové funkce jako Standard a Premium (jako je Azure Active Directory [integrace ověřování](container-registry-authentication.md#individual-login-with-azure-ad), [obrázku odstranění][container-registry-delete], a [webhooky][container-registry-webhook]). Zahrnuté úložiště a propustnost bitové kopie jsou však nejvhodnější pro nižší scénáře použití. |
| **Standard** | Ano | Standardními registry nabízí stejné funkce jako základní s zahrnuty vyšší propustnost úložiště a image. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Ano | Registry úrovně Premium poskytují nejvyšší velikost zahrnutého úložiště a souběžné operace povolení scénářů s vysokými objemy. Kromě vyšší propustnost image Premium přidává funkce včetně [geografickou replikaci] [ container-registry-geo-replication] pro správu jednoho registru napříč několika oblastmi a [obsahu důvěryhodnosti](container-registry-content-trust.md) pro podepisování značka obrázku, a [brány firewall a virtuální sítě (preview)](container-registry-vnet.md) k omezení přístupu k registru. |
|  Klasické (*není k dispozici po dubna 2019*) | Ne | Tato skladová položka povolena počáteční verzi služby Azure Container Registry v Azure. Registry Classic se zálohují na účet úložiště Azure se vytvoří v rámci vašeho předplatného, což omezí možnosti pro službu ACR a poskytují vyšší úrovně možnosti, jako je například vyšší propustnost a geografická replikace. |

> [!IMPORTANT]
> Registru Classic se skladová položka **zastaralé**a nebude k dispozici po **. dubna 2019**. Doporučujeme používat pro všechny nové registry úrovně Basic, Standard nebo Premium. Všechny existující registry Classic by měl upgradovat před dubnem 2019. Informace o upgradu naleznete v tématu [Upgrade registru Classic][container-registry-upgrade].

Basic, Standard a SKU úrovně Premium (dále jen souhrnně nazývané *spravované Registry*) poskytuje všechny stejné programové funkce. Jsou také všechny výhody [úložiště obrázků] [ container-registry-storage] kompletně spravované službou Azure. Volba SKU vyšší úrovně poskytuje větší výkon a škálování. S více úrovní služeb můžete začít s Basic pak převod na Standard a Premium jako zvýšení využití vašeho registru.

## <a name="sku-feature-matrix"></a>Přehled funkcí SKU

Následující tabulka obsahuje podrobnosti o funkcích a omezení úrovní služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna skladové položky

Můžete změnit SKU registru pomocí Azure CLI nebo na webu Azure Portal. Vám můžou volně přesouvat mezi spravovanými skladovými položkami za předpokladu, SKU přecházíte k má požadovaná maximální úložnou kapacitu. Když přepnete z modelu nasazení Classic do jednoho z spravovanými skladovými položkami, nelze přesunout zpátky na Classic – je jednosměrná převod.

### <a name="azure-cli"></a>Azure CLI

Chcete-li přechodu mezi skladovými položkami v Azure CLI, použijte [az acr update] [ az-acr-update] příkazu. Chcete-li například přepnout na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V registru kontejneru **přehled** na webu Azure Portal, vyberte **aktualizace**, vyberte novou **SKU** z rozevírací nabídky SKU.

![Aktualizace skladové položky registru kontejneru na webu Azure portal][update-registry-sku]

Pokud máte registru Classic, nelze vybrat spravované skladové položky na webu Azure portal. Místo toho je nutné nejprve [upgradovat] [ container-registry-upgrade] na spravovaný registr.

## <a name="pricing"></a>Ceny

Informace o cenách v každém skladové jednotky Azure Container Registry, najdete v článku [Container Registry ceny][container-registry-pricing].

Podrobnosti o cenách pro přenosy dat najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Další postup

**Azure Container Registry Roadmap**

Přejděte [plán služby ACR] [ acr-roadmap] na Githubu najdete informace o chystaných funkcí ve službě.

**Azure Container Registry UserVoice**

Odeslat a hlasovat o doporučeních nové funkce v [ACR UserVoice][container-registry-uservoice].

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
