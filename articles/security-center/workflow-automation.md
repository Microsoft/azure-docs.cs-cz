---
title: Automatizace pracovních postupů v Azure Security Center | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvářet a automatizovat pracovní postupy v Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 972b5415b85e82a5afdaf7d85d3a3bcb9e144d4d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384938"
---
# <a name="workflow-automation"></a>Automatizace pracovních postupů

Každý bezpečnostní program obsahuje více pracovních postupů pro reakci na incidenty. Tyto procesy mohou zahrnovat oznámení příslušným zúčastněným stranám, spuštění procesu správy změn a použití konkrétních nápravných kroků. Odborníci na zabezpečení doporučují, abyste automatizovali co nejvíce kroků těchto postupů. Automatizace snižuje režii. Může také zlepšit zabezpečení tím, že zajistí, aby byly kroky procesu prováděny rychle, konzistentně a podle vašich předdefinovaných požadavků.

Tento článek popisuje funkci automatizace pracovních postupů centra zabezpečení Azure. Tato funkce může aktivovat logic apps na výstrahy zabezpečení a doporučení. Můžete například chtít, aby centrum zabezpečení poslalo e-mail určitému uživateli, když dojde k upozornění. Dozvíte se také, jak vytvářet logicové aplikace pomocí [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Pokud jste dříve používali zobrazení Playbooky (Náhled) na postranním panelu, najdete stejné funkce spolu s rozšířenou funkcí na nové stránce automatizace pracovního postupu.


## <a name="requirements"></a>Požadavky

* Chcete-li pracovat s pracovními postupy Azure Logic Apps, musíte mít následující role nebo oprávnění logic apps:

    * [Oprávnění operátoru aplikace logiky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) jsou vyžadována nebo přístup aplikace logiky pro čtení a aktivaci (tato role nemůže vytvářet nebo upravovat aplikace logiky; *spouštět* pouze existující)

    * Pro vytváření a úpravy aplikace logiky jsou vyžadována oprávnění [přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) aplikace Logika.

* Pokud chcete použít konektory aplikace Logika, budete pravděpodobně potřebovat další pověření pro přihlášení k jejich příslušné služby (například vaše outlook/týmy/slack instance)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Vytvoření aplikace logiky a definování, kdy by měla být automaticky spuštěna 

1. V postranním panelu Centra zabezpečení vyberte **automatizaci pracovního postupu**.

    [![Seznam automatizace pracovních postupů](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Na této stránce můžete vytvořit nová pravidla automatizace a také povolit, zakázat nebo odstranit stávající.  
1. Chcete-li definovat nový pracovní postup, klepněte na tlačítko **Přidat automatizaci pracovního postupu**. 

    Zobrazí se podokno s možnostmi nové automatizace. Zde můžete zadat:
    1. Název a popis automatizace.
    1. Aktivační události, které zahájí tento automatický pracovní postup. Můžete například chtít, aby se aplikace logiky spouštěla, když je generována výstraha zabezpečení, která obsahuje "SQL".
    1. Aplikace logiky, která se spustí, když jsou splněny podmínky aktivační události. 

        [![Seznam automatizace pracovních postupů](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. V části Akce klikněte na **Vytvořit nový** a zahajte proces vytváření aplikace logiky.

    Přejdou vás do Azure Logic Apps.

    [![Vytvoření nové aplikace logiky](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Zadejte název, skupinu prostředků a umístění a klepněte na **tlačítko Vytvořit**.

1. V nové aplikaci logiky si můžete vybrat z předdefinovaných šablon z kategorie zabezpečení. Nebo můžete definovat vlastní tok událostí dojít při aktivaci tohoto procesu.

    V návrháři aplikace logiky jsou podporovány následující aktivační události z konektorů Centra zabezpečení:

    * **Když se vytvoří nebo aktivuje doporučení Centra zabezpečení Azure**
    * **Když se vytvoří nebo aktivuje výstraha Centra zabezpečení Azure** 
    
    > [!TIP]
    > Aktivační událost můžete přizpůsobit tak, aby se vztahovala pouze na výstrahy s úrovněmi závažnosti, které vás zajímají.
    
    > [!NOTE]
    > Pokud používáte starší aktivační událost "Když se aktivuje odpověď na výstrahu Centra zabezpečení Azure", vaše logic Apps se nespustí funkce Automatizace pracovního postupu. Místo toho použijte některou z aktivačních událostí uvedených výše. 

    [![Ukázková aplikace logiky](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po definování aplikace logiky se vraťte do podokna definice automatizace pracovního postupu ("Přidat automatizaci pracovního postupu"). Klikněte na **Aktualizovat** a ujistěte se, že nová aplikace logiky je k dispozici pro výběr.

    ![Obnovení](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Vyberte aplikaci logiky a uložte automatizaci. Všimněte si, že rozevírací informace aplikace logiky zobrazuje pouze aplikace logiky s podporou konektorů Security Center uvedených výše.


## <a name="manually-trigger-a-logic-app"></a>Ruční spuštění aplikace logiky

Aplikace Logic Apps můžete také spustit ručně při zobrazení výstrahy zabezpečení nebo doporučení, které nabízí [opravu rychlé opravy](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Chcete-li aplikaci logiky spustit ručně, otevřete výstrahu nebo doporučení, které podporuje opravu rychlé opravy, a klepněte na tlačítko **Aktivovat aplikaci logiky**:

[![Ruční spuštění aplikace logiky](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schémata datových typů

Chcete-li zobrazit nezpracovaná schémata událostí výstrah zabezpečení nebo událostí doporučení předaných instanci aplikace Logika, navštivte [schémata dat automatizace pracovního postupu](https://aka.ms/ASCAutomationSchemas). To může být užitečné v případech, kdy nepoužíváte konektory integrované aplikace logiky centra zabezpečení uvedené výše, ale místo toho používají obecný konektor HTTP aplikace Logika - můžete použít schéma události JSON ručně analyzovat, jak uznáte za vhodné.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vytváření aplikací logiky, jejich ručním spuštění v Centru zabezpečení a automatizaci jejich spuštění. 

Další související materiály naleznete v následujících článcích: 

- [Doporučení zabezpečení v Azure Security Center](security-center-recommendations.md)
- [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
- [Informace o Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Konektory Logic Apps](https://docs.microsoft.com/connectors/)
- [Schémata dat automatizace pracovního postupu](https://aka.ms/ASCAutomationSchemas)
