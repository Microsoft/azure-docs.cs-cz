---
title: Souhrnný řídicí panel pro analýzu Partnerského centra na komerčním trhu
description: Zjistěte, jak získat přístup k grafům, trendům a hodnotám agregovaných dat, které shrnují aktivitu na marketplace z řídicího panelu Souhrn v Centru partnerů.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3da3109bd813fc3b99a4f59e5a357fa351c75394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281370"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Souhrnný řídicí panel v analýze komerčního tržiště

Tento článek obsahuje informace na panelu Souhrn v Centru partnerů. Tento řídicí panel zobrazuje grafy, trendy a hodnoty agregovaných dat, které shrnují aktivitu na trhu pro vaše nabídky.

Chcete-li získat přístup k řídicímu panelu Souhrn, otevřete **[řídicí panel Analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v části Komerční tržiště.

>[!NOTE]
> Podrobné definice terminologie analýzy naleznete v [tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).

## <a name="summary-dashboard"></a>Souhrnný řídicí panel

Panel **Souhrn** představuje přehled založený na jednotlivých typech nabídky. **Přehledy** zobrazují kritické informace na první pohled a poskytují široký přehled o prodejní aktivitě vašich nabídek. Tyto sestavy můžete vizualizovat pomocí řídicího panelu **Souhrn.** Tento článek se zabývá dalšími podrobnostmi o každém z následujících prvků:

- [Rozsah dat](#date-range)
- [Souhrnná sekce](#summary-section)
- [Zákazníci podle zeměpisné polohy](#customers-by-geography)
- [Grafy růstových trendů](#growth-trend-charts)
- [Žebříček zákazníků](#customer-leaderboard)
- [Trend počtu sedadel](#seat-count-trend)
- [Bezplatné zkušební verze SaaS objednávky trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Prvky panelu Souhrn

Následující části popisují, jak používat souhrnný řídicí panel a jak číst data.

### <a name="date-range"></a>Rozsah dat

Výběr rozsahu dat najdete v pravém horním rohu každé stránky. Přizpůsobte výstup **souhrnných** grafů stránek výběrem rozsahu dat na základě posledních 3, 6 nebo 12 měsíců nebo výběrem vlastního období s maximální dobou trvání 12 měsíců. Výchozí časové období je šest měsíců.

![Řídicí panel Analýza centra partnerů](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Souhrnná sekce

V části Souhrn se zobrazuje počet všech vytvořených objednávek, získaných zákazníků a využití vykázaných během vybraného období. Částečný aktuální měsíc bude z výpočtu těchto metrik vyloučen. Příklad: Pokud jste vybrali časový rámec 6 milionů, hodiny využití se vypočítají pro šest měsíců před aktuálním měsícem. Pokud je vybráno vlastní časové období, částečná částka z aktuálního měsíce bude z výpočtu vyloučena.

![Trendy růstu v panelu Souhrn](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Čtení souhrnné části

- **Objednávky**: Počet všech zakoupených objednávek (s výjimkou zrušených objednávek) u nabídek, které jste dosud publikovali.
- **Zákazníci**: Počet všech zákazníků, kteří si zakoupili vaše nabídky a mají alespoň jednu nezrušenou objednávku.
- **Normalizované hodiny využití**: Definováno jako hodiny využití normalizované tak, aby zohledňovala počet jader virtuálních počítače ([počet jader virtuálních počítače] x [hodiny nezpracovaného využití]). Virtuální procesory označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako multiplikátor [počet jader virtuálních v.m.].
- **Nezpracované hodiny využití**: Množství času, po které virtuální počítače běží z hlediska hodin. Procentuální hodnota vedle **celkového počtu objednávek**, **celkovýpočet odběratelů**, **normalizovaných hodin používání**, **nezpracovaných hodin využití**, návštěv **stránek**a **akcí výzvy** představují míru růstu spotřeby pro vybrané období ([využití posledního měsíce – využití prvního měsíce])/ využití prvního měsíce). Jak je popsáno výše, částečné množství aktuálního měsíce bude z této metriky vyloučeno.
- **Růstové trendy**: Pokud najedete na sloupce grafu, sloupcové grafy zobrazí hodnotu pro každý měsíc.
- **Zelený trojúhelník směřující nahoru**: Označuje pozitivní růstový trend.
- **Červený trojúhelník směřující dolů**: Označuje záporný trend růstu ve srovnání s předchozím měsícem.

### <a name="customers-by-geography"></a>Zákazníci podle zeměpisné polohy

**Heatmapa Zákazníci podle zeměpisu** zobrazuje počet zákazníků na mapě světa.

![Zákazníci podle zeměpisné polohy v panelu Souhrn](./media/summary-customers-by-geography.png)

- Mapu můžete přesunout a zobrazit tak přesnou polohu.
- Můžete přiblížit konkrétní umístění.
- Heatmap má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek, normalizovaných hodinách využití v určitém umístění.
- Můžete vyhledat a vybrat zemi v mřížce pro přiblížení místa v mapě. Vraťte se k původnímu zobrazení stisknutím tlačítka **Plochy** na mapě.
- **Nový** zákazník zakoupil jednu z vašich nabídek poprvé během měsíce ve zvoleném časovém období.

### <a name="growth-trend-charts"></a>Grafy růstových trendů

Můžete zobrazit trendy na základě růstu **zakoupených objednávek** (včetně zrušených objednávek), **získaných zákazníků** (včetně ztracených zákazníků) a **hlášeného využití,** které jsou zobrazeny měsíc po měsíci podle vybraného období. Tyto trendy můžete dále analyzovat výběrem odkazů pod grafem, které přecházejí na příslušné stránky **Objednávka**, **Využití**, **Zákazník**nebo **Marketplace Insights.**

Marketplace nabízí **návštěvy stránek a grafy** trendů výzvy k akci se zobrazují pro Azure Marketplace a AppSource na dvou kartách.

![Návštěvy stránek a grafy trendů volání na akce v panelu Souhrn](./media/summary-page-visits-and-cta.png)

Graf **objednávek podle nabídek** uspořádá vaše objednávky podle názvu nabídky.

Výsečový graf **Objednávky podle prodejního kanálu** uspořádá objednávky (včetně objednávek, které zákazníci zrušili) během vybraného období podle prodejního kanálu. Prodejní kanál je typ licenční smlouvy, kterou zákazníci používají k nákupu Azure, což jsou poskytovateli cloudových řešení (CSP), Enterprise, Enterprise prostřednictvím prodejce, GTM a Průběžně platby.

**Využití podle nabídek** a **využití podle prodejních kanálů** výsečové grafy představují rozpis využití podle nejlepších nabídek a prodejních kanálů. Vnitřní výsečový graf představuje nezpracované využití a vnější výsečový graf představuje normalizované využití.

Objednávky **podle typu licence marketplace** a použití podle **výsečových** grafů typu licence marketplace zobrazují rozpis objednávek a použití podle příslušného typu licence. Mezi typy licencí patří:

- **Fakturované prostřednictvím Azure**: Microsoft účtuje zákazníkům vaším jménem, když se rozhodnete prodávat svou nabídku prostřednictvím Microsoftu s tímto typem licence. Typy plateb zahrnují průběžnou platbu prostřednictvím platební karty nebo fakturace enterprise.
- **Přineste si vlastní licenci**: Společnost Microsoft neúčtuje zákazníkům jejich použití s tímto typem nabídky tržiště. Toto použití je uvedeno jako **Získat nyní (zdarma)** na trhu.
- **Zdarma**: Společnost Microsoft neúčtuje zákazníkům za jejich použití s tímto typem nabídky tržiště. Toto použití je uvedeno jako **bezplatná zkušební verze** na trhu.
- **Microsoft jako prodejce**: Představuje nabídky prodávané prodejci společnosti Microsoft jako součást **programu Zprostředkovatel cloudových řešení (CSP).**

### <a name="customer-leaderboard"></a>Žebříček zákazníků

50 nejlepších zákazníků s nejvyšším počtem objednávek je zobrazeno na *předváděčce*, seřazeno podle nejvyššího počtu objednávek a procenta objednávky.

- Výběrem zákazníka zobrazíte podrobnosti profilu, objednávky uspořádané podle nabídky nebo objednávky uspořádané podle typu licence Azure a cenového kanálu.
- Graf **Nabídky podle objednávek** představuje čtyři nejlepší nabídky (podle počtu objednávek) a zbývající nabídky seskupené jako "Rest All".
- **Normalizované využití nabídkou** donut graf představuje prvních pět nabídek podle využití.

> [!NOTE]
> Osobní údaje zákazníka budou předloženy pouze v případě, že zákazník poskytl souhlas. Tyto informace můžete zobrazit, pokud jste se přihlásili pomocí úrovně oprávnění role **vlastníka.** Uživatelé s rolí **přispěvatele** nebudou moci tyto informace zobrazit. [Přečtěte si další informace o uživatelských rolích a oprávněních](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend počtu sedadel

**Objednávky podle místa na místo/ podle grafu lokality** představují rozpis všech objednávek zakoupených podle cenového modelu. Graf **trendů počtu sedadel** zobrazuje počet sedadel oproti objednávkám zakoupeným pro všechny nabídky softwaru jako služby (SaaS) na vašem místě.

### <a name="free-trials-saas-orders-trend"></a>Bezplatné zkušební verze SaaS objednávky trend

**Graf trendů bezplatných zkušebních objednávek SaaS** představuje trend objednávek bezplatných zkušebních verzí, které SaaS nabízí s 30denní zkušební dobou.

## <a name="next-steps"></a>Další kroky

- Přehled analytických přehledů dostupných na komerčním trhu Partnerského centra najdete v [článku Analýza pro komerční tržiště v Centru partnerů](./analytics.md).
- Informace o vašich objednávkách v grafickém formátu a formátu ke stažení najdete [v tématu Dashboard objednávek v analýze komerčního tržiště](./orders-dashboard.md).
- Pro virtuální počítač (VM) nabízí využití a účtované metriky účtované podle objemu dat, najdete [v tématu Využití Řídicí panel v komerčních Marketplace analýzy](./usage-dashboard.md).
- Podrobné informace o zákaznících, včetně trendů růstu, najdete [v tématu Řídicí panel zákazníků v analýze komerčního webu Marketplace](./customer-dashboard.md).
- Seznam žádostí o stažení za posledních 30 dní najdete v článku [Panel stahování v analýze komerčního tržiště](./downloads-dashboard.md).
- Konsolidované zobrazení zpětné vazby od zákazníků u nabídek na Azure Marketplace a AppSource najdete [v tématu Hodnocení a recenze na řídicím panelu Commercial Marketplace analytics](./ratings-reviews.md).
- Nejčastější dotazy týkající se analýzy na komerčním marketplace a komplexní slovník datových termínů naleznete [v tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).
