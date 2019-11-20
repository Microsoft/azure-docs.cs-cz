---
title: Ukázky šablon Azure Resource Manager – služba front-dveří Azure
description: Ukázkové šablony Azure Resource Manageru pro službu Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 2684c9852cb3e946d8ec1840fc166053204dfae0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184594"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Šablony modelu nasazení Azure Resource Manager pro službu Front Door

Následující tabulka obsahuje odkazy na šablony modelu nasazení Azure Resource Manager pro službu Azure Front Door Service. 

| | |
| ---| ---|
| [Vytvoření základní služby Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Vytvoří základní konfiguraci služby Front Door s jedním back-endem. |
| [Vytvoření služby Front Door s více back-endy a back-endovými fondy a směrováním na základě adresy URL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Vytvoří službu Front Door s nakonfigurovaným vyrovnáváním zatížení pro více back-endů v back-endovém fondu i v různých back-endových fondech na základě cesty URL. |
| [Připojení vlastní domény s HTTPS (certifikát spravovaný službou Front Door) ke službě Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Přidá do vaší služby Front Door vlastní doménu a povolí pro ni provoz HTTPS s využitím certifikátu spravovaného službou Front Door a vygenerovaným přes DigiCert. |
| [Vytvoření front-dveří s geografickým filtrováním](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Vytvořte si přední dveře, které umožní nebo zablokuje provoz z určitých zemí nebo oblastí. |
| [Řízení sond stavu pro back-endy ve službě Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Aktualizuje službu Front Door tak, aby změnila nastavení sond stavu tím, že aktualizuje cesty a intervaly odesílání sond. |
| [Vytvoření služby Front Door s konfigurací back-endu aktivní/pohotovostní](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Vytvoří službu Front Door předvádějící směrování na základě priority pro topologii aplikace aktivní/pohotovostní. To znamená, že se ve výchozím nastavení bude veškerý provoz směrovat do primárního back-endu (s nejvyšší prioritou), dokud bude dostupný. |
| [Vytvoření služby Front Door s povoleným ukládáním do mezipaměti pro určité trasy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Vytvoří službu Front Door s povoleným ukládáním do mezipaměti pro definovanou konfiguraci směrování, tedy ukládání všech statických prostředků vašich úloh do mezipaměti. |
| [Konfigurace spřažení relací pro názvy hostitelů služby Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Aktualizuje službu Front Door tak, aby pro hostitele front-endu povolila spřažení relací, aby se následný provoz stejné relace uživatele odesílal do stejného back-endu. |
| [Konfigurace přidávání IP adres klientů na seznam povolených nebo zakázaných ve službě Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Nakonfiguruje službu Front Door tak, aby s využitím vlastního řízení přístupu pomocí IP adres klientů omezovala provoz určitých IP adres klientů. |
| [Konfigurace front-dveří pro provedení akce s konkrétními parametry http](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Nakonfiguruje službu Front Door tak, aby pomocí vlastních pravidel řízení přístupu s využitím parametrů HTTP povolovala nebo blokovala určitý provoz na základě parametrů HTTP v příchozím požadavku. |
| [Konfigurace omezení rychlosti Frontních dveří](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Nakonfiguruje službu Front Door tak, aby pro daného hostitele front-endu omezovala rychlost příchozího provozu. |
| | |

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).