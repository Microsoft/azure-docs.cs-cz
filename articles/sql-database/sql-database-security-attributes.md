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
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204243"
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
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Nabízíme zpracování klíče spravované službou i spravované zákazníkem (druhá možnost prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce (datových služeb Azure)| Ano | Prostřednictvím [s funkcí Always Encrypted](sql-database-always-encrypted.md). |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS a SSL. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano | Platí pro [izolované databáze](sql-database-single-index.yml) pouze. |
| Vkládání podpory virtuálních sítí| Ano | Platí pro [spravovanou instanci](sql-database-managed-instance.md) pouze. |
| Izolace sítě / funkce Podpora brány firewall| Ano | Brány firewall na obě databáze a serveru úroveň; izolace pro sítě [spravovanou instanci](sql-database-managed-instance.md) pouze |
| Podpora pro vynucené tunelování | Ano | [spravovaná instance](sql-database-managed-instance.md) prostřednictvím [Azure ExpressRoute](../expressroute/index.yml) sítě VPN |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Řešení SIEM třetích stran od Impervy (SecureSphere) je také podporována, prostřednictvím [Azure Event Hubs](../event-hubs/index.yml) integraci přes [auditování SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Azure Active Directory. |
| Řízení přístupu – ověření| Ano |  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Ano, pro pouze některé události. |
| Protokolování a auditování RP roviny dat | Ano | Prostřednictvím [auditování SQL](sql-database-auditing.md). |

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
