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
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001040"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributy zabezpečení pro službu Azure SQL Database

Tento článek popisuje běžné atributy zabezpečení integrované do Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL Database obsahuje oba [izolované databáze](sql-database-single-index.yml) a [spravovanou instanci](sql-database-managed-instance.md). Níže uvedené položky platí pro obě nabídky, s výjimkou je-li uvedeno jinak.

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Označuje jako "šifrování v ovládáním", jak je popsáno v článku [s funkcí Always Encrypted](sql-database-always-encrypted.md). Šifrování na straně služby používá [transparentní šifrování dat](transparent-data-encryption-azure-sql.md) (TDE).|
| Šifrování během přenosu:<ul><li>Šifrování ExpressRoute</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Nabízíme zpracování klíče spravované službou i spravované zákazníkem (druhá možnost prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Prostřednictvím [s funkcí Always Encrypted](sql-database-always-encrypted.md). |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS a SSL. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pro [izolované databáze](sql-database-single-index.yml) pouze. |
| Vkládání podpory virtuálních sítí| Ano | Platí pro [spravovanou instanci](sql-database-managed-instance.md) pouze. |
| Izolace sítě a podporu funkce brány firewall| Ano | Brány firewall na obě databáze a serveru úroveň; izolace pro sítě [spravovanou instanci](sql-database-managed-instance.md) pouze |
| Vynucené tunelování podpory| Ano | [spravovaná instance](sql-database-managed-instance.md) prostřednictvím [Azure ExpressRoute](../expressroute/index.yml) sítě VPN |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Řešení SIEM třetích stran od Impervy (SecureSphere) je také podporována, prostřednictvím [Azure Event Hubs](../event-hubs/index.yml) integraci přes [auditování SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Azure Active Directory. |
| Autorizace| Ano |  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Ano, pro pouze některé události. |
| Protokolování roviny dat a auditu | Ano | Prostřednictvím [auditování SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ne  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Atributy dodatečné zabezpečení pro službu SQL Database

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Zobrazit [posouzení ohrožení zabezpečení SQL služba pomáhá identifikovat ohrožení zabezpečení databáze](sql-vulnerability-assessment.md). |
| Preventivní: data zjišťování a klasifikace  | Ano | Zobrazit [Azure SQL Database a SQL Data Warehouse zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md). |
| Zjišťování: detekce hrozeb | Ano | Zobrazit [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md). |
