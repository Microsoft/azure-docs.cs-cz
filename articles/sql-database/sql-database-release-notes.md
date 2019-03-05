---
title: Zpráva k vydání verze Azure SQL Database | Dokumentace Microsoftu
description: Další informace o nové funkce a vylepšení ve službě Azure SQL Database a v dokumentaci k Azure SQL Database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: carlrab
ms.openlocfilehash: af0fd591393f9f187a75f79fea980d41085df5cb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342441"
---
# <a name="sql-database-release-notes"></a>Zpráva k vydání verze SQL Database

Tento článek obsahuje seznam nových funkcí a vylepšení ve službě SQL Database a v dokumentaci k SQL Database. Vylepšení k jiným službám Azure, najdete v části [aktualizací služby](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>2019. března

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Přidání protokolu limity pro izolované databáze|Další informace najdete v tématu [jedno omezení prostředků vCore databáze](sql-database-vcore-resource-limits-single-databases.md)|
| Přidání protokolu limity pro elastické fondy a databáze ve fondu|Další informace najdete v tématu [vCore prostředků omezení Elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md)|
| Přidání transakce protokolu míra zásad správného řízení| Přidat nový obsah pro [transakční protokol míra zásad správného řízení](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)|
| Aktualizace ukázky Powershellu pro izolované databáze a elastických fondů použít az.sql modul | Další informace najdete v tématu [ukázky Powershellu pro izolované databáze a elastické fondy](sql-database-powershell-samples.md#single-database-and-elastic-pools).

## <a name="february-2019"></a>. Února 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
|Vytváření obnovitelného indexu online je teď obecně dostupná| Další informace najdete v tématu [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)|
|Spravovaná instance podporu pro směrovací tabulky vylepšené| Další informace najdete v tématu [požadavky sítě](sql-database-managed-instance-connectivity-architecture.md#network-requirements)|

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
|Přidání managed instance vyjasnění možnost nasazení|Aktualizovat řada článků o vysvětlení použitelnost pro izolované databáze, elastický fond a možnosti nasazení spravované instance |
|Aktualizace databáze tempdb velikosti nákupní model založený na DTU | Další informace najdete v tématu [databáze Tempdb ve službě SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)|
|Aktualizované import a export souboru bacpac pro podporu spravované instance| Další informace najdete v tématu [importovat ze souboru BACPAC](sql-database-import.md) a [exportovat do souboru BACPAC](sql-database-export.md) |

## <a name="january-2019"></a>2019. ledna

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| Členitost další možnosti pro výpočetní prostředky | Úrovně u služeb pro obecné účely a pro důležité obchodní informace [izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) nyní obsahují další možnosti podrobné výpočetní prostředky.|
| Zobrazení záznamů auditu pro spravovanou instanci na webu Azure Portal | Zobrazení [záznamy pro spravované instance auditu](sql-database-managed-instance-auditing.md) ve službě Azure portal teď podporuje.|
| Funkce detekce hrozeb zálohy přejmenován na Pokročilé zabezpečení dat | Funkce detekce hrozeb zálohy přejmenován na [rozšířené zabezpečení dat](sql-advanced-threat-protection.md) pro izolované databáze, elastické fondy a spravované instance |
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Spravované instance a transakční replikace | Přidání článku o používání [transakční replikace s spravované instance](replication-with-sql-database-managed-instance.md) |
| Přidání služby Azure AD s kurzem spravované instance | To [Azure AD s spravovanou instanci](sql-database-managed-instance-aad-security-tutorial.md) kurzu se dozvíte budete muset nakonfigurovat a otestovat managed instance zabezpečení pomocí přihlášení Azure AD. |
| Aktualizovaný obsah pro automatizaci úloh pomocí skriptů příkazů jazyka Transact-SQL | Aktualizovat a vyjasnění obsah pro používání [úlohy automatizace s použitím příkazů jazyka Transact-SQL skriptů](sql-database-job-automation-overview.md) pro izolované databáze, elastické fondy a spravované instance |
| Zabezpečení obsahu pro aktualizaci spravované instance | Aktualizovat a vyjasnění obsah [model zabezpečení pro spravované instance](sql-database-security-overview.md)a kontrastní pomocí model zabezpečení pro izolované databáze a elastické fondy |
| Aktualizovat všechny rychlých startů a kurzů | Všechny rychlých startů a kurzů v [dokumentaci](https://docs.microsoft.com/azure/sql-database) byly aktualizovány a jsme aktualizovali tak, že se shodují se změnami na webu Azure Portal |
| Přehled příručky přidání rychlý start | Přidání úvodní Přehled příručky pro [izolované databáze](sql-database-quickstart-guide.md) a [spravované instance](sql-database-managed-instance-quickstart-guide.md) |
| Přidání databáze SQL Glosář termínů | To [termíny glosáře](sql-database-glossary-terms.md) článek obsahuje konečný seznam podmínek SQL Database a odkazy na primární konceptuální stránky, která vysvětluje výraz v kontextu. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Přispívat k obsahu vylepšení

Sada dokumentace k Azure SQL je open source. Práce v otevřeném prostředí poskytuje několik výhod:

- Plánování úložiště opensourcových umožňuje získávat zpětnou vazbu o tom, které dokumenty jsou nejvíce žádané.
- Úložiště opensourcových zkontrolujte v publikovat nejužitečnější obsah naší nové verzi.
- Otevřít zdroj úložiště aktualizovat v programu open, aby bylo snazší průběžné vylepšování obsahu.

Abyste mohli přispívat k obsahu dokumentace ke službě Azure SQL Database, najdete v článku [Přehled Průvodce pro přispěvatele Microsoftu Docs](https://docs.microsoft.com/en-us/contribute/). Možnosti uživatele na [docs.microsoft.com](https://docs.microsoft.com/) integruje [Githubu](https://github.com/) pracovní postupy přímo na to bylo ještě jednodušší. Začněte tím, že [úpravami dokumentu, který si prohlížíte](https://docs.microsoft.com/en-us/contribute/#quick-edits-to-existing-documents). Případně můžete pomoct s [revizemi nových témat](https://docs.microsoft.com/en-us/contribute/#review-open-prs), nebo [vytvářením užitečných položek problémů](https://docs.microsoft.com/en-us/contribute/#create-quality-issues).
