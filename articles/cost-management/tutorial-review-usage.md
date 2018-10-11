---
title: Kurz – Kontrola využití a nákladů pomocí Cloudyn v Azure | Microsoft Docs
description: V tomto kurzu zkontrolujete využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 72eaa6f085581f34b696a946e2168eceaa21a849
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987709"
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

![Příklad sestavy](./media/tutorial-review-usage/actual-cost01.png)

Sestava obsahuje veškeré výdaje za posledních 30 dnů. Pokud chcete zobrazit jenom výdaje za služby Azure, vyberte skupinu Service (Služba) a potom vyfiltrujte všechny služby Azure. Následující obrázek ukazuje vyfiltrované služby.

![Vyfiltrované služby](./media/tutorial-review-usage/actual-cost02.png)

V předchozím příkladu se od 31. 8. 2017 utratilo méně peněz než předtím. Tento trend pokračuje pro různé služby zhruba devět dnů. Potom pokračují další výdaje stejně jako předtím. Příliš mnoho sloupců ale může ztížit odhalení zjevného trendu. Zobrazení sestavy můžete změnit na spojnicový nebo plošný graf, které znázorňují data jiným způsobem. Z následujícího obrázku je trend lépe patrný.

![Trend v sestavě](./media/tutorial-review-usage/actual-cost03.png)

V příkladu je jasně vidět, že od 31. 8. 2017 klesly náklady na Azure Storage, zatímco výdaje na ostatní služby Azure zůstaly stejné. Co tedy toto snížení výdajů způsobilo? V tomto příkladu došlo k tomu, že někteří zaměstnanci byli na dovolené a službu Storage nepoužívali.

Pokud chcete zhlédnout výukové video o sledování trendů využití a nákladů, podívejte se na video [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analýza informací o fakturaci cloudu pomocí Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Zjišťovat nedostatečnou efektivitu využití

Optimalizační sestavy umožňují zvýšit efektivitu, optimalizovat využití a najít způsoby, jak ušetřit výdaje za cloudové prostředky. Jsou užitečné hlavně kvůli doporučením nákladově efektivního nastavení velikosti, která mají pomoci snížit počty nečinných nebo nákladných virtuálních počítačů.

Když organizace poprvé přesunou prostředky do cloudu, často se setkávají s potížemi, které jsou výsledkem jejich virtualizační strategie. Často používají podobný přístup, jako když vytvářely virtuální počítače pro místní prostředí virtualizace. Potom předpokládají, že už přesunem místních virtuálních počítačů do cloudu dojde ke snížení nákladů. Tento přístup ale náklady pravděpodobně nesníží.

Potíž je v tom, že za svoji stávající infrastrukturu už zaplatily. Uživatelé předtím mohli vytvářet velké virtuální počítače a nechávat je spuštěné, ať už nečinné, nebo ne, a mělo to jen minimální následky. Přesun velkých nebo nečinných virtuálních počítačů do cloudu ale náklady pravděpodobně spíš *zvýší*. Po uzavření smluv s poskytovateli cloudových služeb je důležité přidělení nákladů k prostředkům. Musíte zaplatit za všechno, co potvrdíte, ať už daný prostředek plně využíváte, nebo ne.

Sestava Cost Effective Sizing Recommendations (Doporučení nákladově efektivního nastavení velikosti) určuje potenciální roční úspory tak, že porovnává kapacitu typů instancí virtuálních počítačů s historickými daty o využití CPU a paměti.  

V nabídce v horní části portálu klikněte na **Optimizer** > **Sizing Optimization** > **Cost Effective Sizing Recommendations** (Optimalizace > Optimalizace velikosti > Doporučení nákladově efektivního nastavení velikosti). Filtrujte poskytovatele podle Azure, aby se zobrazily jenom virtuální počítače Azure. Následuje příklad.

![Virtuální počítače Azure](./media/tutorial-review-usage/sizing01.png)

Podle tohoto příkladu je možné díky doporučením na změnu typů instancí virtuálních počítačů ušetřit 3 114 USD. První doporučení zobrazíte kliknutím na symbol plus (+) ve sloupci **Details** (Podrobnosti). Toto jsou podrobnosti prvního doporučení.

![Podrobnosti doporučení](./media/tutorial-review-usage/sizing02.png)

Kliknutím na symbol plus vedle položky **List of Candidates** (Seznam kandidátů) zobrazíte ID instancí virtuálních počítačů.

![Seznam kandidátů](./media/tutorial-review-usage/sizing03.png)

Pokud chcete zhlédnout výukové video o zjišťování nedostatečné efektivity využití, podívejte se na video [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Optimalizace velikosti virtuálního počítače v Cloudyn).

## <a name="create-alerts-for-unusual-spending"></a>Vytváření upozornění na neobvyklé výdaje

Ostatní účastníky můžete automaticky upozorňovat na anomálie ve výdajích a rizika nadměrných výdajů. Upozornění můžete snadno a rychle vytvořit pomocí sestav, které podporují upozornění na základě prahových hodnot rozpočtu a nákladů.

Upozornění na jakékoli výdaje můžete vytvořit pomocí kterékoli sestavy nákladů. V tomto příkladu budete pomocí sestavy Actual Cost Over Time (Skutečné náklady v průběhu času) upozorňovat na to, že se výdaje blíží k celkové výši rozpočtu. K vytvoření upozornění je potřeba provést všechny následující kroky. V nabídce v horní části portálu klikněte na **Costs** > **Cost Analysis** > **Actual Cost Over Time** (Náklady > Analýza nákladů > Skutečné náklady v průběhu času). U položky **Groups** (Skupiny) nastavte hodnotu **Service** (Služba) a u položky **Filter on the service** (Filtrovat podle služby) hodnotu **Azure/VM** (Azure / virtuální počítač). V pravé horní části sestavy klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu).

V okně Save or Schedule this report (Uložit nebo naplánovat tuto sestavu) na kartě **Scheduling** (Plánování) naplánujte, že se vám má sestava s požadovanou frekvencí zasílat e-mailem. Nezapomeňte vybrat možnost **Send via email** (Odeslat e-mailem). Součástí sestavy v e-mailu budou všechny značky, seskupení a filtry, které jste použili. Klikněte na kartu **Threshold** (Prahová hodnota) a vyberte **Actual Cost vs. Threshold** (Skutečné náklady vs. prahová hodnota). Pokud máte celkový rozpočet 500 000 USD a chcete dostat oznámení, když se náklady přiblíží polovině, vytvořte výstrahu **Red alert** (Červená výstraha) na částce 250 000 USD a výstrahu **Yellow alert** (Žlutá výstraha) na částce 240 000 USD. V zadávaných hodnotách nepoužívejte čárky. Potom vyberte počet po sobě jdoucích výstrah. Až dostanete celkový počet výstrah, který jste zadali, už vám žádné další výstrahy chodit nebudou. Uložte naplánovanou sestavu.

![Příklad sestavy](./media/tutorial-review-usage/schedule-alert01.png)

K vytváření výstrah můžete také vybrat metriku Cost Percentage vs. Budget (Procento nákladů z rozpočtu). Pokud použijete tuto metriku, budou se místo částek zobrazovat procenta z rozpočtu.

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
