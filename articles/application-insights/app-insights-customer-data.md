---
title: Pokyny pro osobní data uložená ve službě Azure Application Insights | Microsoft Docs
description: Tento článek popisuje postup správy osobních dat uložených v Azure Application Insights a metody pro identifikaci a jeho odebrání.
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
ms.openlocfilehash: 95e421278b46015e761764792e11dec0351b9785
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294417"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Pokyny pro osobní data uložená ve službě Application Insights

Application Insights je datové úložiště, kde je pravděpodobné, která se má najít osobní data. Tento článek popisuje, kde ve službě Application Insights tato data se obvykle nachází, a také možností dostupných pro zpracování tato data.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro zpracování osobní data

Zatímco bude až vás a vaši společnost nakonec určení strategie, pomocí kterého bude zpracovávat vaší privátní dat (pokud vůbec), níže jsou uvedeny některé možné přístupy. Jsou uvedeny v pořadí podle preference z technického hlediska z většinu k nejméně vhodnější než:
* Kde je to možné, zastavte kolekce, obfuskováním, změnit na anonymní nebo v opačném případě upravte dat shromažďovaných vyloučíte z za _privátní_. Tato metoda je žádoucí, ukládání, jste museli vytvářet nákladná a zvládat data zpracování strategie.
* Není to možné, pokuste se normalizují data pro snížení vlivu na platformě, dat a výkonu. Například místo protokolování explicitní ID uživatele, vytvořte vyhledávání pro data, která bude korelovat uživatelské jméno a jejich podrobnosti interní ID, které může být poté protokolovaná jinde. Tímto způsobem: Pokud jeden z vašich uživatelů požádáni, abyste odstranit jejich osobní údaje, je možné, že pouze odstranění řádku ve vyhledávací tabulce odpovídající uživatele bude dostatečná. 
* Pokud je třeba shromažďovat osobní data, nakonec sestavte proces kolem cesta k vyprázdnění rozhraní API a existující cestu rozhraní API dotazu plnit žádné závazky, které by mohly být kolem export a odstranění všechna privátní data související s uživatelem.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Kde hledat privátní data ve službě Application Insights?

Application Insights je zcela flexibilní úložiště, které při předepisující schéma pro data, umožňuje potlačit každé pole s vlastní hodnoty. Jako takový není možné, že přesně kde privátní data budou nacházet v konkrétní aplikaci. V následujících umístěních a jsou však dobré počáteční body ve vašem inventáři softwaru:

* *IP adresy*: při Application Insights se ve výchozím nastavení obfuskováním všech polí IP adresy na hodnotu "0.0.0.0", protože je celkem běžné vzor přepsat tuto hodnotu s IP Adresou skutečného uživatelského zachování informací o relaci. Analýza dotaz níže můžete použít k vyhledání žádné tabulky, který obsahuje hodnoty ve sloupci IP adresa než "0.0.0.0" během posledních 24 hodin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID uživatelů*: ve výchozím nastavení, použije Application Insights náhodně generované ID pro uživatele a sledování relace. Je však běžně setkat těchto polí potlačena za účelem ukládání ID více relevantní pro aplikaci. Příklad: uživatelských jmen, identifikátory GUID AAD, atd. Tyto identifikátory se často považuje za umístěný v oboru jako osobní data a je třeba proto s odpovídajícím způsobem. Naše doporučení se vždy pokusí obfuskováním nebo změnit na anonymní tyto identifikátory. Kde jsou tyto hodnoty běžně nalezena pole zahrnují session_Id, funkce user_Id, user_AuthenticatedId, user_AccountId, jakož i customDimensions.
* *Vlastní data*: Application Insights umožňuje připojit k libovolnému typu dat sadu vlastní dimenze. Tyto dimenze může být *žádné* data. Všechny vlastní dimenze shromážděných za posledních 24 hodin pomocí následujícího dotazu:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data v paměti a během přenosu*: Application Insights bude sledovat výjimky, požadavků, závislosti volání a trasování. Často dají shromažďovat privátní data na úrovni volání protokolu HTTP a kódu. Zkontrolujte výjimky, požadavků, závislosti a trasování tabulky k identifikaci taková data. Použití [telemetrie inicializátory](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) kde je to možné obfuskováním tato data.
* *Ladicí program zachycení snímku*: [ladicí program snímku](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funkce ve službě Application Insights umožňuje shromažďovat snímky ladění vždy, když je výjimka zachycena v produkční instanci aplikace. Snímky zveřejní trasování zásobníku úplné úvodní výjimky a také hodnoty pro místní proměnné v každé fázi v zásobníku. Bohužel se tato funkce není povolena pro selektivní mazání snap body nebo programový přístup k datům v rámci snímku. Proto pokud výchozí snímku uchování rychlost nesplňuje požadavků na dodržování předpisů, doporučuje se vypnout funkci.

## <a name="how-to-export-and-delete-private-data"></a>Export a Odstranit privátní data

Je __důrazně__ nedoporučuje, pokud to možné, změny struktury vaší shromažďování údajů zakázání shromažďování soukromých dat. zmatení data nebo zavedení anonymity, nebo jinak ji odeberte z za "privátní". Zpracování dat po jeho byla shromažďovat, bude mít za následek náklady na vy a váš tým k definování a automatizaci strategie, sestavení rozhraní pro vaše zákazníky k interakci s jejich daty prostřednictvím a náklady na průběžnou údržbu. Navíc je u nákladná Application Insights a velkého objemu souběžných dotazů nebo volání rozhraní API vyprázdnění by mohly mít negativní vliv na všechny ostatní interakci s funkcí Application Insights. Ale nutné dodat, je skutečně několik platný scénářů, kde je třeba shromažďovat osobní data. Pro tyto případy měla by ji zpracovat data, jak je popsáno v této části.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazení a export

Pro obě zobrazení a export dat požadavky [dotazu API](https://dev.applicationinsights.io/quickstart) by měl být použit. Až budete implementovat bude logiku převést tvaru dat některého vhodné zajistit uživatelům. [Azure Functions](https://azure.microsoft.com/services/functions/) je skvělým místem pro hostování tuto logiku.

### <a name="delete"></a>Odstranění

> [!WARNING]
> Odstranění ve službě Application Insights jsou destruktivní a nevratná! Prosím buďte velmi opatrní při jejich provádění.

Byla provedena k dispozici jako součástí ochrany osobních údajů zpracování scénáře API cestu "mazání". Tato cesta měli šetřit kvůli riziko spojené s tím, potenciálního vlivu na výkon a by mohly zkreslit všechna up agregace, měření a dalších aspektů data Application Insights. Najdete v článku [strategii pro zpracování osobní data](#strategy-for-personal-data-handling) části výše Alternativní přístupy k zpracovávat privátní data.

Vyprázdnění je vysoce privilegované operace, žádné aplikace nebo uživatele v Azure (včetně i vlastníka prostředku) bude mít oprávnění ke spuštění bez vrácení explicitně uděleno role ve službě Správce prostředků Azure. Tato role je _Data Purger_ a pečlivě kvůli potenciální ztráty dat.

Jakmile má přiřazenou roli správce Azure Resource Manager, dvě nové cesty rozhraní API jsou dokumentaci pro vývojáře k dispozici, úplné a volání tvar propojené:

* [Po vyprázdnění](https://docs.microsoft.com/rest/api/application-insights/components/purge) – přebírá objekt zadání parametrů dat odstranit a vrátí odkaz na identifikátor GUID
* GET vyprázdnit stav – vyprázdnění volání POST vrátí hlavičku 'x-ms stav umístění, která bude obsahovat adresu URL, kterou můžete volat k určení stavu rozhraní API vyprázdnění. Příklad:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

Zatímco většina operace vyprázdnění může dokončení mnohem rychlejší než SLA, z důvodu jejich velkou dopad na datovou platformu používané Application Insights formální SLA pro dokončení operace vyprázdnění je nastavený na 30 dní.

## <a name="next-steps"></a>Další postup
Další informace o tom, jak data jsou shromažďována, zpracování a zabezpečené, najdete v části [zabezpečení dat Application Insights](app-insights-data-retention-privacy.md).