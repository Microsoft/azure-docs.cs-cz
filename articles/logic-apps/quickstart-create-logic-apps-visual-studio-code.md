---
title: Automatizace úloh pomocí kódu sady Visual Studio
description: Vytvoření nebo úprava aplikace logiky, která je základem definic JSON pomocí kódu Visual Studio (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 819a60887743f39d3c2ffab3c955b2980cee2725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74784829"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Úvodní příručka: Vytvoření a správa definic pracovních postupů aplikace logiky pomocí kódu Sady Visual Studio

S [Aplikacemi logiky Azure](../logic-apps/logic-apps-overview.md) a visual studio code můžete vytvářet a spravovat aplikace logiky, které vám pomůžou automatizovat úkoly, pracovní postupy a procesy pro integraci aplikací, dat, systémů a služeb napříč organizacemi a podniky. Tento rychlý start ukazuje, jak můžete vytvořit a upravit základní definice pracovního postupu, které používají JavaScript zápis objektu (JSON), pro aplikace logiky prostřednictvím prostředí založeného na kódu. Můžete také pracovat na existujících aplikacích logiky, které už jsou nasazené do Azure.

I když můžete provádět stejné úkoly na [webu Azure Portal](https://portal.azure.com) a ve Visual Studiu, můžete začít rychleji v kódu Visual Studia, když už jste obeznámeni s definicemi aplikací logiky a chcete pracovat přímo v kódu. Můžete například zakázat, povolit, odstranit a aktualizovat již vytvořené aplikace logiky. Můžete také pracovat na aplikacích logiky a účtech integrace z libovolné vývojové platformy, kde běží visual studio kód, jako je Linux, Windows a Mac.

Pro tento článek můžete vytvořit stejnou aplikaci logiky z tohoto [rychlého startu](../logic-apps/quickstart-create-first-logic-app-workflow.md), který se zaměřuje více na základní pojmy. V kódu Visual Studia aplikace logiky vypadá jako tento příklad:

![Příklad definice pracovního postupu aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Než začnete, ujistěte se, že máte tyto položky:

* Pokud nemáte účet Azure a předplatné, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [definicích pracovních postupů aplikace logiky](../logic-apps/logic-apps-workflow-definition-language.md) a jejich struktuře, jak je popsáno pomocí JSON

  Pokud s logicapps teprve začínáte, vyzkoušejte tento [rychlý start](../logic-apps/quickstart-create-first-logic-app-workflow.md), který vytvoří první aplikace logiky na webu Azure Portal a zaměří se na základní koncepty.

* Přístup k webu pro přihlášení k Azure a předplatné Azure

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio Code verze 1.25.1 nebo novější](https://code.visualstudio.com/), který je zdarma

  * Rozšíření kódu Visual Studia pro aplikace Azure Logic Apps

    Toto rozšíření si můžete stáhnout a nainstalovat z [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) nebo přímo z kódu sady Visual Studio. Ujistěte se, že znovu načíst Visual Studio kód po instalaci.

    ![Najít "Rozšíření kódu Visual Studia pro Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Chcete-li zkontrolovat, zda je rozšíření správně nainstalováno, vyberte ikonu Azure, která se zobrazí na panelu nástrojů kódu sady Visual Studio.

    ![Potvrdit správně nainstalovanou příponu](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Další informace naleznete v [tématu Rozšíření Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Chcete-li přispět k open source verzi tohoto rozšíření, navštivte [rozšíření Azure Logic Apps pro Visual Studio Kód na GitHubu](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Otevřete Visual Studio Code. Na panelu nástrojů kódu Visual Studia vyberte ikonu Azure.

   ![Vybrat ikonu Azure na panelu nástrojů Kódu Visual Studia](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. V okně Azure v části **Logic Apps**vyberte **Přihlásit se k Azure**. Až se na přihlašovací stránce Microsoftu zobrazí výzva, přihlaste se pomocí svého účtu Azure.

   ![Vyberte "Přihlásit se do Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Pokud přihlášení trvá déle než obvykle, Visual Studio Code vás vyzve k přihlášení prostřednictvím webu ověřování společnosti Microsoft tím, že vám poskytne kód zařízení. Chcete-li se místo toho přihlásit pomocí kódu, vyberte **použít kód zařízení**.

      ![Místo toho pokračujte s kódem zařízení](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Chcete-li kód zkopírovat, vyberte **kopírovat & otevřít**.

      ![Kopírování kódu pro přihlášení do Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Chcete-li otevřít nové okno prohlížeče a pokračovat na web pro ověřování, vyberte **možnost Otevřít odkaz**.

      ![Potvrzení otevření prohlížeče a přejdeme na web ověřování](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na stránce **Přihlásit se ke svému účtu** zadejte ověřovací kód a vyberte **Další**.

      ![Zadejte ověřovací kód pro přihlášení do Azure.](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Vyberte svůj účet Azure. Po přihlášení můžete zavřít prohlížeč a vrátit se ke kódu sady Visual Studio.

   V podokně Azure se teď v části **Logické aplikace** a **účty integrace** zobrazují předplatná Azure, která jsou přidružená k vašemu účtu. Pokud však nevidíte předplatná, která očekáváte, nebo pokud se v částech zobrazuje příliš mnoho předplatných, postupujte takto:

   1. Přesuňte ukazatel myši na štítek **Logic Apps.** Po zobrazení panelu nástrojů vyberte **Vybrat odběry** (ikona filtru).

      ![Vyhledání nebo filtrování předplatných Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Ze zobrazeného seznamu vyberte předplatná, která chcete zobrazit.

1. V **části Logic Apps**vyberte požadované předplatné. Uzel předplatného rozbalí a zobrazí všechny aplikace logiky, které existují v tomto předplatném.

   ![Vyberte své předplatné Azure.](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > V části **Účty integrace**se při výběru předplatného zobrazí všechny účty integrace, které v tomto předplatném existují.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Vytvořit novou aplikaci logiky

1. Pokud jste se ještě nepřihlásili ke svému účtu Azure a předplatnému z visual studia, [přihlaste se podle předchozích kroků](#sign-in-azure).

1. V kódu Visual Studia v části **Logic Apps**otevřete místní nabídku předplatného a vyberte **Vytvořit aplikaci logiky**.

   ![V nabídce odběr vyberte "Vytvořit aplikaci logiky"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Zobrazí se seznam a zobrazí všechny skupiny prostředků Azure ve vašem předplatném.

1. Ze seznamu skupin prostředků vyberte Vytvořit **novou skupinu prostředků** nebo existující skupinu prostředků. V tomto příkladu vytvořte novou skupinu prostředků.

   ![Vytvoření nové skupiny prostředků Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Zadejte název skupiny prostředků Azure a stiskněte klávesu ENTER.

   ![Zadejte název skupiny prostředků Azure.](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Vyberte oblast Azure, do které chcete uložit metadata aplikace logiky.

   ![Vyberte umístění Azure pro ukládání metadat aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Zadejte název aplikace logiky a stiskněte Enter.

   ![Zadejte název aplikace logiky.](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   V okně Azure se v rámci předplatného Azure zobrazí nová a prázdná aplikace logiky. Visual Studio Code také otevře soubor JSON (.logicapp.json), který obsahuje definici pracovního postupu kostry pro aplikaci logiky. Nyní můžete začít ručně vytvářet definici pracovního postupu aplikace logiky v tomto souboru JSON. Technický odkaz na strukturu a syntaxi pro definici pracovního postupu najdete v [tématu schéma jazyka definice pracovního postupu pro aplikace Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Prázdný soubor JSON definice pracovního postupu aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Tady je například ukázková definice pracovního postupu aplikace logiky, která začíná aktivační událostí RSS a akcí Outlooku Office 365. Obvykle prvky JSON se v každé sekci zobrazují abecedně. Tato ukázka však ukazuje tyto prvky zhruba v pořadí, ve které se v návrháři zobrazí kroky aplikace logiky.

   > [!IMPORTANT]
   > Pokud chcete znovu použít tuto ukázkovou definici aplikace logiky, potřebujete účet @fabrikam.comorganizace Office 365, například . Ujistěte se, že jste fiktivní e-mailovou adresu nahradili vlastní e-mailovou adresou. Pokud chcete použít jiný e-mailový konektor, `Send_an_email_action` jako je Outlook.com nebo Gmail, nahraďte akci podobnou akcí dostupnou z [e-mailového konektoru, který je podporovaný službou Azure Logic Apps](../connectors/apis-list.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Až budete hotovi, uložte definici pracovního postupu aplikace logiky. (Nabídka Soubor > Uložit nebo stiskněte Ctrl+S)

1. Až se zobrazí výzva k nahrání aplikace logiky do předplatného Azure, vyberte **Nahrát**.

   Tento krok publikuje aplikaci logiky na [portálAzure](https://portal.azure.com), který a umožňuje vaše logika žít a běží v Azure.

   ![Nahrání nové aplikace logiky do předplatného Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Zobrazit aplikaci logiky v návrháři

V kódu Visual Studia můžete otevřít aplikaci logiky v návrhovém zobrazení jen pro čtení. I když nelze upravit aplikaci logiky v návrháři, můžete vizuálně zkontrolovat pracovní postup aplikace logiky pomocí zobrazení návrháře.

V okně Azure v části **Logic Apps**otevřete místní nabídku aplikace logiky a vyberte Otevřít **v Návrháři**.

Návrhář jen pro čtení se otevře v samostatném okně a zobrazí pracovní postup vaší aplikace logiky, například:

![Zobrazení aplikace logiky v návrháři jen pro čtení](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Zobrazení na webu Azure Portal

Pokud chcete zkontrolovat aplikaci logiky na webu Azure Portal, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí stejného účtu Azure a předplatného, které je přidružené k vaší aplikaci logiky.

1. Do vyhledávacího pole na webu Azure Portal zadejte název aplikací logiky. Ze seznamu výsledků vyberte aplikaci logiky.

   ![Vaše nová aplikace logiky na webu Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Pokud v kódu Visual Studia upravíte publikovanou aplikaci logiky a uložíte změny, *přepíšete* již nasazenou aplikaci. Chcete-li se vyhnout porušení aplikace logiky v produkčním prostředí a minimalizovat narušení, nejprve deaktivujte aplikaci logiky. Potom můžete reaktivní aplikace logiky poté, co jste potvrdili, že vaše aplikace logiky stále funguje.

1. Pokud jste se ještě nepřihlásili ke svému účtu Azure a předplatnému z visual studia, [přihlaste se podle předchozích kroků](#sign-in-azure).

1. V okně Azure v části **Logic Apps**rozbalte předplatné Azure, abyste mohli zobrazit všechny aplikace logiky v tomto předplatném.

   1. Chcete-li zakázat aplikaci logiky, kterou chcete, otevřete nabídku aplikace logiky a vyberte **Zakázat**.

      ![Zakázání aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Až budete připraveni znovu aktivovat aplikaci logiky, otevřete nabídku aplikace logiky a vyberte **Povolit**.

      ![Povolení aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Úprava nasazené aplikace logiky

V kódu Visual Studia můžete otevřít a upravit definici pracovního postupu pro již nasazenou aplikaci logiky v Azure.

> [!IMPORTANT] 
> Před úpravou aktivně spuštěné aplikace logiky v produkčním prostředí se vyhněte riziku při rozbití této aplikace logiky a minimalizujte narušení tím, že [nejprve zakážete aplikaci logiky](#disable-enable-logic-app).

1. Pokud jste se ještě nepřihlásili ke svému účtu Azure a předplatnému z visual studia, [přihlaste se podle předchozích kroků](#sign-in-azure).

1. V okně Azure v části **Logic Apps**rozbalte předplatné Azure a vyberte požadovanou aplikaci logiky.

1. Otevřete nabídku aplikace logiky a vyberte **Otevřít v editoru**. Nebo vedle názvu aplikace logiky vyberte ikonu úprav.

   ![Otevřít editor pro existující aplikaci logiky](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code otevře soubor .logicapp.json v místní dočasné složce, takže můžete zobrazit definici pracovního postupu aplikace logiky.

   ![Zobrazit definici pracovního postupu pro publikovanou aplikaci logiky](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Proveďte změny v definici pracovního postupu aplikace logiky.

1. Jakmile budete hotovi, uložte změny. (Nabídka Soubor > Uložit nebo stiskněte Ctrl+S)

1. Až se zobrazí výzva k nahrání změn a *přepsání* stávající aplikace logiky na webu Azure Portal, vyberte **Nahrát**.

   Tento krok publikuje aktualizace aplikace logiky na [webu Azure Portal](https://portal.azure.com).

   ![Nahrávání úprav do definice aplikace logiky v Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Zobrazení nebo propagace jiných verzí

V kódu Visual Studia můžete otevřít a zkontrolovat starší verze aplikace logiky. Můžete také povýšit starší verzi na aktuální verzi.

> [!IMPORTANT] 
> Před změnou aktivně spuštěné aplikace logiky v produkčním prostředí se vyhněte riziku při rozbití této aplikace logiky a minimalizujte narušení tím, že [nejprve zakážete aplikaci logiky](#disable-enable-logic-app).

1. V okně Azure v části **Logic Apps**rozbalte předplatné Azure, abyste mohli zobrazit všechny aplikace logiky v tomto předplatném.

1. V části předplatného rozbalte aplikaci **logiky**a rozbalte verze .

   Seznam **verze** zobrazuje starší verze aplikace logiky, pokud existují.

   ![Předchozí verze aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Chcete-li zobrazit starší verzi, vyberte oba kroky:

   * Chcete-li zobrazit definici JSON, **vyberte**v části Verze číslo verze pro tuto definici. Nebo otevřete místní nabídku této verze a vyberte **Otevřít v editoru**.

     V místním počítači se otevře nový soubor, který zobrazuje definici JSON této verze.

   * Chcete-li zobrazit verzi v zobrazení návrháře jen pro čtení, otevřete místní nabídku této verze a vyberte **Otevřít v návrháři**.

1. Chcete-li propagovat starší verzi na aktuální verzi, postupujte takto:

   1. V **části Verze**otevřete místní nabídku předchozí verze a vyberte **Propagovat**.

      ![Povýšení starší verze](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Chcete-li pokračovat i po zobrazení výzvy k potvrzení kódu sady Visual Studio, vyberte **možnost Ano**.

      ![Potvrdit propagaci starší verze](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code povýší vybranou verzi na aktuální verzi a přiřadí nové číslo povýšené verzi. Dříve aktuální verze se nyní zobrazí pod propagovanou verzí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření aplikací logiky pomocí Visual Studia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)