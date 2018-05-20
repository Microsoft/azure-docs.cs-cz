---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: Další informace o funkcích pro zjišťování a klasifikace citlivá data, správu ohrožení zabezpečení vaší databáze a zjišťování neobvyklé aktivity, které může znamenat hrozbu pro vaše databáze Azure SQL.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection pro databázi Azure SQL.

Ochrana před internetovými útoky pokročilé SQL je jednotná balíček pro rozšířené možnosti zabezpečení SQL. Obsahuje funkci pro zjišťování a klasifikace citlivá data, správě vaší databáze ohrožení zabezpečení a zjišťování neobvyklé aktivity, které může znamenat hrozbu pro vaše databáze. Do jednoho umístění přejděte poskytuje pro povolení a správa tyto možnosti. 

## <a name="overview"></a>Přehled

SQL rozšířené hrozba ochrany (ATP) poskytuje sadu rozšířené možnosti zabezpečení SQL, včetně dat zjišťování a klasifikaci, vyhodnocení ohrožení zabezpečení a detekce hrozeb. 

- [Data zjišťování a klasifikace](sql-database-data-discovery-and-classification.md) (aktuálně ve verzi preview) nabízí funkce integrovaná do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivá data v databázích máte. Může sloužit k poskytování získat přehled o stavu vaší databáze klasifikace a sledovat přístup k citlivým datům v databázi a za jeho hranicemi.
- [Vyhodnocení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurace služby, která může zjišťovat, sledovat a vám pomůžou opravit potenciální ohrožení zabezpečení databáze. Poskytuje přehled o stavu vaší zabezpečení a zahrnuje řešitelné postup vyřešit problémy se zabezpečením a zajištění lepších fortifications vaší databáze.
- [Detekce hrozby](sql-database-threat-detection.md) zjistí neobvyklé aktivity, které indikují neobvyklou a potenciálně škodlivé pokusy o přístup, nebo využívat vaší databáze. Nepřetržitě sleduje databáze pro podezřelé aktivity a poskytuje okamžité zabezpečení výstrahy na potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a vzory přístupu k databázi neobvyklé. Výstrah o zjištěných hrozbách zadejte podrobnosti podezřelé aktivity a doporučujeme akce o tom, jak prozkoumat a zmírnit riziko.

Povolte SQL ATP, jakmile povolte všechny z nich zahrnuté funkce. Jedním kliknutím můžete povolit ATP na celou databázi serveru použití pro všechny databáze na serveru. 

Ceny ATP zarovnaná s Azure Security Center úrovně standard na 15 $/ uzlu/měsíc, kde každé chráněné databáze SQL serveru se počítá jako jeden uzel. Prvních 60 dní po povolení jsou považovány za a bezplatné zkušební období a není účtován. Další informace najdete v tématu [Azure Security Center stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Začínáme s ATP 
Následující kroky vám pomůžou začít s ATP. 

## <a name="1-enable-atp"></a>1. Povolit ATP

Povolit ATP přechodem na **Advanced Threat Protection** pod **zabezpečení** záhlaví v podokně pro Azure SQL Database. Chcete-li povolit ATP pro všechny databáze na serveru, klikněte na tlačítko **povolit Advanced Threat Protection na serveru**.

![Povolit ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Náklady ATP je $15 uzlu/měsíc, kde uzlu je celý logickému serveru SQL. Proto platíte jenom jednou pro ochranu všech databází na serveru s ATP. Prvních 60 dní jsou považovány za bezplatnou zkušební verzi.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurace vyhodnocení ohrožení zabezpečení

Chcete-li začít používat vyhodnocení ohrožení zabezpečení, musíte nakonfigurovat účet úložiště, kde se uloží výsledky kontroly. Uděláte to tak, klikněte na kartu vyhodnocení ohrožení zabezpečení.

![Konfigurace VA](./media/sql-advanced-protection/configure_va.png) 

Vyberte nebo vytvořte účet úložiště pro ukládání výsledků kontroly. Můžete také zapnout pravidelně opakované kontroly konfigurace vyhodnocení ohrožení zabezpečení ke spuštění automatického skenování jednou za týden. Souhrn výsledků kontroly se odesílají do e-mailové adresy, které poskytnete.

![Nastavení VA](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Spustit klasifikaci dat, sledování ohrožení zabezpečení a prošetřování výstrah hrozeb

Klikněte na tlačítko **Data zjišťování a klasifikace** karta najdete v části Doporučená citlivé sloupce ke klasifikaci a klasifikaci dat pomocí popisky trvalé velkých a malých písmen. Klikněte **vyhodnocení ohrožení zabezpečení** karet k zobrazení a správa prověřování ohrožení zabezpečení a sestav a sledovat vaše vzrůstem zabezpečení. Pokud byly přijaty výstrahy zabezpečení, klikněte **detekce hrozeb** karty zobrazíte podrobnosti výstrah a zobrazíte sestavu konsolidované na všechny výstrahy ve vašem předplatném Azure prostřednictvím stránky výstrah zabezpečení Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Spravovat nastavení ATP na serveru SQL server

Chcete-li zobrazit a spravovat nastavení Advanced Threat Protection, přejděte na **Advanced Threat Protection** pod **zabezpečení** záhlaví v podokně pro SQL server. Na této stránce můžete povolit nebo zakázat ATP a upravit nastavení detekce hrozeb pro celý server SQL.

![Nastavení serveru](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Spravovat nastavení ATP pro databázi SQL.

Chcete-li přepsat nastavení detekce hrozeb ATP pro konkrétní databázi, zkontrolujte **povolit Advanced Threat Protection na úrovni databáze** zaškrtávací políčko. Tuto možnost použijte pouze v případě, že máte konkrétní požadavky pro příjem výstrah o zjištěných hrozbách samostatné pro jednotlivé databáze, místě nebo kromě výstrahy pro všechny databáze na serveru. 

Jakmile je políčko zaškrtnuté, klikněte na možnost **nastavení detekce hrozeb pro tuto databázi** a potom nakonfigurovat relevantní nastavení pro tuto databázi.

![Nastavení detekce databáze a hrozeb](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Rozšířená ochrana před internetovými útoky nastavení pro váš server můžete také dostupný z podokna ATP databáze. Klikněte na tlačítko **nastavení** v hlavním podokně ATP a pak klikněte na tlačítko **nastavení serveru zobrazení Advanced Threat Protection**. 

![Nastavení databáze](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Další postup 

- Další informace o [Data zjišťování a klasifikace](sql-database-data-discovery-and-classification.md) 
- Další informace o [vyhodnocení ohrožení zabezpečení](sql-vulnerability-assessment.md) 
- Další informace o [detekce hrozeb.](sql-database-threat-detection.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
