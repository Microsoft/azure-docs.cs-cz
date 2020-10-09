---
title: 'Kurz: spuštění PlayBook ve službě Azure Sentinel'
description: Tento kurz vám pomůžete používat playbooky zabezpečení v Azure Sentinel k nastavení automatizovaných odpovědí na hrozby pro problémy související se zabezpečením.
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
ms.openlocfilehash: 0e7d790fa9c5d4052ddb1e20defed1d1cc457a2e
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840196"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Kurz: nastavení automatických odpovědí na hrozby v Azure Sentinel



Tento kurz vám pomůže využít playbooky zabezpečení ve službě Azure Sentinel k nastavení automatizovaných odpovědí na hrozby pro problémy související se zabezpečením zjištěné službou Azure Sentinel.


> [!div class="checklist"]
> * Principy playbooky
> * Vytvoření PlayBook
> * Spustit PlayBook
> * Automatizace odpovědí na hrozby


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co je PlayBook zabezpečení v Azure Sentinel?

PlayBook zabezpečení je kolekce procedur, které je možné spustit z Azure Sentinel v reakci na výstrahu. PlayBook zabezpečení může přispět k automatizaci a orchestraci vaší odpovědi a dá se spustit ručně nebo nastavit tak, aby se spouštěla automaticky, když se aktivují konkrétní výstrahy. Playbooky zabezpečení ve službě Azure Sentinel vychází z [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), což znamená, že získáte všechny možnosti napájení, možností úprav a předdefinovaných šablon Logic Apps. Každý PlayBook se vytvoří pro konkrétní předplatné, které jste si zvolili, ale při zobrazení na stránce Playbooky se zobrazí všechna playbooky v rámci všech vybraných předplatných.

> [!NOTE]
> Playbooky využívá Azure Logic Apps, proto se účtují poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Pokud máte například obavy týkající se škodlivých útočníků, kteří přistupují k síťovým prostředkům, můžete nastavit výstrahu, která vyhledává škodlivé IP adresy, které přistupují k vaší síti. Pak můžete vytvořit PlayBook, který provede následující akce:
1. Když se aktivuje výstraha, otevřete lístek v ServiceNow nebo jakémkoli jiném systému pro lístkování IT.
2. Odešlete zprávu na kanál operací zabezpečení v Microsoft Teams nebo časové rezervy, abyste se ujistili, že analytikům zabezpečení ví o incidentu.
3. Odešlete všechny informace v výstraze správci a správci zabezpečení vaší hlavní sítě. E-mailová zpráva obsahuje také dva přepínače uživatele, které **blokují** nebo **ignorují**.
4. PlayBook bude nadále běžet po přijetí odpovědi od správců.
5. Pokud správci zvolí **blok**, IP adresa je zablokovaná v bráně firewall a uživatel je ve službě Azure AD zakázaný.
6. Pokud správci zvolí možnost **Ignorovat**, výstraha se uzavře v rámci Azure Sentinel a incident je uzavřený v ServiceNow.

Playbooky zabezpečení je možné spustit ručně nebo automaticky. Ruční spouštění znamená, že když obdržíte výstrahu, můžete spustit PlayBook na vyžádání jako reakci na vybranou výstrahu. Když je spouštíte automaticky, znamená to, že při vytváření pravidla korelace nastavíte, aby se při aktivaci výstrahy automaticky spouštěla jedna nebo více playbooky.


## <a name="create-a-security-playbook"></a>Vytvoření PlayBook zabezpečení

Pomocí těchto kroků vytvořte nové PlayBook zabezpečení v Azure Sentinel:

1. Otevřete řídicí panel **Azure Sentinel** .
2. V části **Konfigurace**vyberte **playbooky**.

   ![Aplikace logiky](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na stránce **Azure Sentinel-playbooky** klikněte na tlačítko **Přidat** .

   ![Vytvoření aplikace logiky](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na stránce **vytvořit aplikaci logiky** zadejte požadované informace pro vytvoření nové aplikace logiky a klikněte na **vytvořit**. 

5. V [**Návrháři aplikace logiky**](../logic-apps/logic-apps-overview.md)vyberte šablonu, kterou chcete použít. Pokud vyberete šablonu, která vyžaduje přihlašovací údaje, budete je muset zadat. Alternativně můžete vytvořit nové prázdné PlayBook od začátku. Vyberte **prázdnou aplikaci logiky**. 

   ![Snímek obrazovky, který zobrazuje panel prázdné aplikace logiky](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Přejdete do návrháře aplikace logiky, kde můžete vytvořit novou nebo upravit šablonu. Další informace o vytvoření PlayBook s [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Pokud vytváříte prázdné PlayBook, zadejte do pole **Prohledat všechny konektory a triggery** *Azure Sentinel*a vyberte, **když se aktivuje odpověď na výstrahu Sentinel Azure**. <br>Po vytvoření se nový PlayBook zobrazí v seznamu **playbooky** . Pokud se nezobrazí, klikněte na tlačítko **aktualizovat**.

1. Použijte funkce **získat entity** , které vám umožní získat relevantní entity ze seznamu **entit** , jako jsou účty, IP adresy a hostitelé. To vám umožní spouštět akce u konkrétních entit.

7. Teď můžete definovat, co se stane po aktivaci playbooku. Můžete přidat akci, logickou podmínku, podmínky případu přepínače nebo smyčky.

   ![Návrhář aplikace logiky](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak spustit PlayBook zabezpečení

Můžete spustit PlayBook na vyžádání.

Spuštění PlayBook na vyžádání:

1. Na stránce **incidenty** vyberte incident a klikněte na **Zobrazit úplné podrobnosti**.

2. Na kartě **výstrahy** klikněte na výstrahu, na které chcete spustit PlayBook, a posuňte se doprava a klikněte na **Zobrazit playbooky** a vyberte PlayBook, které chcete **Spustit** , ze seznamu dostupných playbooky v předplatném. 



## <a name="automate-threat-responses"></a>Automatizace odpovědí na hrozby

SIEM/SOC týmy můžou být pravidelně inundated s výstrahami zabezpečení. Objem vygenerovaných výstrah je tak velký, takže správci zabezpečení jsou k dispozici zahlcení. To je velmi často v situacích, kdy se nedá prozkoumat mnoho výstrah, což je ponecháno v organizaci zranitelné vůči útokům, které jsou nepatrné. 

Mnoho z těchto výstrah, pokud není většina, je v souladu s opakovanými vzorci, které lze řešit pomocí konkrétních a definovaných opravných akcí. Sentinel Azure už umožňuje definovat svou nápravu v playbooky. V rámci definice PlayBook je také možné nastavit automatizaci v reálném čase, která vám umožní plně automatizovat definovanou odpověď na konkrétní výstrahy zabezpečení. Díky automatizaci v reálném čase můžou odpovědní týmy významně snižovat své úlohy tím, že plně automatizují rutinní reakce na opakované typy výstrah, což vám umožní soustředit se na jedinečné výstrahy, analyzovat vzory, lovecké hrozby a další informace.

Automatizace odpovědí:

1. Vyberte výstrahu, pro kterou chcete odpověď automatizovat.
1. Na stránce **Upravit pravidlo výstrahy** v části **automatizace v reálném čase**vyberte **aktivovaný PlayBook** , který chcete spustit, když se toto pravidlo výstrahy shoduje.
1. Vyberte **Uložit**.

   ![automatizace v reálném čase](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak spustit PlayBook ve službě Azure Sentinel. Pokračujte na to, [jak proaktivně](hunting.md) procházet s hrozbami pomocí Azure Sentinel.


