---
title: Pokyny pro osobní data uložená v Azure Log Analytics | Microsoft Docs
description: Tento článek popisuje postup správy osobních dat uložených v Azure Log Analytics a metody pro identifikaci a jeho odebrání.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 056779943d05ca743db63f1bc91be058cfae7b30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654847"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Pokyny pro osobní data uložená v analýzy protokolů

Analýzy protokolů je datové úložiště, kde je pravděpodobné, která se má najít osobní data. Tento článek popisuje kde v analýzy protokolů taková data se obvykle nachází, a také možností dostupných pro zpracování taková data.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie pro zpracování osobní data

Zatímco bude až vás a vaši společnost nakonec určení strategie, pomocí kterého bude zpracovávat vaší privátní dat (pokud vůbec), níže jsou uvedeny některé možné přístupy. Jsou uvedeny v pořadí podle preference z technického hlediska z většinu k nejméně vhodnější než:

* Kde je to možné, zastavte kolekce, obfuskováním, změnit na anonymní nebo v opačném případě upravte dat shromažďovaných vyloučíte z za "privátní". Toto je _zdaleka_ žádoucí ušetřit potřebu vytvářet velmi nákladné a zvládat data zpracování strategie.
* Není to možné, pokuste se normalizují data pro snížení vlivu na platformě, dat a výkonu. Například místo protokolování explicitní ID uživatele, vytvořte vyhledávání data, která bude korelovat uživatelské jméno a jejich podrobnosti interní ID, které může být poté protokolovaná jinde. Tímto způsobem, by měl jeden z vašich uživatelů vás vyzve k odstranění jejich osobní údaje, je možné, že pouze odstranění řádku ve vyhledávací tabulce odpovídající uživatele bude dostatečná. 
* Pokud je třeba shromažďovat osobní data, nakonec sestavte proces kolem cesta k vyprázdnění rozhraní API a existující cestu rozhraní API dotazu plnit žádné závazky, které by mohly být kolem export a odstranění všechna privátní data související s uživatelem. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Kde hledat privátní data v Log Analytics?

Analýzy protokolů je flexibilní úložiště, které při předepisující schéma ke svým datům umožňuje přepsání každé pole s vlastní hodnoty. Kromě toho můžete konzumaci žádné vlastní schéma. Jako takový není možné, že přesně kde privátní data budou nacházet v určitém pracovním prostoru. V následujících umístěních a jsou však dobré počáteční body ve vašem inventáři softwaru:

* *IP adresy*: analýzy protokolů shromažďuje řadu informace o IP mezi mnoha různých tabulek. Například následující dotaz uvádí všechny tabulky, které byly shromážděny adresy IPv4 za posledních 24 hodin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID uživatelů*: ID uživatele se nacházejí v širokou škálu řešení a tabulek. Můžete vyhledat konkrétní uživatelské jméno napříč celou datovou sadu pomocí příkazu vyhledávání:
    ```
    search "[username goes here]"
    ```
Nezapomeňte se podívejte, nejen pro čitelná pro člověka uživatelská jména, ale také identifikátory GUID, které lze přímo vysledovat zpět k určitému uživateli!
* *ID zařízení*: jako ID uživatele, zařízení ID se někdy považují za "privátní". Jak je uvedeno výše pro ID uživatele použít ve stejný přístup k identifikaci tabulek tam, kde může se jednat o problém. 
* *Vlastní data*: analýzy protokolů umožňuje kolekce v různých metod: vlastní protokoly a vlastní pole, [rozhraní API sady kolekcí dat protokolu HTTP](log-analytics-data-collector-api.md) , a vlastní data shromažďují jako součást protokoly událostí systému. Všechny tyto jsou náchylné k obsahující privátní data a ověřit, zda existuje taková data vyšetřit.
* *Řešení pro zachycení dat*: mechanismus řešení je otevřený jeden, a proto jsme doporučujeme přečtení všech tabulek, které jsou generované řešení zajistit dodržování předpisů.

## <a name="how-to-export-and-delete-private-data"></a>Export a Odstranit privátní data

Jak je uvedeno v [strategii pro zpracování osobní data](#strategy-for-personal-data-handling) výše v části, je __důrazně__ doporučuje, když se všechny možné změny struktury vaší shromažďování údajů zakázat kolekce privátní data, zmatení data nebo jeho zavedení anonymity nebo jeho odebrání za "privátní" jinak změnou. Zpracování dat bude přední výsledek v náklady na vy a váš tým k definování a automatizaci strategie, sestavení rozhraní pro vaše zákazníky k interakci s jejich daty prostřednictvím a náklady na průběžnou údržbu. Navíc je u nákladná pro analýzy protokolů a velkého objemu souběžných dotazů nebo volání rozhraní API vyprázdnění by mohly mít negativní vliv na všechny ostatní interakce s funkcemi analýzy protokolů. Ale nutné dodat, je skutečně několik platný scénářů, kde je třeba shromažďovat osobní data. Pro tyto případy měla by ji zpracovat data, jak je popsáno v této části.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Zobrazení a export

Pro obě zobrazení a export dat požadavky [dotazu API](https://dev.loganalytics.io/) by měl být použit. Až budete implementovat bude logiku převést tvaru dat některého vhodné zajistit uživatelům. [Azure Functions](https://azure.microsoft.com/services/functions/) je skvělým místem pro hostování tuto logiku.

### <a name="delete"></a>Odstranění

> [!WARNING]
> Odstranění v analýzy protokolů jsou destruktivní a nevratná! Prosím buďte velmi opatrní při jejich provádění.

Jsme provedli k dispozici jako součást zpracování ochrany osobních údajů *mazání* cestu rozhraní API. Tato cesta měli šetřit kvůli riziko spojené s tím, potenciálního vlivu na výkon a by mohly zkreslit všechna up agregace, měření a dalších aspektů data Log Analytics. Najdete v článku [strategii pro zpracování osobní data](#strategy-for-personal-data-handling) části Alternativní přístupy k zpracovávat privátní data.

Vyprázdnění je vysoce privilegované operace, žádné aplikace nebo uživatele v Azure (včetně i vlastníka prostředku) bude mít oprávnění ke spuštění bez vrácení explicitně uděleno role ve službě Správce prostředků Azure. Tato role je _Data Purger_ a opatrně kvůli potenciální ztráty dat. 

Jakmile má přiřazenou roli správce Azure Resource Manager, jsou k dispozici dvě nové rozhraní API cesty: 

* [POST vyprázdnění] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – přebírá objekt zadání parametrů dat odstranit a vrátí odkaz na identifikátor GUID 
* GET vyprázdnit stav – vyprázdnění volání POST vrátí hlavičku 'x-ms stav umístění, která bude obsahovat adresu URL, kterou můžete volat k určení stavu rozhraní API vyprázdnění. Příklad:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Při Očekáváme, že velká většina operací mazání na dokončení mnohem rychlejší než naše SLA, z důvodu jejich velkou dopad na datovou platformu používané analýzy protokolů formální SLA pro dokončení operace vyprázdnění je nastavený na 30 dní. 

## <a name="next-steps"></a>Další postup
Další informace o tom, jak data jsou shromažďována, zpracování a zabezpečené, najdete v části [analýzy protokolů zabezpečení dat](log-analytics-data-security.md).