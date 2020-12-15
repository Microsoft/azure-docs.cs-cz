---
title: Doprovodné materiály k osobním údajům uloženým v Azure Log Analytics | Microsoft Docs
description: Tento článek popisuje, jak spravovat osobní údaje uložené v Azure Log Analytics a metody pro jejich identifikaci a odebrání.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 2bb1e667758a1430e34d222b9a5c537381c07624
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505269"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Doprovodné materiály pro osobní údaje uložené ve službách Log Analytics a Application Insights

Log Analytics je úložiště dat, kde se pravděpodobně nacházejí osobní údaje. Application Insights ukládá data do oddílu Log Analytics. Tento článek pojednává o tom, kde v Log Analytics a Application Insights taková data se obvykle nacházejí, a možnosti, které jsou k dispozici pro zpracování takových dat.

> [!NOTE]
> Pro účely tohoto článku se _data protokolů_ vztahují na data odesílaná do log Analyticsho pracovního prostoru, zatímco _data aplikací_ odkazují na data shromážděná pomocí Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro zpracování osobních údajů

I když bude vaše společnost až do vaší společnosti, abyste nakonec určili strategii, se kterou budete pokládat vaše privátní data (pokud vůbec), jsou zde některé možné přístupy. Jsou uvedené v pořadí podle priority z technického hlediska od nejmenšího po nejvýhodnějších:

* Pokud je to možné, zastavte shromažďování, zmatení, anonymizovat nebo jinak upravte shromažďovaná data, aby se vyloučila, aby se považovala za "soukromá". _To je_ nejjednodušeji upřednostňovaný přístup, takže ušetříte potřebu vytvořit velmi náročnou a ovlivněnou strategii zpracování dat.
* Pokud není to možné, pokuste se normalizovat data a snížit dopad na datovou platformu a výkon. Například místo protokolování explicitního ID uživatele vytvořte vyhledávací data, která budou korelovat uživatelské jméno a jejich podrobnosti s interním ID, které lze následně zaznamenat jinam. To znamená, že pokud jeden z vašich uživatelů požaduje odstranění jejich osobních údajů, je možné, že bude stačit jenom odstranit řádek v tabulce vyhledávání odpovídající tomuto uživateli. 
* Nakonec, pokud musí být shromážděna soukromá data, sestavte proces kolem cesty rozhraní API pro vyprázdnění a stávající cestu rozhraní API pro dotaz tak, aby splňovala jakékoli závazky, které byste mohli vyexportovat a odstranit všechna soukromá data přidružená k uživateli. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Kde hledat soukromá data v Log Analytics?

Log Analytics je flexibilní úložiště, které při stanovení schématu pro vaše data umožňuje přepsat každé pole vlastními hodnotami. Kromě toho lze ingestovat libovolné vlastní schéma. V takovém případě není možné přesně vyslovit, kde se v konkrétním pracovním prostoru budou nacházet privátní data. V následujících umístěních jsou však vhodné počáteční body v inventáři:

### <a name="log-data"></a>Protokolování dat

* *IP adresy*: Log Analytics shromažďuje nejrůznější informace o IP adresách napříč mnoha různými tabulkami. Například následující dotaz zobrazí všechny tabulky, ve kterých byly za posledních 24 hodin shromážděny adresy IPv4:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID uživatelů*: ID uživatelů se nachází ve velkých různých řešeních a tabulkách. Můžete vyhledat konkrétní uživatelské jméno napříč celou datovou sadou pomocí příkazu search:
    ```
    search "[username goes here]"
    ```
  Nezapomeňte se podívat nejen na uživatelsky čitelné uživatelské jméno, ale také identifikátory GUID, které se dají přímo trasovat na konkrétního uživatele.
* *ID zařízení*: jako ID uživatelů se někdy považují ID zařízení za "soukromé". Použijte stejný přístup, jak je uvedeno výše pro ID uživatelů, a Identifikujte tak tabulky, kde se to může týkat. 
* *Vlastní data*: Log Analytics umožňuje shromažďování v různých metodách: vlastní protokoly a vlastní pole, [rozhraní API kolekce dat http](./data-collector-api.md) a vlastní data shromážděná jako součást protokolů systémových událostí. Všechny tyto jsou náchylné k obsahující privátní data a měli byste je prozkoumat, abyste ověřili, jestli taková data existují.
* *Zachycená data řešení*: vzhledem k tomu, že mechanismus řešení je otevřený – ukončený, doporučujeme zkontrolovat všechny tabulky vygenerované řešeními, abyste zajistili dodržování předpisů.

### <a name="application-data"></a>Data aplikací

* *IP adresy*: i když Application Insights bude ve výchozím nastavení zastarat všechna pole IP adres na "0.0.0.0", jedná se o poměrně běžný vzor pro přepsání této hodnoty skutečnou UŽIVATELskou IP adresou, aby bylo možné zachovat informace o relaci. Analytický dotaz níže lze použít k vyhledání jakékoli tabulky, která obsahuje hodnoty ve sloupci IP adresa (kromě "0.0.0.0" za posledních 24 hodin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID uživatelů*: ve výchozím nastavení Application Insights použijí náhodně generované identifikátory pro sledování uživatelů a relací. Je však běžné, že jsou tato pole přepsána pro uložení ID, které je pro aplikaci relevantní. Příklad: uživatelská jména, identifikátory GUID AAD atd. Tato ID se často považují za v oboru jako osobní údaje, a proto by se měla odpovídajícím způsobem zpracovat. Naše doporučení se vždycky snaží zaanonymizovatovat nebo pořídit tato ID. Pole, kde se tyto hodnoty běžně vyskytují, zahrnují session_Id, user_Id, user_AuthenticatedId, user_AccountId a také customDimensions.
* *Vlastní data*: Application Insights umožňuje připojit sadu vlastních dimenzí k jakémukoli datovému typu. Tyto dimenze můžou být *libovolná* data. Pomocí následujícího dotazu identifikujte všechny vlastní dimenze shromážděné za posledních 24 hodin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data v paměti a přenosu*: Application Insights budou sledovat výjimky, požadavky, volání závislostí a trasování. Soukromá data je často možné shromažďovat na úrovni kódu a volání HTTP. Projděte si tabulky výjimky, požadavky, závislosti a trasování a Identifikujte taková data. Použijte [Inicializátory telemetrie](../app/api-filtering-sampling.md) tam, kde je to možné, k dezmatení těchto dat.
* *Snapshot Debugger zachycení*: funkce [Snapshot Debugger](../app/snapshot-debugger.md) v Application Insights umožňuje shromažďovat snímky ladění vždy, když je v produkční instanci aplikace zachycena výjimka. Snímky vystaví kompletní trasování zásobníku, které vede k výjimkám, a také hodnoty pro lokální proměnné v každém kroku v zásobníku. Tato funkce bohužel neumožňuje selektivní odstranění bodů přichycení nebo programový přístup k datům ve snímku. Proto pokud výchozí míra uchování snímku nesplňuje požadavky na dodržování předpisů, doporučujeme tuto funkci vypnout.

## <a name="how-to-export-and-delete-private-data"></a>Jak exportovat a odstranit soukromá data

Jak bylo zmíněno v předchozí [strategii pro zpracování osobních údajů](#strategy-for-personal-data-handling) , __důrazně__ se doporučuje, pokud je to možné, aby se změnila Struktura zásad shromažďování dat, aby se zakázala shromažďování privátních dat, její zatřídění nebo anonymizace, nebo jinak upravovat, aby se odstranila z hlediska "soukromé". Manipulace s daty bude mít za následek, že budou mít náklady na vás a váš tým k definování a automatizaci strategie, vytvoření rozhraní pro vaše zákazníky, aby mohli pracovat s daty a průběžnými náklady na údržbu. Kromě toho je pro Log Analytics a Application Insights výpočetně nákladné a velký objem souběžných volání rozhraní API dotazů nebo vyprázdnění může mít negativní dopad na veškerou interakci s Log Analyticsmi funkcemi. V takovém případě existují některé platné scénáře, ve kterých musí být shromažďována soukromá data. V těchto případech by měla být data zpracována způsobem popsaným v této části.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazit a exportovat

Pro požadavky na zobrazení a export dat by se měla použít rozhraní [API pro Log Analytics dotazů](https://dev.loganalytics.io/) nebo  [rozhraní API pro Application Insights dotazování](https://dev.applicationinsights.io/quickstart) . K implementaci můžete použít logiku pro převod tvaru dat na příslušný objekt, který bude poskytovat vašim uživatelům. [Azure Functions](https://azure.microsoft.com/services/functions/) představuje pro hostování takové logiky Skvělé místo.

> [!IMPORTANT]
>  I když velká většina operací vyprázdnění může trvat mnohem rychlejší než smlouva SLA, **je formální smlouva SLA pro dokončení operací vyprázdnění nastavená na 30 dní** kvůli jejich těžkému dopadu na využitou datovou platformu. Tato smlouva SLA splňuje požadavky na GDPR. Jedná se o automatizovaný proces, takže neexistuje žádný způsob, jak vyžádat zpracování operace rychleji. 

### <a name="delete"></a>Odstranit

> [!WARNING]
> Odstranění v Log Analytics jsou destruktivní a nevratná! Při jejich provádění prosím buďte velmi opatrní.

K dispozici jako součást ochrany osobních údajů, která zpracovává cestu rozhraní API pro *vyprázdnění* . Tato cesta by se měla používat zřídka, protože rizika spojená s tím spojené s tím, jaký je potenciální dopad na výkon, a potenciál pro zkosení všech agregací, měření a dalších aspektů vašich Log Analytics dat. Alternativní přístupy k manipulaci s privátními daty najdete v části [strategie pro zpracování osobních údajů](#strategy-for-personal-data-handling) .

> [!NOTE]
> Po provedení operace vyprázdnění nelze k datům přistupovat, pokud je [stav operace vyprázdnění](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/getpurgestatus) *čeká na vyřízení*. 

Vyprázdnit je vysoce privilegovaná operace, kterou žádná aplikace ani uživatel v Azure (včetně ani vlastníka prostředku) bude mít oprávnění ke spuštění bez explicitního udělení role v Azure Resource Manager. Tato role je modul pro _vyprázdnění dat_ a měla by být řádně delegovaná kvůli možné ztrátě dat. 

> [!IMPORTANT]
> Aby bylo možné spravovat systémové prostředky, požadavky na vyprázdnění jsou omezeny na 50 požadavků za hodinu. Měli byste dávkovat provádění požadavků na vymazání odesláním jednoho příkazu, jehož predikát zahrnuje všechny identity uživatelů, které vyžadují vyprázdnění. Použijte [operátor in](/azure/kusto/query/inoperator) k určení více identit. Před spuštěním žádosti o vymazání byste měli spustit dotaz, abyste ověřili, že jsou očekávané výsledky. 



Po přiřazení role Azure Resource Manager jsou k dispozici dvě nové cesty rozhraní API: 

#### <a name="log-data"></a>Protokolování dat

* [Post](/rest/api/loganalytics/workspacepurge/purge) Return-převezme objekt určující parametry dat, které se mají odstranit, a vrátí identifikátor GUID odkazu. 
* ZÍSKAT stav vyčištění – volání po vyprázdnění vrátí hlavičku x-MS-status-Location, která bude obsahovat adresu URL, kterou můžete zavolat k určení stavu rozhraní API pro vyprázdnění. Například:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Očekáváme, že velká většina operací vyprázdnění pro dokončení mnohem rychlejší, než je naše smlouva SLA, vzhledem k jejich těžkému dopadu na datovou platformu, kterou používá Log Analytics, **je formální smlouva SLA pro dokončení operací vyprázdnění nastavená na 30 dnů**. 

#### <a name="application-data"></a>Data aplikací

* [Post](/rest/api/application-insights/components/purge) Return-převezme objekt určující parametry dat, které se mají odstranit, a vrátí identifikátor GUID odkazu.
* ZÍSKAT stav vyčištění – volání po vyprázdnění vrátí hlavičku x-MS-status-Location, která bude obsahovat adresu URL, kterou můžete zavolat k určení stavu rozhraní API pro vyprázdnění. Například:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  I když velká většina operací vyprázdnění může dokončit mnohem rychlejší, než je smlouva SLA, vzhledem k jejich těžkému dopadu na datovou platformu, kterou používá Application Insights, **je formální smlouva SLA pro dokončení operací vyprázdnění nastavená na 30 dnů**.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak se shromažďují, zpracovávají a zabezpečují Log Analytics data, najdete v článku [Log Analytics zabezpečení dat](./data-security.md).
- Další informace o tom, jak se shromažďují, zpracovávají a zabezpečují Application Insights data, najdete v článku [Application Insights zabezpečení dat](../app/data-retention-privacy.md).

