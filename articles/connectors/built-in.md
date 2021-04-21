---
title: Integrované aktivační události a akce pro Azure Logic Apps
description: Pomocí integrovaných triggerů a akcí můžete vytvářet automatizované pracovní postupy, které integrují aplikace, data, služby a systémy, řídit pracovní postupy a spravovat data pomocí Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796730"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Integrované aktivační události a akce pro Logic Apps


[Integrované triggery a akce](apis-list.md) poskytují způsob, jak [řídit plán a strukturu pracovního postupu](#control-workflow), [spouštět vlastní kód](#run-code-from-workflows), [Spravovat nebo manipulovat s daty](#manage-or-manipulate-data)a provádět další úkoly v pracovních postupech. Liší se od [spravovaných konektorů](managed.md), mnoho integrovaných operací není vázáno na konkrétní službu, systém nebo protokol. Můžete například spustit téměř libovolný pracovní postup podle plánu pomocí triggeru opakování. Nebo můžete pracovní postup nechat počkat, dokud se nevyvolá pomocí triggeru žádosti. Všechny integrované operace se nativně spouštějí ve službě Logic Apps a nevyžadují, abyste vytvořili připojení, než je použijete. 

Logic Apps také poskytuje integrované operace pro menší počet služeb, systémů a protokolů, jako jsou Azure Service Bus, Azure Functions, SQL, AS2 a tak dále. Počet a rozsah se liší v závislosti na tom, zda vytvoříte aplikaci logiky pro více tenantů nebo aplikaci logiky pro jednoho tenanta. V několika případech jsou k dispozici obě předdefinované verze a verze spravovaného konektoru. Ve většině případů předdefinovaná verze nabízí lepší výkon, možnosti, ceny a tak dále. Chcete-li například [vyměňovat zprávy B2B pomocí protokolu AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), vyberte vestavěnou verzi, pokud nepotřebujete sledování, které jsou k dispozici pouze v (nepoužívaném) verzi spravovaného konektoru.

Následující seznam popisuje pouze některé úlohy, které lze provést pomocí [integrovaných triggerů a akcí](#understand-triggers-and-actions):

- Spouštějte pracovní postupy pomocí vlastních a pokročilých plánů. Další informace o plánování najdete [v části "chování opakování" v tématu přehled Logic Appsového konektoru](apis-list.md#recurrence-behavior).
- Organizování a řízení struktury pracovního postupu, například pomocí smyček a podmínek.
- Pracujte s proměnnými, daty, datovými operacemi, transformacemi obsahu a dávkovými operacemi.
- Komunikace s ostatními koncovými body pomocí triggerů a akcí HTTP.
- Přijímat žádosti a reagovat na ně.
- Zavolejte své vlastní funkce (Azure Functions), webové aplikace (Azure App Services), rozhraní API (Azure API Management), další Logic Apps pracovní postupy, které můžou přijímat požadavky atd.

## <a name="understand-triggers-and-actions"></a>Principy aktivačních událostí a akcí

Logic Apps poskytuje následující integrované aktivační události a akce:

:::row:::
    :::column:::
        [![Ikona plánu v Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**CXL**][schedule-doc]
        \
        \
        [**Opakování**][schedule-recurrence-doc]: aktivovat pracovní postup na základě zadaného opakování.
        \
        \
        [**Posuvné okno**][schedule-sliding-window-doc]: Aktivujte pracovní postup, který potřebuje zpracovávat data ve souvislých blocích.
        \
        \
        [**Zpoždění**][schedule-delay-doc]: pozastavit pracovní postup na určenou dobu trvání.
        \
        \
        [**Zpoždění do**][schedule-delay-until-doc]: pozastavit pracovní postup do zadaného data a času.
    :::column-end:::
    :::column:::
        [![Ikona Batch v Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Partie**][batch-doc]
        \
        \
        [**Zprávy Batch**][batch-doc]: Aktivujte pracovní postup, který zpracovává zprávy v dávkách.
        \
        \
        [**Odeslat zprávy do služby Batch**][batch-doc]: volejte existující pracovní postup, který aktuálně začíná triggerem **zprávy dávky** .
    :::column-end:::
    :::column:::
        [![Ikona HTTP v Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Zavolejte koncový bod HTTP nebo HTTPS pomocí triggeru nebo akce HTTP. 
        \
        \
        Můžete také použít tyto další integrované aktivační procedury HTTP a akce: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Ikona žádosti][http-request-icon]][http-request-doc]
        \
        \
        [**Request**][http-request-doc]
        \
        \
        [**Při přijetí požadavku HTTP**][http-request-doc]: Počkejte na požadavek z jiného pracovního postupu, aplikace nebo služby. Díky této aktivační události se váš pracovní postup dá volat, aniž byste ho museli kontrolovat nebo dotazovat podle plánu.
        \
        \
        [**Response**][http-request-doc]: Odpovězte na požadavek přijatý **při přijetí požadavku HTTP** do triggeru v rámci stejného pracovního postupu.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona Azure API Management v Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**API Management Azure**][azure-api-management-doc]
        \
        \
        Zavolejte vlastní triggery a akce v rozhraních API, která definujete, spravujete a publikujete pomocí [Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Poznámka**: při použití [vrstvy spotřeby pro API Management](../api-management/api-management-features.md)není podporována.
    :::column-end:::
    :::column:::
        [![Ikona Azure App Services v Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**App Services Azure**][azure-app-services-doc]
        \
        \
        Zavolejte aplikace, které vytvoříte a budete hostovat na [Azure App Service](../app-service/overview.md), například API Apps a Web Apps.
        \
        \
        Při zahrnutí Swagger se triggery a akce definované těmito aplikacemi zobrazují jako jakékoli jiné aktivační události první třídy a akce v Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Ikona Azure Logic Apps v Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Zavolejte jiné pracovní postupy, které začínají triggerem Request s názvem, **když se přijme požadavek HTTP**.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Spuštění kódu z pracovních postupů

Logic Apps poskytuje integrované akce pro spuštění vlastního kódu v pracovním postupu:

:::row:::
    :::column:::
        [![Ikona Azure Functions v Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Voláním [funkcí hostovaných v Azure](../azure-functions/functions-overview.md) spustíte vlastní *fragmenty kódu* (C# nebo Node.js) v rámci pracovního postupu.
    :::column-end:::
    :::column:::
        [![Ikona vloženého kódu v Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Vložený kód**][inline-code-doc]
        \
        \
        [**Spustit javascriptový kód**][inline-code-doc]: přidejte a spusťte vlastní vložené *fragmenty kódu* JavaScriptu v rámci pracovního postupu.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Pracovní postup ovládacího prvku

Logic Apps poskytuje předdefinované akce pro strukturování a řízení akcí v pracovním postupu:

:::row:::
    :::column:::
        [![Ikona akce podmínky v Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Pomocné**][condition-doc]
        \
        \
        Vyhodnoťte podmínku a spusťte různé akce na základě toho, zda je podmínka pravdivá, nebo false.
    :::column-end:::
    :::column:::
        [![Pro každou ikonu akce v Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**Pro každý**][for-each-doc]
        \
        \
        Proveďte stejné akce u každé položky v poli.
    :::column-end:::
    :::column:::
        [![Ikona akce oboru v Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Jméno**][scope-doc]
        \
        \
        Seskupí akce do *oborů*, které po dokončení akcí v oboru dostanou svůj vlastní stav.
    :::column-end:::
    :::column:::
        [![Ikona akce přepnutí v Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Přepnutí**][switch-doc]
        \
        \
        Akce skupiny do *případů*, kterým jsou přiřazeny jedinečné hodnoty s výjimkou výchozího případu. Spustí pouze tento případ, jehož přiřazená hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud žádné shody neexistují, spusťte výchozí případ.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akce ukončit v Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Ruší**][terminate-doc]
        \
        \
        Zastaví aktivně běžící pracovní postup aplikace logiky. 
    :::column-end:::
    :::column:::
        [![Až do ikony akce v Logic Apps][until-icon]][until-doc]
        \
        \
        [**Vrátí**][until-doc]
        \
        \
        Akce opakujte, dokud není zadaná podmínka pravdivá nebo se změnil stav.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Správa dat nebo manipulace s nimi

Logic Apps poskytuje předdefinované akce pro práci s datovými výstupy a jejich formáty:

:::row:::
    :::column:::
        [![Ikona akce operací s daty v Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Datové operace**][data-operations-doc]
        \
        \
        Provádět operace s daty. 
        \
        \
        **Sestavit**: vytvořte jeden výstup z více vstupů s různými typy. 
        \
        \
        **Vytvoření tabulky CSV**: z pole s objekty JSON vytvořte tabulku s hodnotami oddělenými čárkou (CSV). 
        \
        \
        **Vytvořit tabulku HTML**: vytvoří tabulku HTML z pole s objekty JSON. 
        \
        \
        **Filter Array**: vytvořte pole z položek v jiném poli, které splňuje vaše kritéria. 
        \
        \
        **Join**: vytvoří řetězec ze všech položek v poli a oddělí tyto položky se zadaným oddělovačem. 
        \
        \
        **Analyzovat JSON**: umožňuje vytvořit uživatelsky přívětivé tokeny z vlastností a jejich hodnot v obsahu JSON, abyste je mohli ve svém pracovním postupu použít. 
        \
        \
        **Vyberte**: vytvořte pole s objekty JSON transformující položky nebo hodnoty v jiném poli a namapováním těchto položek na zadané vlastnosti.
    :::column-end:::
    :::column:::
        ![Ikona akce data a času v Logic Apps][date-time-icon]
        \
        \
        **Datum a čas**
        \
        \
        Provádět operace s časovými razítky.
        \
        \
        **Přidat do času**: do časového razítka přidejte zadaný počet jednotek. 
        \
        \
        **Převést časové pásmo**: převede časové razítko ze zdrojového časového pásma na cílové časové pásmo. 
        \
        \
        **Aktuální čas**: vrátí aktuální časové razítko jako řetězec. 
        \
        \
        **Získat budoucí čas**: vrátí aktuální časové razítko plus zadané časové jednotky. 
        \
        \
        **Získat čas v minulosti**: vrátí aktuální časové razítko minus zadané časové jednotky. 
        \
        \
        **Odečíst od času**: odečte počet časových jednotek od časového razítka.
    :::column-end:::
    :::column:::
        [![Ikona akce proměnné v Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Proměnné**][variables-doc]
        \
        \
        Provede operace s proměnnými.
        \
        \
        **Připojit k proměnné pole**: vloží hodnotu jako poslední položku v poli uloženém proměnnou. 
        \
        \
        **Připojit k proměnné řetězce**: vloží hodnotu jako poslední znak v řetězci uloženém proměnnou.
        \
        \
        **Odsnižovat proměnnou**: Zmenšete proměnnou konstantní hodnotou.
        \
        \
        **Přírůstek proměnné**: Zvyšte proměnnou na konstantní hodnotu. 
        \
        \
        **Inicializovat proměnnou**: vytvoří proměnnou a deklaruje její datový typ a počáteční hodnotu. 
        \
        \
        **Nastavení proměnné**: přiřaďte existující proměnné jinou hodnotu. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření vlastních rozhraní API, která můžete volat z Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Vytvoření instance služby Azure API Management pro správu a publikování vašich rozhraní API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrace aplikací logiky s App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrace aplikací logiky s Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Zpracování zpráv ve skupinách nebo jako dávek"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Vyhodnotit podmínku a spustit různé akce na základě toho, jestli je podmínka pravdivá, nebo NEPRAVDA"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Proveďte stejné akce u každé položky v poli."
[http-doc]: ./connectors-native-http.md "Volání koncových bodů HTTP nebo HTTPS z vašich aplikací logiky"
[http-request-doc]: ./connectors-native-reqres.md "Přijímání požadavků HTTP ve vašich aplikacích logiky"
[http-response-doc]: ./connectors-native-reqres.md "Reakce na požadavky HTTP z Logic Apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Volání koncových bodů REST z vašich aplikací logiky"
[http-webhook-doc]: ./connectors-native-webhook.md "Čekání na konkrétní události ze koncových bodů HTTP nebo HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Přidávání a spouštění fragmentů kódu JavaScriptu z aplikací logiky"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrace aplikací logiky s vnořenými pracovními postupy"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Výběr a filtrování polí pomocí akce dotazu"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Spuštění Logic Apps podle plánu"
[schedule-delay-doc]: ./connectors-native-delay.md "Zpoždění spuštění další akce"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Zpoždění spuštění další akce"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Spouštění Logic Apps podle opakovaného plánu"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Spouštění Logic Apps, které potřebují zpracovávat data v souvislých blocích"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Uspořádat akce do skupin, které po dokončení akcí v rámci skupiny dokončí jejich stav, a získat jejich stav"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Uspořádat akce do případů, kterým jsou přiřazeny jedinečné hodnoty. Spustí pouze případ, jehož hodnota odpovídá výsledku z výrazu, objektu nebo tokenu. Pokud žádné shody neexistují, spusťte výchozí případ."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zastavení nebo zrušení aktivně běžícího pracovního postupu pro aplikaci logiky"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Akce opakujte, dokud není zadaná podmínka pravdivá nebo se změnil stav"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Provádění operací s daty, jako je filtrování polí nebo vytváření tabulek CSV a HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Provádění operací s proměnnými, například inicializovat, nastavit, zvýšit, snížit a připojit k řetězci nebo proměnné pole"
