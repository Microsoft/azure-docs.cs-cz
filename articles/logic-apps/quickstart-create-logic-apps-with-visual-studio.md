---
title: Automatizace úloh a pracovních postupů pomocí sady Visual Studio
description: Vytváření, plánování a spouštění automatizovaných pracovních postupů pro podnikovou integraci pomocí Azure Logic Apps a sady Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/27/2020
ms.openlocfilehash: 1301af3bd17ce44720d77aa1b812b78bbe57ffc9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101387"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rychlé zprovoznění: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a sady Visual Studio můžete vytvářet pracovní postupy pro automatizaci úloh a procesů, které integrují aplikace, data, systémy a služby napříč podniky a organizacemi. Tento rychlý start ukazuje, jak můžete navrhovat a sestavovat tyto pracovní postupy vytvářením aplikací logiky v sadě Visual Studio a nasazováním těchto aplikací do Azure. I když můžete provádět tyto úlohy v Azure Portal, Visual Studio umožňuje přidat vaše aplikace logiky do správy zdrojových kódů, publikovat různé verze a vytvářet Azure Resource Manager šablony pro různá prostředí nasazení.

Pokud s Azure Logic Apps teprve začínáte a chcete jenom základní koncepty, vyzkoušejte si [rychlý Start pro vytvoření aplikace logiky v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Návrhář aplikace logiky funguje podobně jak v Azure Portal, tak i v sadě Visual Studio.

V tomto rychlém startu vytvoříte stejnou aplikaci logiky pomocí sady Visual Studio jako rychlý Start Azure Portal. Můžete se také naučit [vytvořit ukázkovou aplikaci v Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)a [vytvářet a spravovat aplikace logiky prostřednictvím rozhraní Azure Command-Line (Azure CLI)](quickstart-logic-apps-azure-cli.md). Tato aplikace logiky monitoruje informační kanál RSS webu a odesílá e-maily pro každou novou položku v tomto informačním kanálu. Vaše dokončená aplikace logiky bude vypadat jako tento pracovní postup vysoké úrovně:

![Snímek obrazovky, který zobrazuje pracovní postup dokončené aplikace logiky na nejvyšší úrovni.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Pokud máte předplatné Azure Government, postupujte podle těchto dalších kroků a [nastavte sadu Visual Studio pro Azure Government Cloud](#azure-government).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio 2019, 2017 nebo 2015 – Community Edition nebo vyšší](https://aka.ms/download-visual-studio). V tomto rychlém startu se používá Visual Studio Community 2017.

    > [!IMPORTANT]
    > Když nainstalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali úlohu **vývoj pro Azure** .

  * [Microsoft Azure SDK pro .NET (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). Další informace o sadě [Azure SDK pro .NET](/dotnet/azure/dotnet-tools?tabs=vs).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Nejnovější Azure Logic Apps nástroje pro rozšíření sady Visual Studio pro požadovanou verzi:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při používání vloženého Návrháře aplikace logiky

  Návrhář potřebuje připojení k Internetu, aby mohl vytvářet prostředky v Azure a číst vlastnosti a data z konektorů ve vaší aplikaci logiky.

* E-mailový účet, který podporuje Logic Apps, jako je například Outlook pro Microsoft 365, Outlook.com nebo Gmail. Pro jiné poskytovatele zkontrolujte [seznam konektorů](/connectors/). V tomto příkladu se používá Office 365 Outlook. Pokud použijete jiného poskytovatele, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Nastavení sady Visual Studio pro Azure Government

### <a name="visual-studio-2017"></a>Visual Studio 2017

Můžete použít rozšíření sady [Visual Studio pro selektory prostředí Azure](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), které si můžete stáhnout a nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

### <a name="visual-studio-2019"></a>Visual Studio 2019

Pokud chcete pracovat s předplatnými Azure Government v Azure Logic Apps, musíte [do sady Visual Studio přidat koncový bod zjišťování pro Azure Government Cloud](../azure-government/documentation-government-connect-vs.md). *Před přihlášením k aplikaci Visual Studio pomocí účtu Azure Government* je však nutné přejmenovat soubor JSON, který je generován po přidání koncového bodu zjišťování pomocí následujících kroků:

1. Zavřete Visual Studio.

1. Najděte vygenerovaný soubor JSON s názvem `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` v tomto umístění:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Přejmenujte soubor JSON na `AadProvider.Configuration.json` .

1. Restartujte Visual Studio.

1. Pokračujte postupem pro přihlašování pomocí účtu Azure Government.

Pokud chcete toto nastavení vrátit zpět, odstraňte soubor JSON v následujícím umístění a restartujte Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

Začněte vytvořením [projektu skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/management/overview.md).

1. Spusťte Visual Studio. Přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt** . (Klávesnice: Ctrl + Shift + N)

   ![Výběr možnosti Nový > Projekt v nabídce Soubor](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. V části **Nainstalováno** , vyberte **Visual C#** nebo **Visual Basic** . Vyberte **cloudovou**  >  **skupinu prostředků Azure** . Pojmenujte svůj projekt, například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Názvy skupin prostředků můžou obsahovat jenom písmena, číslice, tečky ( `.` ), podtržítka ( `_` ), spojovníky ( `-` ) a kulaté závorky ( `(` , `)` ), ale nemohou *končit* tečkami ( `.` ).
   >
   > Pokud se **Cloud** nebo **Skupina prostředků Azure** nezobrazí, ujistěte se, že jste nainstalovali sadu Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle následujících kroků:

   1. V poli **vytvořit nový projekt** vyberte projekt **skupiny prostředků Azure** pro Visual C# nebo Visual Basic. Vyberte **Další** .

   1. Zadejte název skupiny prostředků Azure, kterou chcete použít, a další informace o projektu. Vyberte **Vytvořit** .

1. V seznamu šablon vyberte šablonu **Aplikace logiky** . Vyberte **OK** .

   ![Výběr šablony Aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Jakmile sada Visual Studio vytvoří projekt, otevře se Průzkumník řešení, ve kterém se zobrazí vaše řešení. Ve vašem řešení **LogicApp.js** v souboru neukládá pouze definici aplikace logiky, ale je to také Azure Resource Manager šablona, kterou můžete použít pro nasazení.

   ![Průzkumník řešení se zobrazeným novým řešením aplikace logiky a souborem nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

Když máte projekt skupiny prostředků Azure, vytvořte aplikaci logiky pomocí **prázdné šablony aplikace logiky** .

1. V Průzkumník řešení otevřete místní nabídku souboru **LogicApp.js** . Vyberte **Otevřít pomocí Návrháře aplikace logiky** . (Klávesnice: CTRL + L)

   ![Otevření souboru .json aplikace logiky pomocí Návrháře aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   Visual Studio vás vyzve k zadání předplatného Azure a skupiny prostředků Azure pro vytváření a nasazování prostředků pro vaši aplikaci logiky a připojení.

1. Jako **předplatné** vyberte své předplatné Azure. V případě **skupiny prostředků** vyberte **vytvořit novou** a vytvořte další skupinu prostředků Azure.

   ![Výběr předplatného Azure, skupiny prostředků a umístění prostředků](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Nastavení | Příklad hodnoty | Description |
   | ------- | ------------- | ----------- |
   | Uživatelský účet | Fabrikam <br> sophia-owen@fabrikam.com | Účet, který jste použili při přihlášení k aplikaci Visual Studio |
   | **Předplatné** | Průběžné platby <br> (sophia-owen@fabrikam.com) | Název vašeho předplatného Azure a přidruženého účtu |
   | **Skupina prostředků** | MyLogicApp-RG <br> (USA – západ) | Skupina prostředků Azure a umístění pro ukládání a nasazování prostředků vaší aplikace logiky |
   | **Umístění** | **Stejné jako skupina prostředků** | Typ umístění a konkrétní umístění pro nasazení aplikace logiky Typ umístění je buď oblast Azure, nebo existující [prostředí Integration Service (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Pro tento rychlý Start ponechejte typ umístění nastavený na **oblast** a umístění nastavené na **stejné jako skupina prostředků** . <p>**Poznámka** : po vytvoření projektu skupiny prostředků můžete [změnit typ umístění a umístění](manage-logic-apps-with-visual-studio.md#change-location), ale jiný typ umístění ovlivní vaši aplikaci logiky různými způsoby. |
   ||||

1. Návrhář Logic Apps otevře stránku se zobrazeným úvodním videem a běžně používanými triggery. Posuňte se dolů za video a triggery do **šablon** a vyberte **prázdná aplikace logiky** .

   ![Výběr možnosti Prázdná aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Sestavení pracovního postupu aplikace logiky

Dále přidejte [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS, která se aktivuje při zobrazení nové položky informačního kanálu. Každá aplikace logiky se spouští triggerem, který se aktivuje při splnění určitých kritérií. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

1. V návrháři aplikace logiky pod vyhledávacím polem vyberte **vše** . Do vyhledávacího pole zadejte "RSS". V seznamu triggery vyberte tuto aktivační událost: **když se publikuje položka informačního kanálu** .

   ![Sestavení aplikace logiky přidáním triggeru a akcí](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po zobrazení triggeru v Návrháři dokončete vytváření aplikace logiky pomocí kroků pracovního postupu v [rychlém startu Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)a pak se vraťte k tomuto článku. Jakmile budete hotovi, vaše aplikace logiky bude vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Uložte řešení sady Visual Studio. (Klávesová zkratka: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Nasazení aplikace logiky do Azure

Předtím, než budete moci spustit a otestovat aplikaci logiky, nasaďte aplikaci do Azure ze sady Visual Studio.

1. V místní nabídce projektu v Průzkumník řešení vyberte **nasadit**  >  **Nový** . Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

   ![Vytvoření nasazení aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Pro toto nasazení ponechte výchozí předplatné Azure, skupinu prostředků a další nastavení. Vyberte **Nasadit** .

   ![Nasazení aplikace logiky do skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry** , zadejte název prostředku aplikace logiky. Uložte nastavení.

   ![Zadání názvu nasazení pro aplikaci logiky](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure.

   ![Výstup stavu nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Pokud vaše vybrané konektory potřebují vstup z vaší aplikace, otevře se okno PowerShellu na pozadí a zobrazí se výzva k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Okno PowerShellu](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po dokončení nasazení vaše aplikace logiky bude v Azure Portal živá a spustí se podle zadaného plánu (každou minutu). Pokud aktivační událost najde nové položky informačního kanálu, aktivuje se Trigger, který vytvoří instanci pracovního postupu, která spustí akce aplikace logiky. Vaše aplikace logiky posílá e-mail pro každou novou položku. V opačném případě, pokud aktivační procedura nenajde nové položky, aktivační událost se neaktivuje a "přeskočí" vytváření instancí pracovního postupu. Vaše aplikace logiky počká do dalšího intervalu před kontrolou.

   Tady jsou ukázkové e-maily, které tato aplikace logiky posílá. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

   ![Outlook odešle e-mail za každou novou položku RRSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulujeme, úspěšně jste vytvořili a nasadili aplikaci logiky pomocí sady Visual Studio. Informace o správě aplikace logiky a kontrole historie jejího spuštění najdete v tématu [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Přidat novou aplikaci logiky

Pokud máte existující projekt skupiny prostředků Azure, můžete do tohoto projektu přidat novou prázdnou aplikaci logiky pomocí okna osnova JSON.

1. V Průzkumník řešení otevřete `<logic-app-name>.json` soubor.

1. V nabídce **zobrazení** vyberte **jiný**  >  **Osnova Windows JSON** .

1. Pokud chcete přidat prostředek do souboru šablony, vyberte **Přidat prostředek** v horní části okna osnovy JSON. V okně Osnova JSON otevřete místní nabídku **prostředky** a vyberte **Přidat nový prostředek** .

   ![Okno osnovy JSON](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. V dialogovém okně **Přidat prostředek** v poli hledání vyhledejte `logic app` a vyberte **Aplikace logiky** . Pojmenujte aplikaci logiky a vyberte **Přidat** .

   ![Přidat prostředek](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s aplikací logiky hotovi, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí stejného účtu, který jste použili k vytvoření své aplikace logiky.

1. V nabídce Azure Portal vyberte **skupiny prostředků** , nebo vyhledejte a vyberte **skupiny prostředků** z libovolné stránky. Vyberte skupinu prostředků vaší aplikace logiky.

1. Na stránce **Přehled** vyberte **Odstranit skupinu prostředků** . Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit** .

   ![Skupiny prostředků > Přehled > Odstranit skupinu prostředků](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Odstraňte řešení sady Visual Studio ze svého místního počítače.

## <a name="next-steps"></a>Další kroky

V tomto článku jste pomocí sady Visual Studio sestavili, nasadili a spustili aplikaci logiky. Další informace o správě a provádění pokročilého nasazení Logic Apps se sadou Visual Studio najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
