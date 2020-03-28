---
title: Sledování změn virtuálních počítačů – Azure Event Grid & Logic Apps
description: Kontrola změn ve virtuálních počítačích (VM) pomocí Azure Event Grid a Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982575"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Kurz: Sledování změn virtuálních strojů pomocí Azure Event Grid a Logic Apps

Chcete-li sledovat a reagovat na konkrétní události, ke kterým dochází ve prostředcích Azure nebo prostředcích třetích stran, můžete automatizovat a spouštět úlohy jako pracovní postup vytvořením [aplikace logiky,](../logic-apps/logic-apps-overview.md) která používá minimální kód. Tyto prostředky můžete publikovat události do [mřížky událostí Azure](../event-grid/overview.md). Event Grid tyto události pošle odběratelům, kteří jako své koncové body používají fronty, webhooky nebo [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). Jako odběratel může aplikace logiky čekat na tyto události z mřížky událostí před spuštěním automatizovaných pracovních postupů k provádění úloh.

Tady jsou příklady událostí, které mohou vydavatelé prostřednictvím služby Azure Event Grid posílat odběratelům.

* Vytvoření, čtení, aktualizace nebo odstranění prostředku. Můžete třeba monitorovat změny, za které mohou být účtovány poplatky v předplatném Azure a které ovlivní vaše vyúčtování.

* Přidání osoby do předplatného Azure nebo její odebrání.

* Aplikace provede určitou akci.

* Nová zpráva se zobrazí ve frontě.

Tento kurz vytvoří aplikaci logiky, která monitoruje změny ve virtuálním počítači a odesílá e-maily o těchto změnách. Když vytvoříte aplikaci logiky s odběrem události, která se týká prostředku Azure, tečou události od daného prostředku přes Event Grid do aplikace logiky. Tento průvodce vás provede sestavením této aplikace logiky:

![Přehled – monitorování virtuálního počítače v Event Gridu a aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci logiky, která monitoruje události v Event Gridu.
> * Přidat podmínku, která výslovně kontroluje změny virtuálního počítače.
> * Odeslat e-mail při změně virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele e-mailu podporovaný logic apps pro odesílání oznámení, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/).

  Tento kurz používá účet Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* [Virtuální počítač,](https://azure.microsoft.com/services/virtual-machines) který je sám ve své vlastní skupině prostředků Azure. Pokud jste tak ještě neučinili, vytvořte virtuální počítač pomocí [kurzu Vytvořit virtuální počítač](../virtual-machines/windows/quick-create-portal.md). Publikování událostí virtuálním počítačem [nevyžaduje z vaší strany žádnou další akci](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte > Vytvořit > **aplikaci logiky****integrace** **prostředků**.

   ![Vytvoření aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. V části **Aplikace logiky**zadejte informace o prostředku aplikace logiky. Až to budete mít, vyberte **Vytvořit**.

   ![Zadání podrobností o aplikaci logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*název aplikace logika*> | Zadejte jedinečný název aplikace logiky. |
   | **Předplatné** | Ano | <*Název předplatného Azure*> | Vyberte stejné předplatné Azure pro všechny služby v tomto kurzu. |
   | **Skupina prostředků** | Ano | <*Skupina prostředků Azure*> | Název skupiny prostředků Azure pro vaši aplikaci logiky, kterou můžete vybrat pro všechny služby v tomto kurzu. |
   | **Umístění** | Ano | <*Oblast Azure*> | Pro všechny služby v tomto kurzu vyberte stejnou oblast. |
   |||

1. Po Azure nasadí aplikace logiky, Návrhář logic Apps zobrazí stránku s úvodní video a běžně používané aktivační události. Posuňte se za video a triggery.

1. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Návrhář aplikací logiky nyní zobrazuje aktivační události, které můžete použít ke spuštění aplikace [*logiky.*](../logic-apps/logic-apps-overview.md#logic-app-concepts) Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se spustí aktivační událost, Azure Logic Apps vytvoří instanci pracovního postupu, která spouští aplikaci logiky.

## <a name="add-an-event-grid-trigger"></a>Přidání aktivační události

Teď přidejte aktivační událost Mřížka událostí, kterou používáte ke sledování skupiny prostředků pro váš virtuální počítač.

1. V návrháři zadejte `event grid` do vyhledávacího pole jako filtr. Ze seznamu aktivačních událostí vyberte aktivační událost **When a resource event.**

   ![Vyberte tuto aktivační událost: "Událost prostředku"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Po zobrazení výzvy se přihlaste k Azure Event Grid pomocí přihlašovacích údajů k účtu Azure. V seznamu **tenanta,** který zobrazuje tenanta Azure Active Directory, který je přidružený k vašemu předplatnému Azure, zkontrolujte, že se zobrazí správný klient, například:

   ![Přihlášení pod přihlašovacími údaji Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Pokud jste se přihlásili pod osobním účtem Microsoft, třeba @outlook.com nebo @hotmail.com, nemusí se trigger služby Event Grid zobrazit správně. Jako řešení vyberte [Připojit pomocí instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md)nebo se ověřte jako člen služby Azure Active Directory, která je přidružená k vašemu předplatnému Azure, například *uživatelské jméno*@emailoutlook.onmicrosoft.com.

1. Teď se přihlaste k odběru aplikace logiky k událostem od vydavatele. Zadejte podrobnosti o předplatném událostí, jak je popsáno v následující tabulce, například:

   ![Zadání podrobností o odběru události](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Předplatné** | Ano | <*event-publisher-Azure-subscription-name*> | Vyberte název předplatného Azure, které je přidruženo k *vydavateli události*. V tomto kurzu vyberte název předplatného Azure pro váš virtuální počítač. |
   | **Typ prostředku** | Ano | <*event-publisher-Azure-resource-type*> | Vyberte typ prostředku Azure pro vydavatele událostí. Další informace o typech prostředků Azure najdete v tématu [Zprostředkovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md). V tomto kurzu `Microsoft.Resources.ResourceGroups` vyberte hodnotu pro sledování skupin prostředků Azure. |
   | **Název prostředku** |  Ano | <*event-publisher-Azure-resource-name*> | Vyberte název prostředku Azure pro vydavatele události. Tento seznam se liší v závislosti na vybraném typu prostředku. V tomto kurzu vyberte název skupiny prostředků Azure, která zahrnuje váš virtuální počítač. |
   | **Položka typu události** |  Ne | <*typy událostí*> | Vyberte jeden nebo více konkrétních typů událostí, které chcete filtrovat a odeslat do mřížky událostí. Můžete například volitelně přidat tyto typy událostí, abyste zjistili, kdy jsou prostředky změněny nebo odstraněny: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Další informace najdete v těchto tématech: <p><p>- [Schéma událostí služby Azure Event Grid pro skupiny prostředků](../event-grid/event-schema-resource-groups.md) <br>- [Principy filtrování událostí](../event-grid/event-filtering.md) <br>- [Filtrování událostí pro mřížku událostí](../event-grid/how-to-filter-events.md) |
   | Chcete-li přidat volitelné vlastnosti, vyberte **Přidat nový parametr**a pak vyberte požadované vlastnosti. | Ne | {viz popisy} | * **Filtr předpony**: V tomto kurzu ponechte tuto vlastnost prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec předpony, třeba cestu k určitému prostředku a jeho parametr. <p>* **Filtr přípony**: V tomto kurzu ponechte tuto vlastnost prázdnou. Výchozí chování odpovídá všem hodnotám. Do filtru můžete zadat řetězec přípony, třeba příponu názvu souboru, pokud chcete jenom určité typy souborů. <p>* **Název předplatného**: V tomto kurzu můžete zadat jedinečný název pro odběr událostí. |
   |||

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**. Chcete-li sbalit a skrýt podrobnosti akce v aplikaci logiky, vyberte záhlaví akce.

   ![Uložení aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Když k uložení aplikace logiky použijete trigger služby Event Grid, vytvoří Azure automaticky předplatné události pro aplikaci logiky a vybraný prostředek. Když prostředek publikuje událost do Event Gridu, doručí ji Event Grid automaticky aplikaci logiky. Tato událost iniciuje aplikaci logiky a potom vytvoří a spustí instanci pracovního postupu, který definujete v dalších krocích.

Aplikace logiky je teď funkční a naslouchá událostem Event Gridu, ale zatím nic nedělá, dokud do pracovního postupu nepřidáte akce.

## <a name="add-a-condition"></a>Přidání podmínky

Pokud chcete, aby vaše aplikace logiky spustit pouze v případě, že `Microsoft.Compute/virtualMachines/write` dojde k určité události nebo operaci, přidejte podmínku, která kontroluje pro operaci. Jakmile je tato podmínka splněna, aplikace logiky vám pošle e-mail s podrobnostmi o aktualizovaném virtuálním počítači.

1. V Návrháři aplikace logiky vyberte pod aktivační událostí mřížku událostí **nový krok**.

   ![Vyberte "Nový krok"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. V části **Zvolte akci**zadejte `condition` do vyhledávacího pole jako filtr. Ze seznamu akcí vyberte akci **Podmínka.**

   ![Přidání podmínky](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Návrhář pro Logic Apps přidá do pracovního postupu prázdnou podmínku, včetně cest akcí, které se použijí, pokud je podmínka pravdivá nebo nepravdivá.

   ![Zobrazí se prázdná podmínka.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Přejmenujte název `If a virtual machine in your resource group has changed`podmínky na . Na záhlaví podmínky vyberte tlačítko elipsy (**...**) a vyberte **Přejmenovat**.

   ![Přejmenovat podmínku](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Vytvořte podmínku, `body` která `data` zkontroluje `operationName` událost pro objekt, kde je vlastnost rovna `Microsoft.Compute/virtualMachines/write` operaci. Další informace o [schématu události Event Gridu](../event-grid/event-schema.md).

   1. Na prvním řádku pod **A** klikněte mimo pole vlevo. V seznamu dynamického obsahu, který se zobrazí, vyberte **Výraz**.

      ![Výběrem možnosti Výraz otevřete editor výrazů.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. V editoru výrazů zadejte tento výraz, který vrátí název operace ze spouště, a vyberte **OK**:

      `triggerBody()?['data']['operationName']`

      Například:

      ![Zadání výrazu pro extrahování názvu operace](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. V prostředním poli ponechte operátor **rovná se**.

   1. Do pravého pole zadejte tuto hodnotu, což je konkrétní operace, kterou chcete sledovat:

      `Microsoft.Compute/virtualMachines/write`

   Dokončená podmínka nyní vypadá takto:

   ![Dokončená podmínka, která porovnává operaci](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Pokud přepnete z návrhového zobrazení na zobrazení kódu a zpět do návrhového zobrazení, výraz, který jste zadali v podmínce, se překládá na token **data.operationName:**

   ![Vyřešené tokeny v podmínce](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Uložte svou aplikaci logiky.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

Nyní přidejte [*akci,*](../logic-apps/logic-apps-overview.md#logic-app-concepts) abyste mohli obdržet e-mail, pokud je zadaná podmínka pravdivá.

1. V poli Podmínka je **pravda** vyberte **Přidat akci**.

   ![Přidání akce, pokud podmínka platí](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. V části **Zvolte akci**zadejte `send an email` do vyhledávacího pole jako filtr. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Pak pro konektor vyberte akci „odeslat e-mail“. Například:

   * Pro pracovní nebo školní účet Azure vyberte konektor Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte konektor Outlook.com.

   * Pro účty Gmail vyberte konektor Gmail.

   Tento kurz pokračuje s konektorem Office 365 Outlook. Pokud používáte jiného zprostředkovatele, kroky zůstanou stejné, ale vaše ui může vypadat mírně odlišné.

   ![Výběr akce „Odeslat e-mail“](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Pokud nemáte připojení k poskytovateli e-mailu, přihlaste se po zobrazení výzvy ke svému e-mailovém účtu.

1. Přejmenujte akci odeslat e-mail na tento název:`Send email when virtual machine updated`

1. Zadejte informace o e-mailu, jak je uvedeno v následující tabulce:

   ![Poskytnutí informací o akci e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Chcete-li vybrat výstup z předchozích kroků pracovního postupu, klepněte do textového pole, aby se zobrazil seznam dynamického obsahu, nebo vyberte **Přidat dynamický obsah**. Další výsledky zobrazíte v části **Viz pro** jednotlivé části v seznamu. Chcete-li zavřít seznam dynamického obsahu, vyberte znovu **přidat dynamický obsah.**

   | Vlastnost | Požaduje se | Hodnota | Popis |
   | -------- | -------- | ----- | ----------- |
   | **Akce** | Ano | <*doména příjemce\@*> | Zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Subjekt** | Ano | `Resource updated:` **Subjekt** | Zadejte obsah předmětu e-mailu. Pro účely tohoto kurzu zadejte zadaný text a vyberte pole **Předmět** události. V našem příkladu je v předmětu e-mailu název aktualizovaného prostředku (virtuálního počítače). |
   | **Text** | Ano | `Resource:`**Téma** <p>`Event type:` **Event Type**<p>`Event ID:`**ID**<p>`Time:`**Čas události** | Zadejte obsah e-mailu. V tomto kurzu zadejte zadaný text a vyberte pole **Téma**události , **Typ události**, **ID**a **Čas události** tak, aby e-mail zahrnoval prostředek, který pro aktualizaci vypálil událost, typ události, časové razítko události a ID události. Pro účely tohoto kurzu je prostředek skupiny prostředků Azure vybrané v aktivační události. <p>Pokud chcete do obsahu přidat prázdné řádky, stiskněte Shift + Enter. |
   ||||

   > [!NOTE]
   > Pokud vyberete pole, které představuje pole položek, přidá návrhář automaticky kolem akce, která na toto pole odkazuje, smyčku **For each**. Aplikace logiky tak provede příslušnou akci pro každou položku pole.

   Teď vypadá akce e-mailu podobně jako v tomto příkladu:

   ![Výběr výstupů zahrnutých do e-mailu](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Hotová aplikace logiky může vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Uložte svou aplikaci logiky. Chcete-li sbalit a skrýt podrobnosti jednotlivých akcí v aplikaci logiky, vyberte záhlaví akce.

   Aplikace logiky je teď funkční, ale než provede akci, čeká na změny virtuálního počítače. Pokud teď chcete aplikaci logiky otestovat, pokračujte k další části.

## <a name="test-your-logic-app-workflow"></a>Testování pracovního postupu aplikace logiky

1. Pokud chcete zkontrolovat, jestli aplikace logiky dostává zadané události, aktualizujte virtuální počítač.

   Můžete třeba změnit velikost virtuálního počítače na webu Azure Portal nebo [použijte ke změně velikosti virtuálního počítače Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Za chvíli by vám měl přijít e-mail. Například:

   ![E-mail o aktualizaci virtuálního počítače](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Chcete-li zkontrolovat spuštění a historii aktivačních událostí pro aplikaci logiky, v nabídce aplikace logiky vyberte **Přehled**. Chcete-li zobrazit další podrobnosti o spuštění, vyberte řádek pro toto spuštění.

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

* Chcete-li trvale odstranit aplikaci logiky, v yberte V nabídce aplikace logiky **možnost Přehled**. Na panelu nástrojů vyberte **Odstranit**. Potvrďte, že chcete odstranit aplikaci logiky, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
