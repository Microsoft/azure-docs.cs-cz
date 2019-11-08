---
title: Ovládací prvky zabezpečení
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 58070cab1221b9d9585784d82cf1a48afcedb8af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802827"
---
# <a name="security-controls-for-azure-sql-database"></a>Ovládací prvky zabezpečení pro Azure SQL Database

Tento článek popisuje ovládací prvky zabezpečení, které jsou součástí Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database zahrnuje [jednu databázi](sql-database-single-index.yml) i [spravovanou instanci](sql-database-managed-instance.md). Následující položky platí pro obě nabídky kromě případů, kdy je uvedeno jinak.

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pouze pro [jedinou databázi](sql-database-single-index.yml) . |
| Podpora injektáže v Azure Virtual Network| Ano | Platí jenom pro [spravovanou instanci](sql-database-managed-instance.md) . |
| Izolace sítě a podpora brány firewall| Ano | Brána firewall na úrovni databáze i serveru. Izolace sítě je určena pouze pro [spravovanou instanci](sql-database-managed-instance.md) . |
| Podpora vynuceného tunelování| Ano | [Spravovaná instance](sql-database-managed-instance.md) prostřednictvím sítě VPN [ExpressRoute](../expressroute/index.yml) |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure, například Log Analytics nebo Application Insights| Ano | SecureSphere řešení SIEM z Imperva je také podporováno prostřednictvím integrace služby [Azure Event Hubs](../event-hubs/index.yml) prostřednictvím [auditování SQL](sql-database-auditing.md). |
| Řízení-rovina a Správa – protokolování a audit roviny| Ano | Ano jenom pro některé události |
| Protokolování a audit roviny dat | Ano | Via [SQL audit](sql-database-auditing.md) |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory (Azure AD) |
| Autorizace| Ano | Žádný |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Označuje se jako "použití šifrování", jak je popsáno v článku [Always Encrypted](sql-database-always-encrypted.md). Šifrování na straně serveru používá [transparentní šifrování dat](transparent-data-encryption-azure-sql.md).|
| Šifrování při přenosu:<ul><li>Šifrování Azure ExpressRoute</li><li>Šifrování ve virtuální síti</li><li>Šifrování mezi virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Zpracování šifrovacího klíče, jako je například CMK nebo BYOK| Ano | Jsou nabízeny zpracování klíčů spravovaných službou i zákazníkem. Druhá z nich je nabízena prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce poskytované datovými službami Azure| Ano | Prostřednictvím [Always Encrypted](sql-database-always-encrypted.md). |
| Šifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS/SSL. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, jako je například Správa verzí konfigurace| Ne  | Žádný |

## <a name="additional-security-controls-for-sql-database"></a>Další ovládací prvky zabezpečení pro SQL Database

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Další informace najdete v tématu [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze](sql-vulnerability-assessment.md). |
| Preventivní: zjišťování a klasifikace dat  | Ano | Viz [Azure SQL Database a SQL Data Warehouse klasifikace & zjišťování dat](sql-database-data-discovery-and-classification.md). |
| Detekce: detekce hrozeb | Ano | Viz [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
