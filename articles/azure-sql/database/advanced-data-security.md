---
title: Pokročilé zabezpečení dat
description: Přečtěte si o funkcích pro zjišťování a klasifikaci citlivých dat, správě ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly signalizovat ohrožení vaší Azure SQL Database, spravované instance Azure SQL nebo Azure synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: ed7d4b10219f4d4a3c437331bd1daf870495949d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047850"
---
# <a name="advanced-data-security"></a>Pokročilé zabezpečení dat
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Advanced Data Security (ADS) je jednotný balíček pro pokročilé funkce zabezpečení SQL. Služby ADS jsou k dispozici pro Azure SQL Database, Azure SQL Managed instance a Azure synapse. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, odhalování a omezování možných ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

## <a name="overview"></a>Přehled

Služba ADS (Advanced Data Security) poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně klasifikace dat & klasifikace, posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky.

- [Klasifikace & Discovery dat](data-discovery-and-classification-overview.md) poskytuje možnosti integrované do Azure SQL Database, spravované instance Azure SQL a Azure synapse pro zjišťování, klasifikaci, označování & oznamování citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovatelná služba, která může zjišťovat, sledovat a pomáhat opravovat potenciální ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Advanced Threat Protection](threat-detection-overview.md) zjišťuje nezvyklé aktivity, které mohou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k vaší databázi nebo jejímu zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Upozornění služby Advanced Threat Protection obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

Pokud chcete povolit všechny tyto zahrnuté funkce, povolte reklamu SQL. Jediným kliknutím můžete reklamu povolit pro všechny databáze na [serveru](logical-servers.md) v Azure (které hostují SQL Database nebo Azure synapse Analytics) nebo v instanci spravované instance Azure SQL. Povolení nebo Správa nastavení reklam vyžaduje, aby patřila do role [Správce zabezpečení SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , role správce SQL Database nebo role správce systému SQL Server.

ADS vyrovnává ceny s Azure Security Center úrovně Standard, kde se každý chráněný Server nebo spravovaná instance počítají jako jeden uzel. Nově chráněné prostředky mají nárok na bezplatnou zkušební verzi Security Center úrovně Standard. Další informace najdete na stránce s [cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Začínáme s REKLAMAmi

Následující kroky vám pomohou začít s REKLAMou.

## <a name="1-enable-ads"></a>1. povolení reklam

Povolte reklamu tak, že přejdete na **pokročilé zabezpečení dat** v záhlaví **zabezpečení** serveru nebo spravované instance.

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky kontroly **posouzení ohrožení zabezpečení** . Pokud jste už povolili reklamu pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.

![Povolit reklamy](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> Náklady na reklamu se zarovnají s Azure Security Centermi cenami na úrovni Standard na uzel, kde uzel je celý server nebo spravovaná instance. Proto platíte jenom jednou za ochranu všech databází na serveru nebo na spravované instanci pomocí reklam. S bezplatnou zkušební verzí můžete nejdřív vyzkoušet reklamu.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. zahájení klasifikace dat, sledování ohrožení zabezpečení a vyšetřování výstrah hrozeb

Klikněte na kartu **klasifikace & zjišťování dat** , abyste viděli Doporučené citlivé sloupce pro klasifikaci a klasifikaci dat pomocí popisků trvalé citlivosti. Pokud chcete zobrazit a spravovat kontroly a sestavy ohrožení zabezpečení a sledovat stature zabezpečení, klikněte na kartu **posouzení ohrožení zabezpečení** . Pokud se přijaly výstrahy zabezpečení, klikněte na kartu **Rozšířená ochrana před internetovými útoky** , abyste si zobrazili podrobnosti o výstrahách a zobrazili jste konsolidovanou sestavu se všemi výstrahami ve vašem předplatném Azure prostřednictvím stránky Azure Security Center výstrahy zabezpečení.

## <a name="3-manage-ads-settings"></a>3. Správa nastavení reklam

Pokud chcete zobrazit a spravovat nastavení reklam, přejděte k **rozšířenému zabezpečení dat** v záhlaví **zabezpečení** serveru nebo spravované instance. Na této stránce můžete povolit nebo zakázat reklamy a upravit nastavení posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky pro celý server nebo spravovanou instanci.

![Nastavení serveru](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Správa nastavení reklam pro databázi SQL

Pokud chcete přepsat nastavení reklam pro určitou databázi, zaškrtněte políčko **Povolit rozšířená zabezpečení dat v úrovni databáze** . Tuto možnost použijte jenom v případě, že máte konkrétní požadavek na získání samostatných výstrah rozšířené ochrany před hrozbami nebo výsledků posouzení ohrožení zabezpečení pro jednotlivé databáze, a to v místě nebo kromě výstrah a výsledků přijatých pro všechny databáze na serveru nebo ve spravované instanci.

Po zaškrtnutí políčka můžete nakonfigurovat relevantní nastavení pro tuto databázi.

![Nastavení databáze a rozšířené ochrany před internetovými útoky](./media/advanced-data-security/database_threat_detection_settings.png)

Pokročilá nastavení zabezpečení dat pro váš server nebo spravovanou instanci je možné taky získat z podokna databáze reklamy. V podokně hlavní reklamy klikněte na **Nastavení** a potom klikněte na **Zobrazit rozšířená nastavení serveru zabezpečení dat**.

![Nastavení databáze](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [klasifikaci & Discovery Data](data-discovery-and-classification-overview.md)
- Další informace o [posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md)
- Další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-configure.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
