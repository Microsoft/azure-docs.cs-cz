---
title: Azure Defender pro SQL – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301597"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Seznámení se službou Azure Defender pro SQL

Azure Defender pro SQL zahrnuje dva plány Azure Defenderu, které rozšiřuje [balíček zabezpečení dat](../azure-sql/database/advanced-data-security.md) Azure Security Center, aby se vaše databáze a data nacházely tam, kde jsou umístěné. 

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|**Azure Defender pro servery Azure SQL Database** – všeobecně dostupné (GA)<br>**Azure Defender pro SQL servery na počítačích** – Preview|
|Stanov|Dva plány, které tvoří **Azure Defender pro SQL** , se účtují, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Chráněné verze SQL:|Azure SQL Database <br>Spravovaná instance Azure SQL<br>Azure Synapse Analytics (dříve SQL DW)<br>SQL Server (všechny podporované verze)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Co je Azure Defender pro SQL Protect?

**Azure Defender pro SQL** se skládá ze dvou samostatných plánů Azure Defenderu:

- **Azure Defender pro servery Azure SQL Database** chrání:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender pro SQL servery na počítačích (ve verzi Preview)** rozšiřuje ochranu vašich serverů SQL Azure Native, aby plně podporovala hybridní prostředí a chránila SQL servery (všechny podporované verze) hostované v Azure, v jiných cloudových prostředích a i v místních počítačích.


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jaké jsou výhody Azure Defenderu pro SQL?

Tyto dva plány zahrnují funkce pro identifikaci a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly označovat hrozby pro vaše databáze:

- [Posouzení ohrožení zabezpečení](../azure-sql/database/sql-vulnerability-assessment.md) – služba prohledávání, která zjišťuje, sleduje a usnadňuje napravení potenciálních ohrožení zabezpečení databáze. Kontroly Hodnocení poskytují přehled o stavu zabezpečení počítačů s SQL a podrobnosti o jakýchkoli zjištěních zabezpečení.

- [Rozšířená ochrana před internetovými útoky](../azure-sql/database/threat-detection-overview.md) – služba zjišťování, která nepřetržitě monitoruje vaše servery SQL na hrozbách, jako jsou například injektáže SQL, útoky hrubou silou a zneužití oprávnění. Tato služba poskytuje výstrahy zabezpečení zaměřené na akce v Azure Security Center s podrobnostmi o podezřelé aktivitě, pokyny ke zmírnění hrozeb a možnosti pro pokračování v šetření pomocí ověřování Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Jaký typ výstrah poskytuje Azure Defender pro SQL?

Výstrahy zabezpečení se aktivují, když dojde k těmto akcím:

- **Potenciální útoky prostřednictvím INJEKTÁŽE SQL** – včetně zjištěných chyb, když aplikace generují chybný příkaz SQL v databázi
- **Přístup k databázi neobvyklé a vzory dotazů** – například neobvykle vysoký počet neúspěšných pokusů o přihlášení s různými přihlašovacími údaji (pokus o útok hrubou silou)
- **Podezřelá aktivita databáze** – například změna cíle úložiště exportu pro operaci importu a exportu SQL

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit.



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro SQL.

Související materiály najdete v následujících článcích: 

- [Jak povolit Azure Defender pro servery SQL na počítačích](defender-for-sql-usage.md)
- [Jak povolit Azure Defender pro servery SQL Database](../azure-sql/database/advanced-data-security.md)
- [Seznam upozornění v programu Azure Defender pro SQL](alerts-reference.md#alerts-sql-db-and-warehouse)