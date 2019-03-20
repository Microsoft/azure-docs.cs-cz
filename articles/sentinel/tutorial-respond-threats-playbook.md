---
title: Spuštění playbooku ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak spuštění playbooku v ověřovacích Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5ce409b984ab9110a8ca44f47675045e4d80aed5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088634"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Kurz: Nastavte si odpovědi automatizované hrozeb v Azure Preview Sentinelu

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu vám umožní pomocí playbooků zabezpečení v Azure Sentinelu nastavit automatizované hrozeb odpovědi na otázky související se zabezpečením detekovaných službou Azure Sentinelu.


> [!div class="checklist"]
> * Vysvětlení playbooky
> * Vytvořte playbook
> * Spuštění playbooku


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co je playbook zabezpečení ve službě Azure Sentinelu?

Playbook zabezpečení je kolekce procedur, které lze spustit z Azure Sentinelu v reakci na výstrahy. Playbook zabezpečení může pomoct automatizovat a orchestrovat reakci a můžete spustit ručně nebo ji nastavit na automatické spuštění při aktivaci určitých výstrah. Playbooky zabezpečení ve službě Azure Sentinelu jsou založeny na [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), což znamená, že získáte veškerý výkon, přizpůsobitelnosti a integrované šablony Logic Apps. Každý playbook se vytvoří pro konkrétní předplatné, které zvolíte, ale když se podíváte na stránce Playbooky, uvidíte všechny playbooky ve všech vybraných předplatných.

> [!NOTE]
> Playbooky využívají Azure Logic Apps, proto poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Například pokud máte obavy o útočníkům přístup k síťovým prostředkům, můžete nastavit upozornění, která hledá škodlivé IP adresy, přístupu k síti. Potom můžete vytvořit playbook, který provede následující akce:
1. Když se aktivuje upozornění, otevření lístku ve ServiceNow nebo jakékoli jiné IT systém lístkování.
2. Odešlete zprávu do vašeho kanálu činností zabezpečení v Microsoft Teams nebo Slack, abyste měli jistotu, že vaše analytikům zabezpečení uvědomte o incidentu.
3. Odešlete všechny informace ve výstraze správci vedoucí sítě správu a zabezpečení. E-mailové zprávě také zahrnuje dvě tlačítka – možnost uživatele **bloku** nebo **Ignorovat**.
4. Playbook dále běží i poté přijata odpověď od správce.
5. Pokud správce možnost **bloku**, zablokování IP adresy v bráně firewall a uživatel je zakázaný ve službě Azure AD.
6. Pokud správce možnost **Ignorovat**, výstraha je uzavřena v ověřovacích Azure a je uzavřený incident v ServiceNow.

Playbooky zabezpečení můžete spustit ručně nebo automaticky. Ruční spuštění znamená, že když obdržíte výstrahu, můžete spustit playbook na vyžádání jako odpověď na vybranou výstrahu. Je spuštěna automaticky znamená, že při vytváření pravidla korelace, můžete ho nastavit na automaticky spustit playbooky jeden nebo více, když se aktivuje upozornění.


## <a name="create-a-security-playbook"></a>Vytvořit playbook zabezpečení

Postupujte podle těchto kroků a vytvořte nový playbook zabezpečení v Azure Sentinelu:

1. Otevřít **Azure Sentinelu** řídicího panelu.
2. V části **správu**vyberte **Playbooky**.

   ![Aplikace logiky](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. V **Azure Sentinelu – Playbooky (Preview)** klikněte na **přidat** tlačítko.

   ![Vytvoření aplikace logiky](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. V **vytvořit aplikaci logiky** zadejte požadované informace pro vytvoření nové aplikace logiky a klikněte na tlačítko **vytvořit**. 

5. V [ **návrhář aplikace logiky**](../logic-apps/logic-apps-overview.md), vyberte šablonu, kterou chcete použít. Pokud vyberete šablonu, která vyžaduje přihlašovací údaje, bude mít k jejich zadání. Alternativně můžete vytvořit nový playbook prázdné úplně od začátku. Vyberte **prázdná aplikace logiky**. 

   ![Návrhář aplikace logiky](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Budete přesměrováni na Návrhář aplikace logiky, ve kterém můžete vytvářet nové nebo upravit šablony. Další informace o vytváření playbook s [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Při vytváření prázdné playbook v **prohledat všechny konektory a triggery** zadejte *Azure Sentinelu*a vyberte **po aktivovanýchodpovědinaupozorněníslužbyAzureSentinelu**. <br>Po vytvoření je nový playbook zobrazí v **Playbooky** seznamu. Pokud se nezobrazí, klikněte na tlačítko **aktualizovat**. 

7. Teď můžete definovat, co se stane po aktivaci playbooku. Můžete přidat akci, logickou podmínku, podmínky případu přepínače nebo smyčky.

   ![Návrhář aplikace logiky](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak spustit playbook zabezpečení

Playbooku můžete spouštět na vyžádání.

Pokud chcete spustit playbook na vyžádání:

1. V **případů** stránce vyberte případ a klikněte na **zobrazit úplné podrobnosti**.

2. V **výstrahy** kartu, klikněte na výstrahu, kterou chcete spustit playbook a přejděte úplně vpravo a klikněte na tlačítko **zobrazit playbooky** a vyberte playbook pro **spustit** z seznam dostupných playbooky na předplatné. 




## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ke spuštění playbooku v ověřovacích Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích: V tomto kurzu jste zjistili, jak ke spuštění playbooku v ověřovacích Azure. Dál [jak proaktivně hunt hrozby](hunting.md) pomocí ověřovacích Azure.
> [!div class="nextstepaction"]
> [Nejlepší hrozby](hunting.md) k proaktivnímu ke zjištění hrozeb v síti.

