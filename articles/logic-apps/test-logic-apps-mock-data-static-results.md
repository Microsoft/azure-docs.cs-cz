---
title: Testování aplikací logiky s použitím cvičných dat
description: Nastavení statických výsledků pro testování aplikací logiky s falešnými daty bez ovlivnění produkčních prostředí
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790276"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testování aplikací logiky s falešnými daty nastavením statických výsledků

Při testování aplikací logiky nemusí být připraveni skutečně volat nebo přistupovat k aplikacím, službám a systémům z různých důvodů. Obvykle v těchto scénářích může být možné spustit různé cesty podmínek, vynutit chyby, poskytnout konkrétní těla odezvy zprávy nebo dokonce zkusit přeskočení některých kroků. Nastavením statických výsledků pro akci v aplikaci logiky můžete zesměšňovat výstupní data z této akce. Povolení statických výsledků akce nespustí akci, ale místo toho vrátí falešná data.

Pokud například nastavíte statické výsledky pro akci odeslat poštu v outlooku 365, modul Logic Apps vrátí pouze falešná data, která jste zadali jako statické výsledky, místo volání aplikace Outlook a odeslání e-mailu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete nastavit statické výsledky

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Nastavení statických výsledků

1. Pokud jste tak ještě neučinili, otevřete na [webu Azure Portal](https://portal.azure.com)aplikaci logiky v Návrháři logických aplikací.

1. V akci, při které chcete nastavit statické výsledky, postupujte takto: 

   1. V pravém horním rohu akce zvolte tlačítko elipsy (*...*) a vyberte **Statický výsledek**, například:

      ![Vyberte možnost Statický výsledek > "Povolit statický výsledek"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Zvolte **Povolit statický výsledek**. Pro požadované (*) vlastnosti zadejte mock výstupní hodnoty, které chcete vrátit pro odpověď akce.

      Zde jsou například požadované vlastnosti akce HTTP:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Stav** | Stav akce, který se má vrátit |
      | **Stavový kód** | Konkrétní stavový kód, který chcete vrátit |
      | **Hlavičky** | Obsah záhlaví, který chcete vrátit |
      |||

      ![Vyberte možnost "Povolit statický výsledek"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Chcete-li zadat falešná data ve formátu JavaScript Object Notation (JSON), zvolte **Přepnout do režimu JSON** (![Zvolte "Přepnout do režimu JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. U volitelných vlastností otevřete seznam **Vybrat volitelná pole** a vyberte vlastnosti, které chcete zesměšňovat.

      ![Výběr volitelných vlastností](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Až budete připraveni k uložení, zvolte **Hotovo**.

   V pravém horním rohu akce se v záhlaví nyní zobrazí ikona![testovací kádinky ( Ikona pro statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), což znamená, že jste povolili statické výsledky.

   ![Ikona zobrazující povolené statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Chcete-li najít předchozí spuštění, která používají falešná data, přečtěte si téma [Hledání spuštění, která používají statické výsledky](#find-runs-mock-data) dále v tomto tématu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Opakované použití předchozích výstupů

Pokud vaše aplikace logiky má předchozí spuštění s výstupy můžete znovu použít jako mock výstupy, můžete zkopírovat a vložit výstupy z tohoto spuštění.

1. Pokud jste tak ještě neučinili, otevřete na [webu Azure Portal](https://portal.azure.com)aplikaci logiky v Návrháři logických aplikací.

1. V hlavní nabídce aplikace logiky vyberte **Přehled**.

1. V části **Spustí historii** vyberte spuštění aplikace logiky, které chcete.

1. V pracovním postupu aplikace logiky najděte a rozbalte akci, která má požadované výstupy.

1. Zvolte odkaz **Zobrazit nezpracované výstupy.**

1. Zkopírujte buď celý objekt Zápis objektu JavaScript (JSON) nebo konkrétní pododdíl, který chcete použít, například oddíl výstupů, nebo dokonce pouze oddíl záhlaví.

1. Postupujte podle pokynů pro otevření pole **Statický výsledek** pro vaši akci v [nastavení statických výsledků](#set-up-static-results).

1. Po otevření pole **Statický výsledek** zvolte oba kroky:

   * Chcete-li vložit celý objekt JSON, zvolte![ **Přepnout do režimu JSON** ( Zvolte "Přepnout do režimu JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Zvolte "Přepnout do režimu JSON" pro kompletní objekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Chcete-li vložit pouze oddíl JSON, zvolte **přepnout do režimu JSON** pro tento oddíl, například:

     ![Zvolte "Přepnout do režimu JSON" pro výstupy](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. V editoru JSON vložte dříve zkopírovaný JSON.

   ![Režim JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Jakmile budete hotovi, zvolte **Done** (Hotovo). Chcete-li se vrátit do návrháře,![zvolte Režim](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) **přepnutí (Zvolte** "Přepnout režim editoru" ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Hledání běhů, které používají statické výsledky

Historie spuštění aplikace logiky identifikuje spuštění, kde akce používají statické výsledky. Chcete-li najít tyto spuštění, postupujte takto:

1. V hlavní nabídce aplikace logiky vyberte **Přehled**. 

1. V pravém podokně v části **Historie spuštění**najděte sloupec **Statické výsledky.** 

   Každé spuštění, které obsahuje akce s výsledky, má sloupec **Statické výsledky** nastavený na **Povoleno**, například:

   ![Historie spuštění – sloupec statických výsledků](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Chcete-li zobrazit akce, které používají statické výsledky, vyberte požadované spuštění, ve kterém je sloupec **Statické výsledky** nastaven na **povoleno**.

   Akce, které používají statické výsledky,![zobrazují ikonu](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)zkušební kádinky ( Ikona pro statické výsledky), například:

   ![Historie spuštění – akce, které používají statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Zakázání statických výsledků

Vypnutí statických výsledků nevyhazuje hodnoty z posledního nastavení. Takže při příštím zapnutí statických výsledků můžete pokračovat v používání předchozích hodnot.

1. Najděte akci, kde chcete zakázat statické výstupy. V pravém horním rohu akce zvolte ikonu zkušební kádinky![](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)( Ikona pro statické výsledky ).

   ![Zakázání statických výsledků](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Zvolte **Zakázat statický výsledek** > **Hotovo**.

   ![Zakázání statických výsledků](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Odkaz

Další informace o tomto nastavení v základních definicích pracovního postupu naleznete v [tématu Statické výsledky – odkaz na schéma pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#static-results) a [runtimeConfiguration.staticResult - nastavení konfigurace za běhu](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Další kroky

* Další informace o [aplikacích Azure Logic Apps](../logic-apps/logic-apps-overview.md)