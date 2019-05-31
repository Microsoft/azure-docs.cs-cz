---
title: Atributy zabezpečení pro službu Azure Cosmos DB
description: Kontrolní seznam zabezpečení atributy za vaše rozhodnutí vyzkoušet službu Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 891a8389d7e34c48a00dda8fdebd251a698e5937
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420645"
---
# <a name="security-attributes-azure-cosmos-db"></a>Atributy zabezpečení služby Azure Cosmos DB

Tento článek popisuje běžné atributy zabezpečení integrované do služby Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Všechny databáze Cosmos DB a záloh se šifrují ve výchozím nastavení; Zobrazit [šifrování dat ve službě Azure Cosmos DB](database-encryption-at-rest.md). Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem není podporováno. |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Ano | Všechna data služby Azure Cosmos DB se šifrují při přenosu. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (datových služeb Azure)| Ano | Pouze na úrovni Premium rozhraní API tabulky. Ne všechna rozhraní API tuto funkci podporovat. Zobrazit [Úvod do služby Azure Cosmos DB: Table API](table-introduction.md). |
| Šifrované volání rozhraní API| Ano | Všechna připojení ke službě Azure Cosmos DB podporoval i protokol HTTPS. Azure Cosmos DB podporuje také připojení protokolu TLS 1.2, ale to se ještě nevynucuje. Pokud zákazníci vypnout nižší úroveň TLS na své straně, můžete zajistit pro připojení ke službě Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano |  |
| Vkládání podpory virtuálních sítí| Ano | Pomocí koncového bodu služby virtuální sítě můžete nakonfigurovat účet služby Azure Cosmos DB povolit přístup pouze z konkrétní podsítě virtuální sítě (VNet). Přístup k virtuální síti můžete také kombinovat s pravidly brány firewall.  Zobrazit [přístup služby Azure Cosmos DB z virtuálních sítí](vnet-service-endpoint.md). |
| Izolace sítě a Firewalling podpory| Ano | Podpora brány firewall můžete nakonfigurovat váš účet Azure Cosmos k povolení přístupu jenom ze schválených sadu IP adres, rozsah IP adres a/nebo cloudové služby. Zobrazit [konfigurace IP brány firewall ve službě Azure Cosmos DB](how-to-configure-firewall.md).|
| Podpora pro vynucené tunelování | Ano | Je možné nakonfigurovat na straně klienta ve virtuální síti, kde jsou umístěné virtuální počítače.   |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Všechny požadavky, které se odesílají do služby Azure Cosmos DB jsou protokolovány. [Azure Monitoring](../azure-monitor/overview.md), Azure metrik a protokolování auditu Azure jsou podporovány.  Informace odpovídající na požadavky roviny dat, statistické údaje o dotazu, text dotazu se můžete přihlásit, požádá o MongoDB. Můžete také nastavit výstrahy. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ano na úrovni účtu databáze. na úrovni roviny dat Cosmos DB používá tokeny prostředků a přístupu ke klíčům. |
| Autorizace| Ano | V účtu Azure Cosmos pomocí hlavního klíče (primární i sekundární) a tokeny prostředků nepodporuje. Můžete získat pro čtení a zápis nebo pouze přístup k datům pomocí hlavního klíče pro čtení. Tokeny prostředků umožňují po omezenou dobu přístup k prostředkům, jako jsou dokumenty a kontejnery. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Protokol aktivit pro operace na úrovni účtu jako jsou brány firewall virtuální sítě, v Azure klíčů přístup a IAM. |
| Protokolování a auditování RP roviny dat | Ano | Diagnostika, monitorování, protokolování pro operace na úrovni kontejneru jako například vytvořit kontejner, zřídit propustnost, indexování, zásady a operace CRUD v dokumentech. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ne  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Atributy dodatečné zabezpečení pro službu Cosmos DB

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Různé sdílení prostředků zdroji (CORS) | Ano | Zobrazit [konfigurace prostředků mezi zdroji (CORS) pro sdílení obsahu](how-to-configure-cross-origin-resource-sharing.md). |
