---
title: Monitorování změn virtuálních počítačů – Azure Event Grid a Logic Apps | Microsoft Docs
description: Kontrola změn v konfiguraci virtuálních počítačů pomocí Azure Event Gridu a Logic Apps
keywords: aplikace logiky, event gridy, virtuální počítač, VM
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: ''
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: ea3063b5c445dab85a7ef1e5663c40efc34f961e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303109"
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Monitorování změn virtuálních počítačů pomocí Azure Event Gridu a Logic Apps

Při určitých událostech, ke kterým dochází v prostředcích Azure nebo v prostředcích třetích stran, můžete spustit automatický [pracovní postup aplikace logiky](../logic-apps/logic-apps-overview.md). Tyto prostředky mohou tyto události publikovat do [Azure Event Gridu](../event-grid/overview.md). Event Grid tyto události pošle odběratelům, kteří jako své koncové body používají fronty, webhooky nebo [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). Vy jako odběratel čekáte se svou aplikací logiky na události z Event Gridu, aby spustily automatické pracovní postupy, které provádějí úlohy – všechno bez psaní kódu.

Tady jsou příklady událostí, které mohou vydavatelé prostřednictvím služby Azure Event Grid posílat odběratelům.

* Vytvoření, čtení, aktualizace nebo odstranění prostředku. Můžete třeba monitorovat změny, za které mohou být účtovány poplatky v předplatném Azure a které ovlivní vaše vyúčtování. 
* Přidání osoby do předplatného Azure nebo její odebrání.
* Aplikace provede určitou akci.
* Nová zpráva se zobrazí ve frontě.

V tomto kurzu vytvoříte aplikaci logiky, která monitoruje změny virtuálního počítače a posílá o těchto změnách e-maily. Když vytvoříte aplikaci logiky s odběrem události, která se týká prostředku Azure, tečou události od daného prostředku přes Event Grid do aplikace logiky. Tento průvodce vás provede sestavením této aplikace logiky:

![Přehled – monitorování virtuálního počítače v Event Gridu a aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci logiky, která monitoruje události v Event Gridu.
> * Přidat podmínku, která výslovně kontroluje změny virtuálního počítače.
> * Odeslat e-mail při změně virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

* E-mailový účet u [některého poskytovatele e-mailu podporovaného službou Azure Logic Apps](../connectors/apis-list.md), jako je Office 365 Outlook, Outlook.com nebo Gmail. Tento účet je potřeba k posílání oznámení. Tento kurz používá Office 365 Outlook.

* [Virtuální počítač](https://azure.microsoft.com/services/virtual-machines). Pokud jste to ještě neudělali, vytvořte virtuální počítač. K jeho vytvoření použijte tento [kurz na vytvoření virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/). Publikování událostí virtuálním počítačem [nevyžaduje z vaší strany žádnou další akci](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Vytvoření aplikace logiky, která monitoruje události Event Gridu

Napřed vytvořte aplikaci logiky a přidejte trigger služby Event Grid, který ve skupině prostředků monitoruje váš virtuální počítač. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. V hlavní nabídce Azure vyberte v levém horním rohu možnost **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Vytvořte aplikaci logiky a nastavte ji podle následující tabulky:

   ![Zadání podrobností o aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Nastavení | Navrhovaná hodnota | Popis | 
   | ------- | --------------- | ----------- | 
   | **Název** | *{název-vaší-aplikace-logiky}* | Zadejte jedinečný název aplikace logiky. | 
   | **Předplatné** | *{vaše-předplatné-Azure}* | Pro všechny služby tohoto kurzu vyberte stejné předplatné Azure. | 
   | **Skupina prostředků** | *{vaše-skupina-prostředků-Azure}* | Pro všechny služby v tomto kurzu vyberte stejnou skupinu prostředků Azure. | 
   | **Umístění** | *{vaše-oblast-Azure}* | Pro všechny služby v tomto kurzu vyberte stejnou oblast. | 
   | | | 

4. Až to budete mít, vyberte **Připnout na řídicí panel** a zvolte **Vytvořit**.

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. 
   Až Azure nasadí aplikaci logiky, v Návrháři pro Logic Apps se zobrazí šablony pro obvyklé scénáře, abyste mohli začít rychleji.

   > [!NOTE] 
   > Když vyberete **Připnout na řídicí panel**, aplikace logiky se automaticky otevře v návrháři pro Logic Apps. Nebo můžete aplikaci logiky najít a otevřít ručně.

5. Vyberte šablonu aplikace logiky. V části **Šablony** zvolte **Prázdná aplikace logiky**, abyste mohli sestavit úplně novou aplikaci logiky.

   ![Výběr šablony aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   V návrháři pro Logic Apps se zobrazí [*konektory*](../connectors/apis-list.md) a [*aktivační události*](../logic-apps/logic-apps-overview.md#logic-app-concepts), které můžete použít k vytvoření své aplikace logiky. Zobrazí se také akce, které můžete přidat za aktivační událost, aby plnily úkoly. Aktivační událost vytvoří instanci aplikace logiky a spustí pracovní postup aplikace logiky. 
   Aplikace logiky potřebuje nejprve aktivační událost.

6. Do vyhledávacího pole zadejte filtr „event grid“. Vyberte tuto aktivační událost: **Azure Event Grid - On a resource event** (Azure Event Grid - při události prostředku).

   ![Výběr aktivační události „Azure Event Grid - On a resource event“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Po zobrazení výzvy se přihlaste do Azure Event Gridu pod stejnými přihlašovacími údaji jako do Azure.

   ![Přihlášení pod přihlašovacími údaji Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Pokud jste se přihlásili pod osobním účtem Microsoft, třeba @outlook.com nebo @hotmail.com, nemusí se trigger služby Event Grid zobrazit správně. Jako alternativní řešení zvolte [Připojit pomocí instančního objektu](../azure-resource-manager/resource-group-create-service-principal-portal.md) nebo se přihlaste jako člen služby Azure Active Directory přidružené k vašemu předplatnému Azure, například *uživatelské-jméno*@emailoutlook.onmicrosoft.com.

8. Teď přihlaste aplikaci logiky k odběru událostí vydavatele. Zadejte podrobnosti o odběru události podle následující tabulky:

   ![Zadání podrobností o odběru události](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Nastavení | Navrhovaná hodnota | Popis | 
   | ------- | --------------- | ----------- | 
   | **Předplatné** | *{předplatné-Azure-virtuálního-počítače}* | Vyberte předplatné Azure vydavatele události. V tomto kurzu vyberte předplatné Azure svého virtuálního počítače. | 
   | **Typ prostředku** | Microsoft.Resources.resourceGroups | Vyberte typ prostředku vydavatele události. V tomto kurzu vyberte zadanou hodnotu, aby aplikace logiky monitorovala jenom skupiny prostředků. | 
   | **Název prostředku** | *{název-skupiny-prostředků-virtuálního-počítače}* | Vyberte název prostředku vydavatele. V tomto kurzu vyberte název skupiny prostředků svého virtuálního počítače. | 
   | Pokud chcete upřesnit nastavení, zvolte **Zobrazit pokročilé možnosti**. | *{viz popisy}* | * **Filtr předpon**: V tomto kurzu nechte nastavení prázdné. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec předpony, třeba cestu k určitému prostředku a jeho parametr. <p>* **Filtr přípon**: V tomto kurzu nechte nastavení prázdné. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec přípony, třeba příponu názvu souboru, pokud chcete jenom určité typy souborů.<p>* **Název předplatného**: Zadejte jedinečný název odběru události. |
   | | | 

   Až to budete mít, trigger služby Event Grid bude vypadat podobně jako v následujícím příkladu:
   
   ![Příklad podrobností triggeru služby Event Grid](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti akce, zvolte záhlaví akce.

   ![Uložení aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Když k uložení aplikace logiky použijete trigger služby Event Grid, vytvoří Azure automaticky předplatné události pro aplikaci logiky a vybraný prostředek. Když prostředek publikuje událost do Event Gridu, doručí ji Event Grid automaticky aplikaci logiky. Tato událost iniciuje aplikaci logiky a potom vytvoří a spustí instanci pracovního postupu, který definujete v dalších krocích.

Aplikace logiky je teď funkční a naslouchá událostem Event Gridu, ale zatím nic nedělá, dokud do pracovního postupu nepřidáte akce. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Přidání podmínky, která kontroluje změny virtuálního počítače

Pokud chcete, aby se pracovní postup aplikace logiky spustil, jen když dojde k určité události, přidejte podmínku, která ve virtuálním počítači kontroluje operace „write“. Jakmile je tato podmínka splněna, aplikace logiky vám pošle e-mail s podrobnostmi o aktualizovaném virtuálním počítači.

1. V návrháři pro Logic Apps zvolte pod triggerem služby Event Grid možnost **Nový krok** > **Přidat podmínku**.

   ![Přidání podmínky do aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Návrhář pro Logic Apps přidá do pracovního postupu prázdnou podmínku, včetně cest akcí, které se použijí, pokud je podmínka pravdivá nebo nepravdivá.

   ![Prázdná podmínka](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. V poli **Podmínka** zvolte **Upravit v rozšířeném režimu**.
Zadejte tento výraz:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Vaše podmínka teď vypadá jako v tomto příkladu:

   ![Prázdná podmínka](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Tento výraz zkontrolujte událost `body` objektu `data`, jestli ve vlastnosti `operationName` není operace `Microsoft.Compute/virtualMachines/write`. 
   Další informace o [schématu události Event Gridu](../event-grid/event-schema.md).

3. Pokud chcete zadat popis podmínky, zvolte u obrazce podmínky tlačítko **tři tečky** (**...**) a pak zvolte **Přejmenovat**.

   > [!NOTE] 
   > Další příklady v tomto kurzu obsahují také popis kroků v pracovním postupu aplikace logiky.

4. Zvolte **Upravit v základním režimu**, aby se výraz vyřešil stejně, jako na následujícím obrázku:

   ![Podmínka aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Uložte svou aplikaci logiky.

## <a name="send-email-when-your-virtual-machine-changes"></a>Odeslání e-mailu při změně virtuálního počítače

Teď přidejte [*akci*](../logic-apps/logic-apps-overview.md#logic-app-concepts), která vám pošle e-mail, pokud zadaná podmínka platí.

1. V poli podmínky **Pokud je true** zvolte **Přidat akci**.

   ![Přidání akce, pokud podmínka platí](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Do vyhledávacího pole zadejte jako filtr „e-mail“. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Pak pro konektor vyberte akci „odeslat e-mail“. Příklad: 

   * Pro pracovní nebo školní účet Azure vyberte konektor Office 365 Outlook. 
   * Pro osobní účty Microsoft vyberte konektor Outlook.com. 
   * Pro účty Gmail vyberte konektor Gmail. 

   My budeme pokračovat s konektorem Office 365 Outlook. 
   Pokud používáte jiného poskytovatele, postup je stejný, ale vaše uživatelské rozhraní může vypadat jinak. 

   ![Výběr akce „Odeslat e-mail“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Pokud nemáte připojení k poskytovateli e-mailu, přihlaste se po zobrazení výzvy ke svému e-mailovém účtu.

4. Zadejte podrobnosti o e-mailu podle následující tabulky:

   ![Akce prázdného e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Pokud chcete vybírat z polí, která jsou dostupná ve vašem pracovním postupu, klikněte do textového pole, aby se otevřel seznam **Dynamický obsah**, nebo zvolte **Přidat dynamický obsah**. Pokud chcete zobrazit další pole, zvolte u každého oddílu v seznamu možnost **Zobrazit více**. Pokud chcete seznam **Dynamický obsah** zavřít, zvolte **Přidat dynamický obsah**.

   | Nastavení | Navrhovaná hodnota | Popis | 
   | ------- | --------------- | ----------- | 
   | **Komu** | *{e-mailová-adresa-příjemce}* |Zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu. | 
   | **Předmět** | Aktualizovaný prostředek: **Předmět**| Zadejte obsah předmětu e-mailu. V tomto kurzu zadejte navrhovaný text a vyberte pole události **Předmět**. V našem příkladu je v předmětu e-mailu název aktualizovaného prostředku (virtuálního počítače). | 
   | **Text** | Skupina prostředků: **Téma** <p>Typ události: **Typ události**<p>ID události: **ID**<p>Čas: **Čas události** | Zadejte obsah e-mailu. V tomto kurzu zadejte navrhovaný text a vyberte pole události **Téma**, **Typ události**, **ID** a **Čas události**, aby e-mail obsahoval název skupiny prostředku, typ události, časové razítko události a ID události týkající se aktualizace. <p>Pokud chcete do obsahu přidat prázdné řádky, stiskněte Shift + Enter. | 
   | | | 

   > [!NOTE] 
   > Pokud vyberete pole, které představuje pole položek, přidá návrhář automaticky kolem akce, která na toto pole odkazuje, smyčku **For each**. Aplikace logiky tak provede příslušnou akci pro každou položku pole.

   Teď vypadá akce e-mailu podobně jako v tomto příkladu:

   ![Výběr výstupů zahrnutých do e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Hotová aplikace logiky může vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Uložte svou aplikaci logiky. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti každé akce, zvolte záhlaví akce.

   ![Uložení aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Aplikace logiky je teď funkční, ale než provede akci, čeká na změny virtuálního počítače. 
   Pokud teď chcete aplikaci logiky otestovat, pokračujte k další části.

## <a name="test-your-logic-app-workflow"></a>Testování pracovního postupu aplikace logiky

1. Pokud chcete zkontrolovat, jestli aplikace logiky dostává zadané události, aktualizujte virtuální počítač. 

   Můžete třeba změnit velikost virtuálního počítače na webu Azure Portal nebo [použijte ke změně velikosti virtuálního počítače Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Za chvíli by vám měl přijít e-mail. Příklad:

   ![E-mail o aktualizaci virtuálního počítače](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Pokud chcete zkontrolovat historii spuštění a triggerů své aplikace logiky, zvolte v nabídce aplikace logiky možnost **Přehled**. Pokud chcete zobrazit další podrobnosti o spuštění, zvolte řádek příslušného spuštění.

   ![Historie spuštění aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Pokud chcete zobrazit vstupy a výstupy jednotlivých kroků, rozbalte krok, který chcete zkontrolovat. Tyto informace vám můžou pomoct s diagnostikou a laděním problémů ve vaší aplikaci logiky.
 
   ![Podrobnosti o historii spuštění aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Blahopřejeme k vytvoření a spuštění aplikace logiky, která monitoruje události prostředku prostřednictvím Event Gridu a v případě, že tyto události nastanou, vám pošle e-mail. Také jste se naučili, jak můžete jednoduše vytvářet pracovní postupy, které automatizují procesy a integrují systémy a cloudové služby.

Event Gridy a aplikace logiky umožňují monitorovat i jiné konfigurační změny, například:

* Virtuální počítač získá oprávnění k řízení přístupu na základě role (RBAC).
* Změny provedené ve skupině zabezpečení sítě (NSG) v síťovém rozhraní (NIC).
* Přidání nebo odebrání disků virtuálního počítače.
* Přiřazení veřejné IP adresy síťovému rozhraní virtuálního počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky a provádí akce, za které mohou být ve vašem předplatném Azure účtovány poplatky. Jakmile budete s kurzem a testováním hotovi, nezapomeňte vypnout nebo odstranit všechny prostředky, za které nechcete platit poplatky.

* Pokud chcete aplikaci logiky zastavit, aniž byste odstranili svou práci, vypněte ji. V nabídce aplikace logiky zvolte **Přehled**. Na panelu nástrojů zvolte **Vypnout**.

  ![Vypnutí aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

* Pokud chcete aplikaci logiky trvale odstranit, vyberte v její nabídce **Přehled**. Na panelu nástrojů zvolte **Odstranit**. Potvrďte, že chcete odstranit aplikaci logiky, a pak zvolte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
