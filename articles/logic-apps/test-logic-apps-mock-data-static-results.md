---
title: Testování aplikací logiky s použitím cvičných dat
description: Nastavení statických výsledků pro testování aplikací logiky s využitím převedených dat bez vlivu na produkční prostředí
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74790276"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testování aplikací logiky s využitím povedených dat nastavením statických výsledků

Při testování vašich aplikací logiky možná nebudete připraveni na skutečné volání aplikací, služeb a systémů a získat z nich různé důvody. V těchto scénářích může být nutné spustit různé cesty k podmínkám, vynutit chyby, zadat konkrétní texty odpovědí na zprávy nebo dokonce zkusit přeskočit některé kroky. Nastavením statických výsledků pro akci v aplikaci logiky můžete z této akce napodobovat výstupní data. Povolení statických výsledků akce nespustí akci, ale místo toho vrátí napodobná data.

Pokud jste například nastavili statické výsledky pro akci odeslat poštu v aplikaci Outlook 365, modul Logic Apps vrátí pouze ta data, která jste zadali jako statické výsledky, nikoli volat Outlook a odeslat e-mail.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete nastavit statické výsledky

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Nastavení statických výsledků

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři Logic Apps.

1. Na akci, kde chcete nastavit statické výsledky, postupujte takto: 

   1. V pravém horním rohu akce zvolte tlačítko se třemi tečkami (*...*) a vyberte **statický výsledek**, například:

      ![Vyberte "statický výsledek" > "Povolit statický výsledek"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Vyberte možnost **Povolit statický výsledek**. U povinných vlastností (*) zadejte modely výstupních hodnot, které chcete vrátit pro odpověď akce.

      Tady jsou například požadované vlastnosti pro akci HTTP:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Stav** | Stav akce, která se má vrátit |
      | **Stavový kód** | Konkrétní stavový kód, který se má vrátit |
      | **Záhlaví** | Obsah záhlaví, který se má vrátit |
      |||

      ![Vyberte Povolit statický výsledek.](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Pokud chcete zadat tvarovaná data ve formátu JavaScript Object Notation (JSON), vyberte **Přepnout do režimu JSON** ( ![ klikněte na přepnout do režimu JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ).

   1. U volitelných vlastností otevřete seznam **vybrat volitelná pole** a vyberte vlastnosti, které chcete napisovat.

      ![Vybrat volitelné vlastnosti](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Až budete připraveni na uložení, klikněte na **Hotovo**.

   V pravém horním rohu se v záhlaví akce nyní zobrazuje ikona kádinky testu ( ![ ikona pro statické výsledky ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), která označuje, že jste povolili statické výsledky.

   ![Ikona ukazující povolené statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Pokud chcete najít předchozí spuštění, která používají podobná data, přečtěte si téma [hledání spuštění, které používá statické výsledky](#find-runs-mock-data) dále v tomto tématu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Znovu použít předchozí výstupy

Pokud vaše aplikace logiky obsahuje předchozí běh s výstupy, můžete je použít jako výstupy na výstupu, takže můžete kopírovat a vkládat výstupy z tohoto běhu.

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři Logic Apps.

1. V hlavní nabídce aplikace logiky vyberte **Přehled**.

1. V části **historie spuštění** vyberte požadovaný běh aplikace logiky.

1. V pracovním postupu aplikace logiky vyhledejte a rozbalte akci, která má požadované výstupy.

1. Vyberte odkaz **Zobrazit nezpracované výstupy** .

1. Zkopírujte buď objekt JSON (Complete JavaScript Object Notation), nebo konkrétní dílčí oddíl, který chcete použít, například část výstupy nebo dokonce pouze část záhlaví.

1. Postupujte podle kroků pro otevření pole **statický výsledek** pro akci v [Nastavení statických výsledků](#set-up-static-results).

1. Po otevření pole **statický výsledek** vyberte buď krok:

   * Chcete-li vložit úplný objekt JSON, vyberte možnost **Přepnout do režimu JSON** ( ![ Vyberte přepnout do režimu JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ):

     ![Pro úplný objekt vyberte přepnout do režimu JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Chcete-li vložit pouze oddíl JSON, klikněte vedle popisku této části na příkaz **Přepnout do režimu JSON** pro tuto část, například:

     ![Pro výstupy vyberte přepnout do režimu JSON.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. V editoru JSON vložte dříve zkopírovaný formát JSON.

   ![Režim JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Jakmile budete hotovi, zvolte **Done** (Hotovo). Nebo chcete-li se vrátit k návrháři, vyberte možnost **režim editoru přepnutí** ( ![ Vyberte režim editoru přepnutí ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Hledání spuštění, která používají statické výsledky

Historie spuštění vaší aplikace logiky identifikuje spuštění, kde akce používají statické výsledky. K vyhledání těchto spuštění použijte následující postup:

1. V hlavní nabídce aplikace logiky vyberte **Přehled**. 

1. V pravém podokně v části **historie spuštění**vyhledejte sloupec **statických výsledků** . 

   Každý běh, který zahrnuje akce s výsledky, má sloupec **statických výsledků** nastavený na **povoleno**, například:

   ![Historie spuštění – sloupec statických výsledků](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Pokud chcete zobrazit akce, které používají statické výsledky, vyberte požadované spuštění, ve kterém je sloupec **statických výsledků** nastavený na **povoleno**.

   U akcí, které používají statické výsledky, se zobrazí ikona zkušební kádinka ( ![ ikona pro statické výsledky ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), například:

   ![Historie spuštění – akce, které používají statické výsledky](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Zakázat statické výsledky

Vypnutím statických výsledků nedojde k vygenerování hodnot z poslední instalace. Takže při příštím zapnutí statických výsledků můžete dál používat předchozí hodnoty.

1. Vyhledejte akci, ve které chcete zakázat statické výstupy. V pravém horním rohu akce vyberte ikonu kádinky testu ( ![ ikona pro statické výsledky ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![Zakázat statické výsledky](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Vyberte možnost **Zakázat statický výsledek**  >  **Done**.

   ![Zakázat statické výsledky](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Odkaz

Další informace o tomto nastavení najdete v příslušných definicích pracovních postupů v tématu [statické výsledky – referenční informace ke schématu pro jazyk pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#static-results) a [runtimeConfiguration. staticResult – nastavení konfigurace modulu runtime.](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)