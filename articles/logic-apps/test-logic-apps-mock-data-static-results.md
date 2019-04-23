---
title: Testování aplikací logiky s využitím mock dat – Azure Logic Apps
description: Nastavení statické výsledky pro testování aplikací logiky s využitím mock data, aniž to ovlivní produkční prostředí
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60511934"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testování aplikací logiky s využitím mock data nastavením statické výsledky

Při testování aplikace logiky, nemusí být připraveni skutečně volání nebo přístupu k aplikacím, služby a systémy z různých důvodů. Obvykle v těchto scénářích platí, budete možná muset spustit různé podmínky cesty, vynutit chyby, poskytují konkrétní zprávu těla odpovědi nebo se ani nepokusí přeskočí některé kroky. Nastavením statické výsledky akce ve vaší aplikaci logiky můžete napodobení výstupní data z této akce. Povolení statické výsledky na akci, která není spuštěna akce, ale místo toho vrátí mock data.

Pokud jste nastavili statickou výsledky pro Outlooku 365 odeslat akci e-mailu, modul Logic Apps jenom vrací mock data, která jste zadali jako statické výsledky, spíše než volání Outlooku a odeslat e-mailu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete nastavit statickou výsledky

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Nastavení statické výsledky

1. Pokud jste tak dosud neučinili, v [webu Azure portal](https://portal.azure.com), otevřete aplikaci logiky v návrháři pro Logic Apps.

1. Na akce, ve které chcete nastavit statickou výsledky postupujte podle těchto kroků: 

   1. V pravém horním rohu akce, klikněte na trojtečku (*...* ) tlačítko a vyberte **statické výsledek**, například:

      ![Vyberte "Statické výsledek" > "Povolit statickou výsledek"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Zvolte **povolit statickou výsledek**. Pro vlastnosti požadované (*) zadejte hodnoty mock výstup, který chcete vrátit akce odpovědi.

      Například tady jsou požadované vlastnosti pro akci HTTP:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Stav** | Stav akce vrátit |
      | **Stavový kód** | Konkrétní stavový kód, který má vrátit |
      | **Hlavičky** | Obsah hlavičky pro vrácení |
      |||

      ![Vyberte možnost "Povolit statickou výsledek"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Chcete-li zadat mock data ve formátu JavaScript Object Notation (JSON), zvolte **přepnout na režim JSON** (![zvolit "Přepnout do režimu JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Volitelné vlastnosti otevřít **vyberte volitelná pole** seznam a vyberte vlastnosti, které chcete napodobení.

      ![Vyberte volitelné vlastnosti](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Jakmile budete připraveni na Uložit, zvolte **provádí**.

   V pravém horním rohu akce, záhlaví nyní zobrazuje ikona kádinky testu (![ikonu pro statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), což znamená, že jste povolili statické výsledky.

   ![Ikona znázorňující povolené statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Předchozí spuštění využívající mock dat najdete v tématu [najít spuštění, které používají statické výsledky](#find-runs-mock-data) dále v tomto tématu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Opakované použití předchozích výstupy

Pokud má vaše aplikace logiky předchozí spuštění s výstupy, které můžete znovu použít jako mock výstupy, je můžete zkopírovat a vložit výstup z, která spustí.

1. Pokud jste tak dosud neučinili, v [webu Azure portal](https://portal.azure.com), otevřete aplikaci logiky v návrháři pro Logic Apps.

1. V hlavní nabídce aplikace logiky, vyberte **přehled**.

1. V **historie běhů** části, vyberte aplikaci logiky běh, který chcete.

1. V pracovním postupu vaší aplikace logiky vyhledejte a rozbalte akci, která obsahuje výstupy, které chcete.

1. Zvolte **zobrazit nezpracované výstupy** odkaz.

1. Zkopírujte celý objekt JavaScript Object Notation (JSON) nebo konkrétní dílčí část, kterou chcete použít, například, část Outputs následujícím nebo jenom část záhlaví.

1. Postupujte podle pokynů k prvnímu **statické výsledek** pole pro vaši akci ve [nastavit statickou výsledky](#set-up-static-results).

1. Po **statické výsledek** otevře, zvolte buď krok:

   * Chcete-li vložit kompletní objekt JSON, zvolte **přepnout na režim JSON** (![zvolit "Přepnout do režimu JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Zvolte možnost "Přepnout do režimu JSON" pro celý objekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Chcete-li vložit pouze JSON oddíl, vedle popisku této části zvolte **přepnout na režim JSON** pro tuto část, například:

     ![Zvolte možnost "Přepnout do režimu JSON" pro výstupy](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. V editoru JSON vložte váš dříve zkopírovaný kód JSON.

   ![Režim JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Jakmile budete hotovi, zvolte **Done** (Hotovo). Nebo se vraťte do návrháře, zvolte **přepnout Editor režimu** (![zvolit "Přepnout režim editoru"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Spuštění, které používají statické výsledky hledání

Historie spuštění aplikace logiky identifikuje spuštění, kde použít statické výsledky akce. K vyhledání těchto spuštění, postupujte podle těchto kroků:

1. V hlavní nabídce aplikace logiky, vyberte **přehled**. 

1. V pravém podokně v části **historie běhů**, Najít **statické výsledky** sloupce. 

   Jakékoli spuštění obsahující akce s výsledky se **statické výsledky** sloupec nastaven na **povoleno**, například:

   ![Historie - spuštění statické výsledky sloupec](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Chcete-li zobrazit akce, které používají statické výsledky, vyberte spustit, kam chcete **statické výsledky** sloupec je nastaven na **povoleno**.

   Akce, které používají statické výsledky zobrazit kádinky testu (![ikonu pro statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ikona, například:

   ![Historie - spuštění akce, které používají statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Zakázat statické výsledky

Vypnutí statické výsledky nebude zbavovat hodnoty z poslední instalace. Takže když zapnete statické výsledky při příštím, můžete pokračovat v používání předchozí hodnoty.

1. Najdete akce, ve které chcete zakázat statické výstupy. V pravém horním rohu akce, zvolte ikonu kádinky testu (![ikonu pro statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Zakázat statické výsledky](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Zvolte **zakázat statické výsledek** > **provádí**.

   ![Zakázat statické výsledky](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)