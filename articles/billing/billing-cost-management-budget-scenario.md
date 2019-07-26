---
title: Scénář rozpočtu fakturace a správy nákladů na Azure | Microsoft Docs
description: Naučte se používat Azure Automation k vypnutí virtuálních počítačů na základě konkrétních prahových hodnot rozpočtu.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443477"
---
# <a name="manage-costs-with-azure-budgets"></a>Správa nákladů s využitím služby Azure Budgets

Řízení nákladů je kritická součást pro maximalizaci hodnoty investice do cloudu. K dispozici je několik scénářů, ve kterých jsou důležité informace o viditelnosti, vytváření sestav a nákladové orchestraci, a to pro pokračování obchodních operací. [Rozhraní API pro Azure cost management](https://docs.microsoft.com/rest/api/consumption/) poskytují sadu rozhraní API pro podporu každého z těchto scénářů. Rozhraní API poskytují podrobné informace o využití, které vám umožní zobrazit podrobné náklady na úroveň instance.

Rozpočty se běžně používají jako součást řízení nákladů. Pro rozpočty je možné nastavit obor v Azure. Můžete například zúžit zobrazení rozpočtu na základě předplatného, skupin prostředků nebo kolekce prostředků. Kromě použití rozhraní API rozpočtů k upozorňování e-mailem při dosažení prahové hodnoty rozpočtu můžete pomocí [Azure Monitorch skupin akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) aktivovat orchestraci sady akcí jako výsledek události rozpočtu.

Scénář běžných rozpočtů pro zákazníka, na kterém běží Nekritická úloha, může nastat, když chce spravovat před rozpočtem, a také při prohlížení měsíční faktury získat předvídatelné náklady. Tento scénář vyžaduje určitou cenovou orchestraci prostředků, které jsou součástí prostředí Azure. V tomto scénáři je nastaven měsíční rozpočet $1000 pro předplatné. Prahové hodnoty oznámení se také nastaví tak, aby aktivovaly několik orchestrací. Tento scénář začíná prahovou hodnotou 80%, což zastavuje všechny virtuální počítače ve skupině prostředků jako **volitelné**. Pak se v případě prahové hodnoty 100% nákladů na všechny instance virtuálních počítačů zastaví.
Pokud chcete nakonfigurovat tento scénář, provedete následující akce podle kroků uvedených v jednotlivých částech tohoto kurzu.

Tyto akce, které jsou součástí tohoto kurzu, vám umožní:

- Vytvořte Azure Automation Runbook pro zastavení virtuálních počítačů pomocí webhooků.
- Vytvořte aplikaci logiky Azure, která se aktivuje na základě prahové hodnoty rozpočtu, a zavolejte sadu Runbook se správnými parametry.
- Vytvořte Azure Monitor skupinu akcí, která bude nakonfigurovaná tak, aby aktivovala aplikaci Azure Logic, když se splní prahová hodnota rozpočtu.
- Vytvořte rozpočet Azure s požadovanými prahovými hodnotami a nakabelujte ho do skupiny akcí.

## <a name="create-an-azure-automation-runbook"></a>Vytvoření sady Runbook Azure Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) je služba, která umožňuje skriptovat většinu úloh správy prostředků a spouštět tyto úlohy jako naplánované nebo na vyžádání. V rámci tohoto scénáře vytvoříte [Azure Automation sadu Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) , která bude sloužit k zastavení virtuálních počítačů. Tento scénář vytvoříte pomocí grafického Runbooku [zastavení virtuálních počítačů Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z [Galerie](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) . Když naimportujete Runbook do svého účtu Azure a publikujete ho, bude možné zastavit virtuální počítače při dosažení prahové hodnoty rozpočtu.

### <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
2. Klikněte na tlačítko **vytvořit prostředek** , které najdete v levém horním rohu Azure.
3. Vyberte možnost > **Automatizace** **nástrojů pro správu**.
   > [!NOTE]
   > Pokud nemáte účet Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/).
4. Zadejte informace o účtu. Pro **Vytvoření účtu spustit v Azure jako**vyberte **Ano** , pokud chcete automaticky povolit nastavení potřebná pro zjednodušení ověřování do Azure.
5. Jakmile budete hotovi, klikněte na **Vytvořit** a spusťte nasazování účtu Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Import Runbooku zastavení virtuálních počítačů Azure v2

Pomocí [sady Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)importujte z Galerie grafický Runbook [zastavení virtuálních počítačů Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) .

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů svého účtu Azure.
2.  Otevřete účet Automation tak, že vyberete **všechny** > **účty Automation**Services. Pak vyberte svůj účet Automation.
3.  V části **Automatizace procesu** klikněte na **Galerie runbooků** .
4.  Nastavte **zdroj Galerie** na **centrum skriptů** a vyberte **OK**.
5.  Vyhledejte a vyberte položku galerie [virtuálních počítačů Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) v rámci Azure Portal.
6.  Kliknutím na tlačítko **importovat** zobrazte okno **Import** a vyberte **OK**. Zobrazí se okno s přehledem Runbooku.
7.  Jakmile sada Runbook dokončí proces importu, vyberte možnost **Upravit** , aby se zobrazil grafický editor runbooků a možnost publikování.

    ![Azure – úprava grafického Runbooku](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Kliknutím na tlačítko **publikovat** publikujte Runbook a po zobrazení výzvy vyberte **Ano** . Když publikujete sadu Runbook, přepíšete existující publikovanou verzi s verzí konceptu. V tomto případě nemáte publikovanou verzi, protože jste sadu Runbook vytvořili.

    Další informace o publikování Runbooku najdete v tématu [Vytvoření grafického Runbooku](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Vytvořit Webhooky pro Runbook

Pomocí grafického Runbooku [zastavení virtuálních počítačů Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) vytvoříte dva Webhooky, které spustí runbook v Azure Automation prostřednictvím jedné žádosti HTTP. První Webhook vyvolá Runbook v prahové hodnotě rozpočtu 80% s názvem skupiny prostředků jako parametr a umožní tak zastavení volitelných virtuálních počítačů. Druhý Webhook pak sadu Runbook vyvolá bez parametrů (v 100%), čímž se zastaví všechny zbývající instance virtuálních počítačů.

1. Na stránce **Runbooky** v [Azure Portal](https://portal.azure.com/)klikněte na Runbook **StopAzureV2Vm** , který zobrazí okno s přehledem sady Runbook.
2. Kliknutím  na Webhook v horní části stránky otevřete okno **Přidat Webhook** .
3. Kliknutím na **vytvořit nový Webhook** otevřete okno **vytvořit nový Webhook** .
4. Nastavte **název** Webhooku na **volitelné**. Vlastnost **Enabled** musí být **Ano**. Hodnotu **vypršení platnosti** není nutné měnit. Další informace o vlastnostech Webhooku najdete v tématu [Podrobnosti](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook)Webhooku.
5. Vedle hodnoty URL klikněte na ikonu Kopírovat a zkopírujte adresu URL Webhooku.
   > [!IMPORTANT]
   > Uložte adresu URL Webhooku s názvem **volitelné** na bezpečném místě. Tuto adresu URL použijete později v tomto kurzu. Z bezpečnostních důvodů můžete po vytvoření Webhooku tuto adresu URL znovu zobrazit ani načíst.
6. Kliknutím na **OK** vytvořte nový Webhook.
7. Klikněte na **Konfigurovat parametry a nastavení spuštění** pro zobrazení hodnot parametrů pro sadu Runbook.
   > [!NOTE]
   > Pokud má sada Runbook povinné parametry, nemůžete vytvořit Webhook, pokud nejsou zadány hodnoty.
8. Kliknutím na **OK** přijměte hodnoty parametrů Webhooku.
9. Kliknutím na **vytvořit** vytvořte Webhook.
10. Potom pomocí výše uvedeného postupu vytvořte druhý Webhook s názvem **dokončeno**.
    > [!IMPORTANT]
    > Nezapomeňte uložit obě adresy URL Webhooku pro pozdější použití v tomto kurzu. Z bezpečnostních důvodů můžete po vytvoření Webhooku tuto adresu URL znovu zobrazit ani načíst.

Nyní byste měli mít dva nakonfigurované Webhooky, které jsou k dispozici pomocí adres URL, které jste uložili.

![Webhooky – volitelné a úplné](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Nyní jste hotovi s instalačním programem Azure Automation. Webhooky můžete testovat pomocí jednoduchého testu post, abyste ověřili, že Webhook funguje. Dále musíte vytvořit aplikaci logiky pro orchestraci.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Vytvoření aplikace logiky Azure pro orchestraci

Logic Apps vám pomůžou sestavovat, plánovat a automatizovat procesy jako pracovní postupy, abyste mohli integrovat aplikace, data, systémy a služby v rámci podniků nebo organizací. V tomto scénáři vám [Aplikace logiky](https://docs.microsoft.com/azure/logic-apps/) , kterou vytvoříte, provede trochu víc, než stačí zavolat Webhook Automation, který jste vytvořili.

Rozpočty je možné nastavit tak, aby při splnění zadané prahové hodnoty aktivovaly oznámení. Můžete zadat více prahových hodnot, které budou upozorňovány na, a aplikace logiky vám ukáže možnost provádět různé akce na základě splněné prahové hodnoty. V tomto příkladu nastavíte scénář, ve kterém se dostanete k několika oznámením, první oznámení je, že se dosáhlo 80% rozpočtu, a druhé oznámení je po dosažení 100% rozpočtu. Aplikace logiky se použije k vypnutí všech virtuálních počítačů ve skupině prostředků. Za prvé bude  k dispozici volitelná prahová hodnota v 80%, pak bude dosaženo druhé prahové hodnoty, kde budou všechny virtuální počítače v rámci předplatného vypnuty.

Logic Apps umožňují zadat ukázkové schéma pro Trigger HTTP, ale vyžaduje, abyste nastavili hlavičku **Content-Type** . Vzhledem k tomu, že skupina akcí neobsahuje vlastní záhlaví Webhooku, je nutné analyzovat datovou část v samostatném kroku. Použijete akci **analyzovat** a získáte ji s ukázkovou datovou částí.

### <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

Aplikace logiky provede několik akcí. Následující seznam poskytuje sadu akcí vysoké úrovně, které aplikace logiky provede:
- Rozpozná, kdy se přijme požadavek HTTP.
- Analyzovat předaná data JSON pro určení prahové hodnoty, která byla dosažena
- Pomocí podmíněného příkazu ověřte, zda prahová hodnota dosáhla 80% nebo více rozsahu rozpočtu, ale ne větší než nebo rovna 100%.
    - Pokud je dosaženo této prahové hodnoty, odešlete příspěvek HTTP pomocí Webhooku s názvem **volitelné**. Tato akce vypne virtuální počítače ve skupině "volitelné".
- Pomocí podmíněného příkazu ověřte, zda prahová hodnota dosáhla nebo překročila 100% hodnoty rozpočtu.
    - Pokud byla dosažena prahová hodnota, odešlete příspěvek HTTP pomocí Webhooku s názvem **Complete**. Tato akce vypne všechny zbývající virtuální počítače.

K vytvoření aplikace logiky, která provede výše uvedené kroky, je nutné provést následující kroky:

1.  V [Azure Portal](https://portal.azure.com/)vyberte **vytvořit prostředek** > **Integration** > **Logic App**.

    ![Azure – výběr prostředku aplikace logiky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  V okně **vytvořit aplikaci logiky** zadejte podrobnosti potřebné k vytvoření vaší aplikace logiky, vyberte **Připnout na řídicí panel**a klikněte na **vytvořit**.

    ![Azure – vytvoření aplikace logiky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Když Azure nasadí vaši aplikaci logiky, otevře se **návrhář Logic Apps** a zobrazí se okno s úvodním videem a běžně používanými triggery.

### <a name="add-a-trigger"></a>Přidat trigger

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu. Akce jsou všechny kroky, ke kterým dochází po triggeru.

1.  V části **šablony** okna **Návrháře Logic Apps** vyberte **prázdná aplikace logiky**.
2.  Přidejte [Trigger](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) zadáním příkazu "Request http" do vyhledávacího pole **Návrháře Logic Apps** vyhledejte a vyberte aktivační událost s názvem **požadavek – při přijetí požadavku HTTP**.

    ![Azure-Logic App – Trigger http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Vyberte **Nový krok** > **přidat akci**.

    ![Azure – nový krok – přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Vyhledejte "analyzovat JSON" v vyhledávacím poli **návrháře Logic Apps** vyhledejte a vyberte [akci](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **operace s daty – analyzovat JSON** .

    ![Azure-Logic app – přidat akci analýzy JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Jako název **obsahu** pro datovou část analyzovat JSON zadejte "datová část" nebo použijte značku "tělo" z dynamického obsahu.
6.  V poli **Analýza JSON** vyberte možnost **použít ukázkovou datovou část k vygenerování schématu** .

    ![Aplikace Azure-Logic – k vygenerování schématu použijte ukázková data JSON.](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Do textového pole vložte následující datovou část ukázkového formátu JSON:`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Textové pole se zobrazí jako následující:

    ![Azure-Logic App – ukázková datová část JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Klikněte na **Done** (Hotovo).

### <a name="add-the-first-conditional-action"></a>Přidání první podmíněné akce

Pomocí podmíněného příkazu ověřte, zda prahová hodnota dosáhla 80% nebo více rozsahu rozpočtu, ale ne větší než nebo rovna 100%. Pokud je dosaženo této prahové hodnoty, odešlete příspěvek HTTP pomocí Webhooku s názvem **volitelné**. Tato akce vypne virtuální počítače ve **volitelné** skupině.

1.  Vyberte **Nový krok** > **Přidat podmínku**.

    ![Azure-Logic App – přidání podmínky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  V poli **Podmínka** klikněte na textové pole obsahující **hodnotu** pro zobrazení seznamu dostupných hodnot.

    ![Azure-Logic App – podmínka – pole](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  V horní části seznamu klikněte na **výraz** a v editoru výrazů zadejte následující výraz:`float()`

    ![Azure-Logic App – výraz typu float](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Vyberte **dynamický obsah**, umístěte kurzor do závorek () a výběrem **NotificationThresholdAmount** ze seznamu naplňte úplný výraz.

    Výraz bude následující:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Vyberte **OK** a nastavte výraz.
6.  Select **je větší než nebo rovno** v rozevíracím seznamu **podmínky**.
7.  V poli **zvolit hodnotu** podmínky zadejte `.8`.

    ![Azure-Logic App-float Expression s hodnotou](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Kliknutím na **Přidat** > **řádek** do pole podmínka přidáte další část podmínky.
9.  V poli **Podmínka** klikněte na textové pole obsahující **položku zvolit hodnotu**.
10. V horní části seznamu klikněte na **výraz** a v editoru výrazů zadejte následující výraz:`float()`
11. Vyberte **dynamický obsah**, umístěte kurzor do závorek () a výběrem **NotificationThresholdAmount** ze seznamu naplňte úplný výraz.
12. Vyberte **OK** a nastavte výraz.
13. Příkaz Select **je menší než** v rozevíracím seznamu **podmínky**.
14. V poli **zvolit hodnotu** podmínky zadejte `1`.

    ![Azure-Logic App-float Expression s hodnotou](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. V poli **Pokud je true** vyberte **přidat akci**. Přidáte akci HTTP POST, která ukončí volitelné virtuální počítače.

    ![Azure-Logic App – Přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Zadejte **http** pro vyhledání akce HTTP a vyberte akci **http – http** .

    ![Azure – aplikace logiky – přidat akci HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Jako hodnotu **metody** vyberte **post** jako.
18. Jako hodnotu **identifikátoru URI** zadejte adresu URL Webhooku s názvem **volitelné** , který jste vytvořili dříve v tomto kurzu.

    ![Azure-Logic App – identifikátor URI akce HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. V poli **Pokud je true** vyberte **přidat akci** . Přidáte akci e-mailu, která pošle e-mail s informacemi o tom, že se vypnuly volitelné virtuální počítače.
20. Vyhledejte "Odeslat e-mail" a vyberte akci *odeslání e-mailu* na základě e-mailové služby, kterou používáte.

    ![Azure-Logic App – akce odeslání e-mailu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    U osobních účtů Microsoft, vyberte **Outlook.com**. U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**. Pokud ještě nemáte připojení, budete vyzváni k přihlášení ke svému e-mailovému účtu. Logic Apps vytvoří připojení k e-mailovému účtu.

    Aby aplikace logiky měla přístup k e-mailovým informacím, budete ji muset zpřístupnit.

    ![Azure-Logic App – oznámení o přístupu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Přidejte text **Komu**, **Předmět**a text  e-mailu, který upozorní příjemce na vypnutí volitelných virtuálních počítačů. K naplnění polí předmět a tělo použijte dynamický obsah **NotificationThresholdAmount** a **rozpočet** .

    ![Azure-Logic App – podrobnosti e-mailu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Přidání druhé podmíněné akce

Pomocí podmíněného příkazu ověřte, zda prahová hodnota dosáhla nebo překročila 100% hodnoty rozpočtu. Pokud byla dosažena prahová hodnota, odešlete příspěvek HTTP pomocí Webhooku s názvem **Complete**. Tato akce vypne všechny zbývající virtuální počítače.

1.  Vyberte **Nový krok** > **Přidat podmínku**.

    ![Azure – aplikace logiky – přidat akci](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  V poli **Podmínka** klikněte na textové pole obsahující **hodnotu** pro zobrazení seznamu dostupných hodnot.
3.  V horní části seznamu klikněte na **výraz** a v editoru výrazů zadejte následující výraz:`float()`
4.  Vyberte **dynamický obsah**, umístěte kurzor do závorek () a výběrem **NotificationThresholdAmount** ze seznamu naplňte úplný výraz.

    Výraz bude následující:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Vyberte **OK** a nastavte výraz.
6.  Select **je větší než nebo rovno** v rozevíracím seznamu **podmínky**.
7.  V **poli zvolit hodnotu** podmínky zadejte `1`.

    ![Azure-Logic App – nastavit hodnotu podmínky](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  V poli **Pokud je true** vyberte **přidat akci**. Přidáte akci HTTP POST, která ukončí všechny zbývající virtuální počítače.

    ![Azure-Logic App – Přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Zadejte **http** pro vyhledání akce HTTP a vyberte akci **http – http** .
10. Jako hodnotu **metody** vyberte **post** jako.
11. Jako hodnotu **identifikátoru URI** zadejte adresu URL Webhooku s názvem **dokončeno** , který jste vytvořili dříve v tomto kurzu.

    ![Azure-Logic App – Přidání akce](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. V poli **Pokud je true** vyberte **přidat akci** . Přidáte akci e-mailu, která pošle e-mail s informacemi o tom, že se zbývající virtuální počítače vypnuly.
13. Vyhledejte "Odeslat e-mail" a vyberte akci *odeslání e-mailu* na základě e-mailové služby, kterou používáte.
14. Přidejte text **Komu**, **Předmět**a text  e-mailu, který upozorní příjemce na vypnutí volitelných virtuálních počítačů. K naplnění polí předmět a tělo použijte dynamický obsah **NotificationThresholdAmount** a **rozpočet** .

    ![Azure-Logic App – odeslání podrobností e-mailu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. V horní části okna **návrháře aplikace logiky** klikněte na **Uložit** .

### <a name="logic-app-summary"></a>Souhrn aplikace logiky

Vaše aplikace logiky bude vypadat, až budete hotovi. V nejvíce základních scénářích, kdy nepotřebujete žádnou orchestraci na základě prahové hodnoty, můžete přímo volat skript Automation z **monitorování** a přeskočit krok **Aplikace logiky** .

   ![Azure – aplikace logiky – zobrazení úplné](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Při uložení aplikace logiky byla vygenerována adresa URL, kterou budete moci volat. Tuto adresu URL použijete v další části tohoto kurzu.

## <a name="create-an-azure-monitor-action-group"></a>Vytvoření skupiny akcí Azure Monitor

Skupina akcí je kolekce předvoleb oznámení, které definujete. Když se aktivuje výstraha, může výstraha přijmout konkrétní skupina akcí. Výstraha Azure proaktivně vyvolá oznámení na základě konkrétních podmínek a nabídne možnost provést akci. Výstraha může používat data z několika zdrojů, včetně metrik a protokolů.

Skupiny akcí jsou jediným koncovým bodem, který budete integrovat s vaším rozpočtem. Oznámení můžete nastavit v několika kanálech, ale v tomto scénáři se zaměříte na aplikaci logiky, kterou jste vytvořili dříve v tomto kurzu.

### <a name="create-an-action-group-in-azure-monitor"></a>Vytvoření skupiny akcí v Azure Monitor

Když vytvoříte skupinu akcí, navedete ukazatel na aplikaci logiky, kterou jste vytvořili dříve v tomto kurzu.

1.  Pokud ještě nejste přihlášení k [Azure Portal](https://portal.azure.com/), přihlaste se a vyberte **všechny služby** > **monitorování**.
2.  V části **Nastavení** vyberte **skupiny akcí** .
3.  V okně **skupiny akcí** vyberte **Přidat skupinu akcí** .
4.  Přidejte a ověřte následující položky:
    - Název skupiny akcí
    - Krátký název
    - Subscription
    - Resource group

    ![Azure-Logic App – přidání skupiny akcí](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  V podokně **Přidat skupinu akcí** přidejte akci LogicApp. Pojmenujte akci **rozpočet – BudgetLA**. V podokně **Aplikace logiky** vyberte **předplatné** a **skupinu prostředků**. Pak vyberte **aplikaci logiky** , kterou jste vytvořili dříve v tomto kurzu.
6.  Kliknutím na **OK** nastavte aplikaci logiky. Pak v podokně **Přidat skupinu akcí** vyberte **OK** a vytvořte skupinu akcí.

Jste hotovi se všemi podpůrnými komponentami, které jsou potřeba pro efektivní orchestraci rozpočtu. Teď stačí vytvořit rozpočet a nakonfigurovat ho tak, aby používal vytvořenou skupinu akcí.

## <a name="create-the-azure-budget"></a>Vytvoření rozpočtu Azure

Rozpočet můžete v Azure Portal vytvořit pomocí [funkce rozpočtu](../cost-management/tutorial-acm-create-budgets.md) v cost management. Nebo můžete vytvořit rozpočet pomocí rozhraní REST API, rutin PowerShellu nebo pomocí rozhraní příkazového řádku. Následující postup používá REST API. Před voláním REST API budete potřebovat autorizační token. K vytvoření autorizačního tokenu můžete použít projekt [ARMClient](https://github.com/projectkudu/ARMClient) . **ARMClient** vám umožňuje ověřit Azure Resource Manager a získat token pro volání rozhraní API.

### <a name="create-an-authentication-token"></a>Vytvoření ověřovacího tokenu

1.  Přejděte do projektu [ARMClient](https://github.com/projectkudu/ARMClient) na GitHubu.
2.  Naklonujte úložiště, aby se získala místní kopie.
3.  Otevřete projekt v aplikaci Visual Studio a sestavte ho.
4.  Po úspěšném sestavení by měl být spustitelný soubor ve složce *\bin\debug* .
5.  Spusťte ARMClient. Otevřete příkazový řádek a přejděte do složky *\bin\debug* z kořenového adresáře projektu.
6.  Pokud se chcete přihlásit a ověřit, zadejte na příkazovém řádku tento příkaz:<br>
    `ARMClient login prod`
7.  Zkopírujte **identifikátor GUID** předplatného z výstupu.
8.  Pokud chcete do schránky zkopírovat autorizační token, na příkazovém řádku zadejte následující příkaz, ale v předchozím kroku použijte zkopírované ID předplatného: <br>
    `ARMClient token <subscription GUID from previous step>`

    Po dokončení výše uvedeného kroku se zobrazí následující:<br>
    **Token byl úspěšně zkopírován do schránky.**
9.  Uložte token, který se použije pro kroky v další části tohoto kurzu.

### <a name="create-the-budget"></a>Vytvořit rozpočet

Dále nakonfigurujete **metodu post** pro vytvoření rozpočtu voláním rozhraní REST API pro Azure spotřeb. Post je vývojové prostředí API. Soubory prostředí a kolekce naimportujete do instalačního souboru. Kolekce obsahuje seskupené definice požadavků HTTP, které volají rozhraní REST API pro Azure Spotřeber. Soubor prostředí obsahuje proměnné, které jsou používány kolekcí.

1.  Stáhněte a otevřete [klienta post REST](https://www.getpostman.com/) pro spouštění rozhraní REST API.
2.  V příspěvku vytvořte novou žádost.

    ![Publikování – vytvoření nové žádosti](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Uložte nový požadavek jako kolekci, aby na novém požadavku nebylo nic.

    ![Publikování – uložení nové žádosti](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Změňte požadavek z a `Get` `Put` na akci.
5.  Změňte následující adresu URL tak, `{subscriptionId}` že nahradíte **ID** předplatného, které jste použili v předchozí části tohoto kurzu. Také upravte adresu URL tak, aby obsahovala "SampleBudget" jako hodnotu `{budgetName}`pro:`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Vyberte kartu **hlavičky** v části po.
7.  Přidejte nový **klíč** s názvem "Authorization".
8.  Nastavte **hodnotu** na token, který byl vytvořen pomocí ArmClient na konci poslední části.
9.  Vyberte kartu **tělo** v části post.
10. Vyberte možnost  nezpracovaného tlačítka.
11. Do textového pole vložte následující vzorové definice rozpočtu, ale je nutné nahradit parametry **SubscriptionId**, rozpočtce a **actiongroupname** identifikátorem předplatného, jedinečným názvem pro rozpočet a názvem skupiny akcí. vytvořili jste v adrese URL i v textu požadavku:

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
12. Kliknutím na **Odeslat** odešlete žádost.

Nyní máte všechny součásti, které potřebujete k volání [rozhraní API](https://docs.microsoft.com/rest/api/consumption/budgets)pro rozpočty. Referenční informace k rozhraní API pro rozpočty obsahují další podrobnosti o konkrétních požadavcích, včetně následujících:
    - **rozpočet** – je podporováno více rozpočtů.  Názvy rozpočtů musí být jedinečné.
    - **kategorie** – musí být buď **náklady** , nebo **využití**. Rozhraní API podporuje rozpočty nákladů i využití.
    - **timeGrain** – měsíční, čtvrtletní nebo roční rozpočet. Tato částka se resetuje na konci období.
    - **filtry** – filtry umožňují zúžit rozpočet na konkrétní sadu prostředků v rámci vybraného oboru. Filtrem může být například kolekce skupin prostředků pro rozpočet na úrovni předplatného.
    - **oznámení** – určuje podrobnosti a prahové hodnoty oznámení. Můžete nastavit více prahových hodnot a zadat e-mailovou adresu nebo skupinu akcí pro příjem oznámení.

## <a name="summary"></a>Souhrn

Pomocí tohoto kurzu jste se naučili:
- Vytvoření sady Runbook Azure Automation pro zastavení virtuálních počítačů.
- Jak vytvořit aplikaci logiky Azure, která se aktivuje na základě prahových hodnot rozpočtu, a volat související sadu Runbook se správnými parametry.
- Jak vytvořit Azure Monitor skupinu akcí, která bude nakonfigurovaná tak, aby aktivovala aplikaci Azure Logic, když se splní prahová hodnota rozpočtu.
- Jak vytvořit rozpočet Azure s požadovanými prahovými hodnotami a nakabelovat ho do skupiny akcí.

Teď máte plně funkční rozpočet pro vaše předplatné, který vypne vaše virtuální počítače, když dosáhnete svých nakonfigurovaných prahových hodnot rozpočtu.

## <a name="next-steps"></a>Další postup

- Další informace o scénářích fakturace Azure najdete v tématu scénáře týkající se [Automatizace fakturace a správy nákladů](billing-cost-management-automation-scenarios.md).
