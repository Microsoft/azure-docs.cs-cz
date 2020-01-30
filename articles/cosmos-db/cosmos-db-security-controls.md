---
title: Ovládací prvky zabezpečení pro Azure Cosmos DB
description: Získání kontrolního seznamu pro kontrolu zabezpečení, jako je například síť, monitorování, identita a ochrana dat pro vyhodnocení Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: d06afb3670b1c3af3f1acf0bd690c1e3e266a11c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771795"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Ovládací prvky zabezpečení pro Azure Cosmos DB

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano | Pomocí koncového bodu služby virtuální sítě můžete nakonfigurovat účet Azure Cosmos DB, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Můžete také kombinovat přístup k virtuální síti pomocí pravidel brány firewall. Další informace najdete v tématu věnovaném [přístupu Azure Cosmos DB z virtuálních sítí](VNet-service-endpoint.md). |
| Izolace sítě a podpora brány firewall| Ano | Díky podpoře brány firewall můžete účet Azure Cosmos nakonfigurovat tak, aby povoloval přístup jenom ze schválené sady IP adres, rozsahu IP adres a/nebo cloudových služeb. Další informace najdete v tématu [Konfigurace brány firewall protokolu IP v Azure Cosmos DB](how-to-configure-firewall.md).|
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
| Ověření| Ano | Ano na úrovni databázového účtu; na úrovni roviny dat Cosmos DB používá tokeny prostředků a přístup k klíčům. |
| Autorizace| Ano | Podporováno v účtu Azure Cosmos s hlavními klíči (primárními a sekundárními) a tokeny prostředků. Přístup k datům můžete získat jen pro čtení a zápis nebo pro čtení pomocí hlavních klíčů. Tokeny prostředků umožňují omezený časový přístup k prostředkům, jako jsou dokumenty a kontejnery. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Ve výchozím nastavení jsou všechny databáze a zálohy Azure Cosmos šifrované. viz [šifrování dat v Azure Cosmos DB](database-encryption-at-rest.md). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Viz [konfigurace klíčů spravovaných zákazníkem pro váš účet Azure Cosmos DB](how-to-setup-cmk.md) .  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Pouze v rozhraních API tabulky Premium. Tato funkce nepodporuje všechna rozhraní API. Viz [Úvod do Azure Cosmos DB: rozhraní API pro tabulky](table-introduction.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Všechna Azure Cosmos DBová data jsou při přenosu šifrovaná. |
| Zašifrovaná volání rozhraní API| Ano | Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také protokol TLS 1,2.<br>Je možné vyhovět minimální verzi TLS na straně serveru. Pokud to chcete udělat, kontaktujte prosím [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com). |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ne  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Další ovládací prvky zabezpečení pro Cosmos DB

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Sdílení prostředků mezi zdroji (CORS) | Ano | Viz [Konfigurace sdílení prostředků mezi zdroji (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).