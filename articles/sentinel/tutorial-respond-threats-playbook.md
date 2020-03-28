---
title: 'Kurz: Spuštění scénáře v Azure Sentinelu'
description: 'Kurz: Tento článek popisuje, jak spustit playbook v Azure Sentinelu.'
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
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585080"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Kurz: Nastavení automatických odpovědí na hrozby v Azure Sentinelu



Tento kurz vám pomůže používat scénáře zabezpečení v Azure Sentinelu k nastavení automatických reakcí na hrozby na problémy související se zabezpečením zjištěné Azure Sentinelem.


> [!div class="checklist"]
> * Porozumět playbookům
> * Vytvoření playbooku
> * Spuštění playbooku
> * Automatizace reakcí na hrozby


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co je scénář zabezpečení v Azure Sentinelu?

Playbook zabezpečení je kolekce procedur, které lze spustit z Azure Sentinelu v reakci na výstrahu. Playbook zabezpečení může pomoci automatizovat a organizovat odpověď a lze spustit ručně nebo nastavit, aby se spouštěla automaticky při aktivaci konkrétních výstrah. Scénáře zabezpečení ve Službě Azure Sentinel jsou založené na [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), což znamená, že získáte veškerou výkon, přizpůsobitelnost a integrované šablony logic apps. Každý playbook je vytvořen pro konkrétní předplatné, které zvolíte, ale když se podíváte na stránku Playbook, uvidíte všechny playbooky ve všech vybraných předplatných.

> [!NOTE]
> Playbooky využívají Azure Logic Apps, proto se účtují poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Pokud se například obáváte, že k vašim síťovým prostředkům přistupují útočníci se zlými úmysly, můžete nastavit výstrahu, která vyhledá škodlivé adresy IP přistupující k síti. Potom můžete vytvořit playbook, který provádí následující akce:
1. Po aktivaci výstrahy otevřete lístek v ServiceNow nebo v jiném systému it ticketingu.
2. Odešlete zprávu do kanálu operací zabezpečení v Microsoft Teams nebo Slack, abyste se ujistili, že vaši analytici zabezpečení jsou o incidentu informováni.
3. Odešlete všechny informace ve výstraze svému vedoucímu správci sítě a správci zabezpečení. E-mailová zpráva obsahuje také dvě uživatelská přepínače **Blokovat** nebo **Ignorovat**.
4. Playbook pokračuje v běhu po přijetí odpovědi od správců.
5. Pokud správci zvolí **Blokovat**, IP adresa se v bráně firewall zablokuje a uživatel je ve službě Azure AD zakázaný.
6. Pokud správci zvolte **Ignorovat**, výstraha se zavře v Azure Sentinelu a incident je uzavřen v ServiceNow.

Bezpečnostní playbooky lze spustit ručně nebo automaticky. Ruční spuštění znamená, že když dostanete výstrahu, můžete spustit playbook na vyžádání jako odpověď na vybranou výstrahu. Jejich automatické spuštění znamená, že při vytváření pravidla korelace nastavíte automatické spuštění jednoho nebo více playbooků při aktivaci výstrahy.


## <a name="create-a-security-playbook"></a>Vytvoření scénáře zabezpečení

Podle těchto kroků vytvořte v Azure Sentinelu nový playbook zabezpečení:

1. Otevřete řídicí panel **Azure Sentinelu.**
2. V části **Správa**vyberte **Playbooky**.

   ![Aplikace logiky](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na stránce **Azure Sentinel – playbooky** klikněte na **tlačítko Přidat.**

   ![Vytvoření aplikace logiky](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na stránce **Vytvořit aplikaci Logika** zadejte požadované informace k vytvoření nové aplikace logiky a klikněte na **Vytvořit**. 

5. V [**Návrháři aplikace logiky**](../logic-apps/logic-apps-overview.md)vyberte šablonu, kterou chcete použít. Pokud vyberete šablonu, která vyžaduje pověření, budete je muset poskytnout. Případně můžete vytvořit nový prázdný playbook od začátku. Vyberte **prázdnou aplikaci logiky**. 

   ![Návrhář aplikace logiky](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Budete převedena do Návrháře aplikací logiky, kde můžete buď vytvořit nový nebo upravit šablonu. Další informace o vytvoření playbooku pomocí [logic apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Pokud vytváříte prázdný playbook, v **poli Hledat všechny konektory a aktivační události** zadejte Azure *Sentinel*a vyberte **Při aktivaci odpovědi na výstrahu Azure Sentinelu**. <br>Po jeho vytvoření se nový playbook zobrazí v seznamu **Playbook.** Pokud se nezobrazí, klikněte na **Aktualizovat**.

1. Použijte funkce **Získat entity,** které umožňují získat příslušné entity ze seznamu **Entity,** jako jsou účty, IP adresy a hostitelé. To vám umožní spouštět akce na konkrétní entity.

7. Teď můžete definovat, co se stane po aktivaci playbooku. Můžete přidat akci, logickou podmínku, přepnutí podmínek případu nebo smyčky.

   ![Návrhář aplikace logiky](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak spustit playbook zabezpečení

Playbook můžete spustit na vyžádání.

Spuštění playbooku na vyžádání:

1. Na stránce **incidentů** vyberte incident a klikněte na **Zobrazit úplné podrobnosti**.

2. Na kartě **Upozornění** klikněte na upozornění, na kterém chcete playbook spustit, a posuňte se úplně doprava a klikněte na **Zobrazit playbooky** a vyberte playbook, který chcete **spustit** ze seznamu dostupných playbooků v předplatném. 



## <a name="automate-threat-responses"></a>Automatizace reakcí na hrozby

Týmy SIEM/SOC mohou být pravidelně zaplaveny bezpečnostními výstrahami. Objem vygenerovaných výstrah je tak obrovský, že dostupní správci zabezpečení jsou zahlceni. To má za následek až příliš často v situacích, kdy mnoho výstrah nelze vyšetřit, takže organizace je zranitelná vůči útokům, které zůstávají bez povšimnutí. 

Mnoho, ne-li většina z těchto výstrah odpovídá opakujícím se vzorům, které lze řešit konkrétními a definovanými nápravnámi akcemi. Azure Sentinel už umožňuje definovat nápravu v playbookech. Je také možné nastavit automatizaci v reálném čase jako součást definice playbooku, která vám umožní plně automatizovat definovanou odpověď na konkrétní výstrahy zabezpečení. Pomocí automatizace v reálném čase mohou týmy pro odezvu výrazně snížit své pracovní zatížení tím, že plně zautomatizují rutinní odpovědi na opakující se typy výstrah, což vám umožní soustředit se více na jedinečné výstrahy, analyzovat vzory, lov hrozeb a další.

Automatizace odpovědí:

1. Vyberte výstrahu, pro kterou chcete odpověď automatizovat.
1. Na stránce **Upravit pravidlo výstrah** v části **Automatizace v reálném čase**zvolte **spouštěcí playbook,** který chcete spustit, když se toto pravidlo výstrahy shoduje.
1. Vyberte **Uložit**.

   ![automatizace v reálném čase](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak spustit playbook v Azure Sentinelu. Pokračujte tak, [jak proaktivně lovit hrozby](hunting.md) pomocí Azure Sentinelu.


