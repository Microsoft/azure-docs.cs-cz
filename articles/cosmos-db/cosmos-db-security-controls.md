---
title: Ovládací prvky zabezpečení pro Azure Cosmos DB
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886732"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Ovládací prvky zabezpečení pro Azure Cosmos DB

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano | Pomocí koncového bodu služby virtuální sítě můžete nakonfigurovat účet Azure Cosmos DB, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Můžete také kombinovat přístup k virtuální síti pomocí pravidel brány firewall.  Viz [Azure Cosmos DB přístupu z virtuálních sítí](VNet-service-endpoint.md). |
| Izolace sítě a podpora brány firewall| Ano | Díky podpoře brány firewall můžete účet Azure Cosmos nakonfigurovat tak, aby povoloval přístup jenom ze schválené sady IP adres, rozsahu IP adres a/nebo cloudových služeb. Viz [Konfigurace brány firewall protokolu IP v Azure Cosmos DB](how-to-configure-firewall.md).|
| Podpora vynuceného tunelování| Ano | Dá se nakonfigurovat na straně klienta na virtuální síti, kde jsou umístěné virtuální počítače.   |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Všechny požadavky, které se odesílají do Azure Cosmos DB, se zaznamenávají do protokolu. [Azure Monitoring](../azure-monitor/overview.md), metriky Azure, protokolování Azure audit se podporují.  Můžete protokolovat informace, které odpovídají požadavkům na rovinu dat, statistiky dotazů za běhu, text dotazu a MongoDB požadavky. Můžete také nastavit výstrahy. |
| Protokolování a audit roviny řízení a správy| Ano | Protokol aktivit Azure pro operace na úrovni účtu, jako jsou brány firewall, virtuální sítě, přístup k klíčům a IAM. |
| Protokolování a audit roviny dat | Ano | Protokolování monitorování diagnostiky pro operace na úrovni kontejneru, jako je vytvoření kontejneru, zajištění propustnosti, zásady indexování a operace CRUD v dokumentech. |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ano na úrovni databázového účtu; na úrovni roviny dat Cosmos DB používá tokeny prostředků a přístup k klíčům. |
| Authorization| Ano | Podporováno v účtu Azure Cosmos s hlavními klíči (primárními a sekundárními) a tokeny prostředků. Přístup k datům můžete získat jen pro čtení a zápis nebo pro čtení pomocí hlavních klíčů. Tokeny prostředků umožňují omezený časový přístup k prostředkům, jako jsou dokumenty a kontejnery. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft | Ano | Ve výchozím nastavení jsou všechny databáze a zálohy Cosmos šifrované. viz [šifrování dat v Azure Cosmos DB](database-encryption-at-rest.md). Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se nepodporuje. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Pouze v rozhraních API tabulky Premium. Tato funkce nepodporuje všechna rozhraní API. Viz [Úvod do Azure Cosmos DB: Rozhraní API pro tabulky](table-introduction.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Všechna Azure Cosmos DBová data jsou při přenosu šifrovaná. |
| Zašifrovaná volání rozhraní API| Ano | Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také připojení TLS 1,2, ale toto není ještě vynutilo. Pokud zákazníci na svém konci vypnou nižší úroveň TLS, můžou se připojit k Cosmos DB.  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ne  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Další ovládací prvky zabezpečení pro Cosmos DB

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Sdílení prostředků mezi zdroji (CORS) | Ano | Viz [Konfigurace sdílení prostředků mezi zdroji (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).