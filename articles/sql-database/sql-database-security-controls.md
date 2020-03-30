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
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190694"
---
# <a name="security-controls-for-azure-sql-database"></a>Ovládací prvky zabezpečení pro Azure SQL Database

Tento článek dokumentuje ovládací prvky zabezpečení, které jsou integrované do Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database obsahuje [jednu databázi](sql-database-single-index.yml) i [spravovanou instanci](sql-database-managed-instance.md). Následující položky se vztahují na obě nabídky, pokud není uvedeno jinak.

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pouze pro [jednu databázi.](sql-database-single-index.yml) |
| Podpora vkládání virtuální sítě Azure| Ano | Platí pouze pro [spravovanou instanci.](sql-database-managed-instance.md) |
| Podpora izolace sítě a brány firewall| Ano | Brána firewall na úrovni databáze i serveru. Izolace sítě je pouze pro [spravovanou instanci.](sql-database-managed-instance.md) |
| Podpora vynuceného tunelování| Ano | [Spravovaná instance](sql-database-managed-instance.md) prostřednictvím [expressroute](../expressroute/index.yml) VPN. |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure, jako je analýza protokolů nebo přehledy aplikací| Ano | SecureSphere, řešení SIEM od společnosti Imperva, je také podporováno prostřednictvím integrace [služby Azure Event Hubs](../event-hubs/index.yml) prostřednictvím [auditování SQL](sql-database-auditing.md). |
| Řízení-rovina a řízení-rovina protokolování a audit| Ano | Ano pouze pro některé akce |
| Protokolování a audit datové roviny | Ano | Prostřednictvím [auditu SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory (Azure AD) |
| Autorizace| Ano | Žádný |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Nazývá se "šifrování v provozu", jak je popsáno v článku [Vždy šifrované](sql-database-always-encrypted.md). Šifrování na straně serveru používá [transparentní šifrování dat](transparent-data-encryption-azure-sql.md).|
| Šifrování při přenosu:<ul><li>Šifrování Azure ExpressRoute</li><li>Šifrování ve virtuální síti</li><li>Šifrování mezi virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Zpracování šifrovacího klíče, například CMK nebo BYOK| Ano | Nabízí se zpracování klíčů spravované službou i zákazníkem. Ten je nabízen prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupců poskytované datovými službami Azure| Ano | Prostřednictvím [vždy šifrované](sql-database-always-encrypted.md). |
| Šifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS/TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, například správa verzí konfigurace| Ne  | Žádný |

## <a name="additional-security-controls-for-sql-database"></a>Další ovládací prvky zabezpečení pro databázi SQL

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení zranitelnosti | Ano | Viz [Služba SQL Vulnerability Assessment, která vám pomůže identifikovat chyby zabezpečení databáze](sql-vulnerability-assessment.md). |
| Preventivní: zjišťování a klasifikace dat  | Ano | Viz [Azure SQL Database a zjišťování dat datového skladu SQL & klasifikace](sql-database-data-discovery-and-classification.md). |
| Detekce: detekce hrozeb | Ano | Viz [Rozšířená ochrana před hrozbami pro Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
