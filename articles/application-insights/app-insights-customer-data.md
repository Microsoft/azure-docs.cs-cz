---
title: Pokyny pro osobní údaje uložené ve službě Azure Application Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat osobní údaje uložené v Azure Application Insights a metody pro identifikaci a jeho odebrání.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044709"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Pokyny pro osobní údaje uložené ve službě Application Insights

Application Insights je úložiště dat, kde je pravděpodobně najít osobní údaje. Tento článek popisuje, kde v Application Insights tato data se obvykle nachází, a také k dispozici funkce pro zpracování těchto dat.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro zpracování osobních údajů

Zatímco bude až vám a vaší společnosti nakonec určit strategii, pomocí kterého bude zpracovávat privátních dat (pokud vůbec), tady jsou některé možné přístupy. Jsou uvedeny v pořadí podle priority od technických hlediska od těch s nejvíce k nejméně vhodnější než:
* Kde je to možné, zastavte sadu, obfuskaci, anonymizovat nebo jinak upravit údajů vyloučíte z nepovažoval _privátní_. Tato metoda je oblíbený přístup, vás zbaví nutnosti vytvořit nákladné a působivé datové strategie pro zpracování.
* Je to možné, pokuste se normalizovat data, která mají dopad na datové platformy a výkonu. Například místo protokolování explicitní ID uživatele, vytvořte vyhledávací data, která se porovnat uživatelské jméno a jejich podrobnosti interní ID, které můžete protokolovat pak jinde. Tímto způsobem, pokud jeden z vašich uživatelů vás požádají o odstranění jejich osobní údaje, je možné pouze odstranění řádku ve vyhledávací tabulce, odpovídající uživatel bude mít dostatečná. 
* Proces kolem cestu vyprázdnění rozhraní API a existující rozhraní API cestu dotazu plnit povinnosti, pravděpodobně bude nutné po exportu a odstranění všech privátní dat přidružené k uživateli sestavení a nakonec, pokud je třeba shromažďovat osobní data.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Kde hledat soukromým datům v Application Insights?

Application Insights je zcela flexibilního úložiště, které při systematická schématu ke svým datům, umožňuje potlačit všechna pole s vlastními hodnotami. V důsledku toho je možné napsat přesně kde soukromá data budou nacházet v konkrétní aplikaci. Následujících umístěních, ale jsou dobré počáteční body ve vašem inventáři softwaru:

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

Je __důrazně__ nedoporučuje, pokud je to možné, změny struktury vaší zásady shromažďování údajů zakázat shromažďování dat soukromých, že maskuje nebo anonymizace, nebo jinak ji odebrat z považují za "privátní". Zpracování dat po byl shromážděných, bude mít za následek náklady, abyste mohli vy a váš tým definovat a automatizovat strategii, vytváření rozhraní pro vaše zákazníky k interakci s jejich daty prostřednictvím a náklady na průběžnou údržbu. Kromě toho je nákladné výpočetně pro Application Insights a velké množství souběžných dotazů nebo vymazání volání rozhraní API mohou mít potenciálně negativní dopad na všechny ostatní interakce s funkcemi Application Insights. Který ale nutné dodat, skutečně obsahoval několik platný scénářů, kde je třeba shromažďovat osobní data. Pro tyto případy by měl zpracovat data jak je popsáno v této části.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazení a export

Pro obě zobrazit a exportovat data žádosti [rozhraní API pro dotazy](https://dev.applicationinsights.io/quickstart) by měla sloužit. Logiku pro tvar data převést na odpovídající ten umožní vašim uživatelům budou jenom na vás k implementaci. [Služba Azure Functions](https://azure.microsoft.com/services/functions/) je skvělým místem pro hostování tuto logiku.

### <a name="delete"></a>Odstranění

> [!WARNING]
> Odstraní ve službě Application Insights jsou destruktivní a nevratná! Prosím buďte velmi opatrní při jejich provádění.

Vylepšili jsme k dispozici jako součást ochrany osobních údajů zpracování scénáře rozhraní API cestu "Vymazat". Tato cesta měly používat střídmě, protože riziko spojené s tím, potenciální dopad na výkon a potenciálně zkosení veškeré agregace, měření a další aspekty vašich dat Application Insights. Zobrazit [strategie pro zpracování osobních údajů](#strategy-for-personal-data-handling) výše uvedené části Alternativní přístupy ke zpracování dat soukromých.

Vymazání je vysoce privilegované operace, zda žádná aplikace nebo uživatele v Azure (včetně i vlastníka prostředku) bude mít oprávnění ke spouštění bez výslovně udělena role v Azure Resource Manageru. Tato role je _Data Purger_ a pečlivě kvůli potenciální ztráty dat.

Jakmile byla přiřazena role Azure Resource Manageru, dvě nové rozhraní API cesty jsou dokumentaci pro vývojáře k dispozici, úplný a volat propojený obrazec:

* [Po vymazání](https://docs.microsoft.com/rest/api/application-insights/components/purge) – určení parametrů dat. Chcete-li odstranit objekt převezme a vrátí odkaz na identifikátor GUID
* GET vyprázdnit stav – vyprázdnění volání POST tak vrátí hlavičku "x-ms stav – umístění", který bude obsahovat adresu URL, která můžete volat k určení stavu rozhraní purge API. Příklad:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Přestože většinu operací mazání může dokončení mnohem rychleji než SLA, a to kvůli jejich náročné dopad na datovou platformou používané službou Application Insights **formální smlouva SLA pro dokončení operace vyprázdnění nastavený na 30 dnů**.

## <a name="next-steps"></a>Další postup
Další informace o jak se data shromažďují, zpracování a zabezpečené, naleznete v tématu [zabezpečení dat ve službě Application Insights](app-insights-data-retention-privacy.md).