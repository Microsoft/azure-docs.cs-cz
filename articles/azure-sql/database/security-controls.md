---
title: Ovládací prvky zabezpečení
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442084"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Ovládací prvky zabezpečení pro Azure SQL Database a SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tento článek popisuje ovládací prvky zabezpečení, které jsou integrované do Azure SQL Database a spravované instance Azure SQL.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Yes | Platí jenom pro [SQL Database](../index.yml) . |
| Podpora injektáže v Azure Virtual Network| Yes | Týká se pouze [spravované instance SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Izolace sítě a podpora brány firewall| Yes | Brána firewall na úrovni databáze i serveru. Izolace sítě je určena pouze pro [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Podpora vynuceného tunelování| Yes | [Spravovaná instance SQL](../managed-instance/sql-managed-instance-paas-overview.md) prostřednictvím sítě VPN [ExpressRoute](../expressroute/../index.yml) |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure, například Log Analytics nebo Application Insights| Yes | SecureSphere řešení SIEM z Imperva je také podporováno prostřednictvím integrace služby [Azure Event Hubs](../event-hubs/../index.yml) prostřednictvím [auditování SQL](../../azure-sql/database/auditing-overview.md). |
| Řízení-rovina a Správa – protokolování a audit roviny| Yes | Ano jenom pro některé události |
| Protokolování a audit roviny dat | Yes | Prostřednictvím [auditu SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Yes | Azure Active Directory (Azure AD) |
| Autorizace| Yes | Žádné |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Označuje se jako "použití šifrování", jak je popsáno v článku [Always Encrypted](always-encrypted-certificate-store-configure.md). Šifrování na straně serveru používá [transparentní šifrování dat](transparent-data-encryption-tde-overview.md).|
| Šifrování při přenosu:<ul><li>Šifrování Azure ExpressRoute</li><li>Šifrování ve virtuální síti</li><li>Šifrování mezi virtuálními sítěmi</ul>| Yes | Pomocí protokolu HTTPS. |
| Zpracování šifrovacího klíče, jako je například CMK nebo BYOK| Yes | Jsou nabízeny zpracování klíčů spravovaných službou i zákazníkem. Druhá z nich je nabízena prostřednictvím [Azure Key Vault](../key-vault/../index.yml). |
| Šifrování na úrovni sloupce poskytované datovými službami Azure| Yes | Prostřednictvím [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Šifrovaná volání rozhraní API| Yes | Pomocí protokolu HTTPS/TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, jako je například Správa verzí konfigurace| No  | Žádné |

## <a name="additional-security-controls-for-sql-database"></a>Další ovládací prvky zabezpečení pro SQL Database

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Yes | Další informace najdete v tématu [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze](sql-vulnerability-assessment.md). |
| Preventivní: zjišťování a klasifikace dat  | Yes | Přečtěte si téma [Azure SQL Database a Azure synapse Analytics data discovery & klasifikací](data-discovery-and-classification-overview.md). |
| Detekce: detekce hrozeb | Yes | Viz [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](threat-detection-overview.md). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
