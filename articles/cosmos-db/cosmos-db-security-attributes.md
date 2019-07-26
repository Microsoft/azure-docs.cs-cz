---
title: Atributy zabezpečení pro Azure Cosmos DB
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442559"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Atributy zabezpečení pro Azure Cosmos DB

Tento článek popisuje atributy zabezpečení integrované do Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Všechny databáze Cosmos DB a zálohy jsou standardně šifrované. viz [šifrování dat v Azure Cosmos DB](database-encryption-at-rest.md). Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se nepodporuje. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Všechna Azure Cosmos DBová data jsou při přenosu šifrovaná. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Pouze v rozhraních API tabulky Premium. Tato funkce nepodporuje všechna rozhraní API. Viz [Úvod do Azure Cosmos DB: Rozhraní API pro tabulky](table-introduction.md). |
| Zašifrovaná volání rozhraní API| Ano | Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také připojení TLS 1,2, ale toto není ještě vynutilo. Pokud zákazníci na svém konci vypnou nižší úroveň TLS, můžou se připojit k Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano | Pomocí koncového bodu služby virtuální sítě můžete nakonfigurovat účet Azure Cosmos DB, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Můžete také kombinovat přístup k virtuální síti pomocí pravidel brány firewall.  Viz [Azure Cosmos DB přístupu z virtuálních sítí](VNet-service-endpoint.md). |
| Izolace sítě a podpora brány firewall| Ano | Díky podpoře brány firewall můžete účet Azure Cosmos nakonfigurovat tak, aby povoloval přístup jenom ze schválené sady IP adres, rozsahu IP adres a/nebo cloudových služeb. Viz [Konfigurace brány firewall protokolu IP v Azure Cosmos DB](how-to-configure-firewall.md).|
| Podpora vynuceného tunelování| Ano | Dá se nakonfigurovat na straně klienta na virtuální síti, kde jsou umístěné virtuální počítače.   |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Všechny požadavky, které se odesílají do Azure Cosmos DB, se zaznamenávají do protokolu. [Azure Monitoring](../azure-monitor/overview.md), metriky Azure, protokolování Azure audit se podporují.  Můžete protokolovat informace, které odpovídají požadavkům na rovinu dat, statistiky dotazů za běhu, text dotazu a MongoDB požadavky. Můžete také nastavit výstrahy. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ano na úrovni databázového účtu; na úrovni roviny dat Cosmos DB používá tokeny prostředků a přístup k klíčům. |
| Authorization| Ano | Podporováno v účtu Azure Cosmos s hlavními klíči (primárními a sekundárními) a tokeny prostředků. Přístup k datům můžete získat jen pro čtení a zápis nebo pro čtení pomocí hlavních klíčů. Tokeny prostředků umožňují omezený časový přístup k prostředkům, jako jsou dokumenty a kontejnery. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Protokol aktivit Azure pro operace na úrovni účtu, jako jsou brány firewall, virtuální sítě, přístup k klíčům a IAM. |
| Protokolování a audit roviny dat | Ano | Protokolování monitorování diagnostiky pro operace na úrovni kontejneru, jako je vytvoření kontejneru, zajištění propustnosti, zásady indexování a operace CRUD v dokumentech. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ne  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Další atributy zabezpečení pro Cosmos DB

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Sdílení prostředků mezi zdroji (CORS) | Ano | Viz [Konfigurace sdílení prostředků mezi zdroji (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
