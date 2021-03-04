---
title: Vytváření úloh automatizace pro správu a monitorování prostředků Azure
description: Můžete nastavit automatizované úlohy, které vám pomůžou spravovat prostředky Azure a monitorovat náklady díky vytváření pracovních postupů, které běží na Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719045"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Umožňuje spravovat prostředky Azure a monitorovat náklady vytvořením úloh automatizace (Preview).

> [!IMPORTANT]
> Tato funkce je ve verzi Public Preview, poskytuje se bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pro snazší správu [prostředků Azure](../azure-resource-manager/management/overview.md#terminology) můžete vytvářet automatizované úlohy správy pro konkrétní prostředek nebo skupinu prostředků pomocí šablon úloh služby Automation, které se liší v dostupnosti na základě typu prostředku. Například pro [účet služby Azure Storage](../storage/common/storage-account-overview.md)můžete nastavit úlohu automatizace, která vám pošle měsíční náklady na účet úložiště. Pro [virtuální počítač Azure](https://azure.microsoft.com/services/virtual-machines/)můžete vytvořit úlohu automatizace, která tento virtuální počítač zapíná nebo vypíná podle předdefinovaného plánu.

Úloha automatizace je ve skutečnosti pracovní postup, který běží na službě [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a účtuje se pomocí stejných [sazeb](https://azure.microsoft.com/pricing/details/logic-apps/) a [cenového modelu](../logic-apps/logic-apps-pricing.md). Po vytvoření úlohy můžete zobrazit a upravit základní pracovní postup otevřením úkolu v návrháři aplikace logiky. Po dokončení úlohy alespoň jednoho spuštění můžete zkontrolovat stav, historii, vstupy a výstupy pro každé spuštění.

Tady jsou aktuálně dostupné šablony úloh v této verzi Preview:

| Typ prostředku | Šablony úloh služby Automation |
|---------------|---------------------------|
| Skupiny prostředků Azure | **Při odstranění prostředku** |
| Všechny prostředky v Azure | **Odeslání měsíčních nákladů na prostředek** |
| Virtuální počítače Azure | Dále: <p>- **Vypínání virtuálního počítače** <br>- **Spustit virtuální počítač** |
| Účty úložiště Azure | Dále: <p>- **Odstranit staré objekty blob** |
| Azure Cosmos DB | Také <p>- **Odeslat výsledek dotazu prostřednictvím e-mailu** |
|||

V tomto článku se dozvíte, jak provádět následující úlohy:

* [Vytvoří úlohu automatizace](#create-automation-task) pro určitý prostředek Azure.

* [Projděte si historii úlohy](#review-task-history), která zahrnuje stav spuštění, vstupy, výstupy a další historické informace.

* [Upravte úkol](#edit-task) tak, abyste mohli úlohu aktualizovat, nebo můžete upravit základní pracovní postup úkolu v návrháři aplikace logiky.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Jak se liší úlohy automatizace od Azure Automation?

V současné době můžete vytvořit úlohu automatizace jenom na úrovni prostředků, zobrazit historii spuštění úkolu a upravit základní pracovní postup aplikace logiky úlohy, který používá služba [Azure Logic Apps](../logic-apps/logic-apps-overview.md) . Úlohy služby Automation jsou více základní a nenáročné než [Azure Automation](../automation/automation-intro.md).

Porovnáním Azure Automation je cloudová služba pro automatizaci a konfiguraci, která podporuje konzistentní správu napříč prostředími Azure a mimo Azure. Služba zahrnuje [automatizaci procesů pro orchestraci procesů](../automation/automation-intro.md#process-automation) pomocí [runbooků](../automation/automation-runbook-execution.md), správy konfigurace se [sledováním změn a inventáře](../automation/change-tracking/overview.md), správy aktualizací, sdílených možností a heterogenních funkcí. Automatizace poskytuje plnou kontrolu nad nasazením, provozem a vyřazením úloh a prostředků z provozu.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Prostředek Azure, který chcete spravovat. V tomto článku se jako příklad používá účet úložiště Azure.

* Účet Office 365, pokud chcete postupovat podle příkladu, který vám pošle e-mail pomocí Office 365 Outlooku.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Vytvoření úlohy služby Automation

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek, který chcete spravovat.

1. V nabídce prostředek přejděte do části **Automatizace** a vyberte **úlohy** .

   ![Snímek obrazovky Azure Portal zobrazující nabídku prostředků účtu úložiště, kde je v části automatizace vybraná položka nabídky úkoly](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. V podokně **úlohy** vyberte **Přidat** , abyste mohli vybrat šablonu úlohy.

   ![Snímek obrazovky, který zobrazuje podokno úlohy účtu úložiště, ve kterém je na panelu nástrojů vybraná možnost Přidat](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. V podokně **Přidat úlohu** vyberte v části **Vybrat šablonu** šablonu pro úlohu, kterou chcete vytvořit. Pokud se další stránka nezobrazí, vyberte **Další: ověřování**.

   Tento příklad pokračuje tím, že vyberete šablonu úlohy **Odeslat měsíční náklady na prostředek** .

   ![Snímek obrazovky, který zobrazuje výběry "Odeslat měsíční náklady na prostředek" a "Další: ověřování"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. V části **ověřování** v části **připojení** vyberte **vytvořit** pro každé připojení, které se zobrazí v úloze, abyste mohli zadat přihlašovací údaje pro ověření pro všechna připojení. Typy připojení v jednotlivých úlohách se liší v závislosti na úloze.

   Tento příklad ukazuje pouze jedno z připojení, které je vyžadováno touto úlohou.

   ![Snímek obrazovky, který zobrazuje vybranou možnost vytvořit pro Azure Resource Manager připojení](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Až se zobrazí výzva, přihlaste se pomocí svých přihlašovacích údajů k účtu Azure.

   ![Snímek obrazovky, který zobrazuje výběr, "přihlášení"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Každé úspěšně ověřené připojení vypadá podobně jako v tomto příkladu:

   ![Snímek obrazovky, který zobrazuje úspěšné vytvořené připojení](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Po ověření všech připojení vyberte **Další: Konfigurace** , pokud se další stránka nezobrazí.

1. V části **Konfigurace** zadejte název úlohy a jakékoli další informace vyžadované pro daný úkol. Po dokončení vyberte **Vytvořit**.

   > [!NOTE]
   > Po vytvoření nemůžete změnit název úlohy, takže pokud [upravíte původní pracovní postup](#edit-task-workflow), zvažte název, který se pořád používá. Změny provedené v podkladovém pracovním postupu se vztahují pouze na úkol, který jste vytvořili, nikoli na šablonu úkolu.
   >
   > Pokud například pojmenujte úlohu `Send monthly cost` , ale později upravíte původní pracovní postup tak, aby běžel týdně, nemůžete změnit název úlohy na `Send weekly cost` .

   Úlohy, které odesílají e-mailová oznámení, vyžadují e-mailovou adresu.

   ![Snímek obrazovky, který zobrazuje požadované informace pro vybraný úkol](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Úkol, který jste vytvořili, který je automaticky živý a spuštěný, se teď zobrazí v seznamu **úlohy automatizace** .

   ![Snímek obrazovky, který zobrazuje seznam úloh služby Automation](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Pokud se úloha nezobrazí hned, zkuste aktualizovat seznam úkolů nebo počkat trochu předtím, než se aktualizuje. Na panelu nástrojů vyberte **aktualizovat**.

   Po spuštění vybraného úkolu dostanete e-mail, který bude vypadat jako v tomto příkladu:

   ![Snímek obrazovky, který ukazuje e-mailové oznámení odeslané úlohou](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Kontrola historie úloh

Chcete-li zobrazit historii spuštění úlohy spolu se stavy, vstupy, výstupy a dalšími informacemi, postupujte podle následujících kroků:

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek, který obsahuje historii úlohy, kterou chcete zkontrolovat.

1. V nabídce prostředku v části **Nastavení** vyberte **úlohy automatizace**.

1. V seznamu úlohy vyhledejte úkol, který chcete zkontrolovat. Ve sloupci **spuštění** této úlohy vyberte možnost **Zobrazit**.

   ![Snímek obrazovky zobrazující úlohu a vybranou možnost zobrazení](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   V podokně **historie spuštění** se zobrazí všechna spuštění úlohy spolu se stavy, časy spuštění, identifikátory a doba trvání spuštění.

   ![Snímek obrazovky zobrazující spuštění úkolu, jejich stavy a další informace](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Tady jsou možné stavy pro spuštění:

   | Status | Popis |
   |--------|-------------|
   | **Stornován** | Úloha byla během běhu zrušena. |
   | **Neúspěšný** | Úloha má nejméně jednu neúspěšnou akci, ale pro zpracování této chyby neexistovaly žádné následné akce. |
   | **Spuštěno** | Úloha je aktuálně spuštěná. |
   | **Úspěšný** | Všechny akce byly úspěšné. Úloha může být úspěšně dokončena, pokud se akce nezdařila, ale pro zpracování selhání existovala další akce. |
   | **Čekající** | Běh ještě nebyl spuštěn a pozastaven, protože stále běží dřívější instance úkolu. |
   |||

   Další informace najdete v tématu [Kontrola spuštění historie](../logic-apps/monitor-logic-apps.md#review-runs-history) .

1. Chcete-li zobrazit stavy a další informace pro každý krok v běhu, vyberte tuto možnost spustit.

   Otevře se podokno **spuštění aplikace logiky** a zobrazí se základní pracovní postup, který byl spuštěn.

   * Pracovní postup vždy začíná [*triggerem*](../connectors/apis-list.md#triggers-actions). Pracovní postup pro tento úkol začíná [triggerem **opakování**](../connectors/connectors-native-recurrence.md).

   * Každý krok zobrazuje jeho stav a dobu běhu. Kroky, které mají dobu 0 sekund, trvalo spuštění méně než 1 sekundu.

   ![Snímek obrazovky zobrazující každý krok v době běhu, stavu a běhu](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Chcete-li zkontrolovat vstupy a výstupy pro jednotlivé kroky, vyberte krok, který se rozšíří.

   Tento příklad ukazuje vstupy pro aktivační událost opakování, která nemá žádné výstupy, protože Trigger určuje pouze při spuštění pracovního postupu a poskytuje žádné výstupy pro následné akce.

   ![Snímek obrazovky zobrazující rozbalený Trigger a vstupy](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Naproti tomu má akce **Odeslat e-mail** vstupy z dřívějších akcí v pracovním postupu a ve výstupech.

   ![Snímek obrazovky, který zobrazuje rozšířenou akci, vstupy a výstupy](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Informace o tom, jak můžete vytvářet vlastní automatizované pracovní postupy, abyste mohli integrovat aplikace, data, služby a systémy od kontextu úloh automatizace pro prostředky Azure, najdete v tématu [rychlý Start: vytvoření prvního pracovního postupu integrace pomocí Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Upravit úlohu

Chcete-li změnit úlohu, máte tyto možnosti:

* [Upravte úkol "inline"](#edit-task-inline) , abyste mohli změnit vlastnosti úkolu, například informace o připojení nebo informace o konfiguraci, například vaši e-mailovou adresu.

* [Upravte základní pracovní postup úkolu](#edit-task-workflow) v návrháři aplikace logiky.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Úprava vloženého úkolu

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek, který má úkol, který chcete aktualizovat.

1. V nabídce prostředku v části **Automatizace** vyberte **úlohy**.

1. V seznamu úlohy vyhledejte úkol, který chcete aktualizovat. Otevřete nabídku se třemi tečkami (**...**) úkolu a vyberte **Upravit v řádku**.

   ![Snímek obrazovky, který zobrazuje nabídku otevřené elipsy a vybranou možnost "upravit v řádku"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Ve výchozím nastavení se zobrazí karta **ověřování** a zobrazí se existující připojení.

1. Chcete-li přidat nové přihlašovací údaje pro ověření nebo vybrat jiné existující přihlašovací údaje pro připojení, otevřete nabídku se třemi tečkami (**...**) a vyberte buď možnost **Přidat nové připojení** , nebo je-li k dispozici, jiné přihlašovací údaje pro ověřování.

   ![Snímek obrazovky zobrazující kartu ověřování, existující připojení a vybranou nabídku se třemi tečkami](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Chcete-li aktualizovat další vlastnosti úlohy, vyberte možnost **Další: Konfigurace**.

   Pro úkol v tomto příkladu je jedinou vlastností, kterou je možné upravit, tato e-mailová adresa.

   ![Snímek obrazovky zobrazující kartu Konfigurace](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Upravit základní pracovní postup úlohy

Když změníte základní pracovní postup pro úlohu automatizace, změny ovlivní pouze instanci úlohy, kterou jste vytvořili, a nikoli šablonu, která úlohu vytvořila. Po provedení a uložení změn by název, který jste zadali pro původní úlohu, mohl úkol již přesně popsat, takže bude pravděpodobně nutné znovu vytvořit úlohu s jiným názvem.

> [!TIP]
> Osvědčeným postupem je klonovat původní pracovní postup, abyste mohli místo toho upravit zkopírovanou verzi. Tímto způsobem můžete provádět a testovat změny v kopii, zatímco původní úloha služby Automation dál pracuje a bude běžet bez rizika přerušení nebo přerušení stávajících funkcí. Po dokončení změn a splnění nové verze je možné zakázat nebo odstranit původní úlohu automatizace a použít naklonované verze úlohy Automation. Následující kroky obsahují informace o tom, jak naklonovat pracovní postup.

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek, který má úkol, který chcete aktualizovat.

1. V nabídce prostředku v části **Automatizace** vyberte **úlohy**.

1. V seznamu úlohy vyhledejte úkol, který chcete aktualizovat. Otevřete nabídku se třemi tečkami (**...**) úkolu a vyberte **otevřít v Logic Apps**.

   ![Snímek obrazovky, který zobrazuje nabídku otevřené elipsy a vybranou možnost "otevřít v Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Základní pracovní postup úkolu se otevře ve službě Azure Logic Apps a zobrazí se podokno **přehledu** , kde můžete zobrazit stejnou historii spuštění, která je k dispozici pro daný úkol.

   ![Snímek obrazovky zobrazující úkol v Azure Logic Apps zobrazení s vybraným oknem s přehledem](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Pokud chcete otevřít příslušný pracovní postup v návrháři aplikace logiky, vyberte v nabídce aplikace logiky možnost **Návrhář aplikace logiky**.

   ![Snímek obrazovky, který zobrazuje vybranou možnost nabídky návrhář aplikace logiky a plochu návrháře s podkladovým pracovním postupem](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Nyní můžete upravit vlastnosti triggeru a akcí pracovního postupu a také upravit aktivační událost a akce, které definují vlastní pracovní postup. V souladu s osvědčenými postupy se ale řiďte postupem, jak naklonovat pracovní postup, abyste mohli provádět změny na kopii, zatímco původní pracovní postup bude i nadále fungovat a běžet.

1. Pokud chcete naklonovat pracovní postup a upravit zkopírovanou verzi, použijte následující postup:

   1. V nabídce pracovního postupu aplikace logiky vyberte **Přehled**.

   1. Na panelu nástrojů v podokně Přehled vyberte **klonovat**.

   1. V podokně vytvoření aplikace logiky v části **název** zadejte nový název kopírovaného pracovního postupu aplikace logiky.

      S výjimkou **stavu aplikace logiky** nejsou ostatní vlastnosti k dispozici pro úpravy. 
      
   1. V části **stav aplikace logiky** vyberte **zakázáno** , aby se klonovaný pracovní postup nespouštěl při provádění změn. Pracovní postup můžete povolit, až budete připraveni k otestování svých změn.

   1. Až Azure dokončí zřizování klonovaného pracovního postupu, najděte a otevřete tento pracovní postup v návrháři aplikace logiky.

1. Chcete-li zobrazit vlastnosti triggeru nebo akce, rozbalte tuto aktivační událost nebo akci.

   Trigger opakování můžete například změnit tak, aby běžel týdně, nikoli měsíčně.

   ![Snímek obrazovky, který zobrazuje rozbalenou aktivační událost opakování se seznamem frekvencí otevřený pro zobrazení dostupných možností četnosti](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Další informace o triggeru opakování najdete v tématu [Vytvoření, plánování a spuštění opakujících se úloh a pracovních postupů s triggerem opakování](../connectors/connectors-native-recurrence.md). Další informace o dalších triggerech a akcích, které můžete použít, najdete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md).

1. Změny uložíte kliknutím na tlačítko **Uložit** na panelu nástrojů návrháře.

   ![Snímek obrazovky, který zobrazuje panel nástrojů návrháře a vybrané příkazy Uložit](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Chcete-li otestovat a spustit aktualizovaný pracovní postup, na panelu nástrojů návrháře vyberte možnost **Spustit**.

   Po dokončení běhu Návrhář zobrazí podrobnosti o spuštění pracovního postupu.

   ![Snímek obrazovky zobrazující podrobnosti o spuštění pracovního postupu v Návrháři](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Pokud chcete pracovní postup zakázat, aby úloha nepokračovala v běhu, přečtěte si téma [Správa aplikací logiky v Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Rádi bychom od vás slyšeli! Pokud chcete nahlásit chyby, poskytnout zpětnou vazbu nebo klást otázky k této funkci Preview, [obraťte se na tým Azure Logic Apps](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Další kroky

* [Správa aplikací logiky v Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
