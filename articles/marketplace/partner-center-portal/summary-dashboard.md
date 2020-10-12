---
title: Souhrnný řídicí panel pro analýzy partnerského centra na komerčním webu Marketplace
description: Naučte se, jak získat přístup k grafům, trendům a hodnotám agregovaných dat, která shrnují aktivity Marketplace z řídicího panelu souhrnu v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 8e58de7975bc7f5de1fe2ad71f97c02c901c0c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87304118"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Souhrn v analýzách komerčního marketplace

Tento článek poskytuje informace o řídicím panelu souhrnu v partnerském centru. Tento řídicí panel zobrazuje grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaše nabídky.

Pro přístup k řídicímu panelu souhrnu otevřete **[řídicí panel analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v rámci komerčního tržiště.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).

## <a name="summary-dashboard"></a>Souhrnný řídicí panel

**Souhrnný** řídicí panel zobrazuje přehled na základě každého typu nabídky. **Přehledy** znázorňují nejdůležitější informace na první pohled a poskytují širokou škálu prodejních aktivit vašich nabídek. Tyto sestavy můžete vizualizovat pomocí řídicího panelu **souhrnu** . Tento článek se podrobněji popisuje u každého z následujících prvků:

- [Rozsah dat](#date-range)
- [Oddíl Summary](#summary-section)
- [Zákazníci podle geografie](#customers-by-geography)
- [Grafy geometrického trendu](#growth-trend-charts)
- [Zákaznická tabulek výsledků](#customer-leaderboard)
- [Trend počtu sedadel](#seat-count-trend)
- [Trend bezplatných zkušebních verzí SaaS objednávek](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Prvky řídicího panelu souhrnu

Následující části popisují, jak používat souhrnný řídicí panel a jak číst data.

### <a name="date-range"></a>Rozsah dat

Výběr rozsahu kalendářních dat najdete v pravém horním rohu každé stránky. Výběrem rozsahu kalendářních dat na základě posledních 3, 6 nebo 12 měsíců nebo výběrem vlastního rozsahu kalendářních dat s maximální dobou trvání 12 měsíců upravte výstup grafů stránky **souhrnu** . Výchozí rozsah kalendářních dat je šest měsíců.

![Řídicí panel pro analýzu partnerského centra](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Oddíl Summary

V části Souhrn se zobrazuje počet všech vytvořených objednávek, zákazníků získaných a používání hlášených během vybraného časového období. Částečný aktuální měsíc bude vyloučen z výpočtu těchto metrik. Příklad: Pokud jste vybrali časový rámec 6 min, hodiny využití se vypočítají po dobu šesti měsíců před aktuálním měsícem. Pokud je vybrán vlastní rozsah kalendářních dat, bude z výpočtu vyloučena částečná částka z aktuálního měsíce.

![Trendy růstu na řídicím panelu souhrnu](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Čtení oddílu Summary

- **Orders**: počet zakoupených objednávek (nezahrnuje zrušené objednávky) pro nabídky, které jste dosud publikovali.
- **Zákazníci**: počet všech zákazníků, kteří si zakoupili vaše nabídky a mají aspoň jednu nezrušenou objednávku.
- **Normalizované hodiny používání**: definované jako hodiny využití normalizované na účet pro počet jader virtuálního počítače ([počet jader virtuálních počítačů] x [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů].
- **Nezpracované hodiny využití**: doba, po kterou jsou virtuální počítače spuštěné v hodinách. Procentuální hodnota vedle položky **Celkový počet objednávek**, **Celkový počet zákazníků**, **normalizovaná doba využití**, **nezpracované hodiny využití**, **návštěvy stránky**a **volání akcí** představuje množství nárůstu využití pro vybraný rozsah kalendářních dat ([využití za poslední měsíc – používání prvního měsíce])/první měsíc použití). Jak je popsáno výše, z této metriky bude vyloučena částečná částka aktuálního měsíce.
- **Trendy růstu**: Pokud najedete myší na sloupce grafu, zobrazí se v pruhových grafech hodnota pro každý měsíc.
- **Zelený trojúhelník ukazující nahoru**: označuje pozitivní trend růstu.
- **Červený trojúhelník směřující dolů**: označuje negativní trend růstu vzhledem k předchozímu měsíci.

### <a name="customers-by-geography"></a>Zákazníci podle geografie

**Zákazníci podle geografického** heatmapuu zobrazují počet zákazníků na světové mapě.

![Zákazníci podle geografického řídicího panelu souhrnu](./media/summary-customers-by-geography.png)

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek, normalizovaných hodinách využití v určitém umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Kliknutím na tlačítko **Domů** na mapě se vraťte k původnímu zobrazení.
- **Novému** zákazníkovi si koupili jednu z vašich nabídek poprvé během měsíce v rámci vybraného rozsahu kalendářních dat.

### <a name="growth-trend-charts"></a>Grafy geometrického trendu

Můžete sledovat trendy na základě nárůstu **zakoupených objednávek** (včetně zrušených objednávek), od **zákazníků** , kteří získali (včetně ztracených zákazníků), a podle údajů o **využití** , které se zobrazí po měsících podle vybraného rozsahu kalendářních dat. Tyto trendy můžete dál analyzovat tak, že vyberete odkazy pod grafem, které navigují na stránky příslušné **objednávky**, **použití**, **zákazníka**nebo **webu Marketplace – přehled** .

Na webu Azure Marketplace a AppSource na dvou kartách se zobrazí stránky nabídek Marketplace **a volání na** grafy trendů akcí.

![Návštěvy stránky a volání akcí v grafech trendů na řídicím panelu souhrnu](./media/summary-page-visits-and-cta.png)

Graf **ORDER by nabízí** uspořádání objednávek podle názvu nabídky.

Výsečový graf **objednávky podle prodejních kanálů** uspořádá vaše objednávky (včetně objednávek zrušených zákazníky) během vybraného rozsahu kalendářních dat podle prodejních kanálů. Prodejní kanál je typ licenční smlouvy používané zákazníky k nákupu Azure, což jsou Cloud Solution Provider (CSP), Enterprise, Enterprise prostřednictvím prodejce, GTM a průběžné platby.

**Využití díky nabídkám** a využití v výsečových grafech **prodejních kanálů** prezentuje rozdělení využití pomocí hlavních nabídek a prodejních kanálů v uvedeném pořadí. Vnitřní výsečový graf představuje nezpracované využití a vnější výsečový graf představuje normalizované využití.

Typ licence **Orders by Marketplace** a **využití podle výsečových grafů typ licence na webu Marketplace** zobrazuje Rozpis objednávek a využití podle příslušného typu licence. Mezi typy licencí patří:

- **Účtuje se prostřednictvím Azure**: Microsoft účtuje zákazníky vaším jménem, když se rozhodnete tuto nabídku prodávat prostřednictvím Microsoftu s tímto typem licence. Typy plateb zahrnují platby s průběžnými platbami prostřednictvím kreditní karty nebo fakturace na podnikové úrovni.
- **Přineste si vlastní licenci**: Společnost Microsoft neúčtuje zákazníkům za použití s tímto typem nabídky Marketplace. Toto využití je uvedené na webu Marketplace jako **hned (zdarma)** .
- **Zdarma**: Společnost Microsoft neúčtuje zákazníkům za použití s tímto typem nabídky Marketplace. Toto využití je uvedené jako **bezplatné zkušební verze** na webu Marketplace.
- **Microsoft as prodejce**: představuje nabídky prodávané prodejci Microsoftu jako součást **programu Cloud Solution Provider (CSP)**.

### <a name="customer-leaderboard"></a>Zákaznická tabulek výsledků

Prvních 50 zákazníků s největším počtem objednávek se zobrazí na *vodicí desce*, seřazené podle nejvyšší počet objednávek a procento objednávky.

- Vyberte zákazníka pro zobrazení podrobností o profilu, objednávek uspořádaných podle nabídky nebo objednávek uspořádaných podle typu licence Azure a cenového kanálu.
- Prstencový graf **nabídky podle objednávek** prezentuje horní čtyři nabídky (podle počtu objednávek) a zbývající nabídky seskupené ve formátu "REST All".
- **Normalizované využití pomocí nabídky** prstencový graf představuje pět nejlepších nabídek podle využití.

> [!NOTE]
> Osobní údaje zákazníků se zobrazí jenom v případě, že zákazník poskytl souhlas. Tyto informace můžete zobrazit, pokud jste přihlášeni s úrovní oprávnění role **vlastníka** . Uživatelé s rolí **Přispěvatel** nebude moci zobrazit tyto informace. [Přečtěte si další informace o uživatelských rolích a oprávněních](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend počtu sedadel

V tabulce **ORDER by za pracovní stanici/na jeden web** se zobrazuje rozpis všech objednávek koupených podle cenového modelu. Graf **Trend počtu pracovních míst** zobrazuje místa a objednávky zakoupené pro všechny nabídky softwaru na pracovní stanici (SaaS).

### <a name="free-trials-saas-orders-trend"></a>Trend bezplatných zkušebních verzí SaaS objednávek

Graf **trendu bezplatných zkušebních SaaS objednávek** prezentuje trend objednávek pro bezplatné zkušební verze SaaS nabídky s 30denní zkušební dobou.

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).
