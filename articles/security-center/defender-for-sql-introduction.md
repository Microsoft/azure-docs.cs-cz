---
title: Azure Defender pro SQL – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro SQL.
author: memildin
ms.author: memildin
ms.date: 11/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 5ea820af6d5258b9a98856c9d5243a84d32d931f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461673"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Seznámení se službou Azure Defender pro SQL

Azure Defender pro SQL zahrnuje dva plány Azure Defenderu, které rozšiřuje [balíček zabezpečení dat](../azure-sql/database/azure-defender-for-sql.md) Azure Security Center, aby se vaše databáze a data nacházely tam, kde jsou umístěné. 

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|**Azure Defender pro servery Azure SQL Database** – všeobecně dostupné (GA)<br>**Azure Defender pro SQL servery na počítačích** – Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Stanov|Dva plány, které tvoří **Azure Defender pro SQL** , se účtují, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Chráněné verze SQL:|SQL na virtuálních počítačích Azure – [Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) a [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md)<br>[Servery SQL s povoleným obloukem](https://docs.microsoft.com/sql/sql-server/azure-arc/overview) (zahrnuje místní SQL servery)<br>[Jednoduché databáze](../azure-sql/database/single-database-overview.md) a [elastické fondy](../azure-sql/database/elastic-pool-overview.md) Azure SQL<br>[Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Vyhrazený fond SQL Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Co je Azure Defender pro SQL Protect?

**Azure Defender pro SQL** se skládá ze dvou samostatných plánů Azure Defenderu:

- **Azure Defender pro servery Azure SQL Database** chrání:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Vyhrazený fond SQL ve službě Azure synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

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
- [Jak povolit Azure Defender pro servery SQL Database](../azure-sql/database/azure-defender-for-sql.md)
- [Seznam upozornění v programu Azure Defender pro SQL](alerts-reference.md#alerts-sql-db-and-warehouse)
