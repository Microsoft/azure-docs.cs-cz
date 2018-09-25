---
title: Pokyny pro osobní údaje uložené ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat osobní údaje uložené v Azure Log Analytics a metody pro identifikaci a jeho odebrání.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 9ea004a35f739a8c4f7ee1ed320bd6657ed4e820
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957910"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Pokyny pro osobní údaje uložené v Log Analytics a Application Insights

Log Analytics je úložiště dat, kde je pravděpodobně najít osobní údaje. Application Insights ukládá data do Log Analytics oddílu. Tento článek popisuje, kde v Log Analytics a Application Insights taková data se obvykle nachází, a také k dispozici funkce pro zpracování takových dat.

> [!NOTE]
> Pro účely tohoto článku _můžete vytvářet protokoly dat_ odkazuje na data odeslaná do pracovního prostoru Log Analytics, zatímco _data aplikací_ odkazuje na data shromážděná službou Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro zpracování osobních údajů

Zatímco bude až vám a vaší společnosti nakonec určit strategii, pomocí kterého bude zpracovávat privátních dat (pokud vůbec), tady jsou některé možné přístupy. Jsou uvedeny v pořadí podle priority od technických hlediska od těch s nejvíce k nejméně vhodnější než:

* Kde je to možné, zastavte sadu, obfuskaci, anonymizovat nebo jinak upravit údajů vyloučíte z považují za "privátní". Toto je _zdaleka_ oblíbený přístup, vás zbaví nutnosti vytvořit velmi nákladné a působivé datové strategie pro zpracování.
* Je to možné, pokuste se normalizovat data, která mají dopad na datové platformy a výkonu. Například místo protokolování explicitní ID uživatele, vytvořte vyhledávací data, která se porovnat uživatelské jméno a jejich podrobnosti interní ID, které můžete protokolovat pak jinde. Tímto způsobem by měl jeden z vašich uživatelů vás požádají o odstranění jejich osobní údaje, je možné, že pouze odstranění řádku ve vyhledávací tabulce, odpovídající uživatel bude stačit. 
* Proces kolem cestu vyprázdnění rozhraní API a existující rozhraní API cestu dotazu plnit povinnosti, pravděpodobně bude nutné po exportu a odstranění všech privátní dat přidružené k uživateli sestavení a nakonec, pokud je třeba shromažďovat osobní data. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Kde hledat soukromým datům v Log Analytics?

Log Analytics je flexibilní úložiště, které při systematická schématu ke svým datům, umožňuje potlačit všechna pole s vlastními hodnotami. Kromě toho je možné ingestovat žádné vlastní schéma. V důsledku toho je možné napsat přesně kde soukromým datům najdete v konkrétním pracovním prostorem. Následujících umístěních, ale jsou dobré počáteční body ve vašem inventáři softwaru:

### <a name="log-data"></a>Data protokolu

* *IP adresy*: Log Analytics shromažďuje širokou škálu informace o IP Adrese v mnoha různých tabulkách. Například následující dotaz zobrazí všechny tabulky, kde adresy IPv4 byly shromážděny během posledních 24 hodin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID uživatele*: ID uživatele se nacházejí v širokou škálu řešení a tabulky. Napříč celou datovou sadu pomocí příkazu vyhledávání, můžete vyhledávat konkrétní uživatelské jméno:
    ```
    search "[username goes here]"
    ```
Nezapomeňte se podívat nejen čitelné uživatelská jména, ale také identifikátory GUID, které lze přímo vysledovat zpět s určitým uživatelem!
* *ID zařízení*: jako ID uživatelů, zařízení identifikátory jsou někdy považována za "privátní". Použít stejným způsobem, jak je uvedeno výše pro ID uživatele identifikuje tabulky tam, kde to může být žádný problém. 
* *Vlastní data*: Log Analytics umožňuje kolekci v různých metodách: vlastní protokoly a vlastních polí [rozhraní API kolekce dat HTTP](log-analytics-data-collector-api.md) , a shromažďovat protokoly událostí systému v rámci vlastní data. Všechny z nich může docházet k obsahující soukromá data a by měla být prozkoumána ověřit, zda existuje žádná takováto data.
* *Řešení zachyceným datům*: vzhledem k tomu, že mechanismus řešení je některý z otevřený, doporučujeme zkontrolujete všechny tabulky generovaných řešení, aby zařízení dodržovalo předpisy.

### <a name="application-data"></a>Data aplikací

* *IP adresy*: zatímco Application Insights se ve výchozím nastavení obfuskaci všech polí IP adresy na hodnotu "0.0.0.0", je poměrně běžný vzor pro přepsání této hodnoty s IP Adresou skutečného uživatelského zachovat informace o relaci. Následující dotaz Analytics je možné najít žádné tabulky, který obsahuje hodnoty ve sloupci IP adresa než "0.0.0.0" za posledních 24 hodin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID uživatele*: ve výchozím nastavení, Application Insights používat náhodně generované identifikátory pro uživatele a sledování relace. Je však běžně setkat tato pole potlačena za účelem ukládání ID relevantnější pro aplikaci. Například: uživatelská jména, GUID AAD, atd. Tyto identifikátory jsou často považuje za v oboru jako osobní údaje a proto by měl zpracováno odpovídajícím způsobem. Naše doporučení je vždy pokusí obfuskaci nebo anonymizovat tyto identifikátory. Pole, ve kterém se běžně vyskytují tyto hodnoty patří session_Id, user_Id, user_AuthenticatedId, user_AccountId, jakož i customDimensions.
* *Vlastní data*: Application Insights umožňuje připojit sadu vlastní rozměry libovolného datového typu. Tyto dimenze může být *jakékoli* data. Použijte tento dotaz k identifikaci žádné vlastní dimenzi shromážděných za posledních 24 hodin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data v paměti a během přenosu*: Application Insights bude sledovat výjimky, požadavky, voláním závislostí a trasování. Soukromá data se můžou shromažďovat často v kódu a úroveň volání HTTP. Seznamte se s výjimky, požadavky, závislosti a trasování tabulky k identifikaci těchto údajů. Použití [telemetrie inicializátory](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) povedou obfuskací tato data.
* *Snapshot Debugger zachycení*: [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funkcí ve službě Application Insights umožňuje shromažďovat snímky ladění pokaždé, když je výjimka zachycena na produkční instance vaší aplikace. Snímky bude vystavovat úplné trasování zásobníku tak výjimky, stejně jako hodnoty pro místní proměnné při každém kroku v zásobníku. Bohužel tato funkce neumožňuje selektivní mazání bodů přichycení nebo programový přístup k datům v rámci snímku. Proto pokud je míra uchování snímků výchozí nesplňuje vaše požadavky na dodržování předpisů, doporučujeme vypnout funkci.

## <a name="how-to-export-and-delete-private-data"></a>Jak exportovat a odstranit osobní údaje

Jak je uvedeno v [strategie pro zpracování osobních údajů](#strategy-for-personal-data-handling) výše v části, je __důrazně__ doporučuje, když všechny možné změny struktury vaší zásady shromažďování údajů zakázat kolekce privátní dat, že maskuje anonymizace ho nebo jiné změny tak, odeberte ji ze považují za "privátní". Zpracování dat se předním výsledek náklady, abyste mohli vy a váš tým definovat a automatizovat strategii, vytváření rozhraní pro vaše zákazníky k interakci s jejich daty prostřednictvím a náklady na průběžnou údržbu. Kromě toho je nákladné výpočetně pro Log Analytics a Application Insights a velké množství souběžných dotazů nebo mazání volání rozhraní API mohou mít potenciálně negativní dopad na všechny interakce s funkce Log Analytics. Který ale nutné dodat, skutečně obsahoval několik platný scénářů, kde je třeba shromažďovat osobní data. Pro tyto případy by měl zpracovat data jak je popsáno v této části.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazení a export

Pro obě zobrazit a exportovat data žádosti [dotazování rozhraní API služby Log Analytics](https://dev.loganalytics.io/) nebo [dotazování rozhraní API služby Application Insights](https://dev.applicationinsights.io/quickstart) by měla sloužit. Logiku pro tvar data převést na odpovídající ten umožní vašim uživatelům budou jenom na vás k implementaci. [Služba Azure Functions](https://azure.microsoft.com/services/functions/) díky skvělým místem pro hostování tuto logiku.

### <a name="delete"></a>Odstranění

> [!WARNING]
> Odstraní v Log Analytics jsou destruktivní a nevratná! Prosím buďte velmi opatrní při jejich provádění.

Jsme zpřístupnili jako součást zpracování osobních údajů *vyprázdnit* cestu rozhraní API. Tato cesta měly používat střídmě, protože riziko spojené s tím, potenciální dopad na výkon a potenciálně zkosení veškeré agregace, měření a další aspekty vašich dat Log Analytics. Zobrazit [strategie pro zpracování osobních údajů](#strategy-for-personal-data-handling) části Alternativní přístupy ke zpracování dat soukromých.

Vymazání je vysoce privilegované operace, zda žádná aplikace nebo uživatele v Azure (včetně i vlastníka prostředku) bude mít oprávnění ke spouštění bez výslovně udělena role v Azure Resource Manageru. Tato role je _Data Purger_ a s rozvahou kvůli potenciální ztráty dat. 

Jakmile se přiřadila role Azure Resource Manageru, jsou k dispozici dvě nová rozhraní API cesty: 

#### <a name="log-data"></a>Data protokolu

* [Příspěvku vyprázdnění] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – určení parametrů dat. Chcete-li odstranit objekt převezme a vrátí odkaz na identifikátor GUID 
* GET vyprázdnit stav – vyprázdnění volání POST tak vrátí hlavičku "x-ms stav – umístění", který bude obsahovat adresu URL, která můžete volat k určení stavu rozhraní purge API. Příklad:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Zatímco Očekáváme, že drtivou většinu operací mazání mnohem rychleji než naše smlouva SLA, protože jejich náročné dopad na datovou platformu používá služba Log Analytics, dokončete **formální smlouva SLA pro dokončení operace vyprázdnění nastavený na 30 dnů**. 

#### <a name="application-data"></a>Data aplikací

* [Po vymazání](https://docs.microsoft.com/rest/api/application-insights/components/purge) – určení parametrů dat. Chcete-li odstranit objekt převezme a vrátí odkaz na identifikátor GUID
* GET vyprázdnit stav – vyprázdnění volání POST tak vrátí hlavičku "x-ms stav – umístění", který bude obsahovat adresu URL, která můžete volat k určení stavu rozhraní purge API. Příklad:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Přestože většinu operací mazání může dokončení mnohem rychleji než SLA, a to kvůli jejich náročné dopad na datovou platformou používané službou Application Insights **formální smlouva SLA pro dokončení operace vyprázdnění nastavený na 30 dnů**.

## <a name="next-steps"></a>Další postup
- Další informace o způsobu shromažďují, zpracování a zabezpečená data služby Log Analytics najdete v tématu [zabezpečení dat Log Analytics](log-analytics-data-security.md).
- Další informace o jak shromážděné, zpracované a zabezpečené data Application Insights najdete v tématu [zabezpečení dat ve službě Application Insights](../application-insights/app-insights-data-retention-privacy.md).