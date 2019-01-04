---
title: Rozšířená ochrana před internetovými útoky – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o funkcích pro zjišťování a klasifikace citlivá data, Správa ohrožení zabezpečení vaší databáze a detekuje neobvyklé aktivity, které může znamenat hrozbu pro vaše databáze Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 12/16/2018
ms.openlocfilehash: 40f6b6effa509fd2cfa40ecfc758ac37e0a66778
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538604"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection pro Azure SQL Database

SQL Advanced Threat Protection je jednotný balíček pokročilých možností zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikace citlivá data, zpřístupnění a Zklidňující potenciální ohrožení zabezpečení databáze a detekuje neobvyklé aktivity, které může znamenat hrozbu pro vaše databáze. Poskytuje centrální místo pro povolování a správu těchto možností. 

## <a name="overview"></a>Přehled

SQL pokročilé Threat Protection (ATP) poskytuje sadu pokročilé funkce zabezpečení SQL, včetně dat zjišťování a klasifikace, posouzení ohrožení zabezpečení a detekce hrozeb. 

- [Zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md) (aktuálně ve verzi Preview) poskytuje funkce integrované do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat v databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovatelná služba, která může zjišťovat, sledovat a pomáhat opravovat potenciální ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](sql-database-threat-detection-overview.md) zjišťuje neobvyklé aktivity, které můžou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo její zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Upozornění detekce hrozeb obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

Povolení ochrany ATP v programu SQL po povolte všechny z nich zahrnuty funkce. Jedním kliknutím můžete povolit ATP na celém databázovém serveru a použít pro všechny databáze na tomto serveru. Povolení nebo správu nastavení ochrany ATP v programu, který patří do vyžaduje [správce zabezpečení SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) role, role správce databáze SQL nebo role správce systému SQL server. 

Ceny ochrany ATP v programu v souladu s Azure Security Center úrovně standard, kde každý chráněný server SQL Database se počítá jako jeden uzel. Nově chráněných prostředků nárok na bezplatnou zkušební verzi služby Security Center úrovně standard. Další informace najdete v tématu [stránce s cenami za Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Začínáme se službou ochrana ATP v programu 
Následující kroky vám pomůžou začít s ochrany ATP v programu. 

## <a name="1-enable-atp"></a>1. Povolení ochrany ATP v programu

Povolení ochrany ATP v programu tak, že přejdete do **Advanced Threat Protection** pod **zabezpečení** záhlaví v podokně pro Azure SQL Database. Povolení ochrany ATP v programu pro všechny databáze na serveru, klikněte na tlačítko **povolit rozšířené ochrany před internetovými útoky na serveru**.

![Povolení ochrany ATP v programu](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Náklady na ochrany ATP v programu je v souladu s Azure Security Center úrovně standard ceny podle počtu uzlů, kde je uzel celý logický server SQL. Takže platíte jenom jednou pro ochranu všech databází na serveru se službou ochrana ATP v programu. Můžete vyzkoušet ochrany ATP v programu zpočátku s bezplatnou zkušební verzi.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurovat posouzení ohrožení zabezpečení

Pokud chcete začít používat sken posouzení ohrožení zabezpečení, musíte nakonfigurovat účet úložiště, kde jsou uloženy výsledky kontroly. Uděláte to tak, klikněte na kartu sken posouzení ohrožení zabezpečení.

![Konfigurovat posouzení ohrožení zabezpečení](./media/sql-advanced-protection/configure_va.png) 

Vyberte nebo vytvořte účet úložiště pro ukládání výsledků skenování. Můžete také zapnout pravidelné opakované kontroly konfigurace posouzení ohrožení zabezpečení pro spuštění automatického vyhledávání, jednou za týden. Souhrn výsledků kontroly se pošle e-mailové adresy, které zadáte.

![Nastavení posouzení ohrožení zabezpečení](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Spustit klasifikaci dat, sledování chyb zabezpečení a vyšetřování upozornění na hrozby

Klikněte na tlačítko **Data zjišťování a klasifikace** karty zobrazíte doporučené citlivé sloupce ke klasifikaci a klasifikaci dat pomocí popisků trvalé citlivosti. Klikněte na tlačítko **sken posouzení ohrožení zabezpečení** kartu k zobrazení a správa prověřování ohrožení zabezpečení a sestav a sledování vašich zásadní roli zabezpečení. Pokud byly přijaty výstrahy zabezpečení, klikněte na tlačítko **detekce hrozeb** kartu k zobrazení podrobností výstrah a zobrazíte souhrnnou zprávu na všechny výstrahy ve vašem předplatném Azure prostřednictvím stránky s upozorněními zabezpečení Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Spravovat nastavení ochrany ATP v programu na SQL serveru

Pokud chcete zobrazit a spravovat nastavení rozšířené ochrany před internetovými útoky, přejděte na **rozšířené ochrany před internetovými útoky** pod **zabezpečení** záhlaví v podokně pro SQL server. Na této stránce můžete povolit nebo zakázat ochrany ATP v programu a upravovat nastavení detekce hrozeb pro celý server SQL.

![Nastavení serveru](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Správa nastavení ochrany ATP v programu pro službu SQL database

Chcete-li přepsat nastavení detekce hrozeb ochrany ATP v programu pro určitou databázi, zkontrolujte **povolit rozšířené ochrany před internetovými útoky na úrovni databáze** zaškrtávací políčko. Tuto možnost použijte jenom v případě, že nemáte konkrétní požadavek přijímat výstrahy detekce hrozeb samostatné pro jednotlivé databáze, místo nebo kromě výstrahy přijaté pro všechny databáze na serveru. 

Jakmile je políčko zaškrtnuté, klikněte na tlačítko **nastavení detekce hrozeb pro tuto databázi** a potom nakonfigurovat relevantní nastavení pro tuto databázi.

![Nastavení detekce hrozeb a databáze](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Upřesňující nastavení ochrany před internetovými útoky pro server lze také dosažitelná z podokna ochrana ATP v databázi. Klikněte na tlačítko **nastavení** v hlavním podokně ochrany ATP v programu a pak klikněte na tlačítko **nastavení serveru zobrazení Advanced Threat Protection**. 

![Nastavení databáze](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Další postup 

- Další informace o [zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md) 
- Další informace o [sken posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) 
- Další informace o [detekce hrozeb](sql-database-threat-detection.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
