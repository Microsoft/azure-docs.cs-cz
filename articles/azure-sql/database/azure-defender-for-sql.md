---
title: Azure Defender pro SQL
description: Přečtěte si o funkcích pro správu chyb zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly znamenat hrozbu pro vaši databázi v Azure SQL Database, spravované instanci Azure SQL nebo Azure synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d147303df43c4f86843df518c71316e6a97b6671
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678081"
---
# <a name="azure-defender-for-sql"></a>Azure Defender pro SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender pro SQL je jednotný balíček pro pokročilé funkce zabezpečení SQL. K dispozici je Azure Defender pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, odhalování a omezování možných ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

## <a name="overview"></a>Přehled

Azure Defender poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně posouzení ohrožení zabezpečení SQL a rozšířené ochrany před internetovými útoky.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno nakonfigurovaná služba, která může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje akční kroky pro řešení problémů se zabezpečením a vylepšení fortifications databáze.
- [Advanced Threat Protection](threat-detection-overview.md) zjišťuje nezvyklé aktivity, které mohou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k vaší databázi nebo jejímu zneužití. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje okamžité výstrahy zabezpečení při potenciálních ohroženích zabezpečení, útocích prostřednictvím injektáže Azure SQL a neobvyklé vzory přístupu k databázi. Upozornění služby Advanced Threat Protection obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

Povolením nástroje Azure Defender pro SQL můžete všechny tyto funkce povolit najednou. Jedním kliknutím můžete povolit Azure Defender pro všechny databáze na [serveru](logical-servers.md) v Azure nebo ve spravované instanci SQL. Povolení nebo Správa nastavení v programu Azure Defender vyžaduje, aby patřila do role [Správce zabezpečení SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) nebo jedna z rolí správce databáze nebo serveru.

Další informace o cenách Azure Defenderu pro SQL najdete na [stránce s cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Začínáme s Azure Defenderem

Následující kroky vám pomohou začít s Azure Defenderem.

## <a name="enable-azure-defender"></a>Povolit Azure Defender

K Azure Defenderu se dá dostat prostřednictvím [Azure Portal](https://portal.azure.com). Povolte Azure Defender tak, že přejdete na **Security Center** pod záhlavím **zabezpečení** vašeho serveru nebo spravované instance.

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky kontroly **posouzení ohrožení zabezpečení** . Pokud jste už Azure Defender povolili pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.
>
> Náklady na Azure Defender se zarovnají s Azure Security Centermi cenami na úrovni Standard na uzel, kde uzel je celý server nebo spravovaná instance. Platíte jenom jednou za ochranu všech databází na serveru nebo spravované instanci pomocí Azure Defenderu. Azure Defender můžete vyzkoušet na začátku pomocí bezplatné zkušební verze.

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Zahájení sledování ohrožení zabezpečení a vyšetřování výstrah hrozeb

Pokud chcete zobrazit a spravovat kontroly a sestavy ohrožení zabezpečení a sledovat stature zabezpečení, klikněte na kartu **posouzení ohrožení zabezpečení** . Pokud se přijaly výstrahy zabezpečení, klikněte na kartu **Rozšířená ochrana před internetovými útoky** , abyste si zobrazili podrobnosti o výstrahách a zobrazili jste konsolidovanou sestavu se všemi výstrahami ve vašem předplatném Azure prostřednictvím stránky Azure Security Center výstrahy zabezpečení.

## <a name="manage-azure-defender-settings"></a>Správa nastavení v Azure Defenderu

Pokud chcete zobrazit a spravovat nastavení v Azure Defenderu, přejděte ke službě **Security Center** pod záhlavím **zabezpečení** vašeho serveru nebo spravované instance. Na této stránce můžete povolit nebo zakázat Azure Defender a upravit nastavení posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky pro celý server nebo spravovanou instanci.

## <a name="manage-azure-defender-settings-for-a-database"></a>Správa nastavení v Azure Defenderu pro databázi

Pokud chcete přepsat nastavení Azure Defenderu pro určitou databázi, zaškrtněte políčko **Povolit Azure Defender pro SQL v úrovni databáze** . Tuto možnost použijte jenom v případě, že máte konkrétní požadavek na získání samostatných výstrah rozšířené ochrany před hrozbami nebo výsledků posouzení ohrožení zabezpečení pro jednotlivé databáze, a to v místě nebo kromě výstrah a výsledků přijatých pro všechny databáze na serveru nebo ve spravované instanci.

Po zaškrtnutí políčka můžete nakonfigurovat relevantní nastavení pro tuto databázi.

Nastavení Azure Defenderu pro SQL Server nebo spravovanou instanci se dá získat taky z podokna databáze v Azure Defenderu. V hlavním podokně Azure Defender klikněte na **Nastavení** a pak klikněte na **Zobrazit Azure Defender pro nastavení SQL serveru** .

## <a name="next-steps"></a>Další kroky

- Další informace o [posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md)
- Další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-configure.md)
- Další informace o [Azure Security Center](../../security-center/security-center-introduction.md)