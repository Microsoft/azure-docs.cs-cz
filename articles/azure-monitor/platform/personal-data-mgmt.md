---
title: Pokyny pro osobní data uložená v Azure Log Analytics| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak spravovat osobní data uložená v Azure Log Analytics a metody k jejich identifikaci a odebrání.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248746"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Pokyny pro osobní údaje uložené v Log Analytics a Application Insights

Log Analytics je úložiště dat, kde je pravděpodobné, že budou nalezeny osobní údaje. Application Insights ukládá svá data do oddílu Log Analytics. Tento článek bude diskutovat o tom, kde v Log Analytics a Application Insights se obvykle nacházejí, stejně jako funkce, které máte k dispozici pro zpracování těchto dat.

> [!NOTE]
> Pro účely tohoto článku _data protokolu_ odkazuje na data odeslaná do pracovního prostoru Log Analytics, zatímco _data aplikace_ odkazuje na data shromážděná Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro nakládání s osobními údaji

I když bude na vás a vaší společnosti, abyste nakonec určili strategii, s níž budete zpracovávat vaše soukromá data (pokud vůbec), níže jsou některé možné přístupy. Jsou uvedeny v pořadí podle preference z technického hlediska z většiny do nejméně vhodnějších:

* Pokud je to možné, zastavte shromažďování, zamlžujte, anonymizovat nebo jinak upravte shromažďované údaje tak, aby byly vyloučeny z toho, aby byly považovány za "soukromé". To je _zdaleka_ preferovaný přístup, což vám ušetří potřebu vytvořit velmi nákladnou a působivou strategii zpracování dat.
* Pokud to není možné, pokuste se normalizovat data, abyste snížili dopad na datovou platformu a výkon. Například namísto protokolování explicitní id uživatele, vytvořte vyhledávací data, která budou korelovat uživatelské jméno a jejich podrobnosti na interní ID, které pak může být zaznamenána jinde. Tímto způsobem, pokud jeden z vašich uživatelů vás požádá o odstranění jejich osobních údajů, je možné, že bude stačit pouze odstranění řádku ve vyhledávací tabulce odpovídající uživateli. 
* Nakonec pokud musí být shromažďovány soukromá data, vytvořte proces kolem cesty vyčistit rozhraní API a existující cestu rozhraní API dotazu ke splnění všech povinností, které můžete mít kolem exportu a odstranění všech soukromých dat spojených s uživatelem. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Kde hledat soukromá data v Log Analytics?

Log Analytics je flexibilní úložiště, které při předepisování schématu vašim datům umožňuje přepsat každé pole vlastními hodnotami. Navíc může být ingestováno jakékoli vlastní schéma. Jako takové není možné přesně říci, kde budou soukromá data nalezena ve vašem konkrétním pracovním prostoru. Následující místa jsou však dobrými výchozími body ve vašem inventáři:

### <a name="log-data"></a>Protokolování dat

* *IP adresy*: Služba Log Analytics shromažďuje různé informace o IP adresách v mnoha různých tabulkách. Například následující dotaz zobrazuje všechny tabulky, kde byly shromážděny adresy IPv4 za posledních 24 hodin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID uživatelů*: ID uživatelů se nacházejí v široké škále řešení a tabulek. Konkrétní uživatelské jméno můžete vyhledat v celé datové sadě pomocí příkazu hledat:
    ```
    search "[username goes here]"
    ```
  Nezapomeňte se podívat nejen na lidská čitelná uživatelská jména, ale také na identifikátory GUID, které lze přímo vysledovat zpět k určitému uživateli!
* *ID zařízení*: Podobně jako ID uživatelů jsou ID zařízení někdy považována za "soukromá". Použijte stejný přístup jako výše uvedené pro ID uživatelů k identifikaci tabulek, kde to může být problém. 
* *Vlastní data*: Log Analytics umožňuje shromažďování v různých metod: vlastní protokoly a vlastní pole, [HTTP Kolektor dat API](../../azure-monitor/platform/data-collector-api.md) a vlastní data shromážděná jako součást protokolů systémových událostí. Všechny tyto údaje jsou náchylné k obsahující soukromé údaje a měly by být přezkoumány, aby se ověřilo, zda takové údaje existují.
* *Data se obsazená řešením*: Vzhledem k tomu, že mechanismus řešení je otevřený, doporučujeme zkontrolovat všechny tabulky generované řešeními, aby bylo zajištěno dodržování předpisů.

### <a name="application-data"></a>Data aplikací

* *IP adresy*: Zatímco Application Insights bude ve výchozím nastavení obfuscate všechna pole IP adresy na "0.0.0.0", je to poměrně běžný vzor přepsat tuto hodnotu se skutečnou IP adresu uživatele udržovat informace o relaci. Níže uvedený dotaz Analytics lze použít k vyhledání libovolné tabulky, která obsahuje hodnoty ve sloupci IP adresy kromě "0.0.0.0" za posledních 24 hodin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID uživatelů*: Ve výchozím nastavení budou application insights používat náhodně generovaná ID pro sledování uživatelů a relací. Je však běžné vidět tato pole přepsána uložit ID více relevantní pro aplikaci. Například: uživatelská jména, Identifikátory GUID AAD atd. Tato ID jsou často považována za osobní údaje, a proto by s nimi mělo být náležitě nakládáno. Naše doporučení je vždy pokusit se zamlžovat nebo anonymizovat tyto ID. Mezi pole, kde se tyto hodnoty běžně nacházejí, patří session_Id, user_Id, user_AuthenticatedId, user_AccountId a také vlastnídimenze.
* *Vlastní data*: Application Insights umožňuje připojit sadu vlastních dimenzí k libovolnému datovému typu. Tyto dimenze mohou být *libovolná* data. Následující dotaz slouží k identifikaci všech vlastních dimenzí shromážděných za posledních 24 hodin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data v paměti a na cestě*: Application Insights bude sledovat výjimky, požadavky, volání závislostí a trasování. Soukromá data mohou být často shromažďovány na úrovni volání kódu a HTTP. Zkontrolujte výjimky, požadavky, závislosti a trasování tabulek k identifikaci těchto dat. Pokud je to možné, použijte [telemetrické inicializátory,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) kde je to možné, k zamlžení těchto dat.
* *Snímek Debugger zachycuje:* [Funkce ladicí program snímek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) v Application Insights umožňuje shromažďovat snímky ladění vždy, když je výjimka zachycena v produkční instanci vaší aplikace. Snímky zpřístupní trasování celézásobníku vedoucí k výjimky, stejně jako hodnoty pro místní proměnné na každém kroku v zásobníku. Tato funkce bohužel neumožňuje selektivní odstranění bodů přichycení nebo programový přístup k datům v rámci snímku. Proto pokud výchozí rychlost uchování snímek nesplňuje vaše požadavky na dodržování předpisů, doporučení je vypnout funkci.

## <a name="how-to-export-and-delete-private-data"></a>Jak exportovat a odstranit soukromá data

Jak již bylo zmíněno v části [strategie pro zpracování osobních údajů](#strategy-for-personal-data-handling) dříve, __důrazně__ doporučujeme, aby pokud je to možné, restrukturalizovali zásady shromažďování údajů tak, aby bylo zakázáno shromažďování soukromých údajů, zamlžováno nebo anonymizováno nebo jinak upravováno tak, aby byly považovány za "soukromé". Zpracování dat bude mít za následek především náklady pro vás a váš tým definovat a automatizovat strategii, vytvořit rozhraní pro vaše zákazníky pro interakci s jejich daty prostřednictvím, a průběžné náklady na údržbu. Dále je výpočetní nákladné pro Log Analytics a Application Insights a velký objem souběžných dotazů nebo vymazání volání rozhraní API mají potenciál negativně ovlivnit všechny ostatní interakce s log analytics funkce. To znamená, že skutečně existují některé platné scénáře, kde musí být shromažďovány soukromé údaje. V těchto případech by měly být údaje zpracovány tak, jak je popsáno v této části.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazení a export

Pro požadavky na zobrazení i export dat by se mělo použít [rozhraní API dotazu analýzy protokolů](https://dev.loganalytics.io/) nebo [rozhraní API pro dotaz Application Insights.](https://dev.applicationinsights.io/quickstart) Logika převést tvar dat na odpovídající k doručení uživatelům bude na vás k implementaci. [Azure Functions](https://azure.microsoft.com/services/functions/) je skvělé místo pro hostování takové logiky.

> [!IMPORTANT]
>  Zatímco drtivá většina operací čištění může být dokončena mnohem rychleji než sla, **formální sla pro dokončení operací čištění je nastavena na 30 dní** kvůli jejich silnému dopadu na použitou datovou platformu. Jedná se o automatizovaný proces; neexistuje žádný způsob, jak požádat, aby operace byla zpracována rychleji.

### <a name="delete"></a>Odstranění

> [!WARNING]
> Odstraní v Log Analytics jsou destruktivní a non-reverzibilní! Při jejich provádění buďte velmi opatrní.

Zpřístupnili jsme jako součást ochrany osobních údajů, která zpracovává cestu *rozhraní API pro vymazání.* Tato cesta by měla být používána střídmě vzhledem k riziku s tím spojené, potenciální dopad na výkon a potenciál zkosení všech agregací, měření a další aspekty dat Log Analytics. Alternativní přístupy k nakládání se soukromými údaji naleznete v části [Strategie pro zpracování osobních údajů.](#strategy-for-personal-data-handling)

Vyčistit je vysoce privilegované operace, které žádná aplikace nebo uživatel v Azure (včetně i vlastníka prostředku) bude mít oprávnění ke spuštění, aniž by explicitně udělena role ve Správci prostředků Azure. Tato role je _vyprázdnění dat_ a měla by být opatrně delegována z důvodu potenciální ztráty dat. 

> [!IMPORTANT]
> Za účelem správy systémových prostředků jsou požadavky na vymazání omezeny na 50 požadavků za hodinu. Měli byste dávkové spuštění žádostí o vymazání odesláním jednoho příkazu, jehož predikát zahrnuje všechny identity uživatele, které vyžadují vymazání. Pomocí [operátoru in](/azure/kusto/query/inoperator) zadejte více identit. Dotaz byste měli spustit před spuštěním požadavku na vymazání a ověřit, zda jsou očekávané výsledky. 



Po přiřazení role Správce prostředků Azure jsou k dispozici dvě nové cesty rozhraní API: 

#### <a name="log-data"></a>Protokolování dat

* [Post purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - trvá objekt určující parametry dat odstranit a vrátí referenční identifikátor GUID 
* GET vyčištění stavu - post očištění volání vrátí hlavičku 'x-ms-status-location', která bude obsahovat adresu URL, kterou můžete volat k určení stavu vašeho vyčištění ROZHRANÍ API. Například:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Zatímco očekáváme, že drtivá většina operací čištění dokončit mnohem rychleji než naše SLA, vzhledem k jejich těžký dopad na datové platformě používané Log Analytics, **formální SLA pro dokončení operací čištění je nastavena na 30 dní**. 

#### <a name="application-data"></a>Data aplikací

* [Post purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - trvá objekt určující parametry dat odstranit a vrátí referenční identifikátor GUID
* GET vyčištění stavu - post očištění volání vrátí hlavičku 'x-ms-status-location', která bude obsahovat adresu URL, kterou můžete volat k určení stavu vašeho vyčištění ROZHRANÍ API. Například:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Zatímco drtivá většina operací pročištění může být dokončena mnohem rychleji než sla, vzhledem k jejich silnému dopadu na datovou platformu používanou application insights **je formální sla pro dokončení operací pročištění nastavena na 30 dní**.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak se shromažďují, zpracovávají a zabezpečují data analýzy protokolů, najdete v [tématu Zabezpečení dat analýzy protokolů](../../azure-monitor/platform/data-security.md).
- Další informace o tom, jak se shromažďují, zpracovávají a zabezpečují data Application Insights, najdete v [tématu Zabezpečení dat Application Insights](../../azure-monitor/app/data-retention-privacy.md).
