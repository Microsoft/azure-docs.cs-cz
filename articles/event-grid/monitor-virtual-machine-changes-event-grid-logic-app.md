---
title: Monitorování změn virtuálních počítačů – Azure Event Grid a Logic Apps | Microsoft Docs
description: Kontrola změn v konfiguraci virtuálních počítačů pomocí Azure Event Gridu a Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801087"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Kurz: Monitorování změn virtuálních počítačů pomocí Azure Event Gridu a Logic Apps

Při určitých událostech, ke kterým dochází v prostředcích Azure nebo v prostředcích třetích stran, můžete spustit automatický [pracovní postup aplikace logiky](../logic-apps/logic-apps-overview.md). Tyto prostředky mohou tyto události publikovat do [Azure Event Gridu](../event-grid/overview.md). Event Grid tyto události pošle odběratelům, kteří jako své koncové body používají fronty, webhooky nebo [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). Vy jako odběratel čekáte se svou aplikací logiky na události z Event Gridu, aby spustily automatické pracovní postupy, které provádějí úlohy – všechno bez psaní kódu.

Tady jsou příklady událostí, které mohou vydavatelé prostřednictvím služby Azure Event Grid posílat odběratelům.

* Vytvoření, čtení, aktualizace nebo odstranění prostředku. Můžete třeba monitorovat změny, za které mohou být účtovány poplatky v předplatném Azure a které ovlivní vaše vyúčtování. 
* Přidání osoby do předplatného Azure nebo její odebrání.
* Aplikace provede určitou akci.
* Nová zpráva se zobrazí ve frontě.

Tento kurz vytvoří aplikaci logiky, která sleduje změny pro virtuální počítač a odešle e-mailů o těchto změnách. Když vytvoříte aplikaci logiky s odběrem události, která se týká prostředku Azure, tečou události od daného prostředku přes Event Grid do aplikace logiky. Tento průvodce vás provede sestavením této aplikace logiky:

![Přehled – monitorování virtuálního počítače v Event Gridu a aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci logiky, která monitoruje události v Event Gridu.
> * Přidat podmínku, která výslovně kontroluje změny virtuálního počítače.
> * Odeslat e-mail při změně virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele podporovaného v Logic Apps pro odesílání oznámení, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/). 

  Tento kurz používá účet Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* [Virtuální počítač](https://azure.microsoft.com/services/virtual-machines). Pokud jste tak již neučinili, vytvořte virtuální počítač prostřednictvím [vytvořit virtuální počítač kurzu](../virtual-machines/windows/quick-create-portal.md). Publikování událostí virtuálním počítačem [nevyžaduje z vaší strany žádnou další akci](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure. 

1. V hlavní nabídce Azure zvolte **vytvořit prostředek** > **integrace** > **aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. V části **aplikace logiky**, poskytují informace o vaší aplikaci logiky. Jakmile budete hotoví, vyberte **Vytvořit**.

   ![Zadání podrobností o aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Vlastnost | Navrhovaná hodnota | Popis |
   | -------- | --------------- | ----------- |
   | **Název** | <*logic-app-name*> | Zadejte jedinečný název pro svou aplikaci logiky. |
   | **Předplatné** | <*název_předplatného_Azure*> | Pro všechny služby tohoto kurzu vyberte stejné předplatné Azure. |
   | **Skupina prostředků** | <*Azure-resource-group*> | Pro všechny služby v tomto kurzu vyberte stejnou skupinu prostředků Azure. |
   | **Umístění** | <*Azure-datacenter-region*> | Pro všechny služby v tomto kurzu vyberte stejnou oblast. |
   |||

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. 

1. Jakmile Azure nasadí vaši aplikaci logiky, Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. Posuňte se za video a triggery. 

1. V části **Šablony** zvolte **Prázdná aplikace logiky**.

   ![Výběr šablony aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Návrhář pro Logic Apps se teď zobrazí [ *triggery* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) , můžete použít ke spuštění aplikace logiky. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. 
   Pokaždé, když se trigger aktivuje, Azure Logic Apps vytvoří instanci pracovního postupu, který spustí vaši aplikaci logiky.

## <a name="add-event-grid-trigger"></a>Přidání triggeru služby Event Grid 

Nyní přidáte trigger služby Event Grid, která monitoruje skupiny prostředků pro váš virtuální počítač. 

1. V Návrháři zadejte do vyhledávacího pole "event grid" jako filtr. Ze seznamu triggerů vyberte tento trigger: **Když dojde k události resource - Azure Event Grid**

   ![Vyberte tento trigger: "Na zdroje událostí"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po zobrazení výzvy, přihlaste se k Azure Event Grid pomocí svých přihlašovacích údajů účtu Azure. V **Tenanta** seznam, který ukazuje tenanta Azure Active Directory, který je spojen s vaším předplatným Azure, zkontrolujte, zda se zobrazí správný tenanta.

   ![Přihlášení pod přihlašovacími údaji Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Pokud jste se přihlásili pod osobním účtem Microsoft, třeba @outlook.com nebo @hotmail.com, nemusí se trigger služby Event Grid zobrazit správně. Jako alternativní řešení zvolte [Připojit pomocí instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md) nebo se přihlaste jako člen služby Azure Active Directory přidružené k vašemu předplatnému Azure, například *uživatelské-jméno*@emailoutlook.onmicrosoft.com.

1. Teď přihlaste aplikaci logiky k odběru událostí vydavatele. Zadejte podrobnosti o odběru události podle následující tabulky:

   ![Zadání podrobností o odběru události](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Vlastnost | Požaduje se | Value | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Předplatné** | Ano | <*event-publisher-Azure-subscription-name*> | Vyberte název pro předplatné Azure spojené s zdroj události. Pro účely tohoto kurzu vyberte název předplatného Azure pro váš virtuální počítač. |
   | **Typ prostředku** | Ano | <*event-publisher-Azure-resource-type*> | Vyberte typ prostředku Azure pro zdroj události. Pro účely tohoto kurzu vyberte tuto hodnotu k monitorování skupin prostředků Azure: <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Název prostředku** |  Ano | <*event-publisher-Azure-resource-name*> | Vyberte název prostředku Azure pro zdroj události. Tento seznam se liší v závislosti na typu prostředku, který jste vybrali. Pro účely tohoto kurzu vyberte název skupiny prostředků Azure pro váš virtuální počítač. |
   | **Typ položky události** |  Ne | <*typy událostí*> | Vyberte jeden nebo více konkrétních typů událostí k filtrování a odeslat do služby event grid. Například můžete volitelně přidat tyto typy událostí ke zjištění při změně nebo odstranění prostředků: <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>Další informace najdete v těchto tématech: <p><p>- [Vysvětlení filtrování událostí](../event-grid/event-filtering.md) <br>- [Filtr událostí služby Event Grid](../event-grid/how-to-filter-events.md) <br>- [Schéma událostí Azure Event Grid pro skupiny prostředků](../event-grid/event-schema-resource-groups.md) |
   | **Název předplatného** | Ne | <*Název odběru události*> | Zadejte jedinečný název odběru události. |
   | Volitelná nastavení, zvolte **přidat nový parametr**. | Ne | {popisech} | * **Filtr předpon**: Pro účely tohoto kurzu tuto vlastnost nechte prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec předpony, třeba cestu k určitému prostředku a jeho parametr. <p>* **Filtr přípony**: Pro účely tohoto kurzu tuto vlastnost nechte prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec přípony, třeba příponu názvu souboru, pokud chcete jenom určité typy souborů. |
   |||

   Jakmile budete hotovi, trigger služby Event Grid může vypadat jako v tomto příkladu:

   ![Příklad podrobnosti o triggeru služby Event Grid](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti akce, zvolte záhlaví akce.

   ![Uložení aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Když k uložení aplikace logiky použijete trigger služby Event Grid, vytvoří Azure automaticky předplatné události pro aplikaci logiky a vybraný prostředek. Když prostředek publikuje událost do Event Gridu, doručí ji Event Grid automaticky aplikaci logiky. Tato událost iniciuje aplikaci logiky a potom vytvoří a spustí instanci pracovního postupu, který definujete v dalších krocích.

Aplikace logiky je teď funkční a naslouchá událostem Event Gridu, ale zatím nic nedělá, dokud do pracovního postupu nepřidáte akce. 

## <a name="add-condition"></a>Přidat podmínku

Pokud chcete, aby se pracovní postup aplikace logiky spustil, jen když dojde k určité události, přidejte podmínku, která ve virtuálním počítači kontroluje operace „write“. Jakmile je tato podmínka splněna, aplikace logiky vám pošle e-mail s podrobnostmi o aktualizovaném virtuálním počítači.

1. V návrháři aplikace logiky v oblasti trigger služby event grid, zvolte **nový krok**.

   ![Zvolte možnost "Nový krok"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Do vyhledávacího pole zadejte jako filtr "podmínku". Ze seznamu akcí vyberte tuto akci: **Podmínka**

   ![Přidat podmínku](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Návrhář pro Logic Apps přidá do pracovního postupu prázdnou podmínku, včetně cest akcí, které se použijí, pokud je podmínka pravdivá nebo nepravdivá.

   ![Prázdná podmínka](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Přejmenujte podmínku název `If a virtual machine in your resource group has changed`. V záhlaví podmínky a klikněte na trojtečku (**...** ) tlačítko a vyberte **přejmenovat**.

   ![Přejmenování podmínky](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Vytvořit podmínku, která kontroluje události `body` pro `data` objekt kde `operationName` rovná vlastnost `Microsoft.Compute/virtualMachines/write` operace. Další informace o [schématu události Event Gridu](../event-grid/event-schema.md).

   1. Na prvním řádku pod **A** klikněte mimo pole vlevo. V seznamu dynamického obsahu, který se zobrazí, zvolte **výraz**.

      ![Zvolte možnost "Výraz"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. V editoru výrazů zadejte tento výraz a zvolte **OK**: 

      `triggerBody()?['data']['operationName']`

      Příklad:

      ![Zvolte možnost "Výraz"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. V prostředním poli ponechte operátor **rovná se**.

   1. Do pole vpravo zadejte tuto hodnotu:

      `Microsoft.Compute/virtualMachines/write`

   Dokončená podmínka teď vypadá jako v tomto příkladu:

   ![Dokončená podmínka](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Pokud přejdete z návrhového zobrazení a zobrazení a zpět do návrhového zobrazení kódu, výraz, který jste zadali v podmínka přeloží na **data.operationName** token:

   ![Vyřešit podmínku](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Uložte svou aplikaci logiky.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

Teď přidejte [*akci*](../logic-apps/logic-apps-overview.md#logic-app-concepts), která vám pošle e-mail, pokud zadaná podmínka platí.

1. V poli podmínky **Pokud je true** zvolte **Přidat akci**.

   ![Přidání akce, pokud podmínka platí](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Do vyhledávacího pole zadejte "Odeslat e-mail" jako filtr. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Pak pro konektor vyberte akci „odeslat e-mail“. Příklad: 

   * Pro pracovní nebo školní účet Azure vyberte konektor Office 365 Outlook. 

   * Pro osobní účty Microsoft vyberte konektor Outlook.com. 

   * Pro účty Gmail vyberte konektor Gmail. 

   V tomto kurzu bude pokračovat s konektorem Office 365 Outlook. 
   Pokud používáte jiného poskytovatele, postup zůstávají stejné, ale vaše uživatelské rozhraní může vypadat trochu jinak. 

   ![Výběr akce „Odeslat e-mail“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Pokud nemáte připojení k poskytovateli e-mailu, přihlaste se po zobrazení výzvy ke svému e-mailovém účtu.

1. Přejmenujte název e-mailu odesílání na tento název: `Send email when virtual machine updated`

1. Zadejte podrobnosti o e-mailu podle následující tabulky:

   ![Akce prázdného e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Vyberte z výsledků z předchozího postupu do svého pracovního postupu, klikněte do textového pole tak, aby zobrazil seznam dynamického obsahu nebo zvolte **Přidat dynamický obsah**. Další výsledky, zvolte **zobrazit další** pro každý oddíl v seznamu. Zavření seznamu dynamického obsahu vyberte **Přidat dynamický obsah** znovu.

   | Vlastnost | Požaduje se | Value | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Komu** | Ano | <*příjemce\@domény*> | Zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Předmět** | Ano | Aktualizuje prostředek: **Předmět** | Zadejte obsah předmětu e-mailu. Pro účely tohoto kurzu zadejte zadaný text a vyberte události **subjektu** pole. V našem příkladu je v předmětu e-mailu název aktualizovaného prostředku (virtuálního počítače). |
   | **Text** | Ano | Prostředek: **Téma** <p>Typ události: **Typ události**<p>ID události: **ID**<p>Čas: **Čas události** | Zadejte obsah e-mailu. Pro účely tohoto kurzu zadejte zadaný text a vyberte události **tématu**, **typ události**, **ID**, a **čas události** pole tak, aby vaše e-mailu obsahuje prostředků, která vyvolala událost, typ události, časové razítko události a ID události pro aktualizaci. Pro účely tohoto kurzu je prostředek skupiny prostředků Azure, který je vybraný v aktivační události. <p>Pokud chcete do obsahu přidat prázdné řádky, stiskněte Shift + Enter. |
   ||||

   > [!NOTE]
   > Pokud vyberete pole, které představuje pole položek, přidá návrhář automaticky kolem akce, která na toto pole odkazuje, smyčku **For each**. Aplikace logiky tak provede příslušnou akci pro každou položku pole.

   Teď vypadá akce e-mailu podobně jako v tomto příkladu:

   ![Výběr výstupů zahrnutých do e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Hotová aplikace logiky může vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Uložte svou aplikaci logiky. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti každé akce, zvolte záhlaví akce.

   Aplikace logiky je teď funkční, ale než provede akci, čeká na změny virtuálního počítače. 
   Pokud teď chcete aplikaci logiky otestovat, pokračujte k další části.

## <a name="test-your-logic-app-workflow"></a>Testování pracovního postupu aplikace logiky

1. Pokud chcete zkontrolovat, jestli aplikace logiky dostává zadané události, aktualizujte virtuální počítač.

   Můžete třeba změnit velikost virtuálního počítače na webu Azure Portal nebo [použijte ke změně velikosti virtuálního počítače Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Za chvíli by vám měl přijít e-mail. Příklad:

   ![E-mail o aktualizaci virtuálního počítače](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Chcete-li zkontrolovat spuštění a aktivaci historie pro vaši aplikaci logiky, v nabídce aplikace logiky, vyberte **přehled**. Chcete-li zobrazit další podrobnosti o spuštění, vyberte řádek, ve kterých běží.

   ![Historie spuštění aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Pokud chcete zobrazit vstupy a výstupy jednotlivých kroků, rozbalte krok, který chcete zkontrolovat. Tyto informace vám můžou pomoct s diagnostikou a laděním problémů ve vaší aplikaci logiky.

   ![Podrobnosti o historii spuštění aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Blahopřejeme k vytvoření a spuštění aplikace logiky, která monitoruje události prostředku prostřednictvím Event Gridu a v případě, že tyto události nastanou, vám pošle e-mail. Také jste se naučili, jak můžete jednoduše vytvářet pracovní postupy, které automatizují procesy a integrují systémy a cloudové služby.

Event Gridy a aplikace logiky umožňují monitorovat i jiné konfigurační změny, například:

* Virtuální počítač získá oprávnění k řízení přístupu na základě role (RBAC).
* Změny provedené ve skupině zabezpečení sítě (NSG) v síťovém rozhraní (NIC).
* Přidání nebo odebrání disků virtuálního počítače.
* Přiřazení veřejné IP adresy síťovému rozhraní virtuálního počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky a provádí akce, za které mohou být ve vašem předplatném Azure účtovány poplatky. Jakmile budete s kurzem a testováním hotovi, nezapomeňte vypnout nebo odstranit všechny prostředky, za které nechcete platit poplatky.

* Pokud chcete aplikaci logiky zastavit, aniž byste odstranili svou práci, vypněte ji. V nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů zvolte **Vypnout**.

  ![Vypnutí aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

* Chcete-li trvale odstranit aplikaci logiky, v nabídce aplikace logiky, vyberte **přehled**. Na panelu nástrojů zvolte **Odstranit**. Potvrďte, že chcete odstranit aplikaci logiky a zvolte **odstranit**.

## <a name="next-steps"></a>Další postup

* [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
