---
title: Vytváření a Správa automatizovaných pracovních postupů pomocí Visual Studio Code-Azure Logic Apps
description: Rychlý Start pro vytváření a správu Logic Apps s JSON v Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: b9f9a402ecde09dd00c2b070f784858a141e556b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309002"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Rychlý start: Vytváření a Správa automatizovaných pracovních postupů aplikací logiky – Visual Studio Code

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a Visual Studio Code můžete vytvářet a spravovat aplikace logiky, které vám pomůžou automatizovat úlohy, pracovní postupy a procesy pro integraci aplikací, dat, systémů a služeb v organizacích a podnicích. V tomto rychlém startu se dozvíte, jak můžete vytvářet a upravovat definice pracovních postupů aplikací logiky pomocí schématu definice pracovního postupu v JavaScript Object Notation (JSON) prostřednictvím prostředí založeného na kódu. Můžete také pracovat na stávajících Logic Apps, které už jsou nasazené v Azure. 

I když tyto stejné úlohy můžete provádět v Azure Portal (https://portal.azure.com) a v aplikaci Visual Studio, můžete začít rychleji v Visual Studio Code, když už jste obeznámení s definicemi aplikace logiky a chcete pracovat přímo v kódu. Můžete například zakázat, povolit, odstranit a aktualizovat již vytvořené aplikace logiky. Můžete také pracovat na Logic Apps a účtech pro integraci z libovolné vývojové platformy, kde Visual Studio Code běžet, jako je Linux, Windows nebo Mac.

V tomto článku můžete vytvořit stejnou aplikaci logiky jako v [rychlém startu pro vytvoření aplikace logiky v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), která se zaměřuje na základní koncepty. V Visual Studio Code aplikace logiky vypadá jako v tomto příkladu:

![Hotová aplikace logiky](./media/create-logic-apps-visual-studio-code/overview.png)

Než začnete, ujistěte se, že máte tyto položky:

* Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalost [definicí pracovních postupů aplikací logiky](../logic-apps/logic-apps-workflow-definition-language.md) a jejich struktury, které používají JavaScript Object Notation (JSON) 

  Pokud Logic Apps začínáte, vyzkoušejte si rychlý Start, který vás provede [vytvořením první aplikace logiky v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), která se zaměřuje na základní koncepty. 

* Přístup k webu pro přihlášení k Azure a předplatnému Azure

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje: 

  * [Visual Studio Code verze 1.25.1 nebo novější](https://code.visualstudio.com/), což je zdarma

  * Visual Studio Code rozšíření pro Azure Logic Apps

    Toto rozšíření si můžete stáhnout a nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) nebo přímo v rámci Visual Studio Code. 
    Nezapomeňte znovu načíst Visual Studio Code po instalaci. 

    ![Najít rozšíření Visual Studio Code pro Azure Logic Apps](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Pokud chcete ověřit, jestli je správně nainstalovaná přípona, zobrazí se na panelu nástrojů Visual Studio Code ikona Azure. 

    ![Rozšíření nainstalováno](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Další informace najdete v tématu [rozšíření Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Příspěvky na verzi Open Source tohoto rozšíření můžete zobrazit a odeslat i tak, že navštívíte [rozšíření Azure Logic Apps pro Visual Studio Code na GitHubu](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Otevřete Visual Studio Code. Na panelu nástrojů Visual Studio Code vyberte ikonu Azure. 

   ![Vybrat ikonu Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. V okně Azure v části **Logic Apps**vyberte **Přihlásit se k Azure**. 

   ![Vyberte možnost přihlásit se k Azure.](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Nyní se zobrazí výzva, abyste se přihlásili pomocí poskytnutého ověřovacího kódu. 

1. Zkopírujte kód pro ověřování a pak zvolte **kopírovat & otevřít**, který otevře nové okno prohlížeče.

   ![Výzva k přihlášení](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Zadejte svůj ověřovací kód. Po zobrazení výzvy klikněte na tlačítko **pokračovat**.

   ![Zadejte kód](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Vyberte svůj účet Azure. Po přihlášení můžete zavřít prohlížeč a vrátit se k Visual Studio Code.

   V okně Azure teď podokno Logic Apps a účty pro integraci zobrazuje předplatná Azure ve vašem účtu. 

   ![Výběr předplatného](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Pokud nevidíte odběry, které jste očekávali, zvolte vedle **Logic Apps** popisku **možnost vybrat odběry** (ikona filtru). Vyhledejte a vyberte odběry, které chcete.

1. Pokud si chcete zobrazit všechny existující aplikace logiky nebo účty pro integraci v rámci vašeho předplatného Azure, rozbalte své předplatné.

   ![Zobrazení aplikací logiky a integračních účtů](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

1. Pokud jste se přihlásili ke svému předplatnému Azure z Visual Studio Code v rámci služby, [Přihlaste se hned](#sign-in-azure)podle kroků v tomto článku.

1. V místní nabídce vašeho předplatného vyberte **vytvořit**.

   ![Vyberte vytvořit.](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. V seznamu, který zobrazuje skupiny prostředků Azure ve vašem předplatném, vyberte existující skupinu prostředků nebo **vytvořte novou skupinu prostředků**. 

   Tento příklad vytvoří novou skupinu prostředků:

   ![Vytvořte novou skupinu prostředků](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Zadejte název skupiny prostředků Azure a potom stiskněte klávesu ENTER.

   ![Název skupiny prostředků](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Vyberte umístění datového centra, kde se mají ukládat metadata aplikace logiky.

   ![Vybrat umístění](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Zadejte název aplikace logiky a potom stiskněte klávesu ENTER.

   ![Pojmenování aplikace logiky](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Vaše nová aplikace logiky se teď zobrazí v okně Azure v rámci vašeho předplatného Azure. Nyní můžete začít vytvářet definici pracovního postupu vaší aplikace logiky.

1. V místní nabídce aplikace logiky vyberte **otevřít v editoru**. 

   ![Otevření aplikace logiky v editoru](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code otevře šablonu definice pracovního postupu aplikace logiky (soubor. logicapp. JSON), abyste mohli začít vytvářet pracovní postup vaší aplikace logiky.

   ![Nová definice pracovního postupu aplikace logiky](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. V souboru šablony definice pracovního postupu aplikace logiky začněte sestavovat definici pracovního postupu vaší aplikace logiky. Technické informace najdete v tématu [schéma jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Tady je příklad definice logiky. Prvky JSON se obvykle zobrazují abecedně v každé části, ale v této ukázce jsou tyto prvky přibližně v pořadí, ve kterém se v Návrháři objeví kroky aplikace logiky.

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
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Až budete hotovi, uložte soubor definice aplikace logiky. Když Visual Studio Code zobrazí výzvu k potvrzení odeslání definice aplikace logiky do předplatného Azure, klikněte na **Odeslat**.

   ![Nahrání nové aplikace logiky](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Jakmile Visual Studio Code publikuje vaši aplikaci logiky do Azure, můžete svou aplikaci teď v Azure Portal spustit v reálném čase. 

   ![Aplikace logiky publikovaná v Azure Portal](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Úprava aplikace logiky

Pokud chcete pracovat na stávající aplikaci logiky, která je už nasazená v Azure, můžete otevřít soubor definice pracovního postupu této aplikace v Visual Studio Code.

1. Pokud jste se přihlásili ke svému předplatnému Azure z Visual Studio Code v rámci služby, [Přihlaste se hned](#sign-in-azure)podle kroků v tomto článku.

1. V okně Azure v části **Logic Apps**rozbalte své předplatné Azure a vyberte aplikaci logiky, kterou chcete. 

1. V nabídce vaší aplikace logiky vyberte **otevřít v editoru**. Případně klikněte na ikonu Upravit vedle názvu vaší aplikace logiky.

   ![Otevřít editor pro existující aplikaci logiky](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code otevře soubor. logicapp. JSON pro definici pracovního postupu vaší aplikace logiky.

   ![Otevřená definice pracovního postupu aplikace logiky](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Proveďte změny v definici aplikace logiky.

1. Jakmile budete hotovi, uložte změny.

1. Když Visual Studio Code zobrazí výzvu k aktualizaci definice aplikace logiky v předplatném Azure, vyberte **Odeslat**. 

   ![Nahrání úprav](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> * [Vytváření aplikací logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)