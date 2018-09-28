---
title: Vytvoření a správa automatizovaných pracovních postupů pomocí Visual Studio Code – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření a Správa aplikací logiky v aplikaci Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 09/26/2018
ms.openlocfilehash: 36e8161eb8be16b75d843a2b7a65fc3dd70d9c02
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405284"
---
# <a name="create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Vytvoření a správa pracovních postupů aplikace logiky automatizované – Visual Studio Code

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a Visual Studio Code, můžete vytvářet a spravovat aplikace logiky, které vám pomůžou automatizovat úlohy, pracovní postupy a procesy pro integraci aplikací, dat, systémy a služby napříč organizacím a podnikům umožňují. Tento článek popisuje, jak můžete vytvářet a upravovat definice pracovního postupu aplikací logiky při práci v prostředí založená na kódu. Můžete také pracovat na logic apps, které jsou již nasazeny <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> v cloudu. 

Přestože můžete tyto stejné úlohy provádět <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> a v sadě Visual Studio, můžete začít rychleji v aplikaci Visual Studio Code když budete chtít pracovat přímo v kódu. Například můžete také zakázat, povolit, odstranit a aktualizovat už vytvořeného logic apps. Navíc můžete pracovat na logic apps a účty pro integraci z jakékoli platformy vývoje, kde běží Visual Studio Code, jako je Linux, Windows a Mac.

Pro účely tohoto článku můžete vytvořit stejnou aplikaci logiky jako v [rychlý start k vytvoření aplikace logiky na webu Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), která se zaměřuje více na základní koncepty. Ve Visual Studio Code nastavení aplikace logiky bude vypadat jako v tomto příkladu:

![Hotová aplikace logiky](./media/create-logic-apps-visual-studio-code/overview.png)

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte tyto položky:

* Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní znalosti o aplikaci logiky [definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md) a jejich strukturu, která používá zápis JSON (JavaScript Object)

* Přístup k webu pro přihlášení k Azure a vaším předplatným Azure

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code verze 1.25.1 nebo novější</a>, které je zdarma

  * Rozšíření sady Visual Studio Code pro Azure Logic Apps

    Nainstalujete toto rozšíření z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) nebo přímo v rámci Visual Studio Code. 
    Ujistěte se, že je znovu po instalaci Visual Studio Code. 

    ![Vyhledejte "Rozšíření Visual Studio Code pro Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Zkontrolujte, že rozšíření nainstalováno správně, Azure se zobrazí ikona v panelu nástrojů Visual Studio Code. 

    ![Nainstalované rozšíření](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Další informace najdete v tématu <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Marketplace pro rozšíření</a>. Můžete také zobrazit a odesílat příspěvky v open-source verze tohoto rozšíření najdete [rozšíření Azure Logic Apps pro Visual Studio Code na Githubu](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Otevřete Visual Studio Code. Na panelu nástrojů Visual Studio Code vyberte ikonu Azure. 

   ![Vyberte ikonu systému Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. V okně Azure v rámci **Logic Apps**vyberte **přihlášení k Azure**. 

   ![Vyberte "Přihlaste se k Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Nyní budete vyzváni k přihlášení pomocí zadaného ověřovacího kódu. 

1. Zkopírujte kód ověřování a klikněte na tlačítko **zkopírovat a otevřít**, tím se otevře nové okno prohlížeče.

   ![Přihlásit se zeptat](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Zadejte svůj ověřovací kód. Po zobrazení výzvy zvolte **pokračovat**.

   ![Zadejte kód.](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Vyberte svůj účet Azure. Po přihlášení můžete zavřete okno prohlížeče a vraťte se do Visual Studio Code.

   V okně Azure Logic Apps podokně a podokně účty pro integraci nyní zobrazit předplatná Azure ve vašem účtu. 

   ![Výběr předplatného](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Pokud nevidíte předplatná očekáváte, vedle **Logic Apps** popisek, vyberte **vyberte předplatná** (ikonu filtru). Vyhledejte a vyberte předplatné, které chcete.

1. Chcete-li zobrazit všechny existující aplikace logiky nebo účty pro integraci ve vašem předplatném Azure, rozbalte vaše předplatné.

   ![Zobrazit logic apps a účty pro integraci](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

1. Pokud nejste přihlášeni ke svému předplatnému Azure z uvnitř Visual Studio Code, postupujte podle kroků v tomto článku [přihlásit](#sign-in-azure).

1. V kontextové nabídce vašeho předplatného vyberte **vytvořit**.

   ![Vyberte možnost "Vytvořit"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. V seznamu, který zobrazuje skupin prostředků Azure v rámci vašeho předplatného, vyberte existující skupinu prostředků nebo **vytvořit novou skupinu prostředků**. 

   Tento příklad vytvoří novou skupinu prostředků:

   ![Vytvořte novou skupinu prostředků](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Zadejte název skupiny prostředků Azure a potom stiskněte klávesu ENTER.

   ![Zadejte název skupiny prostředků](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Vyberte umístění datového centra, kam chcete uložit metadata aplikace logiky.

   ![Vybrat umístění](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Zadejte název pro svou aplikaci logiky a potom stiskněte klávesu ENTER.

   ![Název aplikace logiky](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Nové aplikace logiky se teď zobrazí v okně Azure v rámci vašeho předplatného Azure. Teď můžete začít vytvářet definice pracovního postupu aplikace logiky.

1. V místní nabídce aplikace logiky, vyberte **otevřená v editoru**. 

   ![Aplikace logiky otevřené v editoru](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code se otevře šablonu definice pracovního postupu aplikace logiky (. soubor logicapp.json), můžete začít vytvářet pracovní postup aplikace logiky.

   ![Nová definice pracovního postupu aplikace logiky](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. V souboru logiku aplikace pracovního postupu definice šablony můžete začněte sestavovat definice pracovního postupu aplikace logiky. Technické informace najdete v článku [schéma jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Tady je definici příklad logiky. Obvykle JSON prvky se zobrazí podle abecedy v rámci každého oddílu, ale tento příklad ukazuje, tyto prvky zhruba v pořadí, ve kterém se zobrazí v návrháři aplikace logiky kroky.

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

1. Jakmile budete hotovi, uložte soubor definice aplikace logiky. Když vás vyzve k potvrzení odeslání definici aplikace logiky do vašeho předplatného Azure Visual Studio Code vyberte **nahrát**.

   ![Nahrát novou aplikaci logiky](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Poté, co Visual Studio Code publikuje vaši aplikaci logiky do Azure, vaši aplikaci mohou najít nyní za provozu a spuštěná na webu Azure Portal. 

   ![Publikování aplikace logiky na webu Azure portal](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Upravit aplikace logiky

Pro práci na existující aplikaci logiky, která je již nasazené v Azure, můžete otevřít soubor definice pracovního postupu vaší aplikace ve Visual Studio Code.

1. Pokud nejste přihlášeni ke svému předplatnému Azure z uvnitř Visual Studio Code, postupujte podle kroků v tomto článku [přihlásit](#sign-in-azure).

1. V okně Azure v rámci **Logic Apps**rozbalte vaše předplatné Azure a vyberte aplikaci logiky, kterou chcete. 

1. V nabídce aplikace logiky, vyberte **otevřená v editoru**. Nebo vedle názvu aplikace logiky, vyberte ikonu pro úpravy.

   ![Otevřete editor pro existující aplikace logiky](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code se otevře. soubor logicapp.json pro definici pracovního postupu aplikace logiky.

   ![Definice pracovního postupu aplikace logiky otevřené](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Proveďte požadované změny v definici aplikace logiky.

1. Jakmile budete hotovi, uložte změny.

1. Když Visual Studio Code vás vyzve, abyste aktualizovat definici aplikace logiky ve vašem předplatném Azure, zvolte **nahrát**. 

   ![Uložit provedené úpravy](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="http://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

