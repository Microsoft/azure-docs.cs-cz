---
title: Správa aplikací logiky pomocí sady Visual Studio – Azure Logic Apps
description: Správa aplikací logiky a další prostředky Azure pomocí Průzkumníka cloudu Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: 694ff490d7623b2dff26a61ccae8106a276b84af
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447907"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Správa aplikací logiky pomocí sady Visual Studio

Přestože je možné vytvořit, upravit, spravovat a nasazovat aplikace logiky v [webu Azure portal](https://portal.azure.com), pokud chcete přidat aplikace logiky do správy zdrojového kódu, publikovat různé verze a vytvářet můžete také použít Visual Studio [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) šablony pro různá prostředí nasazení. Pomocí Průzkumníka cloudu Visual Studio můžete najít a spravovat vaše aplikace logiky společně s další prostředky Azure. Například můžete otevřít, stáhnout, upravit, spustit a zobrazit historii spuštění, zakázat a povolit logic apps, které jsou už nasazené na webu Azure Portal. Pokud teprve začínáte pracovat se službou Azure Logic Apps v sadě Visual Studio, přečtěte si [postup vytvoření aplikace logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Nasazení nebo publikování aplikace logiky v sadě Visual Studio přepíše verzi této aplikace na webu Azure Portal. Takže pokud provedete změny na portálu Azure portal, které chcete zachovat, ujistěte se, že jste [aktualizovat aplikace logiky v sadě Visual Studio](#refresh) z portálu Azure portal před dalším nasazení nebo publikování ze sady Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje: 

  * [Edice sady Visual Studio. 2019 2017 nebo 2015 – Community nebo vyšší](https://aka.ms/download-visual-studio). 
  V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když instalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali **vývoj pro Azure** pracovního vytížení.
    > Další informace najdete v tématu [spravovat prostředky přidružené k účtům Azure v Průzkumníku cloudu sady Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Instalace Průzkumníka cloudu sady Visual Studio 2015, [stáhněte si Průzkumníka cloudu z webu Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Další informace najdete v tématu [spravovat prostředky přidružené k účtům Azure v Průzkumníku cloudu Visual Studio (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps Tools pro verzi sady Visual Studio, které chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při používání vloženého návrháře pro Logic Apps

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. 
  Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Vyhledání aplikace logiky

V sadě Visual Studio můžete najít všechny aplikace logiky, které jsou přidružené k vašemu předplatnému Azure a jsou nasazené na webu Azure Portal pomocí Průzkumníka cloudu.

1. Otevřít Visual Studio. Na **zobrazení** nabídce vyberte možnost **Průzkumníka cloudu**.

1. V Průzkumníku cloudu, zvolte **správu účtů**. Vyberte předplatné Azure spojené s logic apps a pak zvolte **použít**. Příklad:

   ![Zvolte možnost "Správa účtů"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Závislosti na tom, jestli hledáte podle **skupiny prostředků** nebo **typy prostředků**, postupujte podle těchto kroků:

   * **Skupiny prostředků**: V rámci vašeho předplatného Azure zobrazuje Průzkumník cloudu všechny skupiny prostředků, které jsou spojeny s tímto předplatným. 
   Rozbalte skupinu prostředků, která obsahuje vaši aplikaci logiky a pak vyberte svou aplikaci logiky.

   * **Typy prostředků**: V rámci vašeho předplatného Azure, rozbalte **Logic Apps**. Po Průzkumník cloudu se zobrazí všechny nasazené logic apps, které jsou spojené s vaším předplatným, vyberte svou aplikaci logiky.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otevřít v sadě Visual Studio

V sadě Visual Studio můžete otevřít logic apps dříve vytvořili a nasadili přímo prostřednictvím webu Azure portal nebo jako projekty Azure Resource Manageru pomocí sady Visual Studio.

1. Otevřete Průzkumníka cloudu a najít aplikaci logiky. 

1. V místní nabídce aplikace logiky, vyberte **otevřít pomocí editoru aplikace logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

   Tento příklad ukazuje aplikace logiky podle typu prostředku, takže aplikace logiky se zobrazí pod **Logic Apps** oddílu.

   ![Aplikace logiky otevřít nasazenou z webu Azure portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Až se otevře v návrháři pro Logic Apps, v dolní části návrháře aplikace logiky můžete **zobrazení kódu** tak, aby můžete zkontrolovat základní struktura definic aplikací logiky. 
   Pokud chcete vytvořit šablonu nasazení pro aplikaci logiky, přečtěte si [stažení šablony Azure Resource Manageru](#download-logic-app) pro tuto aplikaci logiky. Další informace o [šablon Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Stáhnout z Azure

Aplikace logiky můžete od si můžete stáhnout [webu Azure portal](https://portal.azure.com) a uložit je jako [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) šablony. Můžete pak místně upravte šablony pomocí sady Visual Studio a přizpůsobení aplikací logiky pro různá prostředí nasazení. Automatické stahování aplikací logiky *parametrizuje* jejich definice uvnitř [šablon Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment), který také pomocí notace JSON (JavaScript Object).

1. V sadě Visual Studio, otevřete Průzkumníka cloudu, pak vyhledejte a vyberte aplikaci logiky, kterou chcete stáhnout z Azure.

2. V místní nabídce vaší aplikace, vyberte **otevřít pomocí editoru aplikace logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

   Návrhář aplikace logiky se otevře a zobrazí aplikace logiky. 
   Chcete-li zkontrolovat základní definici a struktura v dolní části návrháře aplikace logiky zvolte **zobrazení kódu**. 

3. Na panelu nástrojů návrháře zvolte **Stáhnout**.

   ![Zvolte možnost "Stáhnout"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Po zobrazení výzvy k zadání umístění, přejděte do tohoto umístění a uložit šablonu Resource Manageru pro definici aplikace logiky ve formátu JSON (.json). 

Definici aplikace logiky se zobrazí v `resources` dílčí část uvnitř šablony Resource Manageru. Teď můžete upravit definice aplikace logiky a šablony Resource Manageru pomocí sady Visual Studio. Šablony jako projekt Azure Resource Manageru můžete také přidat do řešení sady Visual Studio. Další informace o [projekty Resource Manageru pro logic apps v sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualizovat z Azure

Pokud upravíte aplikace logiky na webu Azure Portal a chcete tyto změny zachovat, ujistěte se, že aktualizujete verzi vaší aplikace v sadě Visual Studio s těmito změnami. 

* V sadě Visual Studio, zvolte na panelu nástrojů návrháře aplikace logiky **aktualizovat**.

  -nebo-

* V Průzkumníku cloudu Visual Studio, otevřete místní nabídku vaší aplikace logiky a vyberte **aktualizovat**.

![Aktualizovat aplikaci logiky s aktualizacemi](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikovat aktualizace aplikace logiky

Až budete připravení nasadit logic app aktualizace ze sady Visual Studio do Azure, na panelu nástrojů návrháře aplikace logiky zvolte **publikovat**.

![Publikování aktualizované logiky aplikace](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ruční spuštění aplikace logiky

Můžete ručně aktivovat aplikace logiky nasazené v Azure ze sady Visual Studio. Na panelu nástrojů návrháře aplikace logiky zvolte **spuštění aktivační události**.

![Ruční spuštění aplikace logiky](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Pokud chcete zkontrolovat stav a Diagnostika potíží s běhy aplikací logiky, můžete zobrazit podrobnosti, jako vstupy a výstupy, kteří běží v sadě Visual Studio.

1. V Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **Otevřít historii spouštění**.

   ![Otevřít historii spouštění](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Chcete-li zobrazit podrobnosti pro konkrétní spuštění, dvakrát klikněte na spustit. Příklad:

   ![Podrobné historie spuštění](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > K seřazení tabulky podle vlastnosti, vyberte záhlaví sloupce pro tuto vlastnost. 

1. Rozbalení kroků, jejichž vstupy a výstupy, které chcete zkontrolovat. Příklad:

   ![Zobrazit vstupy a výstupy pro jednotlivé kroky](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Aniž byste odstranili svou aplikaci logiky, můžete zastavit aktivační událost z ohlásí příště, když je splněna podmínka aktivace. Zákaz aplikace logiky brání modul Logic Apps ve vytváření a spouštění instance budoucích pracovních postupů pro vaši aplikaci logiky.
V Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **zakázat**.

![Zakázat aplikaci logiky](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Pokud zakážete, aplikace logiky, žádná nová spuštění jsou vytvořena instance. Všechny probíhající a nevyřízených spuštění bude pokračovat, dokud jejich dokončení, což může chvíli trvat. 

Jakmile budete připraveni pro vaši aplikaci logiky k obnovení, můžete aktivovat svou aplikaci logiky. V Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **povolit**.

![Povolit aplikaci logiky](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Odstranění aplikace logiky

Odstranění aplikace logiky na webu Azure Portal, v Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **odstranit**.

![Odstranění aplikace logiky](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu. 

## <a name="troubleshooting"></a>Řešení potíží

Při otevření projektu aplikace logiky v návrháři pro Logic Apps, nemusí mít možnost pro výběr vašeho předplatného Azure. Místo toho svou aplikaci logiky se otevře s předplatným Azure, který není ten, který chcete použít. K tomuto chování dochází po otevření souboru .json aplikace logiky, Visual Studio ukládá do mezipaměti první vybrané předplatné pro budoucí použití. Chcete-li tento problém vyřešit, zkuste použijte jeden z těchto kroků:

* Přejmenujte soubor .json aplikace logiky. Mezipaměť předplatného závisí na názvu souboru.

* Chcete-li odebrat dříve vybraná předplatná pro *všechny* aplikací logiky ve vašem řešení, odstraňte složku nastavení služby skryté sady Visual Studio (.vs) v adresáři vašeho řešení. Toto umístění ukládá informace o vašem předplatném.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak spravovat aplikace logiky nasazené pomocí sady Visual Studio. V dalším kroku se další informace o přizpůsobení definic aplikací logiky pro nasazení:

> [!div class="nextstepaction"]
> [Vytváření definic aplikací logiky ve formátu JSON](../logic-apps/logic-apps-author-definitions.md)
