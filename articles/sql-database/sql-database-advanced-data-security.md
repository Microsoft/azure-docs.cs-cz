---
title: Pokročilé zabezpečení dat
description: Seznamte se s funkcemi pro zjišťování a klasifikaci citlivých dat, správu chyb zabezpečení databáze a zjišťování neobvyklých aktivit, které by mohly znamenat ohrožení databáze Azure SQL.
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
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269130"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Pokročilé zabezpečení dat pro Azure SQL Database

Pokročilé zabezpečení dat je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, odhalování a omezování možných ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

## <a name="overview"></a>Přehled

Rozšířené zabezpečení dat (ADS) poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně zjišťování dat & klasifikace, posouzení zranitelnosti a rozšířené ochrany před hrozbami.

- [zjišťování dat & klasifikace](sql-database-data-discovery-and-classification.md) poskytuje funkce integrované do Azure SQL Database pro zjišťování, klasifikaci, označování & ochranu citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovatelná služba, která dokáže zjistit, sledovat a pomoci vám napravit potenciální chyby zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) zjišťuje nezvyklé aktivity, které mohou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k vaší databázi nebo jejímu zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Upozornění služby Advanced Threat Protection obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

Povolte SQL ADS jednou, abyste povolili všechny tyto zahrnuté funkce. Jedním kliknutím můžete povolit ADS pro všechny databáze na serveru SQL Database nebo spravované instanci. Povolení nebo správa nastavení ADS vyžaduje, aby se role [správce zabezpečení SQL,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) role správce databáze SQL nebo role správce serveru SQL. 

Ceny ADS jsou v souladu se standardní vrstvou Azure Security Center, kde se každý chráněný server SQL Database nebo spravovaná instance počítá jako jeden uzel. Nově chráněné prostředky mají nárok na bezplatnou zkušební verzi standardní úrovně Security Center. Další informace najdete na [stránce s cenami Centra zabezpečení Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Začínáme s REKLAMAMI

Následující kroky vám pomohou začít s reklamou ADS.

## <a name="1-enable-ads"></a>1. Povolit REKLAMY

Povolte ads přechodem na **Rozšířené zabezpečení dat** pod nadpisem **Zabezpečení** pro server SQL Database nebo spravovanou instanci. Chcete-li povolit služby ADS pro všechny databáze na databázovém serveru nebo spravované instanci, klepněte na tlačítko **Povolit rozšířené zabezpečení dat na serveru**.

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky testů **posouzení ohrožení zabezpečení.** Pokud jste již povolili ADS pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.

![Povolit reklamy](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Náklady na ADS jsou zarovnány s cenami standardní úrovně Azure Security Center na uzel, kde uzel je celý server SQL Database nebo spravovaná instance. Platíte tedy pouze jednou za ochranu všech databází na databázovém serveru nebo spravované instanci pomocí ADS. Můžete vyzkoušet ADS zpočátku s bezplatnou zkušební verzi.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Začněte klasifikovat data, sledovat slabá místa a vyšetřovat výstrahy hrozeb

Kliknutím na kartu **Data Discovery & klasifikace zobrazíte** doporučené citlivé sloupce, které klasifikují a klasifikují data pomocí popisků trvalé citlivosti. Klepnutím na kartu **pro posouzení ohrožení zabezpečení** zobrazíte a spravujete prohledávací výsledky a sestavy zranitelnosti a sledování stavu zabezpečení. Pokud byly přijaty výstrahy zabezpečení, klikněte na kartu **Advanced Threat Protection** zobrazíte podrobnosti o výstrahách a zobrazíte konsolidovanou sestavu všech výstrah v předplatném Azure prostřednictvím stránky výstrah zabezpečení Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Správa nastavení ADS na serveru SQL Database nebo spravované instanci

Chcete-li zobrazit a spravovat nastavení SLUŽBY ADS, přejděte do části **Rozšířené zabezpečení dat** pod nadpisem **Zabezpečení** pro databázový server SQL nebo spravovanou instanci. Na této stránce můžete povolit nebo zakázat službu ADS a upravit posouzení ohrožení zabezpečení a rozšířené nastavení ochrany před internetovými hrozbami pro celý server sql database nebo spravovanou instanci.

![Nastavení serveru](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Správa nastavení ADS pro databázi SQL

Chcete-li přepsat nastavení ADS pro určitou databázi, zaškrtněte políčko **Povolit rozšířené zabezpečení dat na úrovni databáze.** Tuto možnost použijte pouze v případě, že máte zvláštní požadavek na příjem samostatných výstrah rozšířené ochrany před hrozbami nebo výsledků hodnocení zranitelnosti pro jednotlivé databáze, namísto výstrah a výsledků obdržených pro všechny databáze na databázového serveru nebo spravované instance.

Po zaškrtnutí políčka můžete nakonfigurovat příslušná nastavení pro tuto databázi.
 
![Nastavení databáze a rozšířené ochrany před hrozbami](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Z podokna databáze ADS lze také dosáhnout upřesňujícím nastavení zabezpečení dat pro databázový server nebo spravovanou instanci. V hlavním podokně ADS klepněte na **položku Nastavení** a potom klepněte na tlačítko **Zobrazit nastavení serveru Rozšířené zabezpečení dat**. 

![Nastavení databáze](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Další kroky 

- Další informace o [klasifikaci & zjišťování dat](sql-database-data-discovery-and-classification.md) 
- Další informace o [posouzení zranitelnosti](sql-vulnerability-assessment.md) 
- Další informace o [rozšířené ochraně před hrozbami](sql-database-threat-detection.md)
- Další informace o [Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
