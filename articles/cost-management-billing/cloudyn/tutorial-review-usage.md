---
title: 'Kurz: Kontrola využití a nákladů pomocí Cloudyn v Azure'
description: V tomto kurzu zkontrolujete využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: afdcd458dbda282a23b7b8f7f1cd8459bc8ec5c5
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131271"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Kurz: Kontrola využití a nákladů

Cloudyn zobrazuje využití a náklady, abyste mohli sledovat trendy, zjišťovat nedostatečnou efektivitu a vytvářet upozornění. Všechna data o využití a nákladech se zobrazí na řídicích panelech a v sestavách Cloudyn. Příklady v tomto kurzu vás provedou kontrolou využití a nákladů prostřednictvím řídicích panelů a sestav.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](../cost-management-billing-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Sledovat trendy využití a nákladů
> * Zjišťovat nedostatečnou efektivitu využití
> * Vytvářet upozornění na neobvyklé nebo nadměrné výdaje
> * Exportovat data

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít buď zaregistrovanou zkušební verzi, nebo placené předplatné Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Otevření portálu Cloudyn

Všechna data o využití a nákladech můžete zkontrolovat na portálu Cloudyn. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.

## <a name="track-usage-and-cost-trends"></a>Sledovat trendy využití a nákladů

Pomocí průběžných sestav můžete sledovat částky utracené za využití a náklady. Pokud si chcete prohlédnout trendy, použijte sestavu Actual Cost Over Time (Skutečné náklady v průběhu času). V levé horní části portálu klikněte na **Costs** > **Cost Analysis** > **Actual Cost Over Time** (Náklady > Analýza nákladů > Skutečné náklady v průběhu času). Když sestavu otevřete poprvé, nejsou v ní použité žádné skupiny ani filtry.

Toto je příklad sestavy:

![Ukázková sestava skutečných nákladů v průběhu času](./media/tutorial-review-usage/actual-cost01.png)

Sestava obsahuje veškeré výdaje za posledních 30 dnů. Pokud chcete zobrazit jenom výdaje za služby Azure, vyberte skupinu Service (Služba) a potom vyfiltrujte všechny služby Azure. Následující obrázek ukazuje vyfiltrované služby.

![Příklad znázorňující vyfiltrované služby Azure](./media/tutorial-review-usage/actual-cost02.png)

V předchozím příkladu se od 29. 10. 2018 snížily výdaje. Příliš mnoho sloupců ale může ztížit odhalení zjevného trendu. Zobrazení sestavy můžete změnit na spojnicový nebo plošný graf, které znázorňují data jiným způsobem. Z následujícího obrázku je trend lépe patrný.

![Příklad znázorňující trend snižujících se nákladů na virtuální počítač Azure](./media/tutorial-review-usage/actual-cost03.png)

Na stejném příkladu si můžete všimnout snížení nákladů na virtuální počítače Azure. Ve stejný den se začaly snižovat i náklady na ostatní služby Azure. Co tedy toto snížení výdajů způsobilo? V tomto příkladu se dokončil velký pracovní projekt, takže se snížilo i využití řady služeb Azure.

Pokud chcete zhlédnout výukové video o sledování trendů využití a nákladů, podívejte se na video [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analýza informací o fakturaci cloudu pomocí Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Zjišťovat nedostatečnou efektivitu využití

Optimalizační sestavy umožňují zvýšit efektivitu, optimalizovat využití a najít způsoby, jak ušetřit výdaje za cloudové prostředky. Jsou užitečné hlavně kvůli doporučením nákladově efektivního nastavení velikosti, která mají pomoci snížit počty nečinných nebo nákladných virtuálních počítačů.

Když organizace poprvé přesunou prostředky do cloudu, často se setkávají s potížemi, které jsou výsledkem jejich virtualizační strategie. Často používají podobný přístup, jako když vytvářely virtuální počítače pro místní prostředí virtualizace. Potom předpokládají, že už přesunem místních virtuálních počítačů do cloudu dojde ke snížení nákladů. Tento přístup ale náklady pravděpodobně nesníží.

Potíž je v tom, že za svoji stávající infrastrukturu už zaplatily. Uživatelé předtím mohli vytvářet velké virtuální počítače a nechávat je spuštěné, ať už nečinné, nebo ne, a mělo to jen minimální následky. Přesun velkých nebo nečinných virtuálních počítačů do cloudu ale náklady pravděpodobně spíš *zvýší*. Po uzavření smluv s poskytovateli cloudových služeb je důležité přidělení nákladů k prostředkům. Musíte zaplatit za všechno, co potvrdíte, ať už daný prostředek plně využíváte, nebo ne.

Sestava Cost Effective Sizing Recommendations (Doporučení nákladově efektivního nastavení velikosti) určuje potenciální roční úspory tak, že porovnává kapacitu typů instancí virtuálních počítačů s historickými daty o využití CPU a paměti.  

V nabídce v horní části portálu klikněte na **Optimizer** > **Sizing Optimization** > **Cost Effective Sizing Recommendations** (Optimalizace > Optimalizace velikosti > Doporučení nákladově efektivního nastavení velikosti). V případě potřeby použijte filtr ke snížení počtu výsledků. Následuje příklad.

![Sestava doporučení nákladově efektivní velikosti pro virtuální počítače Azure](./media/tutorial-review-usage/sizing01.png)

Podle tohoto příkladu je možné díky doporučením na změnu typů instancí virtuálních počítačů ušetřit 2 382 USD. První doporučení zobrazíte kliknutím na symbol plus (+) ve sloupci **Details** (Podrobnosti). Toto jsou podrobnosti prvního doporučení.

![Příklad znázorňující podrobnosti doporučení](./media/tutorial-review-usage/sizing02.png)

Kliknutím na symbol plus vedle položky **List of Candidates** (Seznam kandidátů) zobrazíte ID instancí virtuálních počítačů.

![Příklad znázorňující seznam virtuálních počítačů-kandidátů na změnu velikosti](./media/tutorial-review-usage/sizing03.png)

Pokud chcete zhlédnout výukové video o zjišťování nedostatečné efektivity využití, podívejte se na video [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Optimalizace velikosti virtuálního počítače v Cloudyn).

Azure Cost Management poskytuje doporučení pro úsporu nákladů i pro služby Azure. Další informace najdete v tématu [Kurz: Optimalizace nákladů na základě doporučení](../costs/tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Vytváření upozornění na neobvyklé výdaje

Upozornění vám umožňují automaticky informovat účastníky o anomáliích ve výdajích a rizicích nadměrných výdajů. Upozornění můžete vytvářet pomocí sestav, které podporují upozornění na základě prahových hodnot rozpočtu a nákladů.

Tento příklad používá sestavu **Actual Cost Over Time** (Skutečné náklady v průběhu času) k odeslání oznámení, že se výdaje na virtuální počítač Azure blíží k celkové výši rozpočtu. V tomto scénáři máte celkový rozpočet ve výši 20 000 USD a chcete dostat oznámení, když náklady dosáhnou téměř poloviny rozpočtu (9 000 USD), a další upozornění, když náklady dosáhnou výše 10 000 USD.

1. V nabídce v horní části portálu Cloudyn vyberte **Costs** (Náklady) > **Cost Analysis** (Analýza nákladů) > **Actual Cost Over Time** (Skutečné náklady v průběhu času).
2. U položky **Groups** (Skupiny) nastavte hodnotu **Service** (Služba) a u položky **Filter on the service** (Filtrovat podle služby) hodnotu **Azure/VM** (Azure / virtuální počítač).
3. V pravé horní části sestavy vyberte **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu).
4. Pokud si chcete nechat poslat e-mailem sestavu v naplánovaném intervalu, vyberte kartu **Scheduling** (Plánování) v dialogovém okně **Save or Schedule this report** (Uložit nebo naplánovat tuto sestavu). Nezapomeňte vybrat možnost **Send via email** (Odeslat e-mailem). Součástí sestavy v e-mailu budou všechny značky, seskupení a filtry, které používáte.
5. Vyberte kartu **Threshold** (Prahová hodnota) a vyberte **Actual Cost vs. Threshold** (Skutečné náklady vs. prahová hodnota).
   1. Do pole prahové hodnoty **Red alert** (Červená výstraha) zadejte 10000.
   2. Do pole prahové hodnoty **Yellow alert** (Žlutá výstraha) zadejte 9000.
   3. Do pole **Number of consecutive alerts** (Počet po sobě jdoucích výstrah) zadejte počet po sobě jdoucích výstrah, které chcete dostat. Až dostanete celkový počet výstrah, který jste zadali, už vám žádné další výstrahy chodit nebudou.
6. Vyberte **Uložit**.

![Příklad znázorňující červené a žluté výstrahy založené na prahových hodnotách výdajů](./media/tutorial-review-usage/schedule-alert01.png)

K vytváření výstrah můžete také vybrat metriku **Cost Percentage vs. Budget** (Procento nákladů z rozpočtu). To vám umožní zadat prahové hodnoty jako procento rozpočtu místo hodnot měny.

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
> [Odhad budoucí útraty](./tutorial-forecast-spending.md)