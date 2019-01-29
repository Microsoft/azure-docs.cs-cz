---
title: Kurz – Kontrola využití a nákladů pomocí Cloudyn v Azure | Microsoft Docs
description: V tomto kurzu zkontrolujete využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: e240cbadbee87938107c4a9bf034c47db42acf92
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098642"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Kurz: Kontrola využití a nákladů

Cloudyn zobrazuje využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění. Všechna data o využití a nákladech se zobrazí na řídicích panelech a v sestavách Cloudyn. Příklady v tomto kurzu vás provedou kontrolou využití a nákladů prostřednictvím řídicích panelů a sestav.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](overview-cost-mgt.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Sledovat trendy využití a nákladů
> * Zjišťovat nedostatečnou efektivitu využití
> * Vytvářet upozornění na neobvyklé nebo nadměrné výdaje
> * Exportovat data

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít buď zaregistrovanou zkušební verzi, nebo placené předplatné Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Otevření portálu Cloudyn

Všechna data o využití a nákladech můžete zkontrolovat na portálu Cloudyn. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.

## <a name="track-usage-and-cost-trends"></a>Sledovat trendy využití a nákladů

Pomocí průběžných sestav můžete sledovat částky utracené za využití a náklady. Pokud si chcete prohlédnout trendy, použijte sestavu Actual Cost Over Time (Skutečné náklady v průběhu času). V levé horní části portálu klikněte na **Costs** > **Cost Analysis** > **Actual Cost Over Time** (Náklady > Analýza nákladů > Skutečné náklady v průběhu času). Když sestavu otevřete poprvé, nejsou v ní použité žádné skupiny ani filtry.

Toto je příklad sestavy:

![Příklad sestavy Actual Cost Over Time](./media/tutorial-review-usage/actual-cost01.png)

Sestava obsahuje veškeré výdaje za posledních 30 dnů. Pokud chcete zobrazit jenom výdaje za služby Azure, vyberte skupinu Service (Služba) a potom vyfiltrujte všechny služby Azure. Následující obrázek ukazuje vyfiltrované služby.

![Příklad zobrazují se filtrované služeb Azure](./media/tutorial-review-usage/actual-cost02.png)

V předchozím příkladu se od 29. 10. 2018 snížily výdaje. Příliš mnoho sloupců ale může ztížit odhalení zjevného trendu. Zobrazení sestavy můžete změnit na spojnicový nebo plošný graf, které znázorňují data jiným způsobem. Z následujícího obrázku je trend lépe patrný.

![Příklad zobrazující klesající trend nákladů virtuálních počítačů Azure](./media/tutorial-review-usage/actual-cost03.png)

Na stejném příkladu si můžete všimnout snížení nákladů na virtuální počítače Azure. Ve stejný den se začaly snižovat i náklady na ostatní služby Azure. Co tedy toto snížení výdajů způsobilo? V tomto příkladu se dokončil velký pracovní projekt, takže se snížilo i využití řady služeb Azure.

Pokud chcete zhlédnout výukové video o sledování trendů využití a nákladů, podívejte se na video [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analýza informací o fakturaci cloudu pomocí Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Zjišťovat nedostatečnou efektivitu využití

Optimalizační sestavy umožňují zvýšit efektivitu, optimalizovat využití a najít způsoby, jak ušetřit výdaje za cloudové prostředky. Jsou užitečné hlavně kvůli doporučením nákladově efektivního nastavení velikosti, která mají pomoci snížit počty nečinných nebo nákladných virtuálních počítačů.

Když organizace poprvé přesunou prostředky do cloudu, často se setkávají s potížemi, které jsou výsledkem jejich virtualizační strategie. Často používají podobný přístup, jako když vytvářely virtuální počítače pro místní prostředí virtualizace. Potom předpokládají, že už přesunem místních virtuálních počítačů do cloudu dojde ke snížení nákladů. Tento přístup ale náklady pravděpodobně nesníží.

Potíž je v tom, že za svoji stávající infrastrukturu už zaplatily. Uživatelé předtím mohli vytvářet velké virtuální počítače a nechávat je spuštěné, ať už nečinné, nebo ne, a mělo to jen minimální následky. Přesun velkých nebo nečinných virtuálních počítačů do cloudu ale náklady pravděpodobně spíš *zvýší*. Po uzavření smluv s poskytovateli cloudových služeb je důležité přidělení nákladů k prostředkům. Musíte zaplatit za všechno, co potvrdíte, ať už daný prostředek plně využíváte, nebo ne.

Sestava Cost Effective Sizing Recommendations (Doporučení nákladově efektivního nastavení velikosti) určuje potenciální roční úspory tak, že porovnává kapacitu typů instancí virtuálních počítačů s historickými daty o využití CPU a paměti.  

V nabídce v horní části portálu klikněte na **Optimizer** > **Sizing Optimization** > **Cost Effective Sizing Recommendations** (Optimalizace > Optimalizace velikosti > Doporučení nákladově efektivního nastavení velikosti). V případě potřeby použijte filtr ke snížení počtu výsledků. Následuje příklad.

![Náklady na efektivní velikosti sestava doporučení pro virtuální počítače Azure](./media/tutorial-review-usage/sizing01.png)

Podle tohoto příkladu je možné díky doporučením na změnu typů instancí virtuálních počítačů ušetřit 2 382 USD. První doporučení zobrazíte kliknutím na symbol plus (+) ve sloupci **Details** (Podrobnosti). Toto jsou podrobnosti prvního doporučení.

![Podrobnosti o doporučení zobrazující příklad](./media/tutorial-review-usage/sizing02.png)

Kliknutím na symbol plus vedle položky **List of Candidates** (Seznam kandidátů) zobrazíte ID instancí virtuálních počítačů.

![Příklad zobrazující seznam kandidátů virtuálního počítače pro změnu velikosti](./media/tutorial-review-usage/sizing03.png)

Pokud chcete zhlédnout výukové video o zjišťování nedostatečné efektivity využití, podívejte se na video [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Optimalizace velikosti virtuálního počítače v Cloudyn).

Azure Cost Management poskytuje doporučení pro úsporu nákladů i pro služby Azure. Další informace najdete v tématu [kurzu: Optimalizace nákladů od doporučení](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Vytváření upozornění na neobvyklé výdaje

Výstrahy umožňují automaticky zasílání oznámení účastníkům útraty anomálie a rizika nadměrných výdajů. Můžete vytvořit výstrah pomocí sestavy, že podporují výstrahy na základě rozpočtu a prahových hodnot nákladů.

V tomto příkladu **Actual Cost Over Time** sestavy k odesílání oznámení, pokud vaší útraty na Virtuálním počítači Azure přiblíží celkový rozpočet. V tomto scénáři máte celkový rozpočet 20 000 a chcete dostávat oznámení, když náklady se začali přibližovat polovinu vašemu rozpočtu, 9 000 $ a další výstrahy po náklady na 10 000 USD.

1. V nabídce v horní části portálu Cloudyn, zvolte **náklady** > **Cost Analysis** > **Actual Cost Over Time**. 
2. U položky **Groups** (Skupiny) nastavte hodnotu **Service** (Služba) a u položky **Filter on the service** (Filtrovat podle služby) hodnotu **Azure/VM** (Azure / virtuální počítač). 
3. V horní části přímo ze sestavy, vyberte **akce** a pak vyberte **Naplánování sestavy**.
4. Pošlete sami sobě e-mail sestavy v naplánovaných intervalech, vyberte **plánování** kartu **uložit nebo naplánovat tuto** dialogové okno sestavy. Nezapomeňte vybrat možnost **Send via email** (Odeslat e-mailem). Všechny značky, seskupení a filtry, které jste použití jsou zahrnuty v e-mailem sestavu. 
5. Vyberte **prahová hodnota** kartu a potom vyberte **Actual Cost vs. Threshold** (Skutečné náklady vs. prahová hodnota). 
   1. V **červenou výstrahu** prahovou hodnotu zadejte 10000. 
   2. V **žlutá výstraha** zadejte 9000 prahovou hodnotu. 
   3. V **počet po sobě jdoucích výstrah** zadejte počet po sobě jdoucích výstrah pro příjem. Až dostanete celkový počet výstrah, který jste zadali, už vám žádné další výstrahy chodit nebudou. 
6. Vyberte **Uložit**.

![Příklad zobrazující červené a žluté výstrahy podle útraty prahové hodnoty](./media/tutorial-review-usage/schedule-alert01.png)

Můžete také **metriku Cost Percentage vs. Rozpočet** prahová hodnota metriky vytvářet upozornění. To umožňuje zadejte prahové hodnoty jako procenta z rozpočtu místo částek.

## <a name="export-data"></a>Exportovat data

Podobným způsobem, jako pro sestavy vytváříte upozornění, můžete také z jakékoli sestavy exportovat data. Například můžete chtít exportovat seznam účtů služby Cloudyn nebo jiná uživatelská data. Pokud chcete exportovat sestavu, otevřete ji a pak v pravé horní části sestavy klikněte na **Actions** (Akce). Mezi akce, které můžete chtít provést, patří **Export all report data** (Exportovat všechna data sestavy), abyste mohli informace stáhnout nebo vytisknout. Nebo můžete vybrat možnost **Schedule report** (Naplánovat sestavu) a naplánovat odeslání sestavy v e-mailu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Sledovat trendy využití a nákladů
> * Zjišťovat nedostatečnou efektivitu využití
> * Vytvářet upozornění na neobvyklé nebo nadměrné výdaje
> * Exportovat data


V následujícím kurzu se dozvíte, jak na základě historických dat předpovědět budoucí útratu.

> [!div class="nextstepaction"]
> [Odhad budoucí útraty](tutorial-forecast-spending.md)
