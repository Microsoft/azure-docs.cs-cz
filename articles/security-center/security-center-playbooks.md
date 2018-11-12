---
title: Playbook zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže automatizovat reakce na incidenty zabezpečení pomocí playbooků zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 05245b2a7a4f7bf61052b13da5ee2a98be721f7c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259951"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Playbook zabezpečení v Azure Security Center (Preview)
Tento dokument vám pomůže reagovat na problémy související se zabezpečením pomocí playbooků zabezpečení v Azure Security Center.

## <a name="what-is-security-playbook-in-security-center"></a>Co je playbook zabezpečení ve službě Security Center?
Playbook zabezpečení je kolekce procedur, které je možné spustit ze služby Security Center, jakmile se určitý playbook aktivuje z vybrané výstrahy. Playbook zabezpečení může pomoct automatizovat a orchestrovat reakci na konkrétní výstrahu zabezpečení, kterou zjistí služba Security Center. Playbooky zabezpečení ve službě Security Center jsou založené na [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps). To znamená, že můžete používat šablony Logic Apps z kategorie zabezpečení a upravovat je podle svých potřeb nebo můžete vytvářet nové playbooky pomocí [pracovního postupu Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) a službu Security Center používat jako trigger. 

> [!NOTE]
> Playbooky využívají Azure Logic Apps, proto se za ně účtují poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Jak vytvořit playbook zabezpečení ve službě Security Center?
Pokud chcete ve službě Security Center vytvořit nový playbook zabezpečení, postupujte následovně:

1.  Otevřete řídicí panel **Security Center**.
2.  V části **Automatizace a orchestrace** v levém podokně klikněte na **Playbooky (Preview)**.

    ![Aplikace logiky](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. Na stránce **Security Center – Playbooky (Preview)** klikněte na tlačítko **Přidat**.

    ![Vytvoření aplikace logiky](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Na stránce **Vytvořit aplikaci logiky** zadejte požadované informace pro vytvoření nové aplikace logiky a klikněte na tlačítko **Vytvořit**. Po dokončení vytváření se nový playbook zobrazí v seznamu. Pokud se nezobrazí, klikněte na tlačítko **Aktualizovat**. Jakmile se playbook zobrazí, klikněte na něj a začněte ho upravovat.

    ![Vytvoření aplikace logiky](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Zobrazí se **Návrhář aplikace logiky**. Kliknutím na **Prázdná aplikace logiky** vytvořte nový playbook. Můžete také vybrat kategorii **Zabezpečení** a použít některou ze šablon.
    
    ![Návrhář aplikace logiky](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. Do pole **Prohledat všechny konektory a triggery** zadejte *Azure Security Center* a vyberte **Při aktivaci odpovědi na upozornění služby Azure Security Center**.

    ![Trigger](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Teď můžete definovat, co se stane po aktivaci playbooku. Můžete přidat akci, logickou podmínku, podmínky případu přepínače nebo smyčky.

    ![Návrhář aplikace logiky](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>Jak spustit playbook zabezpečení ve službě Security Center?

Playbook zabezpečení ve službě Security Center můžete spustit, když chcete provést orchestraci, získání dalších informací z ostatních služeb nebo nápravné kroky. Pokud chcete získat přístup k playbookům, postupujte následovně:

1.  Otevřete řídicí panel **Security Center**.
2.  V levém podokně v části **Detekce hrozeb** klikněte na **Incidenty a výstrahy zabezpečení**.

    ![Výstrahy](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Klikněte na výstrahu, kterou chcete prozkoumat.
4.  V horní části stránky upozornění klikněte na tlačítko **Spustit playbooky**.

    ![Spuštění playbooku](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Na stránce Playbooky vyberte playbook, který chcete spustit, a klikněte na tlačítko **Spustit**. Pokud chcete playbook před aktivací zobrazit, můžete na něj kliknout a otevře se návrhář.

    ![Playbooky](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Historie

Po spuštění playbooku máte přístup i k předchozím spuštěním a krokům, které obsahují další informace o stavu dříve spuštěných playbooků. Historie se uvádí v kontextu jednotlivých výstrah, což znamená, že historie playbooku zobrazená na této stránce koreluje s výstrahou, která tento playbook aktivovala. 

![Historie](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Pokud chcete zobrazit další podrobnosti o spuštění konkrétního playbooku, kliknutím na samotný playbook zobrazte stránku spuštění aplikace logiky s celým pracovním postupem.

![Podrobnosti](./media/security-center-playbooks/security-center-playbooks-fig14.png)

V tomto pracovním postupu vidíte, jak dlouho trvalo spuštění jednotlivých úloh, a každou úlohu můžete rozbalit a zobrazit její výsledek. 

### <a name="changing-an-existing-playbook"></a>Změna existujícího playbooku

Existující playbook ve službě Security Center můžete změnit, pokud chcete přidat akci nebo podmínku. Stačí kliknout na název playbooku, který chcete změnit, na kartě Playbooky a otevře se Návrhář aplikace logiky.

> [!NOTE]
> Další informace o vytvoření vlastního playbooku pomocí aplikace logiky Azure najdete v tématu [Vytvořte svůj první pracovní postup aplikace logiky pro automatizaci procesů mezi cloudovými aplikacemi a cloudovými službami](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger).


## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste zjistili, jak používat playbooky ve službě Azure Security Center. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center. 
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

