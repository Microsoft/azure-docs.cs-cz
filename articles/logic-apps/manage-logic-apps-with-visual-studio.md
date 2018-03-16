---
title: "Správa aplikací logiky pomocí sady Visual Studio – Azure Logic Apps | Microsoft Docs"
description: "Správa aplikace logiky a dalších prostředků Azure pomocí Průzkumníka cloudové služby Visual Studio"
author: ecfan
manager: SyntaxC4
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: db847d5fa3d5f5b2b2f0293f1756226870a8b47e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Správa aplikací logiky v sadě Visual Studio

I když můžete vytvořit, upravit, správu a nasazení aplikací logiky ve <a href="https://portal.azure.com" target="_blank">portál Azure</a>, Visual Studio můžete také použít, pokud chcete přidat logic apps, abyste ovládací prvek zdroje, publikovat různé verze a vytvořte [prostředků Azure Správce](../azure-resource-manager/resource-group-overview.md) šablony pro jiné nasazení prostředí. Pomocí Průzkumníka Visual Studio cloudu můžete najít a spravovat aplikace logiky společně s dalším prostředkům služby Azure. Například můžete otevřít, stáhnout, upravovat, spuštění, zobrazení historie spouštění, zakázat a povolit logiku aplikace, které jsou už nasazené na portálu Azure. Pokud jste pro práci s Azure Logic Apps v sadě Visual Studio nové, přečtěte si [postup vytvoření aplikace logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Nasazení nebo publikování aplikace logiky ze sady Visual Studio přepíše verze této aplikace na portálu Azure. Takže pokud provedete změny v portálu Azure, která chcete zachovat, ujistěte se, že jste [aktualizovat aplikaci logiky v sadě Visual Studio](#refresh) z portálu Azure před dalším nasazení nebo publikovat ze sady Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">si zaregistrovat bezplatný účet Azure</a>.

* Stažení a instalace těchto nástrojů, pokud je nemáte již: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 nebo Visual Studio 2015 - Community edition nebo větší</a>. 
  Tento rychlý start používá Visual Studio Community 2017, což je bezplatná.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK (2.9.1 nebo novější)</a> a <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">prostředí Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Azure logiku aplikace nástrojů pro Visual Studio 2017</a> nebo <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">verze sady Visual Studio 2015</a> 
  
    Můžete stáhnout a nainstalovat nástroje aplikace logiky Azure přímo z Visual Studio Marketplace nebo další <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">instalaci z tohoto rozšíření v sadě Visual Studio</a>. 
    Ujistěte se, až po dokončení instalace restartujte Visual Studio.

* Přístup k webovému při použití návrháře embedded aplikace logiky

  Návrhář vyžaduje připojení k Internetu k vytváření prostředků v Azure a číst vlastnosti a data z konektorů v aplikaci logiky. 
  Například pokud použijete konektor Dynamics CRM Online, Návrhář zkontroluje vaší instance CRM dostupná výchozí a vlastní vlastnosti.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Najít logic apps

V sadě Visual Studio můžete najít všechny aplikace logiky, které jsou spojeny s předplatným Azure a jsou nasazeny na portálu Azure pomocí Průzkumníku cloudu.

1. Otevřete sadu Visual Studio. Na **zobrazení** nabídce vyberte možnost **Průzkumník cloudu**.

2. V Průzkumníku cloudu, zvolte **správy účtů**. Vyberte předplatné Azure spojené s logic apps, a potom vyberte **použít**. Příklad:

   ![Zvolte "Správa účtů"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Závislosti na tom, jestli hledáte podle **skupiny prostředků** nebo **typy prostředků**, postupujte takto:

   * **Skupiny prostředků**: v rámci vašeho předplatného Azure Cloud Explorer zobrazí všechny skupiny prostředků, které jsou přidruženy toto předplatné. 
   Rozbalte skupinu prostředků, která obsahuje aplikaci logiky, pak vyberte svou aplikaci logiky.

   * **Typy prostředků**: v rámci vašeho předplatného Azure, rozbalte položku **Logic Apps**. Po cloudu Explorer zobrazí všechny aplikace nasazené logiku, které jsou spojeny s předplatným, vyberte svou aplikaci logiky.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otevřete v sadě Visual Studio

V sadě Visual Studio můžete otevřít aplikace logiky dříve vytvořená a nasazená přímo prostřednictvím portálu Azure nebo jako projekty správce prostředků Azure pomocí sady Visual Studio.

1. Otevřete Průzkumník cloudu a vyhledejte aplikaci logiky. 

2. V místní nabídce aplikace logiky, vyberte **spustit s logiku aplikace Editor**.

   Tento příklad ukazuje aplikace logiky podle typů prostředků, takže aplikace logiky se zobrazí v části **Logic Apps** části.

  ![Otevřete nasazené logiku aplikace z portálu Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otevření aplikace logiky v návrháři aplikace logiky, v dolní části návrháře, můžete zvolit **zobrazení kódu** tak, aby podkladová struktura definici aplikace logiky můžete zkontrolovat. 
   Pokud chcete vytvořit šablonu nasazení pro aplikaci logiky, přečtěte si [stažení šablonu Azure Resource Manager](#download-logic-app) pro tuto aplikaci logiky. Další informace o [šablony Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Stáhnout ze služby Azure

Abyste mohli stahovat aplikace logiky z <a href="https://portal.azure.com" target="_blank">portál Azure</a> a uložit je jako [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) šablony. Můžete pak místně upravit šablony pomocí sady Visual Studio a přizpůsobení aplikací logiky pro jiné nasazení prostředí. Stahování aplikací logiky automaticky *parameterizes* jejich definice uvnitř [šablony Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment), který také použít JavaScript Object Notation (JSON).

1. V sadě Visual Studio otevřete Průzkumník cloudu, pak najděte a vyberte aplikaci logiky, která chcete stáhnout ze služby Azure.

2. Na tuto aplikaci místní nabídce vyberte **spustit s logiku aplikace Editor**.

   Návrhář aplikace logiky otevře a zobrazuje aplikaci logiky. 
   Chcete-li zkontrolovat základní definici aplikace logiky a struktura, v dolní části návrháře, zvolte **zobrazení kódu**. 

3. Na panelu nástrojů návrháře zvolte **Stáhnout**.

   ![Zvolte "Stáhnout"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Když se zobrazí výzva k zadání umístění, přejděte do tohoto umístění a uložit šablonu Resource Manageru pro definici aplikace logiky ve formátu JSON (.json). 

Vaše definici aplikace logiky se zobrazí v `resources` část uvnitř šablony Resource Manageru. Nyní můžete upravit definici aplikace logiky a šablony Resource Manageru pomocí sady Visual Studio. K řešení sady Visual Studio můžete také přidat šablony jako projektu Azure Resource Manager. Další informace o [Resource Manager projekty pro logiku aplikace v sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Obnovení z Azure

Pokud upravíte svou aplikaci logiky na portálu Azure a chcete tyto změny zachovat, ujistěte se, že aktualizujete verze této aplikace v sadě Visual Studio s těmito změnami. 

* V sadě Visual Studio, vyberte na panelu nástrojů návrháře aplikace logiky **aktualizovat**.

  -nebo-

* V Průzkumníku cloudu Visual Studio, otevřete aplikaci logiky místní nabídku a vyberte **aktualizovat**. 

![Aktualizovat aplikaci logiky s aktualizacemi](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikování aktualizací aplikací logiky

Až budete připraveni k nasazení vaše logiku aplikace aktualizace ze sady Visual Studio do Azure, na panelu nástrojů návrháře aplikace logiky, vyberte **publikovat**.

![Publikování aplikace aktualizované logiky](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ruční spuštění aplikace logiky

Aplikace logiky nasazené v Azure ze sady Visual Studio můžete spustit ručně. Na panelu nástrojů návrháře aplikace logiky, vyberte **spustit aktivační událost**.

![Ruční spuštění aplikace logiky](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Zkontrolujte stav a diagnostikovat problémy s logiku aplikace běží, můžete zobrazit podrobnosti, například vstupy a výstupy u uživatelů, běží v sadě Visual Studio.

1. V Průzkumníku cloudu, otevřete aplikaci logiky místní nabídku a vyberte **historie spouštění otevřete**.

   ![Otevřete historie spouštění](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Chcete-li zobrazit podrobnosti o konkrétní spustit, dvakrát klikněte na spustit. Příklad:

   ![Podrobné historie spouštění](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Chcete-li seřadí tabulku podle vlastnosti, zvolte na záhlaví sloupce pro tuto vlastnost. 

3. Rozbalte kroky, jejichž vstupy a výstupy, které chcete zkontrolovat. Příklad:

   ![Zobrazení vstupy a výstupy pro každý krok](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Bez odstranění aplikace logiky, můžete zastavit aktivační události z aktivuje další čas, kdy je splněna podmínka aktivace. Zakázání svou aplikaci logiky zabraňuje modul Logic Apps z vytváření a spouštění instancí budoucí pracovního postupu pro svou aplikaci logiky.
V Průzkumníku cloudu, otevřete aplikaci logiky místní nabídku a vyberte **zakázat**.

![Vypnout aplikaci logiky](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Až budete připraveni pro svou aplikaci logiky na operace obnovení, můžete aktivovat svou aplikaci logiky. V Průzkumníku cloudu, otevřete aplikaci logiky místní nabídku a vyberte **povolit**.

![Povolit aplikaci logiky](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Odstranění aplikace logiky

Odstranit aplikaci logiky na portálu Azure v Průzkumníku cloudu, otevřete aplikaci logiky místní nabídky a vyberte **odstranit**.

![Odstranění aplikace logiky](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spravovat nasazené logiku aplikace pomocí sady Visual Studio. V dalším kroku se další informace o přizpůsobení definic aplikace logiky pro nasazení:

> [!div class="nextstepaction"]
> [Definice aplikace logiky Autor ve formátu JSON](../logic-apps/logic-apps-author-definitions.md)