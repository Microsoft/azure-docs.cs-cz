---
title: Atributy zabezpečení pro službu Azure SQL Database
description: Kontrolní seznam zabezpečení atributy za vaše rozhodnutí vyzkoušet Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798691"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributy zabezpečení pro službu Azure SQL Database

Tento článek popisuje běžné atributy zabezpečení, které jsou integrované do Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database obsahuje oba [izolované databáze](sql-database-single-index.yml) a [spravovanou instanci](sql-database-managed-instance.md). Následující položky se vztahují na obě nabídky s výjimkou případů, pokud není uvedeno jinak.

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování, jako je například na straně klienta nebo s funkcí Always Encrypted</ul>| Ano | Volá se, jak je popsáno v článku "šifrování v ovládáním," [s funkcí Always Encrypted](sql-database-always-encrypted.md). Šifrování na straně serveru používá [transparentní šifrování dat](transparent-data-encryption-azure-sql.md).|
| Šifrování během přenosu:<ul><li>Šifrování služby Azure ExpressRoute</li><li>Šifrování ve službě virtual network</li><li>Šifrování mezi virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Zpracování, jako je například CMK nebo BYOK šifrovacího klíče| Ano | Zpracování klíče spravované službou i spravované zákazníkem jsou k dispozici. Je dostupná prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce poskytovaných službou Azure data services| Ano | Prostřednictvím [s funkcí Always Encrypted](sql-database-always-encrypted.md). |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS a SSL. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pro [izolované databáze](sql-database-single-index.yml) pouze. |
| Vkládání podpory služby Azure Virtual Network| Ano | Platí pro [spravovanou instanci](sql-database-managed-instance.md) pouze. |
| Izolace sítě a podpora brány firewall| Ano | Brány firewall na úrovni databáze a úrovni serveru. Izolace sítě je pro [spravovanou instanci](sql-database-managed-instance.md) pouze. |
| Vynucené tunelování podpory| Ano | [Spravovaná instance](sql-database-managed-instance.md) prostřednictvím [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora, jako je například Log Analytics nebo Application Insights pro monitorování Azure| Ano | SecureSphere řešení SIEM od Impervy, také podporu prostřednictvím [Azure Event Hubs](../event-hubs/index.yml) integraci přes [auditování SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory (Azure AD) |
| Authorization| Ano | Žádný |

## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Rovina řízení a rovinou správy protokolování a auditování| Ano | Ano, pro pouze některé události |
| Rovina dat protokolování a auditování | Ano | Via [SQL audit](sql-database-auditing.md) |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, jako je například Správa verzí konfigurace| Ne  | Žádné |

## <a name="additional-security-attributes-for-sql-database"></a>Atributy dodatečné zabezpečení pro službu SQL Database

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Zobrazit [posouzení ohrožení zabezpečení SQL služba pomáhá identifikovat ohrožení zabezpečení databáze](sql-vulnerability-assessment.md). |
| Preventivní: data zjišťování a klasifikace  | Ano | Zobrazit [Azure SQL Database a SQL Data Warehouse zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md). |
| Zjišťování: detekce hrozeb | Ano | Zobrazit [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md). |
