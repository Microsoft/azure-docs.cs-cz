---
title: Automatizace úloh a pracovních postupů pomocí Visual Studio Code
description: Vytvoření nebo úprava definic pracovního postupu aplikace logiky pomocí Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/28/2020
ms.openlocfilehash: c4d4c659fd1c33c9ffc1b833b92d0727b5c17bf7
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922897"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Rychlé zprovoznění: Vytváření a správa definic pracovních postupů aplikací logiky pomocí Visual Studio Code

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a Visual Studio Code můžete vytvářet a spravovat aplikace logiky, které vám pomůžou automatizovat úlohy, pracovní postupy a procesy pro integraci aplikací, dat, systémů a služeb v organizacích a podnicích. V tomto rychlém startu se dozvíte, jak můžete vytvořit a upravit základní definice pracovních postupů, které používají JavaScript Object Notation (JSON) pro Logic Apps prostřednictvím prostředí založeného na kódu. Můžete také pracovat na stávajících Logic Apps, které už jsou nasazené v Azure.

I když tyto stejné úlohy můžete provádět v [Azure Portal](https://portal.azure.com) a v aplikaci Visual Studio, můžete začít rychleji v Visual Studio Code, když už jste obeznámeni s definicemi aplikace logiky a chcete pracovat přímo v kódu. Můžete například zakázat, povolit, odstranit a aktualizovat již vytvořené aplikace logiky. Můžete také pracovat na Logic Apps a účtech pro integraci z libovolné vývojové platformy, kde Visual Studio Code běžet, jako je Linux, Windows nebo Mac.

V tomto článku můžete vytvořit stejnou aplikaci logiky z tohoto [rychlého](../logic-apps/quickstart-create-first-logic-app-workflow.md)startu, která se zaměřuje na základní koncepty. Můžete se také [naučit vytvořit ukázkovou aplikaci v aplikaci Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)a [Naučte se vytvářet a spravovat aplikace prostřednictvím rozhraní Azure Command-Line (CLI)](quickstart-logic-apps-azure-cli.md). V Visual Studio Code aplikace logiky vypadá jako v tomto příkladu:

![Příklad definice pracovního postupu aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Než začnete, ujistěte se, že máte tyto položky:

* Pokud nemáte účet a předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalost [definicí pracovních postupů aplikací logiky](../logic-apps/logic-apps-workflow-definition-language.md) a jejich struktury, jak je popsáno ve formátu JSON

  Pokud s Logic Apps začínáte, vyzkoušejte si tento [rychlý Start](../logic-apps/quickstart-create-first-logic-app-workflow.md), který vytvoří vaše první aplikace logiky v Azure Portal a zaměřuje se na základní koncepty.

* Přístup k webu pro přihlášení k Azure a předplatnému Azure

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio Code verze 1.25.1 nebo novější](https://code.visualstudio.com/), což je zdarma

  * Visual Studio Code rozšíření pro Azure Logic Apps

    Toto rozšíření si můžete stáhnout a nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) nebo přímo v rámci Visual Studio Code. Ujistěte se, že jste po instalaci znovu znovu Visual Studio Code.

    ![Najít rozšíření Visual Studio Code pro Azure Logic Apps](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Chcete-li ověřit, zda je rozšíření nainstalováno správně, vyberte ikonu Azure, která se zobrazí na panelu nástrojů Visual Studio Code.

    ![Potvrďte správnou instalaci rozšíření.](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Další informace najdete v tématu [rozšíření Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Pokud chcete přispět k této verzi Open Source tohoto rozšíření, navštivte [rozšíření Azure Logic Apps pro Visual Studio Code na GitHubu](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Přístup k Azure ze sady Visual Studio

1. Otevřete Visual Studio Code. Na panelu nástrojů Visual Studio Code vyberte ikonu Azure.

   ![Výběr ikony Azure na panelu nástrojů Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. V okně Azure v části **Logic Apps** vyberte **Přihlásit se k Azure**. Až se vám zobrazí přihlašovací stránka Microsoftu, přihlaste se pomocí svého účtu Azure.

   ![Vyberte možnost přihlásit se k Azure.](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Pokud se přihlášení trvá déle než obvykle, Visual Studio Code vás vyzve, abyste se přihlásili přes web Microsoftu pro ověřování tím, že vám poskytne kód zařízení. Chcete-li se místo toho přihlásit pomocí kódu, vyberte **použít kód zařízení**.

      ![Místo toho pokračovat pomocí kódu zařízení](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Chcete-li zkopírovat kód, vyberte možnost **kopírovat & otevřít**.

      ![Kopírovat kód pro přihlášení k Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Chcete-li otevřít nové okno prohlížeče a pokračovat na webu ověřování, vyberte možnost **Otevřít odkaz**.

      ![Potvrzení otevření prohlížeče a přechodu na web ověřování](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na stránce **Přihlásit se k účtu** zadejte svůj ověřovací kód a vyberte **Další**.

      ![Zadejte ověřovací kód pro přihlášení Azure.](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Vyberte svůj účet Azure. Po přihlášení můžete zavřít prohlížeč a vrátit se k Visual Studio Code.

   V podokně Azure se v sekcích **Logic Apps** a **integrace** teď zobrazují předplatná Azure přidružená k vašemu účtu. Pokud ale nevidíte odběry, které jste očekávali, nebo pokud se v sekcích zobrazuje příliš mnoho předplatných, postupujte takto:

   1. Přesuňte ukazatel myši na **Logic Apps** popisku. Po zobrazení panelu nástrojů vyberte **Vybrat odběry** (ikona filtru).

      ![Hledání nebo filtrování předplatných Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. V seznamu, který se zobrazí, vyberte odběry, které chcete zobrazit.

1. V části **Logic Apps** vyberte požadované předplatné. Uzel předplatné rozbalí a zobrazí všechny aplikace logiky, které v tomto předplatném existují.

   ![Vyberte své předplatné Azure.](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > V části **účty pro integraci** vyberte předplatné, ve kterém se zobrazí všechny účty pro integraci, které v tomto předplatném existují.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Vytvoření nové aplikace logiky

1. Pokud jste se ještě přihlásili k účtu Azure a předplatnému z Visual Studio Code, [Přihlaste se hned pomocí předchozích kroků](#access-azure).

1. V aplikaci Visual Studio Code v části **Logic Apps** otevřete místní nabídku vašeho předplatného a vyberte **vytvořit aplikaci logiky**.

   ![V nabídce předplatné vyberte vytvořit aplikaci logiky.](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Zobrazí se seznam, ve kterém se zobrazí všechny skupiny prostředků Azure ve vašem předplatném.

1. V seznamu Skupina prostředků vyberte buď možnost **vytvořit novou skupinu prostředků** , nebo existující skupinu prostředků. V tomto příkladu vytvořte novou skupinu prostředků.

   ![Vytvoření nové skupiny prostředků Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Zadejte název skupiny prostředků Azure a stiskněte klávesu ENTER.

   ![Zadejte název skupiny prostředků Azure.](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Vyberte oblast Azure, do které chcete uložit metadata aplikace logiky.

   ![Vyberte umístění Azure pro ukládání metadat aplikace logiky.](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Zadejte název vaší aplikace logiky a stiskněte klávesu ENTER.

   ![Zadejte název aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   V okně Azure pod vaším předplatným Azure se zobrazí vaše nová a prázdná aplikace logiky. Visual Studio Code také otevře soubor JSON (.logicapp.json), který obsahuje definici pracovního postupu pro vaši aplikaci logiky. Nyní můžete ručně vytvořit definici pracovního postupu aplikace logiky v tomto souboru JSON. Technické informace o struktuře a syntaxi definice pracovního postupu najdete v tématu [schéma jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Prázdný soubor JSON definice pracovního postupu aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Například tady je ukázková definice pracovního postupu aplikace logiky, která začíná triggerem RSS a akcí Office 365 Outlook. Prvky JSON se obvykle zobrazují abecedně v jednotlivých oddílech. Tato ukázka ale tyto prvky zobrazuje přibližně v pořadí, ve kterém se v Návrháři objeví kroky aplikace logiky.

   > [!IMPORTANT]
   > Pokud chcete tuto ukázkovou definici aplikace logiky použít znovu, budete potřebovat účet organizace, například @fabrikam.com . Zajistěte, aby se fiktivní e-mailová adresa nahradila vlastní e-mailovou adresou. Pokud chcete použít jiný e-mailový konektor, jako je Outlook.com nebo Gmail, nahraďte `Send_an_email_action` akci podobnou akcí dostupnou z [e-mailového konektoru, který podporuje Azure Logic Apps](../connectors/apis-list.md).
   >
   > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. 
   > Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). 
   > Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

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

1. Až budete hotovi, uložte definici pracovního postupu aplikace logiky. (Nabídka soubor > uložit, nebo stiskněte klávesy CTRL + S)

1. Po zobrazení výzvy k odeslání vaší aplikace logiky do předplatného Azure vyberte **Odeslat**.

   Tento krok publikuje vaši aplikaci logiky na [Azure Portal](https://portal.azure.com), která a zajišťuje, aby byla logika živá a spuštěná v Azure.

   ![Nahrání nové aplikace logiky do předplatného Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Zobrazení aplikace logiky v Návrháři

V Visual Studio Code můžete aplikaci logiky otevřít v zobrazení návrhu jen pro čtení. I když aplikaci logiky v Návrháři nemůžete upravovat, můžete pracovní postup vaší aplikace logiky vizuálně kontrolovat pomocí zobrazení návrháře.

V okně Azure v části **Logic Apps** otevřete místní nabídku aplikace logiky a v **Návrháři vyberte otevřít**.

Návrhář, který je jen pro čtení, se otevře v samostatném okně a zobrazí pracovní postup vaší aplikace logiky, například:

![Zobrazení aplikace logiky v Návrháři jen pro čtení](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Zobrazení na webu Azure Portal

K revizi aplikace logiky v Azure Portal použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného účtu Azure a předplatného, které je přidružené k vaší aplikaci logiky.

1. Do vyhledávacího pole Azure Portal zadejte název vaší Logic Apps. V seznamu výsledků vyberte svou aplikaci logiky.

   ![Vaše nová aplikace logiky v Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Pokud v Visual Studio Code upravíte publikovanou aplikaci logiky a uložíte změny, *přepíšete* už nasazenou aplikaci. Aby nedošlo k narušení vaší aplikace logiky v produkčním prostředí a minimalizovalo přerušení, nejprve deaktivujte aplikaci logiky. Aplikaci logiky pak můžete znovu aktivovat, až ověříte, že vaše aplikace logiky stále funguje.

1. Pokud jste se ještě přihlásili k účtu Azure a předplatnému z Visual Studio Code, [Přihlaste se hned pomocí předchozích kroků](#access-azure).

1. V okně Azure v části **Logic Apps** rozbalte své předplatné Azure, abyste mohli zobrazit všechny aplikace logiky v tomto předplatném.

   1. Chcete-li zakázat aplikaci logiky, kterou chcete, otevřete nabídku aplikace logiky a vyberte možnost **Zakázat**.

      ![Zakázání aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Až budete připraveni znovu aktivovat aplikaci logiky, otevřete nabídku aplikace logiky a vyberte **Povolit**.

      ![Povolení aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Upravit nasazenou aplikaci logiky

V Visual Studio Code můžete otevřít a upravit definici pracovního postupu pro už nasazenou aplikaci logiky v Azure.

> [!IMPORTANT] 
> Před úpravou aktivně běžící aplikace logiky v produkčním prostředí Vyhněte riziku při rozdělování této aplikace logiky a minimalizaci přerušení tím, že [nejprve zakážete aplikaci logiky](#disable-enable-logic-app).

1. Pokud jste se ještě přihlásili k účtu Azure a předplatnému z Visual Studio Code, [Přihlaste se hned pomocí předchozích kroků](#access-azure).

1. V okně Azure v části **Logic Apps** rozbalte své předplatné Azure a vyberte aplikaci logiky, kterou chcete.

1. Otevřete nabídku aplikace logiky a **v editoru vyberte otevřít**. Případně klikněte na ikonu Upravit vedle názvu vaší aplikace logiky.

   ![Otevřít editor pro existující aplikaci logiky](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code otevře .logicapp.jsv souboru v místní dočasné složce, abyste mohli zobrazit definici pracovního postupu vaší aplikace logiky.

   ![Zobrazit definici pracovního postupu pro publikovanou aplikaci logiky](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Proveďte změny v definici pracovního postupu aplikace logiky.

1. Jakmile budete hotovi, uložte změny. (Nabídka soubor > uložit, nebo stiskněte klávesy CTRL + S)

1. Po zobrazení výzvy k odeslání změn a *přepsání* stávající aplikace logiky v Azure Portal vyberte **Odeslat**.

   Tento krok publikuje vaše aktualizace do aplikace logiky v [Azure Portal](https://portal.azure.com).

   ![Nahrání úprav do definice aplikace logiky v Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Zobrazení nebo zvýšení úrovně jiných verzí

V Visual Studio Code můžete otevřít a zkontrolovat starší verze aplikace logiky. Můžete také zvýšit úroveň předchozí verze na aktuální verzi.

> [!IMPORTANT] 
> Než začnete měnit aktivně běžící aplikace logiky v produkčním prostředí, vyhněte se riziku při rozdělování aplikace logiky a minimalizaci přerušení tím, že [nejprve zakážete aplikaci logiky](#disable-enable-logic-app).

1. V okně Azure v části **Logic Apps** rozbalte své předplatné Azure, abyste mohli zobrazit všechny aplikace logiky v tomto předplatném.

1. V rámci vašeho předplatného rozbalte aplikaci logiky a rozbalte položku **verze**.

   Seznam **verze** zobrazuje starší verze vaší aplikace logiky, pokud existují.

   ![Předchozí verze vaší aplikace logiky](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Chcete-li zobrazit předchozí verzi, vyberte jeden z těchto kroků:

   * Definici JSON zobrazíte tak, že v části **verze** vyberete číslo verze této definice. Případně otevřete místní nabídku této verze a **v editoru vyberte otevřít**.

     V místním počítači se otevře nový soubor a zobrazí se definice JSON této verze.

   * Chcete-li zobrazit verzi v zobrazení návrháře jen pro čtení, otevřete místní nabídku této verze a vyberte možnost **otevřít v Návrháři**.

1. Pro zvýšení úrovně předchozí verze na aktuální verzi použijte následující postup:

   1. V části **verze** otevřete místní nabídku předchozí verze a vyberte **zvýšit úroveň**.

      ![Zvýšit úroveň předchozí verze](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Pokud chcete pokračovat, až Visual Studio Code zobrazí výzvu k potvrzení, vyberte **Ano**.

      ![Potvrdit zvýšení úrovně předchozí verze](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code propaguje vybranou verzi na aktuální verzi a přiřadí nové číslo k propagované verzi. Dříve aktuální verze se nyní zobrazuje v povýšené verzi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření stavových a bezstavových aplikací logiky v Visual Studio Code (Preview)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
