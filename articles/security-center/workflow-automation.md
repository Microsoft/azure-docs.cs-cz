---
title: Automatizace pracovního postupu v Azure Security Center | Microsoft Docs
description: Naučte se vytvářet a automatizovat pracovní postupy v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: memildin
ms.openlocfilehash: ff358f3c90edfcdabb5a1225ad1efa122818b070
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375257"
---
# <a name="workflow-automation"></a>Automatizace pracovního postupu

Každý program zabezpečení zahrnuje několik pracovních postupů pro reakci na incidenty. Tyto procesy mohou zahrnovat oznamování relevantních účastníků, spuštění procesu správy změn a uplatnění specifických kroků k nápravě. Odborníci na zabezpečení doporučují automatizovat tolik kroků těchto postupů, jako je to možné. Automatizace snižuje režijní náklady. Může taky zlepšit zabezpečení tím, že zajistí rychlé, konzistentní a podle vašich předdefinovaných požadavků kroky procesu.

Tento článek popisuje funkci automatizace pracovního postupu Azure Security Center. Tato funkce může aktivovat Logic Apps výstrah zabezpečení a doporučení. Můžete například chtít, aby Security Center při výskytu výstrahy poslat e-mailem konkrétního uživatele. Naučíte se také, jak vytvořit Logic Apps pomocí [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Pokud jste dříve použili zobrazení Playbooky (Preview) na bočním panelu, najdete stejné funkce společně s rozšířenými funkcemi na stránce Nová automatizace pracovního postupu.



## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupné|
|Stanov|Úroveň Free|
|Požadované role a oprávnění:|**Role správce zabezpečení** nebo **vlastník** skupiny prostředků<br>Musí mít taky oprávnění k zápisu pro cílový prostředek.<br><br>Pokud chcete pracovat s Azure Logic Apps pracovními postupy, musíte mít také následující Logic Apps role/oprávnění:<br> - Jsou vyžadována oprávnění [operátora aplikace logiky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) nebo přístup pro čtení/aktivaci aplikace logiky (Tato role nemůže vytvářet ani upravovat aplikace logiky. *spouštějte* pouze existující)<br> - Pro vytváření a úpravu aplikace logiky se vyžadují oprávnění [Přispěvatel aplikace logiky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) .<br>Pokud chcete používat konektory aplikací logiky, budete možná potřebovat další přihlašovací údaje pro přihlášení ke svým příslušným službám (například k instancím aplikace Outlook/Teams nebo časové rezervy).|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) Čína gov, jiné gov|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Vytvoření aplikace logiky a definování jejího automatického spuštění 

1. Z bočního panelu Security Center vyberte **Automatizace pracovního postupu**.

    [![Seznam automatizovaných pracovních postupů](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Na této stránce můžete vytvořit nová pravidla automatizace a zároveň povolit, zakázat nebo odstranit existující.

1. Pokud chcete definovat nový pracovní postup, klikněte na **Přidat automatizaci pracovního postupu**. 

    Zobrazí se podokno s možnostmi pro novou automatizaci. Tady můžete zadat:
    1. Název a popis pro automatizaci.
    1. Triggery, které spustí tento automatický pracovní postup. Můžete například chtít, aby se aplikace logiky spouštěla, když se vygeneruje výstraha zabezpečení, která obsahuje "SQL".
    1. Aplikace logiky, která se spustí, když se splní podmínky triggeru 

        [![Seznam automatizovaných pracovních postupů](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. V části Akce klikněte na **vytvořit nový** a začněte proces vytváření aplikace logiky.

    Budete muset Azure Logic Apps.

    [![Vytvoření nové aplikace logiky](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Zadejte název, skupinu prostředků a umístění a klikněte na **vytvořit**.

1. V nové aplikaci logiky si můžete vybrat z předdefinovaných předdefinovaných šablon z kategorie zabezpečení. Případně můžete definovat vlastní tok událostí, které mají nastat při aktivaci tohoto procesu.

    V návrháři aplikace logiky jsou podporovány následující aktivační události z konektorů Security Center:

    * **Když se vytvoří nebo spustí doporučení Azure Security Center**
    * **Při vytvoření nebo spuštění výstrahy Azure Security Center** 
    
    > [!TIP]
    > Trigger můžete přizpůsobit tak, aby se v něm zobrazovaly jenom výstrahy s úrovněmi závažnosti, které vás zajímají.
    
    > [!NOTE]
    > Pokud používáte starší verzi triggeru, když se aktivuje odpověď na Azure Security Center výstraha, Logic Apps se nespustí funkcí automatizace pracovního postupu. Místo toho použijte kteroukoli z výše uvedených triggerů. 

    [![Ukázková aplikace logiky](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po definování aplikace logiky se vraťte do podokna definice automatizace pracovního postupu (přidat automatizaci pracovního postupu). Kliknutím na **aktualizovat** zajistěte, aby byla nová aplikace logiky dostupná pro výběr.

    ![Aktualizovat](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Vyberte svou aplikaci logiky a uložte automatizaci. Všimněte si, že v rozevírací nabídce aplikace logiky se zobrazí pouze Logic Apps s podpůrnými Security Center konektory uvedenými výše.


## <a name="manually-trigger-a-logic-app"></a>Ruční aktivace aplikace logiky

Logic Apps můžete také spustit ručně při zobrazení jakékoli výstrahy nebo doporučení zabezpečení.

Pokud chcete aplikaci logiky spustit ručně, otevřete výstrahu nebo doporučení a klikněte na **aktivovat aplikaci logiky**:

[![Ruční aktivace aplikace logiky](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schémata datových typů

Chcete-li zobrazit nezpracované schéma událostí výstrah zabezpečení nebo událostí doporučení předaných do instance aplikace logiky, přejděte na [schéma typů dat automatizace pracovního postupu](https://aka.ms/ASCAutomationSchemas). To může být užitečné v případech, kdy neSecurity Center používáte Integrované konektory aplikace logiky, které jsou uvedené výše, ale místo toho se používá obecný konektor HTTP aplikace logiky – můžete použít schéma JSON události a ručně ho analyzovat podle potřeby.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vytváření Logic Apps, automatizaci jejich spouštění v Security Center a jejich ručním spuštění. 

Další související materiály najdete v tématech: 

- [Modul Microsoft Learn, jak pomocí automatizace pracovních postupů automatizovat odpověď zabezpečení](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Doporučení zabezpečení v Azure Security Center](security-center-recommendations.md)
- [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
- [Informace o Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Konektory Logic Apps](https://docs.microsoft.com/connectors/)
- [Schémata datových typů automatizace pracovního postupu](https://aka.ms/ASCAutomationSchemas)
