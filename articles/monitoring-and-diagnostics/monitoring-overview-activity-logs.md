---
title: Přehled protokolu činnosti Azure | Microsoft Docs
description: Zjistěte, co je protokol činnosti Azure a jak ji použít k pochopení událostí v rámci vašeho předplatného Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: johnkem
ms.openlocfilehash: 6e373740d6b5af4b3b7d3dca8877c952d79f8b20
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Sledování aktivity předplatné s protokol činnosti Azure
**Protokol činnosti Azure** je protokol odběru, který poskytuje přehled o události na úrovni předplatného, k nimž došlo v Azure. To zahrnuje celou řadu dat z provozních dat Azure Resource Manager aktualizací na události stavu služby. Protokol aktivit se dřív označovala jako "Protokoly auditu" nebo "Provozní protokoly," od události administrativní kategorie sestavy rovině řízení pro vaše předplatné. Pomocí protokolu činnosti, můžete určit ', kdo a kdy se pro všechny zápisu operace (PUT, POST, DELETE) na prostředky v rámci vašeho předplatného. Můžete také chápou stav operace a další relevantní vlastnosti. Protokol aktivit nezahrnuje operace čtení (GET) nebo operace pro prostředky, které používají Classic nebo model "RDFE".

![Aktivita protokoly vs jiné typy protokolů ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Obrázek 1: Protokoly aktivity vs jiné typy protokolů

Protokol aktivity se liší od [diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md). Protokoly aktivity poskytují data týkající se operací na prostředku z vnějšku ("řízení rovinou"). Diagnostické protokoly jsou vygenerované prostředek a poskytnout informace o operaci prostředku ("data rovinou").

> [!WARNING]
> Protokol činnosti Azure je určen pro činnosti, které ve službě Správce prostředků Azure. Sledovat prostředky pomocí modelu Classic nebo RDFE. Některé typy prostředků Classic mají proxy poskytovatele prostředků v Azure Resource Manageru (například Microsoft.Network). Při práci s typem prostředku Classic prostřednictvím Správce Azure Resource Manager pomocí těchto poskytovatelů prostředků proxy, operace se objeví v protokolu aktivit. Pokud budete používat typ prostředku Classic mimo proxy Azure Resource Manager, vaše akce pouze zaznamenávají v protokolu operací. V protokolu operaci můžete procházet na samostatné části portálu.
>
>

Můžete načíst události z protokolu aktivit pomocí portálu Azure, rozhraní příkazového řádku, rutiny prostředí PowerShell a rozhraní REST API Azure monitorování.

> [!NOTE]

>  [Novější výstrahy)](monitoring-overview-unified-alerts.md) nabízí vylepšené prostředí při vytváření a správa aktivity protokolu pravidla výstrah.  [Další informace](monitoring-activity-log-alerts-new-experience.md).


Zobrazení v následujícím videu Představení protokolu aktivit.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]


## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Protokol aktivit obsahuje několik kategorií data. Úplné podrobnosti o schémat z těchto kategorií [najdete v článku](monitoring-activity-log-schema.md). Mezi ně patří:
* **Správce** – Tato kategorie obsahuje záznam všech vytvořit, operace aktualizace, odstranění a akce provést pomocí Správce prostředků. Typy událostí, zobrazí se v této kategorii příklady "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" každou akci provedenou uživatele nebo aplikace pomocí Resource Manager je modelovaná jako operace na konkrétní typ prostředku. Pokud je typ operaci zápisu, Delete nebo akce, záznamy start a úspěch nebo selhání této operace se zaznamenávají do administrativní kategorie. Administrativní kategorie také zahrnuje všechny změny na řízení přístupu na základě rolí v předplatném.
* **Stav služby** – Tato kategorie obsahuje záznam všechny služby stavu incidentů, kterým došlo v Azure. Je například typ události, které se zobrazí se v této kategorii "SQL Azure v oblasti Východ USA dochází k výpadku." Události stavu služby existují ve pět variantách: je vyžadována akce, jíž obnovení, Incident, údržby, informace nebo zabezpečení a objeví jenom tehdy, pokud máte prostředku v odběru, který bude mít vliv události.
* **Výstrahy** – Tato kategorie obsahuje záznam všech aktivací Azure výstrah. Je například typ události, které se zobrazí se v této kategorii "% využití procesoru na Můjvp je už více než 80 za posledních 5 minut." Výstrahy koncept mají různé systémy Azure – můžete definovat pravidla nějaká a přijímat oznámení v případě podmínky odpovídají daného pravidla. Pokaždé, když podporovaných Azure typu výstrahy, aktivuje,' nebo ke splnění podmínek pro generování oznámení, záznam aktivace také vložena do této kategorie protokolu činnosti.
* **Škálování** – Tato kategorie obsahuje záznam všechny události související s operací škálování stroje podle nastavení automatického škálování, který jste definovali ve vašem předplatném. Je například typ události, které se zobrazí se v této kategorii "Škálování rozšiřování škálování využívajících akce se nezdařila." Použití automatického škálování, můžete automaticky škálovat nebo škálovat počet instancí v typu prostředku podporované na základě času, den nebo zatížení (metriky) dat, na které se používá nastavení automatického škálování. Pokud jsou splněny podmínky škálování nahoru nebo dolů, spuštění a úspěšné nebo neúspěšné události se zaznamenávají v této kategorii.
* **Doporučení** – Tato kategorie obsahuje doporučení události z určité typy prostředků, jako jsou webové servery a servery SQL Server. Tyto události nabízet doporučení, jak lépe využívat zdroje. Pokud máte prostředky, které emitování doporučení pouze zobrazí události tohoto typu.
* **Zabezpečení** – Tato kategorie obsahuje záznam všech výstrah generovaných Azure Security Center. Je například typ události, které se zobrazí se v této kategorii "soubor podezřelé dvojité rozšíření spustit."
* **Zásady a stav prostředků** -tyto kategorie neobsahují žádné události; jsou vyhrazené pro budoucí použití.

## <a name="event-schema-per-category"></a>Schématu události podle kategorie
[V tématu Tento článek vám pomůže porozumět schématu aktivity protokolu události podle kategorie.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Co můžete dělat s protokolu aktivit
Tady jsou některé z akcí, které můžete provést pomocí protokolu aktivit:

![Azure protokol aktivit](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Dotazování a ji zobrazit v **portál Azure**.
* [Vytvořte výstrahu na aktivity protokolu události.](monitoring-activity-log-alerts.md)
* [Stream, aby **centra událostí** ](monitoring-stream-activity-logs-event-hubs.md) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.
* Analyzovat v Power BI pomocí [ **balíček obsahu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Ho uložit pro **účet úložiště** pro archivaci nebo ruční kontroly](monitoring-archive-activity-log.md). Můžete zadat uchování (ve dnech) pomocí **profil protokolu**.
* Dotaz je pomocí rutiny prostředí PowerShell, rozhraní příkazového řádku nebo REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Dotaz protokol aktivit na portálu Azure
V rámci portálu Azure můžete zobrazit aktivitu protokolu na několika místech:
* **Protokol aktivit** , může přistupovat tak, že protokol aktivit v části **všechny služby** v levém navigačním podokně.
* **Monitorování** se zobrazuje v levém navigačním podokně. Protokol aktivit je jeden oddíl Azure monitorování.
* Jakémukoli prostředku **prostředků**, například okno konfigurace pro virtuální počítač. Protokol aktivit je být jedna z částí většina těchto oken prostředků a kliknete na něm automaticky vyfiltruje události, které s ohledem na konkrétní prostředku.

Na portálu Azure můžete filtrovat aktivity protokolu podle těchto polí:
* Časový interval – počáteční a koncový čas pro události.
* Kategorie – kategorie události, jak je popsáno výše.
* Předplatné – jeden nebo více názvů předplatného Azure.
* Skupiny prostředků – jeden nebo více prostředků skupiny v rámci těchto předplatných.
* Prostředek (název) – název konkrétní prostředku.
* Typ prostředku – typ prostředku, například Microsoft.Compute/virtualmachines.
* Operace název – název Azure Resource Manager operace, například Microsoft.SQL/servers/Write.
* Závažnost – úroveň závažnosti události (Informativní upozornění, chyby, kritické).
* Událost iniciovaná - 'volajícího, nebo uživatel, který provádí operaci.
* Otevřete vyhledávání - Toto je již otevřete textového pole hledání, prohledáván všechna pole v všechny události pro tento řetězec.

Jakmile definujete sadu filtrů, můžete ho uložit jako dotaz, který je trvalé napříč relacemi, pokud byste někdy potřebovali provést stejný dotaz s tyto filtry znovu použít v budoucnu. Dotaz můžete taky připnout na řídicí panel Azure vždy dohlížet na konkrétní události.

Kliknutím na tlačítko "Použijí" spustí dotaz a všechny odpovídající události. Kliknutím na libovolnou událost v seznamu zobrazuje souhrn tuto událost, a také úplné nezpracovaném formátu JSON této události.

Pro více síly, můžete kliknout na **hledání protokolů** ikonu, která zobrazuje data protokolu aktivit z [řešení Log Analytics aktivity protokolu analýzy](../log-analytics/log-analytics-activity.md). Okno Protokol činnosti nabízí prostředí základní filtru nebo přejděte na protokoly, ale analýzy protokolů umožňuje otáčení, dotazovat a vizualizovat data výkonnější způsoby.

## <a name="export-the-activity-log-with-a-log-profile"></a>Export protokolu aktivit k profilu protokolu
A **profil protokolu** řídí, jak je export protokolu aktivit. Použití profilu protokolu, můžete nakonfigurovat:

* Kde by měly být odeslány protokol aktivit (účet úložiště nebo Event Hubs)
* Kategorie, které události (akce zápisu, odstranit,) by měly být odeslány. *Význam "kategorie" v profilech protokolu a aktivity protokolu událostí se liší. V profilu protokolu "Kategorie" představuje typ operace (akce zápisu, odstranit,). Vlastnost "kategorie" v aktivity protokolu události, představuje zdroj nebo typ události (například správy, ServiceHealth, upozornění a další).*
* Které oblasti (umístění) mají být exportovány. Nezapomeňte zahrnout "globální", protože mnoho událostí v protokolu aktivit jsou globální události.
* Jak dlouho se uchovávají v účtu úložiště protokol aktivit.
    - Uchování nulový počet dnů znamená, že jsou protokoly v nekonečné smyčce. Hodnota, jinak hodnota může být libovolný počet dnů od 1 do 2147483647.
    - Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pokud jenom jsou vybrané možnosti služby Event Hubs nebo OMS), zásady uchovávání informací nemají žádný vliv.
    - Zásady uchovávání informací jsou použité denní, takže na konci za den (UTC), protokoly dnem, který je teď nad rámec uchovávání se zásada odstraní. Například pokud jste měli zásady uchovávání informací jeden den, od začátku dnešní den protokoly z včerejšek před den by odstraněn.

Můžete použít úložiště účet nebo události rozbočovače obor názvů, který není ve stejném předplatném jako jeden emitování protokoly. Uživatel, který konfiguruje nastavení, musí mít odpovídající RBAC přístup na oba odběry.

Tato nastavení lze nakonfigurovat pomocí možnosti "Export" v okně Protokol aktivit na portálu. Je také možné nakonfigurovat prostřednictvím kódu programu [pomocí rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutiny prostředí PowerShell nebo rozhraní příkazového řádku. Předplatné může mít pouze jeden profil protokolu.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurace protokolu profilů pomocí portálu Azure
Můžete datového proudu protokolu aktivit do centra událostí nebo uložit je do účtu úložiště pomocí možnosti "Export" na webu Azure portal.

1. Přejděte na **protokol aktivit** pomocí nabídky na levé straně na portálu.

    ![Přejděte na protokol aktivit v portálu](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Klikněte **exportovat** tlačítka v horní části okna.

    ![Tlačítko Exportovat portálu](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. V okně, který se zobrazí můžete vybrat:  
  * oblasti, pro které chcete exportovat události
  * Účet úložiště, do které chcete uložit události
  * počet dní, které chcete uchovávat tyto události v úložišti. Nastavení 0 dnů uchovává protokoly navždy.
  * Namespace Service Bus, ve kterém chcete vytvořit pro streamování tyto události centra událostí.

     ![Export protokolu aktivit okno](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klikněte na tlačítko **Uložit** uložit tato nastavení. Nastavení se okamžitě použijí pro vaše předplatné.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurace protokolu profilů pomocí rutin prostředí PowerShell Azure
#### <a name="get-existing-log-profile"></a>Získat stávající profil protokolu
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Přidat profil protokolu
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| Název |Ano |Název vašeho profilu protokolu. |
| StorageAccountId |Ne |ID prostředku účtu úložiště, který má být uložen v protokolu aktivit. |
| serviceBusRuleId |Ne |ID pravidla sběrnice služby pro chcete mít centra událostí, které jsou vytvořené v oboru názvů Service Bus. Je řetězec s Tento formát: `{service bus resource ID}/authorizationrules/{key name}`. |
| Umístění |Ano |Seznam oddělený čárkami oblastí, pro které chcete shromažďovat aktivity protokolu události. |
| retentionInDays |Ano |Počet dní pro události, které by měl být zachován, od 1 do 2147483647. Hodnota nula ukládá protokoly bez omezení (navždy). |
| Kategorie |Ne |Seznam oddělený čárkami kategorií událostí, které by měl být shromážděny. Možné hodnoty jsou zápisu, odstranění a akce. |

#### <a name="remove-a-log-profile"></a>Odebrat profil protokolu
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Konfigurace protokolu profilů pomocí Azure CLI pro různé platformy
#### <a name="get-existing-log-profile"></a>Získat stávající profil protokolu
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` Vlastnost by měla být název pro váš profil protokolu.

#### <a name="add-a-log-profile"></a>Přidat profil protokolu
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| jméno |Ano |Název vašeho profilu protokolu. |
| storageId |Ne |ID prostředku účtu úložiště, který má být uložen v protokolu aktivit. |
| serviceBusRuleId |Ne |ID pravidla sběrnice služby pro chcete mít centra událostí, které jsou vytvořené v oboru názvů Service Bus. Je řetězec s Tento formát: `{service bus resource ID}/authorizationrules/{key name}`. |
| Umístění |Ano |Seznam oddělený čárkami oblastí, pro které chcete shromažďovat aktivity protokolu události. |
| retentionInDays |Ano |Počet dní pro události, které by měl být zachován, od 1 do 2147483647. Hodnota nula ukládá protokoly bez omezení (navždy). |
| Kategorie |Ne |Seznam oddělený čárkami kategorií událostí, které by měl být shromážděny. Možné hodnoty jsou zápisu, odstranění a akce. |

#### <a name="remove-a-log-profile"></a>Odebrat profil protokolu
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>Další kroky
* [Další informace o protokolu činnosti (dříve protokoly auditu)](../azure-resource-manager/resource-group-audit.md)
* [Datový proud protokolu Azure činnosti do centra událostí](monitoring-stream-activity-logs-event-hubs.md)
