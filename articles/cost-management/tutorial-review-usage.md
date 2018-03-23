---
title: Kurz – Kontrola využití a nákladů ve službě Azure Cost Management | Microsoft Docs
description: V tomto kurzu zkontrolujete využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet výstrahy.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 558dcd65051c0134a87205dcd8bbf432d7763fd2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Kurz: Kontrola využití a nákladů

Azure Cost Management zobrazuje využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění. Všechna data o využití a nákladech se zobrazí na řídicích panelech a v sestavách Cloudyn. Příklady v tomto kurzu vás provedou kontrolou využití a nákladů prostřednictvím řídicích panelů a sestav. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Sledování trendů využití a nákladů
> * Zjišťovat nedostatečnou efektivitu využití
> * Vytvářet upozornění na nedostatečné nebo nadměrné výdaje

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít zaregistrovanou zkušební verzi nebo placené předplatné služby Azure Cost Management.

## <a name="open-the-cloudyn-portal"></a>Otevření portálu Cloudyn

Všechna data o využití a nákladech můžete zkontrolovat na portálu Cloudyn. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.

## <a name="track-usage-and-cost-trends"></a>Sledování trendů využití a nákladů

Pomocí průběžných sestav můžete sledovat částky utracené za využití a náklady. Pokud si chcete prohlédnout trendy, použijte sestavu Actual Cost Over Time (Skutečné náklady v průběhu času). V nabídce sestav v horní části portálu klikněte na položky **Cost (Náklady)** > **Cost Analysis (Analýza nákladů)** > **Actual Cost Over Time (Skutečné náklady v průběhu času)**. Když sestavu otevřete poprvé, nejsou v ní použité žádné skupiny ani filtry.

Toto je příklad sestavy:

![Příklad sestavy](./media/tutorial-review-usage/actual-cost01.png)

Sestava obsahuje veškeré výdaje za posledních 30 dnů. Pokud chcete zobrazit jenom výdaje za služby Azure, vyberte skupinu Service (Služba) a potom vyfiltrujte všechny služby Azure. Následující obrázek ukazuje vyfiltrované služby.

![Vyfiltrované služby](./media/tutorial-review-usage/actual-cost02.png)

V předchozím příkladu se od 31. 8. 2017 utratilo méně peněz než předtím. Tento trend pokračuje pro různé služby zhruba devět dnů. Potom pokračují další výdaje stejně jako předtím. Příliš mnoho sloupců ale může ztížit odhalení zjevného trendu. Zobrazení sestavy můžete změnit na spojnicový nebo plošný graf, které znázorňují data jiným způsobem. Z následujícího obrázku je trend lépe patrný.

![Trend v sestavě](./media/tutorial-review-usage/actual-cost03.png)

V příkladu je jasně vidět, že od 31. 8. 2017 klesly náklady na Azure Storage, zatímco výdaje na ostatní služby Azure zůstaly stejné. Co tedy toto snížení výdajů způsobilo? V tomto příkladu došlo k tomu, že někteří zaměstnanci byli na dovolené a službu Storage nepoužívali.

Pokud chcete zhlédnout výukové video o sledování trendů využití a nákladů, podívejte se na video [Analyzing your cloud billing data vs. time with Azure Cost Management](https://youtu.be/7LsVPHglM0g) (Analýza informací o fakturaci cloudu pomocí služby Azure Cost Management).

## <a name="detect-usage-inefficiencies"></a>Zjišťovat nedostatečnou efektivitu využití

Optimalizační sestavy umožňují zvýšit efektivitu, optimalizovat využití a najít způsoby, jak ušetřit výdaje za cloudové prostředky. Jsou užitečné hlavně kvůli doporučením nákladově efektivního nastavení velikosti, která mají pomoci snížit počty nečinných nebo nákladných virtuálních počítačů.

Když organizace poprvé přesunou prostředky do cloudu, často se setkávají s potížemi, které jsou výsledkem jejich virtualizační strategie. Často používají podobný přístup, jako když vytvářely virtuální počítače pro místní prostředí virtualizace. Potom předpokládají, že už přesunem místních virtuálních počítačů do cloudu dojde ke snížení nákladů. Tento přístup ale náklady pravděpodobně nesníží.

Potíž je v tom, že za svoji stávající infrastrukturu už zaplatily. Uživatelé předtím mohli vytvářet velké virtuální počítače a nechávat je spuštěné, ať už nečinné, nebo ne, a mělo to jen minimální následky. Přesun velkých nebo nečinných virtuálních počítačů do cloudu ale náklady pravděpodobně spíš *zvýší*. Po uzavření smluv s poskytovateli cloudových služeb je důležité přidělení nákladů k prostředkům. Musíte zaplatit za všechno, co potvrdíte, ať už daný prostředek plně využíváte, nebo ne.

Sestava Cost Effective Sizing Recommendations (Doporučení nákladově efektivního nastavení velikosti) určuje potenciální roční úspory tak, že porovnává kapacitu typů instancí virtuálních počítačů s historickými daty o využití CPU a paměti.  

V nabídce sestav v horní části portálu klikněte na **Optimizer (Optimalizace)** > **Pricing Optimization (Optimalizace cen)** > **Cost Effective Sizing Recommendations (Doporučení nákladově efektivního nastavení velikosti)**. Filtrujte poskytovatele podle Azure, aby se zobrazily jenom virtuální počítače Azure. Následuje příklad.

![Virtuální počítače Azure](./media/tutorial-review-usage/sizing01.png)

Podle tohoto příkladu je možné díky doporučením na změnu typů instancí virtuálních počítačů ušetřit 3114 USD. První doporučení zobrazíte kliknutím na symbol plus (+) ve sloupci **Details** (Podrobnosti). Toto jsou podrobnosti prvního doporučení.

![Podrobnosti doporučení](./media/tutorial-review-usage/sizing02.png)

Kliknutím na symbol plus vedle položky **List of Candidates** (Seznam kandidátů) zobrazíte ID instancí virtuálních počítačů.

![Seznam kandidátů](./media/tutorial-review-usage/sizing03.png)

Pokud chcete zhlédnout výukové video o zjišťování nedostatečné efektivity využití, podívejte se na video [Optimizing VM Size in Azure Cost Management](https://youtu.be/1xaZBNmV704) (Optimalizace velikosti virtuálního počítače ve službě Azure Cost Management).

## <a name="create-alerts-for-unusual-spending"></a>Vytváření upozornění na neobvyklé výdaje

Ostatní účastníky můžete automaticky upozorňovat na anomálie ve výdajích a rizika nadměrných výdajů. Upozornění můžete snadno a rychle vytvořit pomocí sestav, které podporují výstrahy na základě prahových hodnot rozpočtu a nákladů.

Upozornění na jakékoli výdaje můžete vytvořit pomocí kterékoli sestavy nákladů. V tomto příkladu budete pomocí sestavy Actual Cost Over Time (Skutečné náklady v průběhu času) upozorňovat na to, že se výdaje blíží k celkové výši rozpočtu. V nabídce sestav v horní části portálu klikněte na položky **Cost (Náklady)** > **Cost Analysis (Analýza nákladů)** > **Actual Cost Over Time (Skutečné náklady v průběhu času)**. U položky **Groups** (Skupiny) nastavte hodnotu **Service** (Služba) a u položky **Filter on the service** (Filtrovat podle služby) hodnotu **Azure/VM** (Azure / virtuální počítač). V pravé horní části sestavy klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu).

Na kartě **Scheduling** (Plánování) si naplánujte, že se vám má sestava s požadovanou frekvencí zasílat e-mailem. Součástí sestavy v e-mailu budou všechny značky, seskupení a filtry, které jste použili. Klikněte na kartu **Threshold** (Prahová hodnota) a vyberte **Actual Cost vs. Threshold** (Skutečné náklady vs. prahová hodnota). Pokud jste měli celkový rozpočet 500 000 USD a chtěli jste dostat oznámení, když se náklady přiblíží polovině, vytvořte výstrahu **Red alert** (Červená výstraha) na částce 250 000 USD a výstrahu **Yellow alert** (Žlutá výstraha) na částce 240 000 USD. Potom vyberte počet po sobě jdoucích výstrah. Až dostanete celkový počet výstrah, který jste zadali, už vám žádné další výstrahy chodit nebudou. Uložte naplánovanou sestavu.

![Příklad sestavy](./media/tutorial-review-usage/schedule-alert01.png)

K vytváření výstrah můžete také vybrat metriku Cost Percentage vs. Budget (Procento nákladů z rozpočtu). Pokud použijete tuto metriku, budou se místo částek zobrazovat procenta z rozpočtu.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Sledování trendů využití a nákladů
> * Zjišťovat nedostatečnou efektivitu využití
> * Vytvářet upozornění na nedostatečné nebo nadměrné výdaje


V následujícím kurzu se dozvíte, jak na základě historických dat předpovědět budoucí výdaje.

> [!div class="nextstepaction"]
> [Odhad budoucích výdajů](tutorial-forecast-spending.md)
