---
title: Scénář rozpočtu pro správu nákladů a fakturace Azure
description: Naučte se používat automatizaci Azure k vypnutí virtuálních počítačů na základě konkrétních rozpočtových prahů.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: de0e9c631a97891e75c091c75a34b7dd94a52894
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131458"
---
# <a name="manage-costs-with-azure-budgets"></a>Správa nákladů s využitím služby Azure Budgets

Řízení nákladů je důležitou součástí maximalizace využití investic do cloudu. Existuje několik scénářů, kdy jsou viditelnost nákladů, vytváření jejich sestav a orchestrace na základě nákladů zcela nezbytné pro průběžné obchodní operace. [Rozhraní API Azure Cost Management](/rest/api/consumption/) poskytují skupinu rozhraní API pro podporu těchto scénářů. Rozhraní API poskytují podrobnosti o využití a umožňují vám tak zobrazit podrobné náklady na úrovni instance.

Jako součást kontroly nákladů se běžně používají rozpočty. V Azure je možné nastavit rozpočtový rozsah. Zobrazení rozpočtu můžete například zúžit podle předplatného, skupin prostředků nebo kolekce prostředků. Kromě toho, že rozhraní API pro rozpočty můžete využít k zasílání e-mailových upozornění na dosažení rozpočtového prahu, můžete také pomocí [skupin akcí služby Azure Monitor](../../azure-monitor/platform/action-groups.md) aktivovat orchestrovanou sadu akcí jako výsledek události rozpočtu.

Obvyklý scénář rozpočtu by mohl nastat, když zákazník, který používá nekritickou úlohu, chce spravovat rozpočet a také získat přehled předvídatelných nákladů při zobrazení měsíční faktury. Tento scénář vyžaduje určitou orchestraci prostředků, které jsou součástí prostředí Azure, na základě nákladů. V tomto scénáři je pro předplatné nastaven měsíční rozpočet 1 000 USD. Jsou také nastaveny prahové hodnoty upozornění pro aktivaci několika orchestrací. Tento scénář začíná s 80% rozpočtovým prahem, který zastaví všechny virtuální počítače ve skupině prostředků **Volitelné**. Při dosažení 100% rozpočtového prahu se zastaví všechny instance virtuálních počítačů.

Při konfiguraci tohoto scénáře provedete následující akce podle postupu uvedeného v jednotlivých částech tohoto kurzu.

Akce, které jsou součástí tohoto kurzu, vám umožní:

- Vytvořit runbook Azure Automation, který pomocí webhooků zastaví virtuální počítače
- Vytvořit aplikaci logiky Azure, která se aktivuje na základě hodnoty rozpočtového prahu a zavolá runbook se správnými parametry
- Vytvořit skupinu akcí Azure Monitor, která se nakonfiguruje tak, aby aktivovala aplikaci logiky Azure při dosažení rozpočtového prahu
- Vytvořit rozpočet Azure s požadovanými prahovými hodnotami a propojit ho se skupinou akcí

## <a name="create-an-azure-automation-runbook"></a>Vytvoření runbooku Azure Automation

[Azure Automation](../../automation/automation-intro.md) je služba, která umožňuje skriptovat většinu úloh správy prostředků a tyto úlohy pak spouštět jako naplánované nebo na vyžádání. V rámci tohoto scénáře vytvoříte [runbook Azure Automation](../../automation/automation-runbook-types.md), který se použije k zastavení virtuálních počítačů. K vytvoření tohoto scénáře použijete grafický runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Zastavit virtuální počítače Azure V2) z [galerie](../../automation/automation-runbook-gallery.md). Po importu tohoto runbooku do účtu Azure a jeho publikování budete moct při dosažení rozpočtového prahu zastavit virtuální počítače.

### <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
2. Vyberte tlačítko **Vytvořit prostředek** v levém horním rohu Azure.
3. Vyberte **Nástroje pro správu** > **Automatizace**.
   > [!NOTE]
   > Pokud ještě nemáte účet Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/).
4. Zadejte informace o účtu. U možnosti **Vytvořit účet Spustit v Azure jako** zvolte **Ano**, aby se automaticky povolila nastavení potřebná pro zjednodušení ověřování do Azure.
5. Jakmile budete hotovi, vyberte **Vytvořit** a spusťte nasazování účtu Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Import runbooku Stop Azure V2 VMs

Pomocí [runbooku Azure Automation](../../automation/automation-runbook-types.md) naimportujte grafický runbook [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
1. Otevřete účet Automation tak, že vyberete **Všechny služby** > **Účty služby Automation**. Potom vyberte svůj účet služby Automation.
1. V části **Automatizace procesů** vyberte **Galerie runbooků**.
1. Nastavte možnost **Zdrojová databáze galerie** na **Centrum skriptů** a vyberte **OK**.
1. Na webu Azure Portal vyhledejte a vyberte položku galerie [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b).
1. Vyberte **Importovat**, aby se zobrazila oblast **Importovat**, a vyberte **OK**. Zobrazí se oblast přehledu runbooku.
1. Jakmile runbook dokončí proces importu, vyberte **Upravit** a zobrazte editor grafického runbooku a možnost publikování.  
    ![Azure – Úprava grafického runbooku](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Výběrem možnosti **Publikovat** runbook publikujte a po zobrazení výzvy vyberte **Ano**. Když runbook publikujete, přepíšete publikovanou verzi verzí v režimu konceptu. V tomto případě ještě publikovanou verzi nemáte, protože jste runbook teprve vytvořili.
    Další informace o publikování runbooku najdete v článku, který se věnuje [vytvoření grafického runbooku](../../automation/learn/automation-tutorial-runbook-graphical.md).

## <a name="create-webhooks-for-the-runbook"></a>Vytváření webhooků pro runbook

Pomocí grafického runbooku [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) vytvoříte dva webhooky pro spuštění runbooku ve službě Azure Automation prostřednictvím jednoho požadavku HTTP. První webhook vyvolá runbook při dosažení 80 % rozpočtového prahu s názvem skupiny prostředků jako parametrem a umožní tak zastavení volitelných virtuálních počítačů. Potom druhý webhook vyvolá runbook bez parametrů (při dosažení 100 %), který zastaví všechny zbývající instance virtuálních počítačů.

1. Na stránce **Runbooky** na webu [Azure Portal](https://portal.azure.com/) vyberte runbook **StopAzureV2Vm**, který zobrazí oblast přehledu runbooku.
1. Výběrem možnosti **Webhook** v horní části stránky otevřete oblast **Přidat webhook**.
1. Výběrem možnosti **Vytvořit nový webhook** otevřete oblast **Vytvořit nový webhook**.
1. Nastavte **Název** webhooku na možnost **Volitelné**. Vlastnost **Povoleno** musí být nastavena na **Ano**. Hodnotu **Konec platnosti** měnit nemusíte. Další informace o vlastnostech webhooků najdete v tématu věnovaném [vlastnostem webhooků](../../automation/automation-webhooks.md#webhook-properties).
1. Vedle hodnoty adresy URL vyberte ikonu kopírování a zkopírujte adresu URL webhooku.
   > [!IMPORTANT]
   > Uložte adresu URL webhooku s názvem **Volitelné** do bezpečného umístění. Tuto adresu URL použijete později v tomto kurzu. Z bezpečnostních důvodů už adresu URL po vytvoření webhooku nebudete moci zobrazit ani načíst.
1. Vyberte **OK** a vytvořte nový webhook.
1. Výběrem možnosti **Nakonfigurovat parametry a nastavení spouštění** zobrazte hodnoty parametrů pro runbook.
   > [!NOTE]
   > Pokud má runbook povinné parametry, nebudete moci webhook vytvořit, dokud nezadáte jejich hodnoty.
1. Vyberte **OK** a přijměte hodnoty parametrů webhooku.
1. Výběrem možnosti **Vytvořit** webhook vytvořte.
1. Dále postupujte podle výše uvedených kroků a vytvořte druhý webhook s názvem **Dokončené**.
    > [!IMPORTANT]
    > Nezapomeňte uložit adresy URL obou webhooků, které budete potřebovat později v tomto kurzu. Z bezpečnostních důvodů už adresu URL po vytvoření webhooku nebudete moci zobrazit ani načíst.

Nyní byste měli mít dva nakonfigurované webhooky, které jsou dostupné prostřednictvím uložených adres URL.

![Webhooky – Volitelné a Dokončené](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

S nastavením služby Azure Automation jste teď hotovi. Webhooky si můžete otestovat pomocí jednoduchého testu Postman, který ověří, že webhooky fungují. Dále musíte vytvořit aplikaci logiky pro orchestraci.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Vytvoření aplikace logiky Azure pro orchestraci

Logic Apps pomáhá vytvářet, plánovat a automatizovat procesy jako pracovní postupy, takže můžete integrovat aplikace, data, systémy a služby napříč podniky a organizacemi. V tomto scénáři [aplikace logiky](../../logic-apps/index.yml), kterou vytvoříte, provede o něco více, než jen zavolá vytvořený webhook automatizace.

Rozpočty je možné nastavit tak, aby při dosažení zadané prahové hodnoty aktivovaly upozornění. Můžete zadat více prahových hodnot, při jejichž dosažení se pošle upozornění, a aplikace logiky vám poskytne možnost provádění různých akcí na základě dosažené prahové hodnoty. V tomto příkladu nastavíte scénář, ve kterém dostanete několik upozornění. První upozornění se pošle při dosažení 80 % rozpočtu a druhé při dosažení 100 % rozpočtu. Potom se použije aplikace logiky, která vypne všechny virtuální počítače ve skupině prostředků. Nejprve se dosáhne první prahové hodnoty **Volitelné** při 80 %, potom se dosáhne druhé prahové hodnoty, kdy se všechny virtuální počítače v předplatném vypnou.

Aplikace logiky vám umožňují poskytovat ukázkové schéma pro aktivační událost HTTP, ale vyžadují, abyste nastavili hlavičku **Content-Type**. Vzhledem k tomu, že skupina akcí nemá vlastní hlavičky pro webhook, musíte datovou část parsovat v samostatném kroku. Použijete akci **Parsovat** s ukázkovou datovou částí.

### <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Aplikace logiky bude provádět několik akcí. V následujícím seznamu najdete skupinu základních akcí, které aplikace logiky provede:

- Rozpozná, když obdrží požadavek HTTP.
- Analyzuje předaná data JSON a určí prahovou hodnotu, která byla dosažena.
- Pomocí podmíněného příkazu zkontrolujte, zda prahová částka dosáhla 80 % nebo více z rozpočtového rozsahu, ale nedosáhla 100 % nebo více.
  - Pokud byla dosažena prahová částka, pošle HTTP POST pomocí webhooku s názvem **Volitelné**. Tato akce vypne virtuální počítače ve skupině Volitelné.
- Pomocí podmíněného příkazu zkontrolujte, zda prahová částka dosáhla nebo překročila 100 % hodnoty rozpočtu.
  - Pokud bylo dosaženo prahové částky, pošle HTTP POST pomocí webhooku s názvem **Dokončené**. Tato akce vypne všechny zbývající virtuální počítače.

K vytvoření aplikace logiky, která provede výše uvedené kroky, je třeba dodržet tento postup:

1. Na webu [Azure Portal](https://portal.azure.com/) vyberte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.  
    ![Azure – Výběr prostředku Aplikace logiky](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. V oblasti **Vytvořit aplikaci logiky** zadejte podrobnosti potřebné k vytvoření aplikace logiky, vyberte **Připnout na řídicí panel** a pak **Vytvořit**.  
    ![Azure – Vytvoření aplikace logiky](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Jakmile Azure nasadí vaši aplikaci logiky, otevře se **Návrhář pro Logic Apps** se zobrazenou oblastí s úvodním videem a běžně používanými triggery.

### <a name="add-a-trigger"></a>Přidání triggeru

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu. Akce jsou všechny kroky, které se stanou po aktivaci triggeru.

1. V části **Šablony** v oblasti **Návrhář pro Logic Apps** zvolte **Prázdná aplikace logiky**.
1. Přidejte [trigger](../../logic-apps/logic-apps-overview.md#logic-app-concepts) zadáním řetězce „požadavek http“ do vyhledávacího pole **Návrháře pro Logic Apps** a vyhledejte a vyberte trigger s názvem **Požadavek – Při přijetí požadavku HTTP**.  
    ![Azure – Aplikace logiky – Trigger HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Vyberte **Nový krok** > **Přidat akci**.  
    ![Azure – Nový krok – Přidání akce](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Do vyhledávacího pole **Návrháře pro Logic Apps** zadejte řetězec „parsovat JSON“ a vyhledejte a vyberte [akci](../../logic-apps/logic-apps-overview.md#logic-app-concepts) **Operace s daty – Parsovat JSON**.  
    ![Azure – Aplikace logiky – Přidání akce parsování JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Jako název **obsahu** pro parsování datové části JSON zadejte řetězec „datová část“, nebo použijte značku Text z nabídky dynamického obsahu.
1. V poli **Parsovat JSON** vyberte možnost **K vygenerování schématu použijte ukázkovou datovou část**.  
    ![Azure – Aplikace logiky – Použití ukázkových dat JSON k vygenerování schématu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Do textového pole vložte následující ukázkovou datovou část JSON: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   Zobrazí se následující textové pole:  
    ![Azure – Aplikace logiky – Ukázková datová část JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Vyberte **Done** (Hotovo).

### <a name="add-the-first-conditional-action"></a>Přidání první podmíněné akce

Pomocí podmíněného příkazu zkontrolujte, zda prahová částka dosáhla 80 % nebo více z rozpočtového rozsahu, ale nedosáhla 100 % nebo více. Pokud byla dosažena prahová částka, pošle HTTP POST pomocí webhooku s názvem **Volitelné**. Tato akce vypne virtuální počítače ve skupině **Volitelné**.

1. Vyberte **Nový krok** > **Přidat podmínku**.  
    ![Azure – Aplikace logiky – Přidání podmínky](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. V poli **Podmínka** vyberte textové pole obsahující text `Choose a value`, aby se zobrazil seznam dostupných hodnot.  
    ![Azure – Aplikace logiky – Pole Podmínka](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. V horní části seznamu vyberte **Výraz** a do editoru výrazů zadejte následující výraz: `float()`.  
    ![Azure – Aplikace logiky – Výraz Float](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Vyberte **Dynamický obsah**, umístěte kurzor do závorek () a ze seznamu vyberte **NotificationThresholdAmount**, aby se naplnil výraz dokončení.
   Výraz bude následující:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Výběrem **OK** výraz nastavte.
1. V rozevíracím seznamu v části **Podmínka** vyberte **je větší než nebo rovno**.
1. Do pole **Zvolit hodnotu** podmínky zadejte `.8`.  
    ![Snímek obrazovky s dialogovým oknem Podmínka a vybranými hodnotami](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. V poli Podmínka vyberte **Přidat** > **Přidat řádek** a přidejte další část podmínky.
1. V poli **Podmínka** vyberte textové pole obsahující text `Choose a value`.
1. V horní části seznamu vyberte **Výraz** a do editoru výrazů zadejte následující výraz: `float()`.
1. Vyberte **Dynamický obsah**, umístěte kurzor do závorek () a ze seznamu vyberte **NotificationThresholdAmount**, aby se naplnil výraz dokončení.
1. Výběrem **OK** výraz nastavte.
1. V rozevíracím seznamu v části **Podmínka** vyberte **je menší než**.
1. Do pole **Zvolit hodnotu** podmínky zadejte `1`.  
    ![Snímek obrazovky s dialogovým oknem Podmínka a dvěma podmínkami](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. V poli **Pokud je true** vyberte **Přidat akci**. Přidáte akci HTTP POST, která vypne volitelné virtuální počítače.  
    ![Azure – Aplikace logiky – Přidání akce](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Zadejte **HTTP** a vyhledejte akci HTTP. Vyberte akci **HTTP – HTTP**.  
    ![Azure – Aplikace logiky – Přidání akce HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Jako hodnotu v poli **Metoda** vyberte **Post**.
1. Jako hodnotu **URI** zadejte adresu URL webhooku s názvem **Volitelné**, který jste vytvořili dříve v tomto kurzu.  
    ![Azure – Aplikace logiky – Identifikátor URI akce HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. V poli **Pokud je true** vyberte **Přidat akci**. Přidáte e-mailovou akci, která pošle příjemci e-mail s upozorněním na vypnutí volitelných virtuálních počítačů.
1. Vyhledejte řetězec „poslat e-mail“ a vyberte akci *poslání e-mailu* na základě služby, kterou používáte.  
    ![Azure – Aplikace logiky – Akce odeslání e-mailu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    U osobních účtů Microsoft, vyberte **Outlook.com**. U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu. Logic Apps vytvoří připojení k e-mailovému účtu.
   Aplikaci logiky budete muset povolit přístup k informacím e-mailu.  
    ![Azure – Aplikace logiky – Oznámení o přístupu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Přidejte text **Komu**, **Předmět** a **Text zprávy** e-mailu, který příjemce upozorní na vypnutí volitelných virtuálních počítačů. Pro naplnění polí předmětu a textu zprávy použijte dynamický obsah **BudgetName** a **NotificationThresholdAmount**. 
    ![Azure – Aplikace logiky – Podrobnosti e-mailu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Přidání druhé podmíněné akce

Pomocí podmíněného příkazu zkontrolujte, zda prahová částka dosáhla nebo překročila 100 % hodnoty rozpočtu. Pokud bylo dosaženo prahové částky, pošle HTTP POST pomocí webhooku s názvem **Dokončené**. Tato akce vypne všechny zbývající virtuální počítače.

1. Vyberte **Nový krok** > **Přidat podmínku**.  
    ![Snímek obrazovky s dialogovým oknem Pokud je true a vybranou možností Přidat akci](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. V poli **Podmínka** vyberte textové pole obsahující text `Choose a value`, aby se zobrazil seznam dostupných hodnot.
1. V horní části seznamu vyberte **Výraz** a do editoru výrazů zadejte následující výraz: `float()`.
1. Vyberte **Dynamický obsah**, umístěte kurzor do závorek () a ze seznamu vyberte **NotificationThresholdAmount**, aby se naplnil výraz dokončení.
   Výraz bude vypadat přibližně takto:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Výběrem **OK** výraz nastavte.
1. V rozevíracím seznamu v části **Podmínka** vyberte **je větší než nebo rovno**.
1. Do pole **Zvolit hodnotu** podmínky zadejte `1`.  
    ![Azure – Aplikace logiky – Nastavení hodnoty podmínky](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. V poli **Pokud je true** vyberte **Přidat akci**. Přidáte akci HTTP POST, která vypne všechny zbývající virtuální počítače.  
    ![Snímek obrazovky s dialogovým oknem Pokud je true, kde můžete přidat akci HTTP POST](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Zadejte **HTTP** a vyhledejte akci HTTP. Vyberte akci **HTTP – HTTP**.
1. Jako hodnotu v poli **Metoda** vyberte **Post**.
1. Jako hodnotu **URI** zadejte adresu URL webhooku s názvem **Dokončené**, který jste vytvořili dříve v tomto kurzu.  
    ![Snímek obrazovky s dialogovým oknem HTTP, kde můžete zadat hodnotu adresy URL](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. V poli **Pokud je true** vyberte **Přidat akci**. Přidáte e-mailovou akci, která pošle příjemci e-mail s upozorněním na vypnutí zbývajících virtuálních počítačů.
1. Vyhledejte řetězec „poslat e-mail“ a vyberte akci *poslání e-mailu* na základě služby, kterou používáte.
1. Přidejte text **Komu**, **Předmět** a **Text zprávy** e-mailu, který příjemce upozorní na vypnutí volitelných virtuálních počítačů. Pro naplnění polí předmětu a textu zprávy použijte dynamický obsah **BudgetName** a **NotificationThresholdAmount**.  
    ![Azure – Aplikace logiky – Podrobnosti o odeslání e-mailu](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. V horní části oblasti **Návrhář aplikace logiky** vyberte **Uložit**.

### <a name="logic-app-summary"></a>Souhrn aplikace logiky

Až budete hotovi, budete vaše aplikace logiky vypadat nějak takto. Ve většině základních scénářů, kdy nepotřebujete žádnou orchestraci na základě prahové hodnoty, můžete přímo zavolat automatizační skript ze služby **Monitor** a přeskočit krok **Aplikace logiky**.

![Azure – Aplikace logiky – Zobrazení Dokončené](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Při uložení aplikace logiky se vygenerovala adresa URL, kterou budete moct volat. Tuto adresu URL použijete v další části tohoto kurzu.

## <a name="create-an-azure-monitor-action-group"></a>Vytvoření skupiny akcí služby Azure Monitor

Skupina akcí je kolekce předvoleb upozornění, která definujete. Při aktivaci výstrahy určitá skupina akcí obdrží upozornění. Výstraha Azure proaktivně vyvolá upozornění na základě konkrétních podmínek a poskytne příležitost k provedení akce. Výstraha může používat data z více zdrojů včetně metrik a protokolů.

Skupiny akcí jsou jediným koncovým bodem, který integrujete s rozpočtem. Upozornění můžete nastavit v mnoha kanálech, ale pro účely tohoto scénáře se zaměříte na aplikaci logiky, kterou jste vytvořili dříve v tomto kurzu.

### <a name="create-an-action-group-in-azure-monitor"></a>Vytvoření skupiny akcí ve službě Azure Monitor

Po vytvoření skupiny akcí budete odkazovat na aplikaci logiky, kterou jste vytvořili dříve v tomto kurzu.

1. Pokud ještě nejste přihlášeni k webu [Azure Portal](https://portal.azure.com/), přihlaste se a vyberte **Všechny služby** > **Monitor**.
1. Vyberte **Upozornění** a pak vyberte **Spravovat akce**.
1. V oblasti **Skupiny akcí** vyberte **Přidat skupinu akcí**.
1. Přidejte a ověřte následující položky:
    - Název skupiny akcí
    - Krátký název
    - Předplatné
    - Skupina prostředků  
    ![Azure – Aplikace logiky – Přidání skupiny akcí](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. V okně **Přidat skupinu akcí** přidejte akci LogicApp. Pojmenujte akci **Budget-BudgetLA**. V okně **Aplikace logiky** vyberte **Předplatné** a **Skupina prostředků**. Potom vyberte **aplikaci logiky**, kterou jste vytvořili dříve v tomto kurzu.
1. Vyberte **OK** a nastavte aplikaci logiky. Potom vyberte **OK** v okně **Přidat skupinu akcí** a vytvořte skupinu akcí.

Máte hotové všechny podpůrné komponenty potřebné k efektivní orchestraci rozpočtu. Nyní vše, co musíte udělat, je vytvořit rozpočet a nakonfigurovat ho na používání skupiny akcí, kterou jste vytvořili.

## <a name="create-the-azure-budget"></a>Vytvoření rozpočtu Azure

Rozpočet můžete vytvořit na webu Azure Portal pomocí [funkce Rozpočet](../costs/tutorial-acm-create-budgets.md) ve službě Cost Management. Rozpočet můžete vytvořit také pomocí rozhraní REST API, rutin PowerShellu nebo rozhraní příkazového řádku. Následující postup používá rozhraní REST API. Než zavoláte rozhraní REST API, budete potřebovat autorizační token. Pokud chcete vytvořit autorizační token, můžete použít projekt [ARMClient](https://github.com/projectkudu/ARMClient). **ARMClient** vám umožňuje ověřit se v Azure Resource Manageru a získat token pro volání rozhraní API.

### <a name="create-an-authentication-token"></a>Vytvoření ověřovacího tokenu

1. Přejděte na projekt [ARMClient](https://github.com/projectkudu/ARMClient) na GitHubu.
1. Naklonujte úložiště, abyste získali místní kopii.
1. Otevřete projekt v sadě Visual Studio a zkompilujte ho.
1. Po úspěšné kompilaci by se měl spustitelný soubor nacházet ve složce *\bin\debug*.
1. Spusťte projekt ARMClient. Otevřete příkazový řádek a z kořenové složky projektu přejděte na složku *\bin\debug*.
1. Přihlaste se a ověřte zadáním následujícího příkazu na příkazovém řádku:<br>
    `ARMClient login prod`
1. Z výstupu zkopírujte **identifikátor GUID předplatného**.
1. Pokud chcete autorizační token zkopírovat do schránky, zadejte následující příkaz na příkazovém řádku, ale nezapomeňte použít zkopírované ID předplatného z kroku výše: <br>
    `ARMClient token <subscription GUID from previous step>`

    Po dokončení výše uvedeného kroku se zobrazí následující:<br>
    **Token byl úspěšně zkopírován do schránky.**
1. Uložte token, abyste ho mohli použít v postupu v další části tohoto kurzu.

### <a name="create-the-budget"></a>Vytvořit rozpočet

Dále nakonfigurujete prostředí **Postman** a vytvoříte rozpočet zavoláním rozhraní REST API služby Azure Consumption. Postman je vývojové prostředí rozhraní API. Do prostředí Postman naimportujete soubory prostředí a kolekce. Kolekce obsahuje seskupené definice požadavků HTTP, které volají rozhraní REST API Azure Consumption. Soubor prostředí obsahuje proměnné, které kolekce používá.

1. Stáhněte a otevřete [klienta REST Postman](https://www.getpostman.com/), abyste mohli spouštět rozhraní REST API.
1. V prostředí Postman vytvořte nový požadavek.  
    ![Postman – Vytvoření nového požadavku](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Nový požadavek uložte jako kolekci, aby nový požadavek nic neobsahoval.  
    ![Postman – Uložení nového požadavku](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Změňte požadavek z akce `Get` na akci `Put`.
1. Upravte následující adresu URL tak, že `{subscriptionId}` nahradíte **ID předplatného**, které jste použili v předchozí části tohoto kurzu. Upravte také adresu URL tak, aby obsahovala SampleBudget jako hodnotu pro `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`.
1. V prostředí Postman vyberte kartu **Headers** (Hlavičky).
1. Přidejte nový **klíč** s názvem Authorization (Autorizace).
1. Nastavte **hodnotu** na token, který jste vytvořili pomocí projektu ArmClient na konci poslední části.
1. V prostředí Postman vyberte kartu **Body** (Text zprávy).
1. Vyberte možnost **nezpracovaného** tlačítka.
1. Do textového pole vložte níže uvedenou definici ukázkového rozpočtu, ale v adrese URL i v textu požadavku musíte za parametry `subscriptionID`, `resourcegroupname` a `actiongroupname` nahradit ID předplatného, jedinečný název skupiny prostředků a název skupiny akcí, kterou jste vytvořili:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Kliknutím na **Send** (Odeslat) požadavek odešlete.

Nyní máte všechny součásti, které potřebujete pro volání [rozhraní API pro rozpočty](/rest/api/consumption/budgets). Referenční informace k rozhraní API pro rozpočty obsahují další podrobnosti o konkrétních požadavcích, mezi které patří:

- **budgetName** – podporuje se více rozpočtů.  Názvy rozpočtů musí být jedinečné.
- **category** – musí se jednat o **náklady** nebo **využití**. Rozhraní API podporuje rozpočty nákladů a využití.
- **timeGrain** – měsíční, čtvrtletní nebo roční rozpočet. Částka se na konci období resetuje.
- **filters** – filtry umožňují zúžit rozpočet na konkrétní skupiny prostředků v rámci vybraného rozsahu. Filtrem může být například kolekce skupin prostředků pro rozpočet na úrovni předplatného.
- **notifications** – určuje podrobnosti a prahové hodnoty pro upozornění. Můžete nastavit více prahových hodnot a zadat e-mailovou adresu nebo skupinu akcí pro příjem upozornění.

## <a name="summary"></a>Souhrn

V tomto kurzu jste se naučili:

- Jak vytvořit runbook Azure Automation pro zastavení virtuálních počítačů
- Jak vytvořit aplikaci logiky Azure, která se aktivuje na základě hodnot rozpočtového prahu a zavolá související runbook se správnými parametry
- Jak vytvořit skupinu akcí služby Azure Monitor, která se nakonfigurovala tak, aby aktivovala aplikaci logiky Azure při dosažení rozpočtového prahu
- Jak vytvořit rozpočet Azure s požadovanými prahovými hodnotami a propojit ho se skupinou akcí

Nyní máte plně funkční rozpočet pro předplatné, který při dosažení nakonfigurovaných rozpočtových prahů vypne virtuální počítače.

## <a name="next-steps"></a>Další kroky

- Další informace o scénářích fakturací Azure najdete v článku [Scénáře automatizace pro fakturaci a správu nákladů](cost-management-automation-scenarios.md).