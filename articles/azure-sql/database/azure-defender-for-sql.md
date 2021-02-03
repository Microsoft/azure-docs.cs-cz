---
title: Azure Defender for SQL
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
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508799"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender pro SQL je jednotný balíček pro pokročilé funkce zabezpečení SQL. K dispozici je Azure Defender pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, odhalování a omezování možných ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jaké jsou výhody Azure Defenderu pro SQL?

Azure Defender poskytuje sadu pokročilých funkcí zabezpečení SQL, včetně posouzení ohrožení zabezpečení SQL a rozšířené ochrany před internetovými útoky.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno nakonfigurovaná služba, která může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje akční kroky pro řešení problémů se zabezpečením a vylepšení fortifications databáze.
- [Advanced Threat Protection](threat-detection-overview.md) zjišťuje nezvyklé aktivity, které mohou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k vaší databázi nebo jejímu zneužití. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje okamžité výstrahy zabezpečení při potenciálních ohroženích zabezpečení, útocích prostřednictvím injektáže Azure SQL a neobvyklé vzory přístupu k databázi. Upozornění služby Advanced Threat Protection obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

Povolením nástroje Azure Defender pro SQL můžete všechny tyto funkce povolit najednou. Jedním kliknutím můžete povolit Azure Defender pro všechny databáze na [serveru](logical-servers.md) v Azure nebo ve spravované instanci SQL. Povolení nebo Správa nastavení v programu Azure Defender vyžaduje, aby patřila do role [Správce zabezpečení SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) nebo jedna z rolí správce databáze nebo serveru.

Další informace o cenách Azure Defenderu pro SQL najdete na [stránce s cenami Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Povolení Azure Defenderu

K Azure Defenderu se dá dostat prostřednictvím [Azure Portal](https://portal.azure.com). Povolte Azure Defender tak, že přejdete na **Security Center** pod hlavičkou **zabezpečení** serveru nebo spravované instance.

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky kontroly **posouzení ohrožení zabezpečení** . Pokud jste už Azure Defender povolili pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.
>
> Náklady na Azure Defender se zarovnají s Azure Security Centermi cenami na úrovni Standard na uzel, kde uzel je celý server nebo spravovaná instance. Platíte jenom jednou za ochranu všech databází na serveru nebo spravované instanci pomocí Azure Defenderu. Azure Defender můžete vyzkoušet na začátku pomocí bezplatné zkušební verze.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Povolení Azure Defenderu pro SQL z databází Azure SQL":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Sledování ohrožení zabezpečení a vyšetřování upozornění na hrozby

Pokud chcete zobrazit a spravovat kontroly a sestavy ohrožení zabezpečení a sledovat stature zabezpečení, klikněte na kartu **posouzení ohrožení zabezpečení** . Pokud se přijaly výstrahy zabezpečení, klikněte na kartu **Rozšířená ochrana před internetovými útoky** , abyste si zobrazili podrobnosti o výstrahách a zobrazili jste konsolidovanou sestavu se všemi výstrahami ve vašem předplatném Azure prostřednictvím stránky Azure Security Center výstrahy zabezpečení.

## <a name="manage-azure-defender-settings"></a>Správa nastavení v Azure Defenderu

Zobrazení a Správa nastavení v programu Azure Defender:

1. Z oblasti **zabezpečení** serveru nebo spravované instance vyberte **Security Center**.

    Na této stránce se zobrazí stav Azure Defenderu pro SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Kontrola stavu Azure Defenderu pro SQL v databázích SQL Azure":::

1. Pokud je povolený Azure Defender pro SQL, zobrazí se odkaz **Konfigurace** , jak je znázorněno na předchozím obrázku. Pokud chcete upravit nastavení pro Azure Defender pro SQL, vyberte **Konfigurovat**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="nastavení serveru zabezpečení":::

1. Proveďte potřebné změny a vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

- Další informace o [posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md)
- Další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-configure.md)
- Další informace o [Azure Security Center](../../security-center/security-center-introduction.md)