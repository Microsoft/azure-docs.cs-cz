---
title: Monitorování změn virtuálních počítačů – Azure Event Grid & Logic Apps
description: Kontrolovat změny virtuálních počítačů pomocí Azure Event Grid a Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: ed48a4e5bab807695000fe6cdbecf1c1b7b01e9b
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325812"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps

Pokud chcete monitorovat a reagovat na konkrétní události, ke kterým dochází v prostředcích Azure nebo prostředků třetích stran, můžete automatizovat a spouštět úlohy jako pracovní postup vytvořením [Aplikace logiky](../logic-apps/logic-apps-overview.md) , která používá minimální kód. Tyto prostředky můžou publikovat události do služby [Azure Event Grid](../event-grid/overview.md). Event Grid tyto události pošle odběratelům, kteří jako své koncové body používají fronty, webhooky nebo [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). V rámci předplatitele může vaše aplikace logiky počkat na tyto události z Event gridu, než se spustí automatizované pracovní postupy pro provádění úloh.

Tady jsou příklady událostí, které mohou vydavatelé prostřednictvím služby Azure Event Grid posílat odběratelům.

* Vytvoření, čtení, aktualizace nebo odstranění prostředku. Můžete třeba monitorovat změny, za které mohou být účtovány poplatky v předplatném Azure a které ovlivní vaše vyúčtování.

* Přidání osoby do předplatného Azure nebo její odebrání.

* Aplikace provede určitou akci.

* Nová zpráva se zobrazí ve frontě.

V tomto kurzu se vytvoří aplikace logiky, která monitoruje změny virtuálního počítače, a pošle e-maily o těchto změnách. Když vytvoříte aplikaci logiky s odběrem události, která se týká prostředku Azure, tečou události od daného prostředku přes Event Grid do aplikace logiky. Tento průvodce vás provede sestavením této aplikace logiky:

![Přehled – monitorování virtuálního počítače v Event Gridu a aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci logiky, která monitoruje události v Event Gridu.
> * Přidat podmínku, která výslovně kontroluje změny virtuálního počítače.
> * Odeslat e-mail při změně virtuálního počítače.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele e-mailu, který podporuje Logic Apps pro posílání oznámení, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/).

  V tomto kurzu se používá účet Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* [Virtuální počítač](https://azure.microsoft.com/services/virtual-machines) , který je sám ve své vlastní skupině prostředků Azure. Pokud jste to ještě neudělali, vytvořte virtuální počítač pomocí [kurzu Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md). Publikování událostí virtuálním počítačem [nevyžaduje z vaší strany žádnou další akci](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **vytvořit prostředek** > **integrace** **Aplikace logiky** > .

   ![Vytvoření aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. V části **Aplikace logiky**zadejte informace o vašem prostředku aplikace logiky. Až to budete mít, vyberte **Vytvořit**.

   ![Zadání podrobností o aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*Logic-App-name*> | Zadejte jedinečný název aplikace logiky. |
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Vyberte stejné předplatné Azure pro všechny služby v tomto kurzu. |
   | **Skupina prostředků** | Ano | <*Azure-Resource-group*> | Název skupiny prostředků Azure pro vaši aplikaci logiky, kterou můžete vybrat pro všechny služby v tomto kurzu. |
   | **Umístění** | Ano | <*Azure-region*> | Pro všechny služby v tomto kurzu vyberte stejnou oblast. |
   |||

1. Když Azure nasadí vaši aplikaci logiky, Návrhář Logic Apps zobrazí stránku s úvodním videem a běžně používanými triggery. Posuňte se za video a triggery.

1. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Návrhář Logic Apps nyní zobrazuje [*triggery*](../logic-apps/logic-apps-overview.md#logic-app-concepts) , které můžete použít ke spuštění aplikace logiky. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se Trigger aktivuje, Azure Logic Apps vytvoří instanci pracovního postupu, která spustí vaši aplikaci logiky.

## <a name="add-an-event-grid-trigger"></a>Přidat aktivační událost Event Grid

Nyní přidejte aktivační událost Event Grid, kterou použijete k monitorování skupiny prostředků pro váš virtuální počítač.

1. V Návrháři zadejte do vyhledávacího pole `event grid` jako filtr. V seznamu aktivační události vyberte, kdy se má aktivovat **událost prostředku** .

   ![Vyberte tuto aktivační událost: "na události prostředku"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po zobrazení výzvy se přihlaste k Azure Event Grid s přihlašovacími údaji k účtu Azure. V seznamu **tenant** , ve kterém se zobrazuje tenant Azure Active Directory přidružený k vašemu předplatnému Azure, ověřte, že se zobrazí správný tenant, například:

   ![Přihlášení pod přihlašovacími údaji Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Pokud jste se přihlásili pod osobním účtem Microsoft, třeba @outlook.com nebo @hotmail.com, nemusí se trigger služby Event Grid zobrazit správně. Alternativním řešením je, že vyberete [připojit s instančním objektem](../active-directory/develop/howto-create-service-principal-portal.md)nebo ověřit jako člena Azure Active Directory, který je přidružený k vašemu předplatnému Azure, třeba *user-name*@emailoutlook.onmicrosoft.com.

1. Nyní můžete aplikaci logiky přihlašovat k událostem od vydavatele. Zadejte podrobnosti o odběru události, jak je popsáno v následující tabulce, například:

   ![Zadání podrobností o odběru události](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Předplatné** | Ano | <*událost – Publisher-Azure-Subscription-name*> | Vyberte název předplatného Azure, které je přidružené k *vydavateli události*. Pro tento kurz vyberte název předplatného Azure pro váš virtuální počítač. |
   | **Typ prostředku** | Ano | <*událost-Publisher-Azure-Resource-type*> | Vyberte typ prostředku Azure pro vydavatele události. Další informace o typech prostředků Azure najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/resource-manager-supported-services.md). Pro tento kurz vyberte hodnotu `Microsoft.Resources.ResourceGroups` pro monitorování skupin prostředků Azure. Pokud jste chtěli monitorovat pouze virtuální počítače,  |
   | **Název prostředku** |  Ano | <*událost-Publisher-Azure-Resource-name*> | Vyberte název prostředku Azure pro vydavatele události. Tento seznam se liší v závislosti na typu prostředku, který jste vybrali. V tomto kurzu vyberte název skupiny prostředků Azure, která zahrnuje váš virtuální počítač. |
   | **Položka typu události** |  Ne | <*Typ události*> | Vyberte jeden nebo více konkrétních typů událostí pro filtrování a odeslání do služby Event Grid. Volitelně můžete například přidat tyto typy událostí k detekci, kdy jsou prostředky změněny nebo odstraněny: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Další informace najdete v těchto tématech: <p><p>@no__t – 0[Azure Event Grid schéma událostí pro skupiny prostředků](../event-grid/event-schema-resource-groups.md) <br>- [pochopení filtrování událostí](../event-grid/event-filtering.md) <br>@no__t – 0[události filtru pro Event Grid](../event-grid/how-to-filter-events.md) |
   | Chcete-li přidat volitelné vlastnosti, vyberte možnost **Přidat nový parametr**a pak vyberte požadované vlastnosti. | Ne | {Zobrazit popisy} | **Filtr předpon*** : pro tento kurz nechte tuto vlastnost prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec předpony, třeba cestu k určitému prostředku a jeho parametr. <p>**filtr přípon*** : pro tento kurz nechte tuto vlastnost prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec přípony, třeba příponu názvu souboru, pokud chcete jenom určité typy souborů. <p>**název Předplatného*** : pro tento kurz můžete zadat jedinečný název pro vaše předplatné událostí. |
   |||

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti akce, vyberte záhlaví akce.

   ![Uložení aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Když k uložení aplikace logiky použijete trigger služby Event Grid, vytvoří Azure automaticky předplatné události pro aplikaci logiky a vybraný prostředek. Když prostředek publikuje událost do Event Gridu, doručí ji Event Grid automaticky aplikaci logiky. Tato událost iniciuje aplikaci logiky a potom vytvoří a spustí instanci pracovního postupu, který definujete v dalších krocích.

Aplikace logiky je teď funkční a naslouchá událostem Event Gridu, ale zatím nic nedělá, dokud do pracovního postupu nepřidáte akce.

## <a name="add-a-condition"></a>Přidat podmínku

Pokud chcete, aby aplikace logiky běžela pouze v případě, že dojde k určité události nebo operace, přidejte podmínku, která kontroluje operaci `Microsoft.Compute/virtualMachines/write`. Jakmile je tato podmínka splněna, aplikace logiky vám pošle e-mail s podrobnostmi o aktualizovaném virtuálním počítači.

1. V návrháři aplikace logiky pod triggerem Event Grid vyberte **Nový krok**.

   ![Vyberte nový krok.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. V části **zvolit akci**zadejte do vyhledávacího pole `condition` jako filtr. V seznamu akce vyberte akci **Podmínka** .

   ![Přidat podmínku](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Návrhář pro Logic Apps přidá do pracovního postupu prázdnou podmínku, včetně cest akcí, které se použijí, pokud je podmínka pravdivá nebo nepravdivá.

   ![Zobrazí se prázdná podmínka.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Přejmenujte název podmínky na `If a virtual machine in your resource group has changed`. V záhlaví podmínky vyberte tlačítko se třemi tečkami (.. **.** ) a pak vyberte **Přejmenovat**.

   ![Přejmenovat podmínku](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Vytvořte podmínku, která kontroluje událost `body` pro objekt `data`, u kterého je vlastnost `operationName` rovna operaci `Microsoft.Compute/virtualMachines/write`. Další informace o [schématu události Event Gridu](../event-grid/event-schema.md).

   1. Na prvním řádku pod **A** klikněte mimo pole vlevo. V seznamu dynamický obsah, který se zobrazí, vyberte možnost **výraz**.

      ![Výběrem výrazu otevřete Editor výrazů.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. V editoru výrazů zadejte tento výraz, který vrátí název operace z triggeru a vyberte **OK**:

      `triggerBody()?['data']['operationName']`

      Například:

      ![Zadejte výraz pro extrakci názvu operace.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. V prostředním poli ponechte operátor **rovná se**.

   1. Do pravého pole zadejte tuto hodnotu, což je konkrétní operace, kterou chcete monitorovat:

      `Microsoft.Compute/virtualMachines/write`

   Vaše dokončená podmínka teď vypadá jako v tomto příkladu:

   ![Dokončena podmínka, která porovnává operaci](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Pokud přepnete ze zobrazení Návrh do zobrazení kódu a zpět do návrhového zobrazení, výraz, který jste zadali v podmínce, se přeloží na token **data. OperationName** :

   ![Vyřešené tokeny v podmínce](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Uložte svou aplikaci logiky.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

Teď přidejte [*akci*](../logic-apps/logic-apps-overview.md#logic-app-concepts) , abyste mohli obdržet e-mail, když je zadaná podmínka pravdivá.

1. V poli podmínka **Pokud je nastavena hodnota true** vyberte **přidat akci**.

   ![Přidání akce, pokud podmínka platí](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. V části **zvolit akci**zadejte do vyhledávacího pole `send an email` jako filtr. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Pak pro konektor vyberte akci „odeslat e-mail“. Například:

   * Pro pracovní nebo školní účet Azure vyberte konektor Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte konektor Outlook.com.

   * Pro účty Gmail vyberte konektor Gmail.

   Tento kurz pokračuje s konektorem Office 365 Outlook. Pokud používáte jiného poskytovatele, postup zůstane stejný, ale vaše uživatelské rozhraní se může mírně lišit.

   ![Výběr akce „Odeslat e-mail“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Pokud nemáte připojení k poskytovateli e-mailu, přihlaste se po zobrazení výzvy ke svému e-mailovém účtu.

1. Přejmenujte akci odeslat e-mail na tento název: `Send email when virtual machine updated`

1. Zadejte informace o e-mailu, jak je uvedeno v následující tabulce:

   ![Zadání informací o akci e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Pokud chcete vybrat výstup z předchozích kroků pracovního postupu, klikněte do textového pole, aby se zobrazil seznam dynamického obsahu, nebo vyberte **Přidat dynamický obsah**. Další výsledky získáte výběrem **Zobrazit více** pro každý oddíl v seznamu. Chcete-li zavřít seznam dynamického obsahu, vyberte znovu **Přidat dynamický obsah** .

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Komu** | Ano | <*příjemce @ no__t-2domain*> | Zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Předmět** | Ano | `Resource updated:` **Předmět** | Zadejte obsah předmětu e-mailu. Pro tento kurz zadejte zadaný text a vyberte pole **Předmět** události. V našem příkladu je v předmětu e-mailu název aktualizovaného prostředku (virtuálního počítače). |
   | **Text** | Ano | @no__t – 0 – **téma** <p>**Typ události** `Event type:`<p>`Event ID:` **ID**<p>@no__t – 0 – **čas události** | Zadejte obsah e-mailu. Pro tento kurz zadejte zadaný text a vyberte pole **události,** **Typ události**, **ID**a **dobu události** , aby váš e-mail zahrnoval prostředek, který vyvolal událost, typ události, časové razítko události a ID události pro Update. V tomto kurzu je prostředkem skupina prostředků Azure vybraná v aktivační události. <p>Pokud chcete do obsahu přidat prázdné řádky, stiskněte Shift + Enter. |
   ||||

   > [!NOTE]
   > Pokud vyberete pole, které představuje pole položek, přidá návrhář automaticky kolem akce, která na toto pole odkazuje, smyčku **For each**. Aplikace logiky tak provede příslušnou akci pro každou položku pole.

   Teď vypadá akce e-mailu podobně jako v tomto příkladu:

   ![Výběr výstupů zahrnutých do e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Hotová aplikace logiky může vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Uložte svou aplikaci logiky. Pokud chcete v aplikaci logiky sbalit a skrýt podrobnosti o jednotlivých akcích, vyberte záhlaví akce.

   Aplikace logiky je teď funkční, ale než provede akci, čeká na změny virtuálního počítače. Pokud teď chcete aplikaci logiky otestovat, pokračujte k další části.

## <a name="test-your-logic-app-workflow"></a>Testování pracovního postupu aplikace logiky

1. Pokud chcete zkontrolovat, jestli aplikace logiky dostává zadané události, aktualizujte virtuální počítač.

   Můžete třeba změnit velikost virtuálního počítače na webu Azure Portal nebo [použijte ke změně velikosti virtuálního počítače Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Za chvíli by vám měl přijít e-mail. Například:

   ![E-mail o aktualizaci virtuálního počítače](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Pokud chcete zkontrolovat historii spuštění a triggeru pro vaši aplikaci logiky, v nabídce aplikace logiky vyberte **Přehled**. Chcete-li zobrazit více podrobností o spuštění, vyberte řádek pro daný běh.

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

* Pokud chcete aplikaci logiky zastavit, aniž byste odstranili svou práci, vypněte ji. V nabídce aplikace logiky vyberte **Přehled**. Na panelu nástrojů vyberte **Zakázat**.

  ![Vypnutí aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Pokud se nabídka aplikace logiky nezobrazí, zkuste se vrátit na řídicí panel Azure a otevřít aplikaci logiky znovu.

* Pokud chcete aplikaci logiky trvale odstranit, vyberte v nabídce aplikace logiky **Přehled**. Na panelu nástrojů vyberte **Odstranit**. Potvrďte, že chcete odstranit aplikaci logiky, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
