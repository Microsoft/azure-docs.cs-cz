---
title: Řídicí panel Přehledy Marketplace v analýzách komerčního marketplace
description: Získejte přístup k souhrnným webovým analýzám Marketplace v partnerském centru, které vám umožní měřit zapojení zákazníka v Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553931"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Přehledy Marketplace v analýzách komerčního marketplace

Tento článek poskytuje informace o řídicím panelu přehledů Marketplace v partnerském centru. Tento řídicí panel zobrazuje souhrn Web Analytics pro komerční web Marketplace, který umožňuje vydavatelům měřit zapojení zákazníků na příslušné stránky s podrobnostmi o produktu, které jsou uvedené v online obchodech s komerčním Marketplace: Microsoft AppSource a Azure Marketplace.

Pokud chcete získat přístup k řídicímu panelu **přehledů Marketplace** v partnerském centru, vyberte v části komerční Marketplace možnost **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **přehledy Marketplace**.

Podrobné definice terminologie analýz najdete v tématu [terminologie a běžné otázky k komerčním](./analytics-faq.md)analýzám na webu Marketplace.

## <a name="marketplace-insights-dashboard"></a>Řídicí panel přehledů Marketplace

Řídicí panel přehledů Marketplace nabízí přehled Azure Marketplace a AppSource nabízí obchodní výkon. Tento řídicí panel poskytuje širokou škálu následujících funkcí:

- Trend návštěv stránky
- Volání akcí trendu
- Návštěvy stránky a volání akcí proti nabídkám, referenčním doménám a ID kampaní
- Přehledy Marketplace podle geografického využití
- Tabulka podrobností webu Marketplace Insights

Řídicí panel přehledů Marketplace poskytuje navštívených data, která by se neměla korelovat se zájemci vygenerovanými v koncovém bodu zájemce.

> [!NOTE]
> Maximální latence mezi uživateli, kteří navštěvují nabídky na Azure Marketplace nebo AppSource a vytváření sestav v partnerském centru, je 48 hodin.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Prvky řídicího panelu Insights pro Marketplace

Řídicí panel přehledů Marketplace zobrazí podrobnosti o webové telemetrie pro Azure Marketplace a AppSource na dvou oddělených kartách. Následující části popisují, jak používat řídicí panel přehledů Marketplace a jak číst data.

### <a name="month-range"></a>Rozsah měsíce

Výběr rozsahu měsíce můžete najít v pravém horním rohu každé stránky. Výběrem rozsahu měsíců na základě posledních 6 nebo 12 měsíců nebo vybráním vlastního rozsahu měsíců s maximální dobou trvání 12 měsíců si přizpůsobíte výstup grafů na stránce s **přehledem Marketplace** . Výchozí rozsah měsíce (perioda výpočtu) je šest měsíců.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Znázorňuje měsíční filtry na řídicím panelu webu Marketplace Insights.":::

> [!NOTE]
> Všechny metriky v widgetech a sestavách vizualizace jsou v době výpočtu vybrané uživatelem.

### <a name="page-visits-trends"></a>Trendy navštíví stránky

V tabulce **Návštěvníci** služby Marketplace Insights se zobrazuje počet _návštěv stránek_ a _jedinečných návštěvníků_ pro vybrané období výpočtu.

**Návštěvy stránky**: Toto číslo představuje počet odlišných uživatelských relací na stránce seznamu nabídek (Stránka s podrobnostmi o produktu) pro vybrané období výpočtu. Indikátory červeného a zeleného procenta reprezentují procentuální nárůst počtu návštěv stránky. Graf trendu představuje počet návštěv stránek v měsíci na měsíc.

**Jedineční návštěvníci**: Toto číslo představuje počet jedinečných návštěvníků během vybraného období výpočtu pro nabídky v Azure Marketplace a AppSource. Návštěvník, který navštívil jednu nebo více stránek s podrobnostmi o produktu, bude počítat jako jeden jedinečný návštěvník.

[![Znázorňuje návštěvníky grafu na řídicím panelu přehledů Marketplace.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Volání akcí trendu

Toto číslo představuje počet volání kliknutí **na tlačítko akce** na stránce seznam nabídek (Stránka s podrobnostmi o produktu). _Volání akce_ se počítá, když uživatel vybere tlačítka **získat nyní**, **bezplatná zkušební verze**, **Kontaktní osoba** nebo **testovací jednotka** .

[![Znázorňuje volání grafu akce na řídicím panelu přehledů Marketplace.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Návštěvy stránky a volání akcí proti nabídkám, referenčním doménám a ID kampaní

**Referenční domény**: výběr konkrétní referenční domény zobrazuje měsíční trend návštěv stránky a volání akce v grafu napravo.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Znázorňuje graf referenční domény na řídicím panelu přehledů Marketplace.":::

**Nabídky**: vyberte konkrétní nabídku pro zobrazení měsíčního trendu návštěv stránek a volání akce v grafu napravo.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Znázorňuje graf aliasů nabídky na řídicím panelu přehledů Marketplace.":::

**ID kampaní**: výběrem konkrétního ID kampaně byste měli být schopni pochopit úspěch kampaně. U každé kampaně byste měli být schopni zobrazit měsíční trend návštěv stránek a volání, která se v grafu napravují na akci.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Znázorňuje graf kampaně na řídicím panelu přehledů Marketplace.":::

### <a name="marketplace-insights-by-geography"></a>Přehledy Marketplace podle geografického využití

Pro vybrané období výpočtu zobrazuje heatmapu počet návštěv stránek, jedinečných návštěvníků a volání akce (výzva). Světlá tmavá barva na mapě představuje nízkou a vysokou hodnotu jedinečných návštěvníků. Vyberte záznam v tabulce pro přiblížení země nebo oblasti.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Ukazuje geografickou dvojstránkový graf na řídicím panelu přehledů Marketplace.":::

Všimněte si, že:

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek a normalizovaných hodinách použití v určitém umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Vraťte se k původnímu zobrazení výběrem tlačítka **Domů** na mapě.

### <a name="marketplace-insights-details-table"></a>Tabulka podrobností webu Marketplace Insights

Tato tabulka obsahuje seznam návštěv stránky a volání akcí pro vybrané stránky nabídek seřazené podle data.

- Data je možné extrahovat do. TSV nebo. Soubor CSV, pokud je počet záznamů menší než 1 000.
- Pokud je počet záznamů vyšší než 1 000, exportovaná data budou asynchronně umístěna na stránku ke stažení po dobu příštích 30 dnů.
- Filtrovat data podle názvů a názvů kampaní pro zobrazení dat, která vás zajímají.

> [!TIP]
> Pomocí ikony stáhnout v pravém horním rohu libovolného widgetu můžete data stáhnout. Svůj názor na každé widgety můžete poskytnout kliknutím na ikonu "palec nahoru" nebo "palec".

| Název sloupce v<br>uživatelské rozhraní | Název atributu | Definice | Název sloupce v programovém programu<br>přístup k sestavám |
| ------------ | ------------- | ------------- | ------------- |
| Date (Datum) | Datum návštěvy | Datum návštěvy stránky nebo výzva kliknutí na generování události na stránce nabídky v Azure Marketplace a/nebo AppSource. | Date (Datum) |
| Název nabídky | Název nabídky | Název nabídky komerčního tržiště. | OfferName |
| Referenční doména | Referenční doména | Název domény odkazů, ze které se stránka navštíví Pokud pro návštěvě stránky nejsou zachyceny žádné referenční domény, pak je odpovídající položka "referenční doména není k dispozici". |  ReferralDomain |
| Jméno země | Jméno země | Název země, ze které došlo k návštěvě stránky. | Country |
| Návštěvy stránky | Návštěvy stránky | Počet návštěv stránky přidružených k názvu nabídky pro konkrétní datum. | PageVisits |
| Získat hned | Získat hned | Počet kliknutí na stránku získat nyní výzva na stránce nabídky pro konkrétní datum. | GetItNow |
| Kontaktujte mě | Kontaktujte mě | Počet kliknutí na kontaktní mne výzva na stránce nabídky pro konkrétní datum. | ContactMe |
| Testovací verze | Testovací verze | Počet kliknutí na "testovací jednotku" výzva na stránce nabídky pro konkrétní datum. | TestDrive |
| Bezplatná zkušební verze | Bezplatná zkušební verze | Počet kliknutí na výzva bezplatné zkušební verze na stránce nabídky pro konkrétní datum. | FreeTrial |
|||||

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na komerčním webu Marketplace najdete v tématu [přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./partner-center-portal/downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení & kontroly řídicího panelu Analytics v partnerském centru](./partner-center-portal/ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [terminologie a běžné otázky ke komerčním analýzám na webu Marketplace](./analytics-faq.md).