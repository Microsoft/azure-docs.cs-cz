---
title: Automatizace pracovního postupu v Azure Security Center | Microsoft Docs
description: Naučte se vytvářet a automatizovat pracovní postupy v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: cbe6adc37025f0b727fa5f5d3ace46ed34afaa9e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895218"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatizace odpovědí na triggery Security Center

Každý program zabezpečení zahrnuje několik pracovních postupů pro reakci na incidenty. Tyto procesy mohou zahrnovat oznamování relevantních účastníků, spuštění procesu správy změn a uplatnění specifických kroků k nápravě. Odborníci na zabezpečení doporučují automatizovat tolik kroků těchto postupů, jako je to možné. Automatizace snižuje režijní náklady. Může taky zlepšit zabezpečení tím, že zajistí rychlé, konzistentní a podle vašich předdefinovaných požadavků kroky procesu.

Tento článek popisuje funkci automatizace pracovního postupu Azure Security Center. Tato funkce může aktivovat Logic Apps výstrah zabezpečení a doporučení. Můžete například chtít, aby Security Center při výskytu výstrahy poslat e-mailem konkrétního uživatele. Naučíte se také, jak vytvořit Logic Apps pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|**Role správce zabezpečení** nebo **vlastník** skupiny prostředků<br>Musí mít taky oprávnění k zápisu pro cílový prostředek.<br><br>Pokud chcete pracovat s Azure Logic Apps pracovními postupy, musíte mít také následující Logic Apps role/oprávnění:<br> - Jsou vyžadována oprávnění [operátora aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-operator) nebo přístup pro čtení/aktivaci aplikace logiky (Tato role nemůže vytvářet ani upravovat aplikace logiky. *spouštějte* pouze existující)<br> - Pro vytváření a úpravu aplikace logiky se vyžadují oprávnění [Přispěvatel aplikace logiky](../role-based-access-control/built-in-roles.md#logic-app-contributor) .<br>Pokud chcete používat konektory aplikací logiky, budete možná potřebovat další přihlašovací údaje pro přihlášení ke svým příslušným službám (například k instancím aplikace Outlook/Teams nebo časové rezervy).|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Vytvoření aplikace logiky a definování jejího automatického spuštění 

1. Z bočního panelu Security Center vyberte **Automatizace pracovního postupu** .

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Seznam automatizovaných pracovních postupů":::

    Na této stránce můžete vytvořit nová pravidla automatizace a zároveň povolit, zakázat nebo odstranit existující.

1. Pokud chcete definovat nový pracovní postup, klikněte na **Přidat automatizaci pracovního postupu** . 

    Zobrazí se podokno s možnostmi pro novou automatizaci. Tady můžete zadat:
    1. Název a popis pro automatizaci.
    1. Triggery, které spustí tento automatický pracovní postup. Můžete například chtít, aby se aplikace logiky spouštěla, když se vygeneruje výstraha zabezpečení, která obsahuje "SQL".

        > [!NOTE]
        > Pokud je vaše aktivační událost doporučení s "poddoporučeními", například by se **měly opravit výsledky posouzení ohrožení zabezpečení v databázích SQL** , aplikace logiky se neaktivuje pro každé nové zjištění zabezpečení; pouze v případě, že dojde ke změně stavu nadřazeného doporučení.

    1. Aplikace logiky, která se spustí, když se splní podmínky triggeru 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Seznam automatizovaných pracovních postupů":::

1. V části Akce klikněte na **vytvořit nový** a začněte proces vytváření aplikace logiky.

    Budete muset Azure Logic Apps.

    [![Vytvoření nové aplikace logiky](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Zadejte název, skupinu prostředků a umístění a klikněte na **vytvořit** .

1. V nové aplikaci logiky si můžete vybrat z předdefinovaných předdefinovaných šablon z kategorie zabezpečení. Případně můžete definovat vlastní tok událostí, které mají nastat při aktivaci tohoto procesu.

    > [!TIP]
    > Někdy v aplikaci logiky jsou parametry zahrnuty v konektoru jako součást řetězce a nikoli ve vlastním poli. Příklad pro EAN postup extrakce parametrů naleznete v tématu Krok #14 [práce s parametry aplikace logiky při sestavování Azure Security Center automatizaci pracovního postupu](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    Návrhář aplikace logiky podporuje tyto aktivační události Security Center:

    * **Když se vytvoří nebo aktivuje doporučení Azure Security Center** – Pokud aplikace logiky spoléhá na doporučení, které se přestává používat jako zastaralé nebo nahrazené, vaše automatizace přestane fungovat a bude nutné Trigger aktualizovat. Pokud chcete sledovat změny v doporučeních, přečtěte si téma [Azure Security Center poznámky k verzi](release-notes.md).

    * **Když se vytvoří nebo aktivuje výstraha Azure Security Center** – Trigger můžete přizpůsobit tak, aby se v něm zobrazovaly jenom výstrahy se úrovněmi závažnosti, které vás zajímají.
    
    > [!NOTE]
    > Pokud používáte starší verzi triggeru, když se aktivuje odpověď na Azure Security Center výstraha, vaše aplikace logiky se nespustí pomocí funkce automatizace pracovního postupu. Místo toho použijte kteroukoli z výše uvedených triggerů. 

    [![Ukázková aplikace logiky](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po definování aplikace logiky se vraťte do podokna definice automatizace pracovního postupu (přidat automatizaci pracovního postupu). Kliknutím na **aktualizovat** zajistěte, aby byla nová aplikace logiky dostupná pro výběr.

    ![Aktualizovat](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Vyberte svou aplikaci logiky a uložte automatizaci. Všimněte si, že v rozevírací nabídce aplikace logiky se zobrazí pouze Logic Apps s podpůrnými Security Center konektory uvedenými výše.


## <a name="manually-trigger-a-logic-app"></a>Ruční aktivace aplikace logiky

Logic Apps můžete také spustit ručně při zobrazení jakékoli výstrahy nebo doporučení zabezpečení.

Pokud chcete aplikaci logiky spustit ručně, otevřete výstrahu nebo doporučení a klikněte na **aktivovat aplikaci logiky** :

[![Ruční aktivace aplikace logiky](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Konfigurace automatizace pracovních postupů ve velkém měřítku pomocí zadaných zásad

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

K nasazení konfigurací automatizace napříč vaší organizací použijte Azure Policy níže popsaných zásad "DeployIfNotExist", které jsou popsané níže, a vytvořte a nakonfigurujte postupy pro automatizaci pracovního postupu.

Začínáme se [šablonami automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Postup při implementaci těchto zásad:

1. V následující tabulce vyberte zásadu, kterou chcete použít:

    |Cíl  |Zásada  |ID zásady  |
    |---------|---------|---------|
    |Automatizace pracovního postupu pro výstrahy zabezpečení|[Nasazení automatizace pracovních postupů pro upozornění služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Automatizace pracovního postupu pro doporučení zabezpečení|[Nasazení automatizace pracovních postupů pro doporučení služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    ||||

    > [!TIP]
    > Můžete je také najít hledáním Azure Policy:
    > 1. Otevřete Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Seznam automatizovaných pracovních postupů":::
    > 2. V nabídce Azure Policy vyberte **definice** a vyhledejte je podle názvu. 

1. Na stránce příslušné Azure Policy vyberte **přiřadit** .
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Seznam automatizovaných pracovních postupů":::

1. Otevřete každou kartu a nastavte požadované parametry:
    1. Na kartě **základy** nastavte obor pro zásadu. Chcete-li použít centralizovanou správu, přiřaďte tuto zásadu ke skupině pro správu obsahující předplatná, která budou používat konfiguraci automatizace pracovních postupů. 
    1. Na kartě **parametry** nastavte podrobnosti skupiny prostředků a datového typu. 
        > [!TIP]
        > Každý parametr obsahuje popis, který vysvětluje možnosti, které máte k dispozici.
        >
        > Karta parametry Azure Policy (1) poskytuje přístup k podobným možnostem konfigurace jako stránka automatizace pracovního postupu Security Center (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Seznam automatizovaných pracovních postupů" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Pokud chcete toto přiřazení použít u stávajících předplatných, otevřete kartu **náprava** a vyberte možnost vytvoření úlohy nápravy.

1. Zkontrolujte stránku Souhrn a vyberte **vytvořit** .


## <a name="data-types-schemas"></a>Schémata datových typů

Chcete-li zobrazit nezpracované schéma událostí výstrah zabezpečení nebo událostí doporučení předaných do instance aplikace logiky, přejděte na [schéma typů dat automatizace pracovního postupu](https://aka.ms/ASCAutomationSchemas). To může být užitečné v případech, kdy neSecurity Center používáte Integrované konektory aplikace logiky, které jsou uvedené výše, ale místo toho se používá obecný konektor HTTP aplikace logiky – můžete použít schéma JSON události a ručně ho analyzovat podle potřeby.


## <a name="faq-for-workflow-automation"></a>Nejčastější dotazy týkající se automatizace pracovních postupů

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Podporuje automatizace pracovních postupů všechny scénáře pro provozní kontinuitu a zotavení po havárii (BCDR)?

Při přípravě prostředí pro BCDR scénáře, kde u cílového prostředku dochází k výpadku nebo jiné havárii, je zodpovědnost vaší organizace proti ztrátě dat vytvořením záloh podle pokynů z Azure Event Hubs, Log Analytics pracovního prostoru a aplikace logiky.

Pro každou aktivní automatizaci doporučujeme vytvořit identickou (zakázanou) automatizaci a uložit ji do jiného umístění. Když dojde k výpadku, můžete tyto automatizace zálohování povolit a udržovat běžné operace.

Přečtěte si další informace o [provozní kontinuitě a zotavení po havárii pro Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vytváření Logic Apps, automatizaci jejich spouštění v Security Center a jejich ručním spuštění. 

Související materiály najdete v tématech: 

- [Modul Microsoft Learn, jak pomocí automatizace pracovních postupů automatizovat odpověď zabezpečení](/learn/modules/resolve-threats-with-azure-security-center/)
- [Doporučení zabezpečení v Azure Security Center](security-center-recommendations.md)
- [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
- [Informace o Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
- [Schémata datových typů v rámci automatizace pracovních postupů](https://aka.ms/ASCAutomationSchemas)