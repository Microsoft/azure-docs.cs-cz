---
title: Pokročilé zabezpečení dat
description: Přečtěte si o funkcích pro zjišťování a klasifikaci citlivých dat, správě ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly poukazovat na hrozbu pro vaši databázi SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: a4f4306023b37c3b1cba46acd11ec5c0a489d0d6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822575"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Rozšířené zabezpečení dat pro Azure SQL Database

Rozšířené zabezpečení dat je jednotný balíček pro pokročilé funkce zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, zpřístupněníí a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly znamenat hrozbu pro vaši databázi. Poskytuje centrální místo pro povolování a správu těchto možností.

## <a name="overview"></a>Přehled

Služba ADS (Advanced Data Security) poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně klasifikace dat & klasifikace, posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky.

- [Klasifikace & Discovery dat](sql-database-data-discovery-and-classification.md) poskytuje možnosti integrované do Azure SQL Database pro zjišťování, klasifikaci a označování & ochrany citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je jednoduchá konfigurace služby, která může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo jejím zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Výstrahy rozšířené ochrany před internetovými útoky poskytují podrobné informace o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit.

Pokud chcete povolit všechny tyto zahrnuté funkce, povolte reklamu SQL. Pomocí jednoho kliknutí můžete reklamu povolit pro všechny databáze na serveru SQL Database nebo spravované instanci. Povolení nebo Správa nastavení reklam vyžaduje, aby patřila do role [Správce zabezpečení SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , role správce SQL Database nebo role správce systému SQL Server. 

ADS vyrovnává ceny s Azure Security Center úrovně Standard, kde se každý chráněný SQL Database Server nebo spravovaná instance počítají jako jeden uzel. Nově chráněné prostředky mají nárok na bezplatnou zkušební verzi Security Center úrovně Standard. Další informace najdete na stránce s [cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Začínáme s REKLAMAmi

Následující kroky vám pomohou začít s REKLAMou.

## <a name="1-enable-ads"></a>1. povolení reklam

Povolte reklamu tak, že přejdete na **pokročilé zabezpečení dat** v záhlaví **zabezpečení** pro vaši instanci serveru SQL Database nebo spravovaných. Pokud chcete povolit reklamu pro všechny databáze na databázovém serveru nebo ve spravované instanci, klikněte na **Povolit rozšířená data zabezpečení na serveru**.

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky kontroly **posouzení ohrožení zabezpečení** . Pokud jste už povolili reklamu pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.

![Povolit reklamy](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Náklady na reklamu se zarovnají s Azure Security Centermi cenami na úrovni Standard na uzel, kde uzel je celý SQL Database Server nebo spravovaná instance. Proto platíte jenom jednou za ochranu všech databází na databázovém serveru nebo na spravované instanci pomocí reklam. S bezplatnou zkušební verzí můžete nejdřív vyzkoušet reklamu.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. zahájení klasifikace dat, sledování ohrožení zabezpečení a vyšetřování výstrah hrozeb

Klikněte na kartu **klasifikace & zjišťování dat** , abyste viděli Doporučené citlivé sloupce pro klasifikaci a klasifikaci dat pomocí popisků trvalé citlivosti. Pokud chcete zobrazit a spravovat kontroly a sestavy ohrožení zabezpečení a sledovat stature zabezpečení, klikněte na kartu **posouzení ohrožení zabezpečení** . Pokud se přijaly výstrahy zabezpečení, klikněte na kartu **Rozšířená ochrana před internetovými útoky** , abyste si zobrazili podrobnosti o výstrahách a zobrazili jste konsolidovanou sestavu se všemi výstrahami ve vašem předplatném Azure prostřednictvím stránky Azure Security Center výstrahy zabezpečení.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Správa nastavení reklam na vašem serveru SQL Database nebo spravované instanci

Pokud chcete zobrazit a spravovat nastavení reklam, přejděte k **rozšířenému zabezpečení dat** pod hlavičkou **zabezpečení** pro váš SQL Database Server nebo spravovanou instanci. Na této stránce můžete povolit nebo zakázat reklamy a upravit nastavení posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky pro celý SQL Database Server nebo spravovanou instanci.

![Nastavení serveru](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Správa nastavení reklam pro databázi SQL

Pokud chcete přepsat nastavení reklam pro určitou databázi, zaškrtněte políčko **Povolit rozšířená zabezpečení dat v úrovni databáze** . Tuto možnost použijte jenom v případě, že máte konkrétní požadavek na získání samostatných výstrah rozšířené ochrany před internetovými útoky nebo výsledků posouzení ohrožení zabezpečení pro jednotlivé databáze, a to místo nebo kromě výstrah a výsledků přijatých pro všechny databáze na portálu. databázový server nebo spravovaná instance.

Po zaškrtnutí políčka můžete nakonfigurovat relevantní nastavení pro tuto databázi.
 
![Nastavení databáze a rozšířené ochrany před internetovými útoky](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Pokročilá nastavení zabezpečení dat pro váš databázový server nebo spravovanou instanci lze získat také z podokna databáze reklamy. V podokně hlavní reklamy klikněte na **Nastavení** a potom klikněte na **Zobrazit rozšířená nastavení serveru zabezpečení dat**. 

![Nastavení databáze](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Další kroky 

- Další informace o [klasifikaci & Discovery Data](sql-database-data-discovery-and-classification.md) 
- Další informace o [posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) 
- Další informace o [Rozšířené ochraně před internetovými útoky](sql-database-threat-detection.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
