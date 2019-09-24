---
title: Správa aplikací logiky pomocí sady Visual Studio – Azure Logic Apps
description: Správa aplikací logiky a dalších prostředků Azure pomocí Průzkumníka cloudu sady Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: db4143b3bf75d1745245d5baae267a55ce71e95f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212593"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Správa aplikací logiky pomocí sady Visual Studio

I když můžete vytvářet, upravovat, spravovat a nasazovat Logic Apps v [Azure Portal](https://portal.azure.com), můžete také použít Visual Studio, pokud chcete přidat své aplikace logiky do správy zdrojových kódů, publikovat různé verze a vytvářet [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) šablony pro různá prostředí nasazení. Pomocí Průzkumníka cloudu sady Visual Studio můžete najít a spravovat aplikace logiky spolu s dalšími prostředky Azure. Můžete například otevřít, stáhnout, upravit, spustit, zobrazit historii spuštění, zakázat a povolit aplikace logiky, které jsou již nasazeny v Azure Portal. Pokud s Azure Logic Apps v aplikaci Visual Studio začínáte, přečtěte si, [jak vytvářet aplikace logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Nasazení nebo publikování aplikace logiky ze sady Visual Studio přepíše verzi této aplikace v Azure Portal. Takže pokud provedete změny v Azure Portal, které chcete zachovat, nezapomeňte [aktualizovat aplikaci logiky v aplikaci Visual Studio](#refresh) z Azure Portal před dalším nasazením nebo publikováním ze sady Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio 2019, 2017 nebo 2015 – Community Edition nebo vyšší](https://aka.ms/download-visual-studio). V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když nainstalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali úlohu **vývoj pro Azure** .
    > Další informace najdete v tématu [Správa prostředků přidružených k vašim účtům Azure v Průzkumníkovi cloudu sady Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Chcete-li nainstalovat Průzkumníka cloudu pro Visual Studio 2015, [Stáhněte si z Visual Studio Marketplace Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Další informace najdete v tématu [Správa prostředků přidružených k vašim účtům Azure v Průzkumníkovi cloudu sady Visual Studio (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Sada Azure SDK (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps nástroje pro verzi sady Visual Studio, kterou chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při použití vloženého návrháře Logic Apps

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Hledání aplikací logiky

V aplikaci Visual Studio můžete najít všechny aplikace logiky, které jsou přidruženy k vašemu předplatnému Azure a které jsou nasazeny v Azure Portal pomocí Průzkumníka cloudu.

1. Otevřít Visual Studio. V nabídce **zobrazení** vyberte **Průzkumník cloudu**.

1. V Průzkumníku cloudu vyberte **Správa účtů**. Vyberte předplatné Azure přidružené k vašim Logic Apps a pak vyberte **použít**. Příklad:

   ![Vyberte Správa účtů.](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. V závislosti na tom, jestli hledáte podle **skupin prostředků** nebo **typů prostředků**, postupujte podle následujících kroků:

   * **Skupiny prostředků**: V části vaše předplatné Azure zobrazí Průzkumník cloudu všechny skupiny prostředků, které jsou přidružené k tomuto předplatnému. Rozbalte skupinu prostředků, která obsahuje vaši aplikaci logiky, a pak vyberte svou aplikaci logiky.

   * **Typy prostředků**: V rámci vašeho předplatného Azure rozbalte **Logic Apps**. Jakmile Průzkumník cloudu zobrazí všechny nasazené aplikace logiky, které jsou přidružené k vašemu předplatnému, vyberte svou aplikaci logiky.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otevřít ve Visual Studiu

V aplikaci Visual Studio můžete otevřít aplikace logiky, které byly dříve vytvořeny a nasazeny přímo prostřednictvím Azure Portal nebo jako projekty skupiny prostředků Azure pomocí sady Visual Studio.

1. Otevřete Průzkumníka cloudu a Najděte aplikaci logiky.

1. V místní nabídce aplikace logiky vyberte **otevřít pomocí programu Logic App Editor**.

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   Tento příklad ukazuje Logic Apps podle typu prostředku, takže se vaše aplikace logiky zobrazí v části **Logic Apps** .

   ![Otevřít nasazenou aplikaci logiky z Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Jakmile se aplikace logiky otevře v Návrháři Logic Apps, můžete v dolní části návrháře vybrat **zobrazení kódu** , abyste mohli zkontrolovat základní strukturu definice aplikace logiky. Pokud chcete vytvořit šablonu nasazení pro aplikaci logiky, přečtěte si, [Jak stáhnout šablonu Azure Resource Manager](#download-logic-app) pro tuto aplikaci logiky. Přečtěte si další informace o [Správce prostředků šablonách](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Stáhnout z Azure

Můžete si stáhnout Logic Apps z [Azure Portal](https://portal.azure.com) a uložit je jako šablonu [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) . Pak můžete místně upravit šablony pomocí sady Visual Studio a přizpůsobit Logic Apps pro různá prostředí nasazení.  Automatické stahování aplikací logiky *parameterizes* jejich definice v [šablonách správce prostředků](../azure-resource-manager/template-deployment-overview.md), které také používají JavaScript Object Notation (JSON).

1. V aplikaci Visual Studio otevřete Průzkumníka cloudu. Najděte a vyberte aplikaci logiky, kterou chcete stáhnout z Azure.

1. V místní nabídce této aplikace vyberte **otevřít pomocí programu Logic App Editor**.

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   Otevře se Návrhář aplikace logiky a zobrazí se aplikace logiky. Chcete-li zkontrolovat základní definici a strukturu aplikace logiky, v dolní části návrháře vyberte možnost **zobrazení kódu**.

1. Na panelu nástrojů návrháře vyberte **Stáhnout**.

   ![Vyberte Stáhnout.](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

1. Po zobrazení výzvy k zadání umístění přejděte do tohoto umístění a uložte šablonu Správce prostředků pro definici aplikace logiky ve formátu JSON (. JSON).

   Vaše definice aplikace logiky se zobrazí `resources` v podčásti uvnitř šablony Správce prostředků. Nyní můžete upravit definici aplikace logiky a šablonu Správce prostředků pomocí sady Visual Studio. Šablonu můžete přidat také jako [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do řešení sady Visual Studio. Přečtěte si o [projektech skupin prostředků Azure pro Logic Apps v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Odkaz na účet pro integraci

Pokud chcete vytvářet aplikace logiky pro podnikové integrace B2B (Business-to-Business), můžete aplikaci logiky propojit s dříve vytvořeným [integračním účtem](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který existuje ve stejné oblasti jako aplikace logiky. Účet pro integraci obsahuje artefakty B2B, jako jsou obchodní partneři, smlouvy, schémata a mapy, a umožňuje aplikaci logiky používat konektory B2B k ověřování XML a kódování a dekódování plochého souboru. I když [Tento odkaz můžete vytvořit pomocí Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), můžete také použít Visual Studio po splnění [požadavků](#requirements)a vaše aplikace logiky existuje jako soubor JSON (. JSON) v [projektu skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Přečtěte si o [projektech skupin prostředků Azure pro Logic Apps v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. V aplikaci Visual Studio otevřete projekt skupiny prostředků Azure, který obsahuje vaši aplikaci logiky.

1. V Průzkumník řešení otevřete místní nabídku souboru **< Logic-App-name >. JSON** a vyberte **otevřít v návrháři aplikace logiky**. Kombinace CTRL + L)

   ![Otevření souboru. JSON aplikace logiky pomocí návrháře aplikace logiky](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

1. Aby se zajistilo, že návrhář aplikace logiky má fokus, vyberte kartu nebo plochu návrháře, aby se v podokně vlastnosti zobrazila vlastnost **účet pro integraci** pro vaši aplikaci logiky.

   ![V podokně vlastnosti se zobrazuje vlastnost účet pro integraci.](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties.png)

1. Otevřete seznam **účet pro integraci** a vyberte účet pro integraci, který chcete propojit s vaší aplikací logiky, například:

   ![Otevřete seznam vlastností "účet pro integraci".](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Až budete hotovi, nezapomeňte uložit řešení sady Visual Studio.

Když nastavíte vlastnost **integrační účet** v sadě Visual Studio a uložíte aplikaci logiky jako šablonu Azure Resource Manager, tato šablona obsahuje také deklaraci parametru pro vybraný účet integrace. Další informace o parametrech šablony a Logic Apps najdete v [tématu Přehled: Automatizujte nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)aplikace logiky.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualizace z Azure

Pokud aplikaci logiky upravíte v Azure Portal a chcete tyto změny zachovat, ujistěte se, že jste aktualizovali verzi aplikace v aplikaci Visual Studio s těmito změnami.

* V sadě Visual Studio na panelu nástrojů návrháře aplikace logiky vyberte **aktualizovat**.

  -nebo-

* V Průzkumníku cloudu sady Visual Studio otevřete místní nabídku aplikace logiky a vyberte **aktualizovat**.

![Aktualizace aplikace logiky s aktualizacemi](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikování aktualizací aplikace logiky

Až budete připraveni nasadit aktualizace aplikace logiky ze sady Visual Studio do Azure, klikněte na panelu nástrojů návrháře aplikací logiky na **publikovat**.

![Publikování aktualizované aplikace logiky](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ruční spuštění aplikace logiky

Aplikaci logiky nasazenou v Azure můžete ručně aktivovat ze sady Visual Studio. Na panelu nástrojů návrháře aplikace logiky vyberte **Spustit Trigger**.

![Ruční spuštění aplikace logiky](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Chcete-li zkontrolovat stav a diagnostikovat problémy s spuštěním aplikace logiky, můžete zkontrolovat podrobnosti, jako jsou vstupy a výstupy, pro tyto běhy v aplikaci Visual Studio.

1. V Průzkumníku cloudu otevřete místní nabídku aplikace logiky a vyberte **otevřít historii spuštění**.

   ![Otevřít historii spuštění](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Chcete-li zobrazit podrobnosti o konkrétním spuštění, dvakrát klikněte na spuštění. Příklad:

   ![Podrobná historie spuštění](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Chcete-li seřadit tabulku podle vlastnosti, vyberte záhlaví sloupce pro danou vlastnost.

1. Rozbalte kroky, jejichž vstupy a výstupy chcete zkontrolovat, například:

   ![Zobrazit vstupy a výstupy pro jednotlivé kroky](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Bez odstranění aplikace logiky můžete zastavit aktivaci triggerem při dalším spuštění podmínky triggeru. Zakázáním aplikace logiky znemožníte Logic Appsmu stroji vytvářet a spouštět budoucí instance pracovního postupu pro aplikaci logiky. V Průzkumníku cloudu otevřete místní nabídku aplikace logiky a vyberte **Zakázat**.

![Zakázání aplikace logiky](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Když aplikaci logiky zakážete, nevytvoří se žádná nová spuštění. Všechny probíhající a probíhající běhy budou pokračovat, dokud nebudou dokončeny, což může trvat déle.

Pokud chcete aplikaci logiky znovu aktivovat, v Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **Povolit**.

![Povolení aplikace logiky](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Odstranění aplikace logiky

Pokud chcete aplikaci logiky odstranit z Azure Portal, v Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **Odstranit**.

![Odstranění aplikace logiky](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu. 

## <a name="troubleshooting"></a>Řešení potíží

Když otevřete projekt aplikace logiky v Návrháři Logic Apps, nebudete mít možnost pro výběr vašeho předplatného Azure. Místo toho se aplikace logiky otevře s předplatným Azure, které není ten, který chcete použít. K tomuto chování dochází, protože po otevření souboru. JSON aplikace logiky Visual Studio uloží první vybrané předplatné do mezipaměti pro budoucí použití. Chcete-li tento problém vyřešit, zkuste jeden z následujících kroků:

* Přejmenujte soubor. JSON aplikace logiky. Mezipaměť odběrů závisí na názvu souboru.

* Pokud chcete odebrat dříve vybraná předplatná pro *všechny* aplikace logiky ve vašem řešení, odstraňte složku nastavení skryté sady Visual Studio (. vs) v adresáři vašeho řešení. V tomto umístění se ukládají informace o vašem předplatném.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spravovat nasazené aplikace logiky pomocí sady Visual Studio. Další informace o přizpůsobení definic aplikace logiky pro nasazení:

> [!div class="nextstepaction"]
> [Vytváření definic aplikací logiky ve formátu JSON](../logic-apps/logic-apps-author-definitions.md)
