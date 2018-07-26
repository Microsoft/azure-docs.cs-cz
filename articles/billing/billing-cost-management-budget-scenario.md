---
title: Scénář rozpočtu správy nákladů a fakturací Azure | Dokumentace Microsoftu
description: Další informace o použití Azure automatation vypnutí virtuálních počítačů založených na specifické prahové hodnoty.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 7/25/2018
ms.author: erikre
ms.openlocfilehash: 19d8fbf82410136fd6a50a2088dd9b774dd6cb27
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259207"
---
# <a name="manage-costs-with-azure-budgets"></a>Správa nákladů s rozpočty Azure

Řízení nákladů je zásadní součástí pro maximalizaci hodnoty vašich investic v cloudu. Existuje několik scénářů, kde je nejdůležitější pro pokračování obchodní operace viditelnost náklady, vytváření sestav a náklady na základě Orchestrace. [Azure API Management náklady](https://docs.microsoft.com/rest/api/consumption/) obsahují sadu rozhraní API podporovat každé z těchto scénářů. Rozhraní API poskytují podrobnosti o použití, umožňuje zobrazit náklady na detailní instance.

Rozpočty se běžně používají jako součást náklady pod kontrolou. Obor rozpočty lze nastavit v Azure. Například můžete zúžit zobrazení rozpočtu na základě předplatného, skupiny prostředků nebo kolekci prostředků. Kromě použití rozpočty rozhraní API pro upozornění prostřednictvím e-mailu je dosaženo prahové hodnoty rozpočtu, můžete použít [skupiny akcí Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) k aktivaci iniciovat organizovaně, což sadu akcí v důsledku události rozpočtu.

Běžný scénář rozpočty pro zákazníka s méně náročné úlohy může dojít, když chtějí spravovat proti rozpočtu a také dostat k předvídatelné náklady při pohledu na měsíční faktuře. Tento scénář vyžaduje některé Orchestrace náklady podle prostředků, které jsou součástí prostředí Azure. V tomto scénáři je nastavit měsíční rozpočtu 1 000 USD pro předplatné. Prahové hodnoty upozornění jsou také nastavena aktivace několik Orchestrace. Tento scénář začíná prahovou hodnotu 80 % nákladů, které se zastaví všechny virtuální počítače ve skupině prostředků **volitelné**. Pak na náklady na 100 %, prahová hodnota, se zastaví všechny instance virtuálních počítačů.
Konfigurace tohoto scénáře, dokončí následující akce podle postupu uvedeného v každé části tohoto kurzu. 

Tyto akce zahrnuté v tomto kurzu vám umožní:

- Vytvoření Runbooku Azure Automation k zastavování virtuálních počítačů pomocí webhooků.
- Vytvořte aplikaci logiky Azure aktivovat podle prahová hodnota rozpočtu a volalo runbook pomocí správné parametry.
- Vytvoření skupiny akcí monitorování Azure, který bude nakonfigurován pro aktivaci aplikace logiky Azure, pokud je dodržena prahová hodnota rozpočtu.
- Vytvoření Azure rozpočtu pomocí požadované prahové hodnoty a propojit je do skupiny akcí.

## <a name="create-an-azure-automation-runbook"></a>Vytvoření Runbooku Azure Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) je služba, která umožňuje skriptu většinu úkolů správy prostředků a spusťte tyto úlohy jako buď plánované, nebo na vyžádání. V rámci tohoto scénáře, se vytvoří [runbooku Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types) , který se použije k zastavování virtuálních počítačů. Budete používat [zastavení virtuálních počítačů V2 Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafický runbook z [Galerie](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) k vytvoření tohoto scénáře. Importováním této sady runbook ke svému účtu Azure a jeho publikování, bude moct zastavit virtuální počítače, když je dosaženo prahové hodnoty rozpočtu. 

### <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
2.  Klikněte na tlačítko **vytvořit prostředek** nalezeno tlačítko v levém horním rohu Azure.
3.  Vyberte **nástroje pro správu** > **automatizace**.
    > [!NOTE]
    > Pokud nemáte účet Azure, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/).
4.  Zadejte informace o vašem účtu. Pro **vytvoření účtu Azure spustit jako**, zvolte **Ano** se automaticky povolit nastavení potřebné k zjednoduší ověřování do Azure.
5.  Jakmile budete hotovi, klikněte na **Vytvořit** a spusťte nasazování účtu Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Import runbooku zastavení virtuálních počítačů V2 Azure

Pomocí [runbooku Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types), importovat [zastavení virtuálních počítačů V2 Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafický runbook z galerie.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
2.  Otevřete svůj účet Automation tak, že vyberete **všechny služby** > **účty Automation**. Vyberte svůj účet Automation.
3.  Klikněte na tlačítko **Galerie Runbooků** z **automatizace procesů** oddílu.
4.  Nastavte **zdrojová databáze Galerie** k **centra skriptů** a vyberte **OK**.
5.  Vyhledejte a vyberte [zastavení virtuálních počítačů V2 Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) položky galerie na webu Azure portal.
6.  Klikněte na tlačítko **Import** tlačítka pro zobrazení **Import** okna a vyberte **OK**. Zobrazí se okno přehledu sady runbook.
7.  Po dokončení procesu importu runbooku vyberte **upravit** zobrazíte možnosti editoru a publikování grafický runbook.
    
    ![Azure – upravit grafický runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Klikněte na tlačítko **publikovat** tlačítko Publikovat sadu runbook a potom vyberte **Ano** po zobrazení výzvy. Když runbook publikujete, přepíšete všechny existující publikované verze s koncept. V tomto případě žádný publikovanou verzi máte, protože jste vytvořili sadu runbook. 

    Další informace o publikování sady runbook najdete v tématu [vytvoření grafického runbooku](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Vytvořit webhook pro runbook

Použití [zastavení virtuálních počítačů V2 Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafický runbook, musíte vytvořit dva Webhooky pro spuštění sady runbook ve službě Azure Automation prostřednictvím jednoho požadavku HTTP. První webhooku se vyvolání sady runbook v prahovou hodnotu 80 % rozpočtu s názvem skupiny prostředků jako parametr, povolení volitelné virtuálních počítačů má být zastaven. Pak se druhý webhooku vyvolání sady runbook bez parametrů (na 100 %), které se zastaví všechny zbývající instance virtuálních počítačů.

1.  Z **sady Runbook** stránku [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **StopAzureV2Vm** sady runbook, která zobrazí okno přehledu sady runbook. 
2.  Klikněte na tlačítko **Webhooku** v horní části stránky otevřete **přidat Webhook** okno.
3.  Klikněte na tlačítko **vytvořit nový webhook** otevřít **vytvořit nový webhook** okno.
4.  Nastavte **název** webhooku pro **volitelné**. **Povoleno** musí být vlastnost **Ano**. **Expires** není potřeba změnit hodnotu. Další informace o vlastnostech Webhooku, najdete v části [podrobnosti webhooku](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5.  Vedle hodnoty adresy URL kliknutím na ikonu kopírování zkopírujte adresu URL webhooku. 
    > [!IMPORTANT]
    > Uložit adresu URL webhooku s názvem **volitelné** na bezpečném místě. Adresa URL použijete později v tomto kurzu. Z bezpečnostních důvodů se po vytvoření webhooku, nelze zobrazit ani znovu načíst adresu URL.
6.  Klikněte na tlačítko **OK** vytvořit nový webhook.
7.  Klikněte na tlačítko **nakonfigurovat parametry a nastavení spouštění** zobrazíte parametr hodnoty pro sadu runbook. 
    > [!NOTE]
    > Pokud má runbook povinné parametry, pak nemůžete vytvořit webhook, pokud jsou k dispozici hodnoty.
8.  Klikněte na tlačítko **OK** tak, aby přijímal hodnoty parametrů webhooku.
9.  Klikněte na tlačítko **vytvořit** k vytvoření webhooku.
10. Potom postupujte podle pokynů výše vytvořte druhý webhooku s názvem **Complete**. 
    > [!IMPORTANT]
    > Nezapomeňte uložit obě adresy URL webhooku pro pozdější použití v tomto kurzu. Z bezpečnostních důvodů se po vytvoření webhooku, nelze zobrazit ani znovu načíst adresu URL.

Teď byste měli mít dvě nakonfigurované webhooky, které jsou každý k dispozici prostřednictvím adresy URL, které jste si uložili. 

![Webhooky - nepovinné a dokončení](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Teď budete hotovi s nastavením služby Azure Automation. Webhooky můžete otestovat s jednoduchý test Postman k ověření, že funguje webhooku. Dále musíte vytvořit aplikaci logiky pro orchestraci.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Vytvořit aplikaci logiky Azure pro Orchestrace

Logic Apps umožňují vytvářet, plánovat a automatizovat procesy jako pracovní postupy, takže můžete integrovat aplikace, data, systémy a služby napříč podniky a organizacemi. V tomto scénáři [aplikace logiky](https://docs.microsoft.com/azure/logic-apps/) vytvoříte se provést trochu více než jen volání webhook služby automation, který jste vytvořili. 

Rozpočty lze nastavit k aktivaci oznámení, když je splněna zadanou prahovou hodnotu. Můžete zadat více prahové hodnoty, která vás upozorní na a aplikace logiky ukazuje možnosti můžete provádět různé akce podle prahovou hodnotu splněny. V tomto příkladu nastavíte si scénář, kde můžete získat několika oznámení, je první oznámení pro při dosáhla 80 % rozpočtu a druhé oznámení je po 100 % rozpočtu bylo dosaženo. Aplikace logiky se použije k vypnutí všech virtuálních počítačů ve skupině prostředků. Nejprve je potřeba **volitelné** 80 % bude dosaženo prahové hodnoty a pak bude dosaženo druhý prahové hodnoty ve kterém se ukončí všechny virtuální počítače v rámci předplatného.

Logic apps umožňují vám poskytují vzorku schématu pro HTTP trigger, ale vyžadují, abyste nastavili **Content-Type** záhlaví. Vzhledem k tomu, že skupina akcí nemá vlastní hlavičky pro webhook, musí parsování datové části v samostatném kroku. Budete používat **analyzovat** akce a poskytnout ukázkovou datovou část.

### <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Aplikace logiky provede několik akcí. Následující seznam obsahuje základní sadu akcí, které aplikace logiky provede:
- Rozpozná při přijetí požadavku HTTP
- Analyzovat data JSON k určení prahovou hodnotu, která se dosáhlo předaný
- Použití podmíněného příkazu ke kontrole, jestli je úroveň mezní dosáhla 80 % nebo více rozsahu rozpočet, ale ne větší než nebo rovna 100 %.
    - Pokud se dosáhlo tato částka prahové hodnoty, odeslání požadavku HTTP POST, pomocí webhooku s názvem **volitelné**. Tato akce vypne virtuální počítače ve skupině "Volitelné".
- Pomocí podmíněného příkazu ke kontrole, jestli má dosáhli nebo Přesáhli jste 100 % rozpočtu hodnoty Částka prahové hodnoty.
    - Pokud bylo dosaženo mezní, odeslání požadavku HTTP POST, pomocí webhooku s názvem **Complete**. Tato akce vypne všechny zbývající virtuální počítače.

Následující kroky jsou potřeba k vytvoření aplikace logiky, která bude provádět výše uvedené kroky:

1.  V [webu Azure portal](https://portal.azure.com/)vyberte **vytvořit prostředek** > **integrace** > **aplikace logiky**.
    
    ![Azure – vyberte prostředek aplikace logiky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  V **vytvořit aplikaci logiky** okno, zadejte podrobnosti potřeba vytvořit aplikaci logiky, vyberte **připnout na řídicí panel**a klikněte na tlačítko **vytvořit**. 
    
    ![Azure – vytvoření aplikace logiky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Jakmile Azure nasadí vaši aplikaci logiky **návrhář pro Logic Apps** otevře a zobrazí okno s úvodním videa a běžně používané aktivační události. 

### <a name="add-a-trigger"></a>Přidat trigger

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu. Akce jsou všechny kroky, ke kterým dochází po aktivaci triggeru. 

1.  V části **šablony** z **návrhář pro Logic Apps** okně zvolte **prázdná aplikace logiky**.
2.  Přidat [aktivační událost](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) tak, že zadáte "požadavek http" **návrhář pro Logic Apps** vyhledávacího pole najděte a zvolte aktivační událost s názvem **žádosti – přijetí požadavku HTTP při**.
    
    ![Azure – aplikace logiky – triggeru Http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png) 
3.  Vyberte **nový krok** > **přidat akci**. 
    
    ![Azure – nový krok – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png) 
4.  Vyhledejte "parsování formátu JSON" v **návrhář pro Logic Apps** vyhledávacího pole najděte a vyberte **operace s daty – Parsovat JSON** [akce](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts). 
    
    ![Přidat Azure – aplikace logiky – parsovat JSON akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png) 
5.  Zadejte "Datovou část" jako **obsahu** název pro datovou část Parsovat JSON.
6.  Vyberte **k vygenerování schématu použít ukázkovou datovou část** možnost **Parsovat JSON** pole.
    
    ![Azure – aplikace logiky – použití ukázkových dat JSON k vygenerování schématu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png) 
7.  Vložte do textového pole následující ukázkovou datovou část JSON: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
    
    Textové pole se zobrazí takto:
        
    ![Azure – aplikace logiky – ukázkovou datovou část JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png) 
8.  Klikněte na **Done** (Hotovo).

### <a name="add-the-first-conditional-action"></a>Přidat první podmíněné akce

Použití podmíněného příkazu ke kontrole, jestli je úroveň mezní dosáhla 80 % nebo více rozsahu rozpočet, ale ne větší než nebo rovna 100 %. Pokud se dosáhlo tato částka prahové hodnoty, odeslání požadavku HTTP POST, pomocí webhooku s názvem **volitelné**. Tato akce vypne virtuální počítače v **volitelné** skupiny.

1.  Vyberte **nový krok** > **přidat podmínku**.
    
    ![Azure – aplikace logiky – přidání podmínky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png) 
2.  V **podmínku** klikněte na textové pole obsahující **zvolit hodnotu** zobrazíte seznam dostupných hodnot.
    
    ![Pole Azure – aplikace logiky – podmínky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png) 
        
3.  Klikněte na tlačítko **výraz** v horní části seznamu a v editoru výrazů zadejte následující výraz: `float()`
    
    ![Výraz typu Float Azure – aplikace logiky –](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png) 
        
4.  Vyberte **dynamický obsah**, umístěte kurzor mezi závorky () a vyberte **NotificationThresholdAmount** ze seznamu a naplnit celý výraz. 
    
    Výraz bude následující:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
    
5.  Vyberte **OK** nastavit výraz. 
6.  Vyberte **je větší než nebo rovna hodnotě** v rozevíracím seznamu z **podmínku**.
7.  V **zvolit hodnotu** zadejte podmínky `.8`.
    
    ![Výraz typu Float Azure – aplikace logiky – s hodnotou](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png) 
        
8.  Klikněte na tlačítko **přidat** > **přidat řádek** v rámci pole podmínky přidat další část podmínku.
9.  V **podmínku** klikněte na textové pole obsahující **zvolit hodnotu**.
10. Klikněte na tlačítko **výraz** v horní části seznamu a v editoru výrazů zadejte následující výraz: `float()`
11. Vyberte **dynamický obsah**, umístěte kurzor mezi závorky () a vyberte **NotificationThresholdAmount** ze seznamu a naplnit celý výraz. 
12. Vyberte **OK** nastavit výraz. 
13. Vyberte **je menší než** v rozevíracím seznamu z **podmínku**.
14. V **zvolit hodnotu** zadejte podmínky `1`.
    
    ![Výraz typu Float Azure – aplikace logiky – s hodnotou](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png) 
        
15. V **při hodnotě true** vyberte **přidat akci**. Přidejte akci HTTP POST, který vypne volitelné virtuálních počítačů.
    
    ![Azure – aplikace logiky – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png) 
        
16. Zadejte **HTTP** vyhledávat akce HTTP a vyberte **HTTP – HTTP** akce.
    
    ![Azure – aplikace logiky – akce HTTP přidat](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png) 
        
17. Vyberte **příspěvek** jako pro **metoda** hodnotu.
18. Zadejte adresu URL webhooku s názvem **volitelné** , kterou jste vytvořili dříve v tomto kurzu jako **Uri** hodnotu.
    
    ![Identifikátor URI pro Azure – aplikace logiky – akce HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png) 
        
19. Vyberte **přidat akci** v **při hodnotě true** pole. Přidejte akci e-mailu, který odešle e-mail s upozorněním příjemci, kterého vypnout volitelné virtuálních počítačů.
20. Vyhledejte "Odeslat e-mail" a vyberte *odeslání e-mailu* akce podle e-mailové služby, které používáte.
        
    ![Azure – aplikace logiky – akce Odeslat e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png) 
    
    U osobních účtů Microsoft, vyberte **Outlook.com**. U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu. Logic Apps vytvoří připojení k e-mailovému účtu.
        
    Je potřeba povolit přístup k informacím vašeho e-mailové aplikace logiky.
    
    ![Všimněte si, že Azure – aplikace logiky – přístup](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png) 
        
21. Přidat **k**, **předmět**, a **tělo** text e-mailu, který upozorní příjemci, kterého vypnout volitelné virtuálních počítačů. Použití **BudgetName** a **NotificationThresholdAmount** dynamický obsah k vyplnění polí předmětu a textu.
    
    ![Podrobnosti o e-mailu Azure – aplikace logiky –](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png) 
 
### <a name="add-the-second-conditional-action"></a>Přidejte druhý podmíněné akce

Pomocí podmíněného příkazu ke kontrole, jestli má dosáhli nebo Přesáhli jste 100 % rozpočtu hodnoty Částka prahové hodnoty. Pokud bylo dosaženo mezní, odeslání požadavku HTTP POST, pomocí webhooku s názvem **Complete**. Tato akce vypne všechny zbývající virtuální počítače.

1.  Vyberte **nový krok** > **přidat podmínku**.
    
    ![Azure – aplikace logiky – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png) 
        
2.  V **podmínku** klikněte na textové pole obsahující **zvolit hodnotu** zobrazíte seznam dostupných hodnot.
3.  Klikněte na tlačítko **výraz** v horní části seznamu a v editoru výrazů zadejte následující výraz: `float()`
4.  Vyberte **dynamický obsah**, umístěte kurzor mezi závorky () a vyberte **NotificationThresholdAmount** ze seznamu a naplnit celý výraz. 
    
    Výraz bude následující:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
        
5.  Vyberte **OK** nastavit výraz. 
6.  Vyberte **je větší než nebo rovna hodnotě** v rozevíracím seznamu z **podmínku**.
7.  V **zvolte hodnoty pole** podmínky zadejte `1`.
    
    ![Hodnota podmínky Azure – aplikace logiky – nastavení](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png) 
        
8.  V **při hodnotě true** vyberte **přidat akci**. Přidejte akci HTTP POST, který vypne všechny zbývající virtuální počítače.
    
    ![Azure – aplikace logiky – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png) 
    
9.  Zadejte **HTTP** vyhledávat akce HTTP a vyberte **HTTP – HTTP** akce.
10. Vyberte **příspěvek** jako pro **metoda** hodnotu.
11. Zadejte adresu URL webhooku s názvem **Complete** , kterou jste vytvořili dříve v tomto kurzu jako **Uri** hodnotu.
    
    ![Azure – aplikace logiky – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png) 
        
12. Vyberte **přidat akci** v **při hodnotě true** pole. Přidejte akci e-mailu, který odešle e-mail s upozorněním příjemci, kterého vyply zbývající virtuální počítače.
13. Vyhledejte "Odeslat e-mail" a vyberte *odeslání e-mailu* akce podle e-mailové služby, které používáte.
14. Přidat **k**, **předmět**, a **tělo** text e-mailu, který upozorní příjemci, kterého vypnout volitelné virtuálních počítačů. Použití **BudgetName** a **NotificationThresholdAmount** dynamický obsah k vyplnění polí předmětu a textu.
    
    ![Podrobnosti o Azure – aplikace logiky – odeslání e-mailu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png) 
        
15. Klikněte na tlačítko **Uložit** v horní části **návrhář aplikace logiky** okno.

### <a name="logic-app-summary"></a>Přehled aplikace logiky

Zde je, co vaše aplikace logiky bude vypadat jako až budete hotovi. V nejzákladnější scénářů, kdy není nutné žádné založené na prahových hodnotách Orchestrace, lze přímo volat automatizační skript z **monitorování** a Přejít **aplikace logiky** kroku.

   ![Azure – aplikace logiky – úplné zobrazení](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png) 
 
Při uložení aplikace logiky vytvořená adresa URL, že budou moct volat. V další části tohoto kurzu budete používat tuto adresu URL. 

## <a name="create-an-azure-monitor-action-group"></a>Vytvoření skupiny akcí Azure Monitor

Skupiny akcí je kolekce předvolby oznámení, které definujete. Když se aktivuje upozornění, můžou určité akce skupiny získat upozornění, když dostávat oznámení. Azure upozornění proaktivně vyvolá oznámení na základě určitých podmínek a představuje příležitost k akci. Výstrahu můžete použít data z víc zdrojů, včetně metrik a protokolů.

Skupiny akcí jsou pouze koncový bod, které se integrují s rozpočtem. Můžete nastavit upozornění v počet kanálů, ale pro tento scénář, který se zaměřuje na aplikaci logiky, který jste vytvořili dříve v tomto kurzu. 

### <a name="create-an-action-group-in-azure-monitor"></a>Vytvoření skupiny akcí ve službě Azure Monitor

Když vytvoříte skupinu akcí, bude odkazovat na aplikaci logiky, kterou jste vytvořili dříve v tomto kurzu. 

1.  Pokud nejste ještě přihlášeni k [webu Azure portal](https://portal.azure.com/), přihlášení a výběr **všechny služby** > **monitorování**. 
2.  Vyberte **skupiny akcí** z **nastavení** oddílu.
3.  Vyberte **přidat skupinu akcí** z **skupiny akcí** okno.
4.  Přidání a ověření následujících položek:
    - Název skupiny akcí
    - Krátký název
    - Předplatné
    - Skupina prostředků
    
    ![Azure – aplikace logiky – přidání skupiny akcí](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png) 
        
5.  V rámci **přidat skupinu akcí** podokně Přidat aplikaci LogicApp akce. Název akce **rozpočtu BudgetLA**. V **aplikace logiky** podokně, vyberte **předplatné** a **skupiny prostředků**. Vyberte **aplikace logiky** , kterou jste vytvořili dříve v tomto kurzu.
6.  Klikněte na tlačítko **OK** nastavit aplikaci logiky. Vyberte **OK** v **přidat skupinu akcí** podokno a vytvořit skupinu akcí.
    
Pak budete hotovi s pomocné součásti nezbytná k orchestraci efektivně vašemu rozpočtu. Nyní vše, co je třeba provést je vytvořit rozpočtu a nakonfigurujte ho na použití skupiny akcí, které jste vytvořili.

## <a name="create-the-azure-budget"></a>Vytvořit Azure rozpočet 

Rozpočty nyní nemají portálového prostředí v Azure. Ale budete moct volat buď REST API, rutin prostředí Powershell, nebo pomocí rozhraní příkazového řádku. Následující postup používá rozhraní REST API. Volání rozhraní REST API, musíte nejdřív autorizační token. Chcete-li vytvořit autorizační token, můžete použít [ARMClient](https://github.com/projectkudu/ARMClient) projektu. **ARMClient** umožňuje sami ověření na Azure Resource Manageru a získání tokenu pro volání rozhraní API. 

### <a name="create-an-authentication-token"></a>Vytvoření ověřovacího tokenu

1.  Přejděte [ARMClient](https://github.com/projectkudu/ARMClient) projektu na Githubu.
2.  Naklonujte úložiště do místní kopie.
3.  Otevřete projekt v sadě Visual Studio a sestavte ho.
4.  Po úspěšném sestavení spustitelný soubor by měl mít v *\bin\debug* složky.
5.  Spusťte ARMClient. Otevřete příkazový řádek a přejděte *\bin\debug* složku z kořenového adresáře projektu.
6.  Přihlásit se a ověření, zadejte následující příkaz na příkazovém řádku:<br>
    `ARMClient login prod`
7.  Kopírovat **guid předplatného** z výstupu.
8.  Pokud chcete zkopírovat autorizační token do schránky, zadejte následující příkaz příkazového řádku, ale je třeba použít ID předplatného zkopírovaný v předchozím kroku: <br>
    `ARMClient token <subscription GUID from previous step>`
    
    Po dokončení kroku požadavků, zobrazí se následující:<br>
    **Token zkopírováno do schránky úspěšně.**
9.  Uložte token, který má použít k provedení kroků v další části tohoto kurzu.

### <a name="create-the-budget"></a>Vytvoření rozpočtu

Dále je nutné nakonfigurovat **Postman** vytvořit rozpočtu voláním rozhraní Azure Consumption REST API. Postman je rozhraní API vývojové prostředí. Soubory prostředí a kolekce budou naimportovány do Postman. Kolekce obsahuje seskupené definice požadavků protokolu HTTP, které volají rozhraní Azure Consumption REST API. Prostředí obsahuje proměnné, které jsou používány v kolekci.

1.  Stáhnout a otevřít [klienta Postman REST](https://www.getpostman.com/) ke spuštění rozhraní REST API.
2.  V nástroji Postman vytvořte novou žádost.
    
    ![Postman – vytvořte novou žádost](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png) 
        
3.  Uložte novou žádost o jako kolekci, tak, aby se nová žádost nemá nic na něj.
    
    ![Postman – uložení nové žádosti](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png) 
        
4.  Změnit žádost od `Get` k `Put` akce.
5.  Upravte následující adresu URL tak, že nahradíte `{subscriptionId}` s **ID předplatného** , který jste použili v předchozí části tohoto kurzu. Navíc upravit adresu URL jako hodnota zahrnout "SampleBudget" `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Vyberte **záhlaví** kartu v rámci nástroje Postman.
7.  Přidat nový **klíč** s názvem "Autorizace".
8.  Nastavte **hodnotu** do tokenu, který byl vytvořen pomocí ArmClient na konci v poslední části. 
9.  Vyberte **tělo** kartu v rámci nástroje Postman. 
10. Vyberte **nezpracovaná** tlačítko možnost.
11. Vložením do textového pole, pod ukázková definice rozpočet, ale je třeba nahradit **subscriptionid**, **budgetname**, a **actiongroupname** parametry s vaší id předplatného, jedinečný název pro váš rozpočet a název skupiny akcí, kterou jste vytvořili v adresu URL a text žádosti:
    
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
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
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
    ```
12. Stisknutím klávesy **odeslat** odešlete žádost.

Teď máte všechno potřebné k volání [rozhraní API s nižším rozpočtem](https://docs.microsoft.com/rest/api/consumption/budgets). Reference k rozhraní API rozpočty obsahuje další podrobnosti o konkrétních požadavcích, včetně následujících:
    - **budgetName** -více rozpočtů jsou podporovány.  Rozpočet názvy musí být jedinečné.
    - **kategorie** – musí být buď **náklady** nebo **využití**. Rozhraní API podporuje rozpočtů nákladů a využití.
    - **timeGrain** – měsíčně, čtvrtletně nebo ročně rozpočtu. Velikost resetuje na konci období.
    - **filtry** – filtry umožňuje zúžit rozpočtu na konkrétní sadu prostředků v rámci vybraný obor. Filtr může být například kolekci skupin prostředků pro předplatné úrovně rozpočtu.
    - **oznámení** – Určuje podrobnosti oznámení a prahové hodnoty. Můžete nastavit prahové hodnoty více a zadejte e-mailovou adresu nebo skupinu akcí pro příjem oznámení.

## <a name="summary"></a>Souhrn

Podle tohoto kurzu jste se naučili:
- Postup vytvoření Runbooku Azure Automation k zastavování virtuálních počítačů.
- Jak vytvořit aplikaci logiky Azure, která se aktivuje založené na prahových hodnot rozpočtu a volat související sady runbook se správné parametry.
- Vytvoření skupiny akcí monitorování Azure, který bude nastavený tak, aby aktivace aplikace logiky Azure, pokud je dodržena prahová hodnota rozpočtu.
- Jak vytvořit rozpočet Azure s požadované prahové hodnoty a propojit je do skupiny akcí.

Teď máte plně funkční rozpočtu pro předplatné, které se vypnout seznam vašich virtuálních počítačů při dosažení vašeho nakonfigurované prahové hodnoty. 

## <a name="next-steps"></a>Další postup

- Další informace o fakturaci scénáře služby Azure, najdete v části [scénáře automatizace správy nákladů a fakturace](billing-cost-management-automation-scenarios.md).
