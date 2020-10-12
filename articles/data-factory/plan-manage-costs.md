---
title: Plánování a Správa nákladů na Azure Data Factory
description: Tento článek popisuje, jak můžete plánovat a spravovat náklady na Azure Data Factory
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83691261"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Plánování a Správa nákladů na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory je služba pro integraci dat bez serveru, která je vytvořená pro škálování v cloudu.  To znamená, že není k dispozici výpočetní výkon, který musíte naplánovat pro zatížení ve špičce; místo toho určíte, kolik prostředků se má přidělit na vyžádání na operaci, což vám umožní navrhovat procesy ETL mnohem škálovatelným způsobem. Navíc se ADF účtuje podle plánu založeného na spotřebě, což znamená, že platíte jenom za to, co využijete.

Tento článek popisuje, jak můžete plánovat a spravovat náklady na Azure Data Factory.

*   Nejdřív na začátku projektu ETL provádíte kontrolu konceptu a použijete kombinaci cenové kalkulačky pro jednotlivé kanály a cenové kalkulačky k odhadování nákladů.
*   Po nasazení kanálů do produkčního prostředí použijete funkce služby cost management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty.
*   Kromě toho můžete zobrazit informace o využití jednotlivých kanálů a o spotřebě za jednotlivé aktivity, abyste zjistili, které kanály a které aktivity jsou Costliest a identifikovali kandidáty na snížení nákladů.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Odhad nákladů pomocí využití kanálu a běhu aktivit a cenové kalkulačky

Pomocí [cenové kalkulačky ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) můžete získat odhad nákladů na spuštění úlohy ETL v Azure Data Factory.  Chcete-li použít kalkulačku, musíte zadat podrobnosti, jako je počet spuštění aktivit, počet hodin jednotek integrace dat, typ výpočtů používaných pro tok dat, počet jader, počet instancí, doba spuštění a tak dále.

Jedním z nejčastějších dotazů týkajících se cenové kalkulačky je, jaké hodnoty by se měly používat jako vstupy.  Během fáze posuzování konceptů můžete provádět zkušební běhy pomocí ukázkových datových sad, abyste porozuměli spotřebě různých měřičů ADF.  Na základě spotřeby pro ukázkovou datovou sadu můžete vymezit spotřebu pro celou datovou sadu a plán provozního provozu.

> [!NOTE]
> Ceny použité v těchto příkladech jsou hypotetické a nejsou určené k implikuje skutečné ceny.

Řekněme například, že potřebujete přesunout 1 TB dat denně z AWS S3 na Azure Data Lake Gen2.  Pro měření propustnosti příjmu dat a pochopení odpovídající spotřeby fakturace můžete použít ověření pro přesun 100 GB dat.

Tady je ukázková podrobnost spuštění aktivity kopírování (vaše skutečná vzdálenost se bude lišit v závislosti na tvaru konkrétní datové sady, rychlosti sítě, omezeních odchozích dat na účtu S3, omezeních přenosů na ADLS Gen2 a dalších faktorech).

![Spuštění kopírování S3](media/plan-manage-costs/s3-copy-run-details.png)

Využitím [monitorování spotřeby na úrovni spuštění kanálu](#monitor-consumption-at-pipeline-run-level)můžete zobrazit odpovídající množství spotřebovaného měřiče přesunu dat:

![Spotřeba kanálu kopírování S3](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Proto celkový počet DIÚ hodin trvá přesun 1 TB za den v celém měsíci:

1,2667 (DIÚ-hodiny) * (1 TB/100 GB) × 30 (dny za měsíc) = 380 DIÚ-hodiny

Nyní můžete do cenové kalkulačky služby ADF (DIÚ) zapojte 30 spuštění aktivit a 380 hodin, abyste získali odhad měsíčního vyúčtování:

![Cenové kalkulačky pro kopírování S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů.  Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů.  Když vytvoříte rozpočet, můžete to udělat buď na úrovni předplatného, nebo s nižší členitosti přidáním dalších filtrů, jako je ID prostředku a název měřiče.  Nemůžete ale vytvářet rozpočty pro jednotlivé kanály v rámci továrny.

## <a name="monitor-costs-at-factory-level"></a>Monitorování nákladů na úrovni továrny

Při zahájení používání Azure Data Factory můžete zobrazit náklady vzniklé v podokně [Analýza nákladů](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) v Azure Portal.

1. Chcete-li zobrazit [analýzu nákladů](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis), otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte možnost **otevřít analýzu nákladů**.
2. Výchozí zobrazení zobrazuje akumulované náklady na aktuální měsíc.  Můžete přepnout na jiný časový rozsah a jinou členitost, například denně nebo měsíčně.
3. Chcete-li zúžit náklady na jednu službu, například Azure Data Factory, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**.  Pak ze seznamu zvolte **Azure Data Factory v2** .
4. Můžete přidat další filtry, abyste mohli analyzovat náklady na konkrétní instanci továrny a konkrétní členitost měřičů ADF.

   ![Analýza nákladů](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorování spotřeby na úrovni kanálu za běhu

V závislosti na typech aktivit, které máte ve vašem kanálu, kolik dat přesouváte a transformují a složitosti transformace, bude spuštění kanálu v Azure Data Factory obsahovat různé měřiče účtování.

Můžete zobrazit množství spotřeby různých měřičů pro jednotlivé kanály, které běží v prostředí Azure Data Factory uživatele. Chcete-li otevřít monitorování, vyberte dlaždici **monitorování & spravovat** v okně objektu pro vytváření dat [Azure Portal](https://portal.azure.com/). Pokud už jste v uživatelském prostředí ADF, klikněte na ikonu **monitorování** na levém bočním panelu. Výchozí zobrazení monitorování je seznam spuštění kanálu.

Když kliknete na tlačítko **spotřebovat** vedle názvu kanálu, zobrazí se automaticky otevírané okno s informacemi o tom, že se spotřeba pro váš kanál spustí agregované napříč všemi aktivitami v rámci kanálu.

![Spotřeba spuštění kanálu](media/plan-manage-costs/pipeline-run-consumption.png)

![Podrobnosti o spotřebě kanálu](media/plan-manage-costs/pipeline-consumption-details.png)

Zobrazení spotřeba za běhu znázorňuje množství spotřebované na jednotlivých měřičích ADF pro konkrétní spuštění kanálu, ale nezobrazuje skutečnou cenu, protože se vám fakturovaná částka závisí na typu účtu Azure a typu používané měny.  Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitorování spotřeby na úrovni aktivity – spuštění
Jakmile pochopíte agregovanou spotřebu na úrovni běhu s kanálem, existují scénáře, kdy potřebujete dále přejít k podrobnostem a určit, která z nich je nejdražších aktivit v rámci kanálu.

Pokud chcete zobrazit spotřebu na úrovni spuštění aktivit, přejděte do uživatelského rozhraní služby Data Factory **Author & monitor** . Na kartě **monitorování** , kde vidíte seznam spuštění kanálu, klikněte na odkaz **název kanálu** pro přístup k seznamu spuštění aktivit v běhu kanálu.  Klikněte na tlačítko **výstup** vedle názvu aktivity a vyhledejte vlastnost **BILLABLEDURATION** ve výstupu JSON:

Tady je ukázka z běhu aktivity kopírování:

![Kopírovat výstup](media/plan-manage-costs/copy-output.png)

A tady je ukázka z běhu aktivity toku dat mapování:

![Výstup toku dat](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny fungují Azure Data Factory, najdete v následujících článcích:

- [Stránka s cenami Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Principy Azure Data Factory příklady](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Azure Data Factory cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
