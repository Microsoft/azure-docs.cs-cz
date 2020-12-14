---
title: Azure Defender pro SQL – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: e9a2259f1e15a94ad1f2a9eabf7d3a2f3ed419f1
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387798"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Seznámení se službou Azure Defender pro SQL

Azure Defender pro SQL zahrnuje dva plány Azure Defenderu, které rozšiřuje [balíček zabezpečení dat](../azure-sql/database/azure-defender-for-sql.md) Azure Security Center, aby se vaše databáze a data nacházely tam, kde jsou umístěné. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|**Azure Defender pro servery Azure SQL Database** – všeobecně dostupné (GA)<br>**Azure Defender pro SQL servery na počítačích** – všeobecně dostupné (GA) |
|Stanov|Dva plány, které tvoří **Azure Defender pro SQL** , se účtují, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Chráněné verze SQL:|[SQL na virtuálních počítačích Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Servery SQL s podporou ARC Azure](https://docs.microsoft.com/sql/sql-server/azure-arc/overview)<br>Místní SQL servery na počítačích s Windows bez ARC Azure<br>[Jednoduché databáze](../azure-sql/database/single-database-overview.md) a [elastické fondy](../azure-sql/database/elastic-pool-overview.md) Azure SQL<br>[Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Vyhrazený fond SQL Azure synapse Analytics (dříve SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Cloud|![Ano ](./media/icons/yes-icon.png) komerční cloudy<br>![Ano ](./media/icons/yes-icon.png) US gov<br>![Ano ](./media/icons/yes-icon.png) Čína gov (**částečný**: podmnožina výstrah a posouzení ohrožení zabezpečení pro servery SQL. Ochrana proti hrozbám v případě chování není k dispozici.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Co je Azure Defender pro SQL Protect?

**Azure Defender pro SQL** se skládá ze dvou samostatných plánů Azure Defenderu:

- **Azure Defender pro servery Azure SQL Database** chrání:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Spravovaná instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Vyhrazený fond SQL ve službě Azure synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender pro servery SQL na počítačích** rozšiřuje ochranu pro vaše servery SQL nativní pro Azure tak, aby plně podporovala hybridní prostředí a chránila SQL servery (všechny podporované verze) hostované v Azure, v jiných cloudových prostředích a dokonce i v místních počítačích:
    - [SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Místní SQL servery:
        - [SQL Server s podporou služby Azure Arc (Preview)](https://docs.microsoft.com/sql/sql-server/azure-arc/overview)
        - [SQL Server běžící na počítačích s Windows bez ARC Azure](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jaké jsou výhody Azure Defenderu pro SQL?

Tyto dva plány zahrnují funkce pro identifikaci a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly označovat hrozby pro vaše databáze:

- [Posouzení ohrožení zabezpečení](../azure-sql/database/sql-vulnerability-assessment.md) – služba prohledávání, která zjišťuje, sleduje a usnadňuje napravení potenciálních ohrožení zabezpečení databáze. Kontroly Hodnocení poskytují přehled o stavu zabezpečení počítačů s SQL a podrobnosti o jakýchkoli zjištěních zabezpečení.

- [Rozšířená ochrana před internetovými útoky](../azure-sql/database/threat-detection-overview.md) – služba zjišťování, která nepřetržitě monitoruje vaše servery SQL na hrozbách, jako jsou například injektáže SQL, útoky hrubou silou a zneužití oprávnění. Tato služba poskytuje výstrahy zabezpečení zaměřené na akce v Azure Security Center s podrobnostmi o podezřelé aktivitě, pokyny ke zmírnění hrozeb a možnosti pro pokračování v šetření pomocí ověřování Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Jaký typ výstrah poskytuje Azure Defender pro SQL?

Výstrahy zabezpečení pro analýzu hrozeb se aktivují, když jsou:

- **Potenciální útoky prostřednictvím INJEKTÁŽE SQL** – včetně zjištěných chyb, když aplikace generují chybný příkaz SQL v databázi
- **Přístup k databázi neobvyklé a vzory dotazů** – například neobvykle vysoký počet neúspěšných pokusů o přihlášení s různými přihlašovacími údaji (pokus o útok hrubou silou)
- **Podezřelá databázová aktivita** – například oprávněný uživatel, který přistupuje k SQL Server z napadeného počítače, který se přenáší na Server c&s kryptografickým přístupem.

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit.



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro SQL.

> [!div class="nextstepaction"]
> [Kontrola ohrožení zabezpečení v SQL serverech pomocí Azure Defenderu](defender-for-sql-usage.md)

Související materiály najdete v následujících článcích: 

- [Jak povolit Azure Defender pro servery SQL Database](../azure-sql/database/azure-defender-for-sql.md)
- [Seznam výstrah zabezpečení pro SQL Server](alerts-reference.md#alerts-sql-db-and-warehouse)
