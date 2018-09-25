---
title: Hledání napříč prostředky pomocí Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete zadávat dotazy na prostředky z několika pracovních prostorů a aplikace pro App Insights ve vašem předplatném.
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
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 716f96b7723a947c35eb54acd67f13261363fd38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972874"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Provedení prohledávání protokolů napříč prostředky ve službě Log Analytics  

Dříve pomocí Azure Log Analytics, jste mohli analyzovat data pouze z v rámci aktuálního pracovního prostoru a omezený možnost dotazování napříč několika pracovními prostory definovanými v rámci vašeho předplatného.  Kromě toho může hledat pouze položky telemetrická data shromážděná z vaší webové aplikace pomocí Application Insights přímo ve službě Application Insights nebo ze sady Visual Studio.  Kvůli tomu také bylo obtížné analyzovat nativně provozní a data aplikací společně.   

Nyní se můžete dotazovat nejen napříč několika pracovních prostorů Log Analytics, ale také data z konkrétní aplikace Application Insights ve stejné skupině prostředků, jiné skupiny prostředků nebo jiného předplatného. To vám poskytne systémová přehled o datech.  Lze provést pouze tyto typy dotazů v [Log Analytics](log-analytics-log-search-portals.md#log-analytics-page). Počet prostředků (pracovních prostorů Log Analytics a Application Insights aplikaci), které mohou obsahovat v jediném dotazu je omezený na 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Dotazování napříč pracovních prostorů Log Analytics a ze služby Application Insights
Chcete-li odkazovat na jiný pracovní prostor v dotazu, použijte [ *pracovní prostor* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) identifikátor a pro aplikace ze služby Application Insights, použijte [ *aplikace* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())identifikátor.  

### <a name="identifying-workspace-resources"></a>Identifikační prostředky pracovního prostoru
Následující příklady ukazují dotazy napříč pracovními prostory Log Analytics k vrácení souhrnný počet protokolů z tabulky aktualizace v pracovním prostoru s názvem *contosoretail it*. 

Identifikace pracovní prostor může být jedním z několika způsobů:

* Název prostředku – je popisný název pracovního prostoru, někdy označovány jako *název komponenty*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identifikace podle názvu pracovního prostoru předpokládá jedinečnost napříč všemi předplatnými přístupné. Pokud máte více aplikací se zadaným názvem, že se dotaz nezdaří z důvodu nejednoznačnosti. V takovém případě musíte použít jeden z další identifikátory.

* Úplný název - je "úplný název" pracovního prostoru, skládá z názvu předplatného, skupiny prostředků a název komponenty v tomto formátu: *subscriptionName/skupina prostředků/název komponenty*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID pracovního prostoru - ID pracovního prostoru je jedinečný, nezměnitelný, identifikátor přiřazený do každého pracovního prostoru reprezentovaná jako globálně jedinečný identifikátor (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – definované Azure jedinečnou identitu pracovního prostoru. ID prostředku, které se používají, když je nejednoznačný název prostředku.  U pracovních prostorů, formát je: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/pracovních prostorů/componentName*.  

    Příklad:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Update | count
    ```

### <a name="identifying-an-application"></a>Určení aplikace
Následující příklady vrátí souhrnný počet požadavků na přístup aplikaci s názvem *fabrikamapp* ve službě Application Insights. 

Identifikuje aplikaci ve službě Application Insights můžete docílit, když se *app(Identifier)* výrazu.  *Identifikátor* argument určuje aplikace pomocí jedné z následujících akcí:

* Název prostředku – je lidské čitelný název aplikace, která se někdy označuje jako *název komponenty*.  

    `app("fabrikamapp")`

* Úplný název - je "úplný název" aplikace skládá z názvu předplatného, skupiny prostředků a název komponenty v tomto formátu: *subscriptionName/skupina prostředků/název komponenty*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Vzhledem k tomu, že názvy předplatného Azure nejsou jedinečné, tento identifikátor může být nejednoznačný. 
    >

* ID – identifikátor GUID aplikace aplikace.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure ID prostředku - definované Azure jedinečnou identitu aplikace. ID prostředku, které se používají, když je nejednoznačný název prostředku. Formát je: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/součásti/componentName*.  

    Příklad:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Provádění dotazu napříč více zdrojů
Z některého z vašich instancí prostředku můžete dotazovat více zdrojů, může jít o pracovních prostorech a aplikacích kombinovat.
    
Příklad pro dotazování napříč dvěma pracovními prostory.    
    ```
    union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
    | where TimeGenerated >= ago(1h)
    | where UpdateState == "Needed"
    | summarize dcount(Computer) by Classification
    ```

## <a name="next-steps"></a>Další postup

Zkontrolujte [protokolu v log Analytics search odkaz](https://docs.loganalytics.io/docs/Language-Reference) zobrazíte všechny možnosti syntaxi dotazů k dispozici ve službě Log Analytics.    
