---
title: 'Kurz: použití playbooky s pravidly automatizace v Azure Sentinel'
description: Tento kurz vám umožní využít playbooky spolu s pravidly automatizace v Azure Sentinel k automatizaci reakce na incidenty a napravit hrozby zabezpečení.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600584"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Kurz: použití playbooky s pravidly automatizace v Azure Sentinel

V tomto kurzu se dozvíte, jak pomocí playbooky společně s pravidly automatizace automatizovat reakci na incidenty a opravit bezpečnostní hrozby zjištěné službou Azure Sentinel. Po dokončení tohoto kurzu budete moct:

> [!div class="checklist"]
>
> * Vytvoření pravidla automatizace
> * Vytvoření PlayBook
> * Přidání akcí do PlayBook
> * Připojení PlayBook k pravidlu automatizace nebo pravidla analýzy pro automatizaci odpovědi na hrozbu

## <a name="what-are-automation-rules-and-playbooks"></a>Co jsou pravidla automatizace a playbooky?

Pravidla automatizace vám pomůžou při třídění incidentů v Azure Sentinel. Můžete je použít k automatickému přiřazení incidentů správným pracovníkům, zavření incidentů s vysokou závažností nebo známých falešně pozitivních výsledků, změně jejich závažnosti a přidání značek. Jsou to také mechanizmus, kterým můžete spustit playbooky v reakci na incidenty.

Playbooky jsou kolekce procedur, které lze spustit z protokolu Azure Sentinel v reakci na výstrahu nebo incident. PlayBook může přispět k automatizaci a orchestraci vaší odpovědi a je možné ji nastavit tak, aby se automaticky spouštěla, když se generují konkrétní výstrahy nebo incidenty, a to prostřednictvím připojení k pravidlu analýzy nebo pravidla automatizace. Dá se taky spustit ručně na vyžádání.

Playbooky ve službě Azure Sentinel vychází z pracovních postupů vytvořených v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), což znamená, že získáte všechny možnosti napájení, úprav a předdefinovaných šablon Logic Apps. Každý PlayBook se vytvoří pro konkrétní předplatné, ke kterému patří, ale displej **playbooky** zobrazuje všechny dostupné playbooky v rámci všech vybraných předplatných.

> [!NOTE]
> Vzhledem k tomu, že playbooky využívají Azure Logic Apps, mohou platit další poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) .

Například pokud chcete zabránit potenciálně napadeným uživatelům v pohybu kolem sítě a krádeži informací, můžete vytvořit automatizovanou mnohotvárnou reakci na incidenty vygenerované pravidly, které zjišťují napadené uživatele. Začnete vytvořením PlayBook, který provede následující akce:

1. Když je PlayBook volán pravidlem automatizace, které ho předává incidentu, PlayBook otevře lístek v [ServiceNow](/connectors/service-now/) nebo jakémkoli jiném systému pro lístkování IT.

1. Pošle zprávu na kanál operací zabezpečení v [Microsoft Teams](/connectors/teams/) nebo [časovou rezervu](/connectors/slack/) , aby se zajistilo, že analytikům zabezpečení ví o incidentu.

1. Odesílá také všechny informace v incidentu v e-mailové zprávě vašemu správci a správci zabezpečení vaší hlavní sítě. E-mailová zpráva bude obsahovat přepínače **blokování** a **Ignorovat** uživatele.

1. PlayBook počká, dokud odpověď neobdrží od správců, a pak pokračuje v dalším postupu.

1. Pokud správci zvolí **blok**, pošle příkaz službě Azure AD, který zakáže uživatele, a druhý pro bránu firewall, která zablokuje IP adresu.

1. Pokud správci zvolí možnost **Ignorovat**, PlayBook uzavře incident v rámci Azure Sentinel a lístek v ServiceNow.

Pokud chcete aktivovat PlayBook, vytvoříte pravidlo automatizace, které se spustí, když se tyto incidenty generují. Toto pravidlo provede tyto kroky:

1. Pravidlo změní stav incidentu na **aktivní**.

1. Přiřadí incident analytikovi, který je úkolem správy tohoto typu incidentu.

1. Přidá značku "ohrožený uživatel".

1. Nakonec volá PlayBook, který jste právě vytvořili. ([Pro tento krok se vyžadují speciální oprávnění](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Playbooky lze spustit automaticky v reakci na incidenty tím, že vytvoříte pravidla automatizace, která volají playbooky jako akce, jako v předchozím příkladu. Můžou se také spouštět automaticky v reakci na výstrahy. Při vygenerování výstrahy si poupozorní pravidlo analýzy, aby automaticky spouštělo jeden nebo více playbooky. 

Můžete také zvolit, že se má PlayBook spustit ručně na vyžádání, jako odpověď na zvolenou výstrahu.

Získejte úplnější a podrobný Úvod k automatizaci reakce na hrozby pomocí [pravidel automatizace](automate-incident-handling-with-automation-rules.md) a [Playbooky](automate-responses-with-playbooks.md) v Azure Sentinel.

> [!IMPORTANT]
>
> - **Pravidla automatizace** a použití **triggeru incidentu** pro Playbooky jsou momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="create-a-playbook"></a>Vytvoření PlayBook

Pomocí těchto kroků vytvořte nové PlayBook ve službě Azure Sentinel:

### <a name="prepare-the-playbook-and-logic-app"></a>Příprava PlayBook a aplikace logiky

1. V navigační nabídce **Azure Sentinel** vyberte **Automation**.

1. V horní nabídce vyberte **vytvořit** a **přidejte nové PlayBook**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Přidat nový PlayBook":::

    Otevře se nová karta prohlížeče a přejdete k průvodci **vytvořením aplikace logiky** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Vytvoření aplikace logiky":::

1. Zadejte své **předplatné** a **skupinu prostředků** a v části **název aplikace logiky** zadejte název PlayBook.

1. V poli **oblast** vyberte oblast Azure, ve které se mají ukládat informace o aplikaci logiky.

1. Pokud chcete tuto aktivitu PlayBook sledovat pro účely diagnostiky, zaškrtněte políčko **Povolit Log Analytics** a zadejte název **pracovního prostoru Log Analytics** .

1. Pokud chcete použít značky pro váš PlayBook, klikněte na **Další: značky >** (Nepřipojeno k značkám, které používají pravidla automatizace. [Přečtěte si další informace o značkách](../azure-resource-manager/management/tag-resources.md). V opačném případě klikněte na tlačítko **zkontrolovat + vytvořit**. Potvrďte podrobnosti, které jste zadali, a klikněte na **vytvořit**.

1. Při vytváření a nasazování PlayBook (to může trvat několik minut) se dostanete na obrazovku s názvem **Microsoft. EmptyWorkflow**. Po zobrazení zprávy "vaše nasazení je dokončeno" klikněte na tlačítko **Přejít k prostředku.**

1. Přejdete k novému [návrháři Logic Apps](../logic-apps/logic-apps-overview.md)PlayBook, kde můžete začít navrhovat pracovní postup. Zobrazí se obrazovka s krátkým úvodním videem a běžně používané triggery a šablony aplikace logiky. [Přečtěte si další informace](../logic-apps/logic-apps-create-logic-apps-from-templates.md) o vytváření playbook pomocí Logic Apps.

1. Vyberte šablonu **prázdná aplikace logiky** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galerie šablon návrháře Logic Apps":::

### <a name="choose-the-trigger"></a>Výběr triggeru

Každý PlayBook musí začínat triggerem. Aktivační událost definuje akci, která spustí PlayBook a schéma, které bude PlayBook očekávat.

1. Na panelu hledání vyhledejte Sentinel Azure. Pokud se zobrazí ve výsledcích, vyberte možnost **Azure Sentinel** .

1. Na kartě výsledné **aktivační události** se zobrazí dvě triggery, které nabízí Azure Sentinel:
    - Když se aktivuje odpověď na výstrahu Sentinel Azure
    - Když bylo aktivováno pravidlo pro vytvoření incidentu služby Azure Sentinel

   Vyberte Trigger, který odpovídá typu PlayBook, který vytváříte.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Výběr triggeru pro PlayBook":::

### <a name="add-actions"></a>Přidat akce

Nyní můžete definovat, co se stane při volání PlayBook. Výběrem možnosti **Nový krok** můžete přidat akce, logické podmínky, smyčky nebo podmínky případu přepnutí. Tento výběr otevře nový rámec v návrháři, kde můžete zvolit systém nebo aplikaci, pro kterou chcete pracovat, nebo podmínku, kterou chcete nastavit. Do panelu hledání v horní části rámce zadejte název systému nebo aplikace a pak zvolte z dostupných výsledků.

V každém z těchto kroků se kliknutím na libovolné pole zobrazí panel se dvěma nabídkami: **dynamický obsah** a **výraz**. V nabídce **dynamického obsahu** můžete přidat odkazy na atributy výstrahy nebo incidentu, které byly předány do PlayBook, včetně hodnot a atributů všech zúčastněných entit. V nabídce **výrazu** můžete zvolit z velké knihovny funkcí a přidat další logiku k vašim krokům.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Návrhář aplikace logiky":::

Tento snímek obrazovky ukazuje akce a podmínky, které byste přidali při vytváření PlayBook popsaných v příkladu na začátku tohoto dokumentu. Jediným rozdílem je to, že v PlayBook, který se tady zobrazuje, použijete **aktivační událost výstrahy** místo **triggeru incidentu**. To znamená, že tento PlayBook budete volat z pravidla analýzy přímo, nikoli z pravidla automatizace. Oba způsoby volání PlayBook budou popsány níže.

## <a name="automate-threat-responses"></a>Automatizace odpovědí na hrozby

Vytvořili jste PlayBook a definovali Trigger, nastavili podmínky a nastavili akce, které bude trvat, a výstupy, které budou vytvořeny. Teď je potřeba určit kritéria, pod kterými se spustí, a nastavit mechanismus automatizace, který se spustí, když budou tato kritéria splněná.

### <a name="respond-to-incidents"></a>Reakce na incidenty

PlayBook můžete použít k reakci na **incident** vytvořením [pravidla automatizace](automate-incident-handling-with-automation-rules.md) , které se spustí při vygenerování incidentu, a pak bude volat PlayBook.

Vytvoření pravidla automatizace:

1. V okně **Automatizace** v navigační nabídce Azure Sentinel vyberte **vytvořit** v horní nabídce a pak **přidejte nové pravidlo**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Přidat nové pravidlo":::

1. Otevře se panel **vytvořit nové pravidlo automatizace** . Zadejte název pravidla.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Vytvoření pravidla automatizace":::

1. Pokud chcete, aby pravidlo automatizace vstoupilo v platnost jenom na určitá analytická pravidla, určete, která z nich mění podmínku **název pravidla analýzy** .

1. Přidejte všechny další podmínky, na které chcete, aby tato aktivace pravidla automatizace byla závislá. Klikněte na **Přidat podmínku** a v rozevíracím seznamu vyberte podmínky. Seznam podmínek je vyplněný podrobnostmi o výstrahách a polích identifikátoru entity.

1. Vyberte akce, které má pravidlo automatizace provést. K dispozici jsou tyto akce: **přiřadit vlastníka**, **změnit stav**, **změnit závažnost**, **Přidat značky** a **Spustit PlayBook**. Můžete přidat tolik akcí, kolik chcete.

1. Pokud přidáte akci **Spustit PlayBook** , zobrazí se výzva k výběru z rozevíracího seznamu dostupné playbooky. Z pravidel automatizace se dají spouštět jenom playbooky, které začínají **triggerem incidentu** , takže se v seznamu zobrazí jenom ty.

    > [!IMPORTANT]
    > Aby bylo možné spustit playbooky z pravidel automatizace, musí být pro službu Azure Sentinel udělená explicitní oprávnění. Pokud se v rozevíracím seznamu zobrazí PlayBook "zobrazeno šedě", znamená to, že Sentinel nemá oprávnění k této skupině prostředků PlayBook. Kliknutím na odkaz **Spravovat oprávnění PlayBook** přiřaďte oprávnění.
    > Na panelu **Spravovat oprávnění** , který se otevře, označte zaškrtávací políčka skupin prostředků obsahující playbooky, která chcete spustit, a klikněte na **použít**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Správa oprávnění":::
    > - Sami musíte mít oprávnění **vlastníka** pro libovolnou skupinu prostředků, ke které chcete udělit oprávnění Azure Sentinel, a musíte mít roli **Přispěvatel aplikace logiky** v jakékoli skupině prostředků obsahující playbooky, kterou chcete spustit.
    > - Pokud se PlayBook, který chcete spustit, nachází v jiném tenantovi, musíte ve víceklientském nasazení udělit oprávnění Azure Sentinel ke spuštění PlayBook v tenantovi PlayBook.
    >    1. V nabídce navigace v Azure Sentinel v tenantovi playbooky vyberte **Nastavení**.
    >    1. V okně **Nastavení** vyberte kartu **Nastavení** a pak rozšíření **oprávnění PlayBook** .
    >    1. Kliknutím na tlačítko **Konfigurovat oprávnění** otevřete panel **Spravovat oprávnění** , který je uvedený výše, a pokračujte podle pokynů uvedených tady.

1. Nastavte datum vypršení platnosti pravidla automatizace, pokud chcete, aby byl nějaký.

1. **Chcete-li určit** , kde v pořadí pravidel automatizace bude toto pravidlo spuštěno, zadejte číslo.

1. Klikněte na **Použít**. A je to hotové!

[Objevte další způsoby](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) vytváření pravidel automatizace.

### <a name="respond-to-alerts"></a>Reakce na výstrahy

PlayBook můžete použít k reakci na **výstrahu** vytvořením **pravidla analýzy** nebo úpravou stávající, která se spustí, když se výstraha vygeneruje, a v [Průvodci analytickým pravidlem](tutorial-detect-threats-custom.md)vybere vaše PlayBook jako automatickou odpověď.

1. V okně **Analýza** v navigační nabídce Azure Sentinel vyberte pravidlo analýzy, pro které chcete odpověď automatizovat, a v podokně podrobností klikněte na **Upravit** .

1. V **Průvodci pravidlo analýzy – na stránce Upravit existující pravidlo** vyberte kartu **automatizovaná odpověď** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Karta automatizovaná odpověď":::

1. Z rozevíracího seznamu vyberte PlayBook. Můžete zvolit více než jeden PlayBook, ale k dispozici bude jenom playbooky pomocí **triggeru výstrahy** .

1. Na kartě **Kontrola a vytvoření** vyberte **Uložit**.

## <a name="run-a-playbook-on-demand"></a>Spuštění playbooku na vyžádání

Můžete také spustit PlayBook na vyžádání.

 > [!NOTE]
 > Pouze playbooky pomocí **triggeru výstrahy** lze spustit na vyžádání.

Spuštění PlayBook na vyžádání:

1. Na stránce **incidenty** vyberte incident a klikněte na **Zobrazit úplné podrobnosti**.

2. Na kartě **výstrahy** klikněte na výstrahu, na které chcete spustit PlayBook, a posuňte se doprava a klikněte na **Zobrazit playbooky** a vyberte PlayBook, které chcete **Spustit** , ze seznamu dostupných playbooky v předplatném.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat pravidla playbooky a Automation ve službě Azure Sentinel k reakci na hrozby. 
- Přečtěte si, jak [aktivně získat hrozby](hunting.md) pomocí Azure Sentinel.
