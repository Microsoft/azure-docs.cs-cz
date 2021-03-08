---
title: Azure Defender for SQL
description: Přečtěte si o funkcích pro správu chyb zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly znamenat hrozbu pro vaši databázi v Azure SQL Database, spravované instanci Azure SQL nebo Azure synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452304"
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
Existují různé způsoby, jak povolit plány v programu Azure Defender. Můžete ji povolit na úrovni předplatného (**doporučeno**) z:

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programově pomocí REST API, Azure CLI, PowerShellu nebo Azure Policy](#enable-azure-defender-plans-programatically)

Případně ho můžete povolit na úrovni prostředků, jak je popsáno v tématu [Povolení Azure Defenderu pro Azure SQL Database na úrovni prostředků](#enable-azure-defender-for-azure-sql-database-at-the-resource-level) .

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Povolit Azure Defender pro Azure SQL Database na úrovni předplatného od Azure Security Center
Povolení služby Azure Defender pro Azure SQL Database na úrovni předplatného v rámci Azure Security Center:

1. Z [Azure Portal](https://portal.azure.com)otevřete **Security Center**.
1. V nabídce Security Center vyberte **ceny a nastavení**.
1. Vyberte příslušné předplatné.
1. Změňte nastavení plánu na **zapnuto**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Povolení služby Azure Defender pro Azure SQL Database na úrovni předplatného.":::

1. Vyberte **Uložit**.


### <a name="enable-azure-defender-plans-programatically"></a>Povolit programově plánů Azure Defenderu 

Flexibilita Azure umožňuje řadu programových metod pro povolení plánů Azure Defenderu. 

K povolení Azure Defenderu pro vaše předplatné použijte libovolný z následujících nástrojů: 

| Metoda       | Pokyny                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API     | [Rozhraní API pro ceny](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [AZ Security Price](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Ceny sady prostředků](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Povolit Azure Defender pro Azure SQL Database na úrovni prostředků

Na úrovni předplatného doporučujeme povolit plány Azure Defenderu. to vám může usnadnit vytváření nechráněných prostředků. Pokud ale máte organizační důvod pro povolení Azure Defenderu na úrovni serveru, postupujte podle následujících kroků:

1. Z [Azure Portal](https://portal.azure.com)otevřete Server nebo spravovanou instanci.
1. Pod záhlavím **zabezpečení** vyberte **Security Center**.
1. Vyberte **Povolit Azure Defender pro SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Povolte Azure Defender pro SQL z databází Azure SQL.":::

> [!NOTE]
> Účet úložiště se automaticky vytvoří a nakonfiguruje tak, aby ukládal výsledky kontroly **posouzení ohrožení zabezpečení** . Pokud jste už Azure Defender povolili pro jiný server ve stejné skupině prostředků a oblasti, použije se existující účet úložiště.
>
> Náklady na Azure Defender se zarovnají s Azure Security Centermi cenami na úrovni Standard na uzel, kde uzel je celý server nebo spravovaná instance. Platíte jenom jednou za ochranu všech databází na serveru nebo spravované instanci pomocí Azure Defenderu. Azure Defender můžete vyzkoušet na začátku pomocí bezplatné zkušební verze.


## <a name="manage-azure-defender-settings"></a>Správa nastavení v Azure Defenderu

Zobrazení a Správa nastavení v programu Azure Defender:

1. Z oblasti **zabezpečení** serveru nebo spravované instance vyberte **Security Center**.

    Na této stránce se zobrazí stav Azure Defenderu pro SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Kontroluje se stav služby Azure Defender pro SQL v databázích Azure SQL.":::

1. Pokud je povolený Azure Defender pro SQL, zobrazí se odkaz **Konfigurace** , jak je znázorněno na předchozím obrázku. Pokud chcete upravit nastavení pro Azure Defender pro SQL, vyberte **Konfigurovat**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Nastavení pro Azure Defender pro SQL":::

1. Proveďte potřebné změny a vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

- Další informace o [posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md)
- Další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-configure.md)
- Další informace o [Azure Security Center](../../security-center/security-center-introduction.md)