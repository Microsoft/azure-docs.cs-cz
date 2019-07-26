---
title: Atributy zabezpečení pro Azure SQL Database
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444391"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributy zabezpečení pro Azure SQL Database

Tento článek popisuje atributy zabezpečení, které jsou součástí Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database zahrnuje [jednu databázi](sql-database-single-index.yml) i [spravovanou instanci](sql-database-managed-instance.md). Následující položky platí pro obě nabídky kromě případů, kdy je uvedeno jinak.

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém umístění:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování, jako je například strana klienta nebo Always Encrypted</ul>| Ano | Označuje se jako "použití šifrování", jak je popsáno v článku [Always Encrypted](sql-database-always-encrypted.md). Šifrování na straně serveru používá [transparentní šifrování dat](transparent-data-encryption-azure-sql.md).|
| Šifrování při přenosu:<ul><li>Šifrování Azure ExpressRoute</li><li>Šifrování ve virtuální síti</li><li>Šifrování mezi virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Zpracování šifrovacího klíče, jako je například CMK nebo BYOK| Ano | Jsou nabízeny zpracování klíčů spravovaných službou i zákazníkem. Druhá z nich je nabízena prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce poskytované datovými službami Azure| Ano | Prostřednictvím [Always Encrypted](sql-database-always-encrypted.md). |
| Šifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pouze pro [jedinou databázi](sql-database-single-index.yml) . |
| Podpora injektáže v Azure Virtual Network| Ano | Platí jenom pro [spravovanou instanci](sql-database-managed-instance.md) . |
| Izolace sítě a podpora brány firewall| Ano | Brána firewall na úrovni databáze i serveru. Izolace sítě je určena pouze pro [spravovanou instanci](sql-database-managed-instance.md) . |
| Podpora vynuceného tunelování| Ano | [Spravovaná instance](sql-database-managed-instance.md) prostřednictvím sítě VPN [ExpressRoute](../expressroute/index.yml) |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure, například Log Analytics nebo Application Insights| Ano | SecureSphere řešení SIEM z Imperva je také podporováno prostřednictvím integrace služby [Azure Event Hubs](../event-hubs/index.yml) prostřednictvím [auditování SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory (Azure AD) |
| Authorization| Ano | Žádný |

## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení-rovina a Správa – protokolování a audit roviny| Ano | Ano jenom pro některé události |
| Protokolování a audit roviny dat | Ano | Via [SQL audit](sql-database-auditing.md) |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, jako je například Správa verzí konfigurace| Ne  | Žádné |

## <a name="additional-security-attributes-for-sql-database"></a>Další atributy zabezpečení pro SQL Database

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Další informace najdete v tématu [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze](sql-vulnerability-assessment.md). |
| Preventivní: zjišťování a klasifikace dat  | Ano | Viz [Azure SQL Database a SQL Data Warehouse klasifikace & zjišťování dat](sql-database-data-discovery-and-classification.md). |
| Detekce: detekce hrozeb | Ano | Viz [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md). |
