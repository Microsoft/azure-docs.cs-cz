---
title: Dotazy auditu v Azure Monitor dotazy protokolu
description: Podrobnosti o protokolech auditu dotazů protokolu, které poskytují telemetrii o dotazech protokolů spouštěných v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 7ce0aea6bb257f0a52a843ce53cc904ed0a775dd
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536196"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Auditovat dotazy v protokolech Azure Monitor (Preview)
Protokoly auditu dotazů na protokol poskytují telemetrii o dotazech protokolů spouštěných v Azure Monitor. Obsahuje například informace o tom, kdy se dotaz spustil, kdo ho spustil, jaký nástroj se použil, text dotazu a Statistika výkonu popisující spuštění dotazu.


## <a name="configure-query-auditing"></a>Konfigurace auditování dotazů
Auditování dotazů je povoleno s [nastavením diagnostiky](../platform/diagnostic-settings.md) v pracovním prostoru Log Analytics. To vám umožní posílat data auditu do aktuálního pracovního prostoru nebo jakéhokoli jiného pracovního prostoru v rámci předplatného, do Azure Event Hubs posílat mimo Azure nebo Azure Storage k archivaci. 

### <a name="azure-portal"></a>portál Azure
Přístup k nastavení diagnostiky pro Log Analytics pracovní prostor v Azure Portal v jednom z následujících umístění:

- V nabídce **Azure monitor** vyberte **nastavení diagnostiky** a pak vyhledejte a vyberte pracovní prostor.

    [![Nastavení diagnostiky Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png)](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- V nabídce **Log Analytics pracovní prostory** vyberte pracovní prostor a pak vyberte **nastavení diagnostiky**.

    [![Nastavení diagnostiky Log Analytics pracovní prostor ](media/log-query-audit/diagnostic-setting-workspace.png)](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Šablona Resource Manageru
Můžete získat příklad Správce prostředků šablonu z [nastavení diagnostiky pro Log Analytics pracovní prostor](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Data auditu
Záznam auditu se vytvoří pokaždé, když se spustí dotaz. Pokud data odešlete do pracovního prostoru Log Analytics, uloží se do tabulky s názvem *LAQueryLogs*. Následující tabulka popisuje vlastnosti v jednotlivých záznamech dat auditu.

| Pole | Popis |
|:---|:---|
| TimeGenerated         | Čas UTC při odeslání dotazu. |
| CorrelationId         | Jedinečné ID pro identifikaci dotazu. Dá se použít při řešení potíží při kontaktování Microsoftu o pomoc. |
| AADObjectId           | Azure Active Directory ID uživatelského účtu, který spustil dotaz  |
| AADTenantId           | ID tenanta uživatelského účtu, který spustil dotaz  |
| AADEmail              | E-mail tenanta uživatelského účtu, který spustil dotaz  |
| AADClientId           | ID a vyřešený název aplikace použité ke spuštění dotazu. |
| RequestClientApp      | Vyřešený název aplikace použitý ke spuštění dotazu. |
| QueryTimeRangeStart   | Začátek časového rozsahu vybraného pro dotaz Tato hodnota nemusí být naplněna v některých případech, například když je dotaz spuštěn z Log Analytics a v dotazu není zadán časový rozsah místo pro výběr času. |
| QueryTimeRangeEnd     | Konec časového rozsahu vybraného pro dotaz Tato hodnota nemusí být naplněna v některých případech, například když je dotaz spuštěn z Log Analytics a v dotazu není zadán časový rozsah místo pro výběr času.  |
| QueryText             | Text dotazu, který byl spuštěn. |
| RequestTarget         | Adresa URL rozhraní API se použila k odeslání dotazu.  |
| Třída requestContext        | Seznam prostředků, na které byl dotaz požádán ke spuštění. Obsahuje až tři řetězcová pole: pracovní prostory, aplikace a prostředky. V rámci předplatného nebo skupiny prostředků se budou zobrazovat cílené dotazy jako *prostředky*. Zahrnuje cíl odvozený pomocí RequestTarget.<br>ID prostředku pro každý prostředek bude zahrnuto, pokud je možné ho vyřešit. Pokud se při přístupu k prostředku vrátí chyba, nemusí být možné ho vyřešit. V takovém případě bude použit konkrétní text z dotazu.<br>Pokud dotaz používá nejednoznačný název, například název pracovního prostoru existující ve více předplatných, bude použit tento dvojznačný název. |
| RequestContextFilters | Sada filtrů zadaná jako součást vyvolání dotazu. Zahrnuje až tři možná pole řetězců:<br>-ResourceType-typ prostředku pro omezení oboru dotazu<br>-Pracovní prostory – seznam pracovních prostorů, na které se má dotaz omezit<br>-WorkspaceRegions-seznam oblastí pracovního prostoru pro omezení dotazu |
| ResponseCode          | Kód odpovědi HTTP vrácený při odeslání dotazu. |
| ResponseDurationMs    | Čas, který má být vrácen pro odpověď.  |
| ResponseRowCount     | Celkový počet řádků vrácených dotazem. |
| StatsCPUTimeMs       | Celkový výpočetní čas, který se používá pro výpočet, analýzu a načítání dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedKB | Množství dat, ke kterým byl přidaný dotaz zpracován. Ovlivněno velikostí cílové tabulky, použitým časovým rozsahem, použitými filtry a počtem odkazovaných sloupců. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedStart | Čas nejstarší dat, ke kterým byl přidaný dotaz zpracován. Ovlivněno explicitním časovým rozsahem dotazu a použitými filtry. To může být větší než explicitní časové období z důvodu dělení dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsDataProcessedEnd  |Čas nejnovějších dat, ke kterým došlo při zpracování dotazu. Ovlivněno explicitním časovým rozsahem dotazu a použitými filtry. To může být větší než explicitní časové období z důvodu dělení dat. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsWorkspaceCount | Počet pracovních prostorů, ke kterým dotaz přistupoval. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |
| StatsRegionCount | Počet oblastí, ke kterým dotaz přistupoval. Vyplní se jenom v případě, že dotaz vrátí stavový kód 200. |

## <a name="considerations"></a>Požadavky

- Dotazy jsou protokolovány pouze při spuštění v kontextu uživatele. V rámci Azure nebude zaznamenána žádná služba pro službu. Dvě primární sady dotazů, které toto vyloučení zahrnuje, jsou výpočty fakturace a automatizované provádění výstrah. V případě výstrah se neprotokoluje pouze plánovaný dotaz výstrahy. počáteční spuštění výstrahy na obrazovce pro vytvoření výstrahy se provádí v uživatelském kontextu a bude k dispozici pro účely auditu. 
- Statistiky výkonu nejsou k dispozici pro dotazy přicházející z proxy serveru Azure Průzkumník dat. Všechna ostatní data pro tyto dotazy budou i nadále naplněna.
- Pomocný parametr *h* v řetězcích, který zapisuje [řetězcové literály](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) , nebude mít vliv na protokoly auditu dotazů. Dotazy budou zachyceny přesně tak, jak byly odeslány bez řetězce, který je zakódován. Měli byste zajistit, aby tato data mohli používat jenom uživatelé, kteří mají oprávnění k dodržování předpisů, a to pomocí různých režimů Kubernetes RBAC nebo Azure RBAC, které jsou dostupné v Log Analytics pracovních prostorech.
- Dotazy, které obsahují data z několika pracovních prostorů, budou zachyceny pouze v těch pracovních prostorech, ke kterým má uživatel přístup.

## <a name="costs"></a>Náklady  
Pro diagnostické rozšíření Azure se neúčtují žádné náklady, ale pro ingestování dat vám můžou být účtovány poplatky. Podívejte se na [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/) pro cíl, kde shromažďujete data.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [nastavení diagnostiky](../platform/diagnostic-settings.md).
- Přečtěte si další informace o [optimalizaci dotazů protokolu](query-optimization.md).
