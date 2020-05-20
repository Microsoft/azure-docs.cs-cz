---
title: Řídicí panel objednávky partnerského centra v analýzách komerčního tržiště
description: Naučte se, jak získat přístup k analytickým sestavám o objednávkách nabídek na webu Marketplace v grafickém a ke stažení.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0c5bfb6632a22a890ac2387f06464c18be7dd714
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699054"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Objednávky v analýzách komerčního marketplace

Tento článek poskytuje informace o **řídicím panelu objednávky** v partnerském centru. Tento řídicí panel zobrazuje informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení.

Pokud chcete získat přístup k **řídicímu panelu objednávky** v analytických nástrojích partnerského centra, otevřete **[řídicí panel analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v rámci komerčního tržiště.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).

## <a name="orders-dashboard"></a>Řídicí panel objednávek

**Řídicí panel objednávky** v nabídce **analyzovat** zobrazuje aktuální objednávky všech nabídek SaaS. Můžete zobrazit grafické reprezentace následujících položek:

- [Souhrn objednávky](#order-summary)
- [Objednávky podle geografického](#orders-by-geography)
- [Objednávky podle nabídek](#orders-by-offers)
- [Trend objednávek na web versus na pracovišti](#orders-trend-per-site-versus-per-seat)
- [Objednávky podle SKU](#orders-by-skus)
- [Trend objednávek a sedadel](#orders-and-seats-trend)
- [Tabulka podrobností objednávky](#order-details-table)

> [!NOTE]
> Existují rozdíly mezi zobrazením sestav analýzy v portál partnerů cloudu (CPP) a v novém programu komerčního obchodu v partnerském centru. Jedním z nich je, že **přehledy prodejců** v CPP mají **objednávky & použití** , která zobrazuje data pro nabídky založené na využití a nabídky nevyužívající použití. Stránka **objednávky** v partnerském centru má samostatnou kartu pro nabídky SaaS.

## <a name="order-dashboard-details"></a>Objednat podrobnosti řídicího panelu

Tato část popisuje analytické sestavy podrobněji.

### <a name="order-summary"></a>Souhrn objednávky

V části objednávka Summary (souhrn objednávky) se zobrazuje počet všech zakoupených objednávek (s výjimkou zrušených objednávek), zrušených objednávek a míst.

Procentuální hodnota vedle Celková objednávka představuje množství nárůstu vybraného rozsahu kalendářních dat.

![Shrnutí pořadí analýzy partnerského centra](./media/order-summary.png)

- Zelený trojúhelník ukazující nahoru indikuje kladný trend růstu.
- Červený trojúhelník ukazující dolů označuje negativní trend růstu vzhledem k předchozímu měsíci.
- Trendy růstu jsou znázorněny pomocí mikropruhových grafů. Můžete zobrazit hodnotu pro každý měsíc přesunutím ukazatele myši na sloupce v grafu.
- Zrušené objednávky jsou počty objednávek, které se dřív nakoupily a pak zrušily během vybraného časového období.
- Křesla jsou počty míst vytvořených během vybraného rozsahu dat.

### <a name="orders-by-geography"></a>Objednávky podle geografického

Heatmapu **ORDER by geografie** zobrazuje počet objednávek na mapě světa a ukazuje, že jsou na základě země zákazníka mapovány místa. Tato heatmapu funguje stejně jako **[Zákazník podle geografické heatmapu](./customer-dashboard.md#customer-by-geography)**.

![Partnerské centrum analyzuje objednávky podle geografického středu](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Objednávky podle nabídek

**Příkazy ORDER by nabízí** prstencový graf uspořádává objednávky (včetně zrušených objednávek) podle jejich názvů nabídek.

- Horní nabídky se zobrazují v grafu a zbývající nabídky jsou seskupené jako "REST All".
- V legendě můžete vybrat konkrétní nabídky, aby se zobrazily pouze nabídky v grafu.
- Při najetí myší na řez v grafu se zobrazí počet objednávek a procento této nabídky ve srovnání s celkovým počtem objednávek napříč všemi nabídkami.
- **Trend podle nabídky ORDER by** zobrazuje trendy růstu po měsících. Sloupec month (měsíc) představuje počet objednávek podle názvu nabídky. Spojnicový graf zobrazuje trend procenta nárůstu vykreslený na ose z.
- Pomocí posuvníku v horní části grafu se můžete posunout doprava a doleva podél osy x a soustředit se na konkrétní datové body.
- Graf trendu můžete zobrazit tak, že vyberete konkrétní položku v legendě.
- Můžete se také rozhodnout zobrazit trendy a data pro **zrušené objednávky**. Graf bude fungovat stejným způsobem jako v **nabídce Orders by** .

### <a name="orders-trend-per-site-versus-per-seat"></a>Trend objednávek na web versus na pracovišti

Graf na **jeden web oproti pracovnímu** prstenci znázorňuje rozdělení na SaaS webu a objednávky SaaS na pracovní stanici zakoupené zákazníky (Tento graf obsahuje zrušené objednávky). Sloupcový graf představuje trend počtu SaaSů na webu a objednávek SaaS na pracovní stanici koupených zákazníky (Tento graf obsahuje zrušené objednávky).

### <a name="orders-by-skus"></a>Objednávky podle SKU

Graf **ORDER by SKU** představuje trend objednávek na úrovni skladových jednotek (SKU) pro všechny vaše nabídky (zahrnuje zrušené objednávky). Prstencový graf představuje rozpis pěti objednávek SKU a sloupcového grafu představuje trend objednávek pro prvních pět SKU.

### <a name="orders-and-seats-trend"></a>Trend objednávek a sedadel

Graf **trendu objednávek a sedadel** představuje nejvyšší 50 nabídky s největším počtem objednávek. Tyto hodnoty jsou zobrazeny na kartě vedoucí a jsou seřazeny podle nejvyšší počet objednávek a procento objednávky.

- **Objednávky podle SKU**: vyberte nabídku pro zobrazení rozpisu počtu objednávek pro prvních pět SKU v grafu.
- **Místa na SKU**: Měsíční trend sedadel pro 5 hlavních SKU. Pokud nabídka, kterou jste vybrali, není na pracovní stanici, nezobrazí se v tomto plošném grafu žádná data.

### <a name="canceled-orders-by-offers"></a>Zrušené objednávky podle nabídek

Výsečový graf, který **zruší objednávky podle** , uspořádá všechny zrušené objednávky podle jejich názvů nabídek. Horní nabídky se zobrazí v grafu a zbytek nabídek se seskupuje jako "zbývající vše". V legendě můžete vybrat konkrétní nabídky, které se zobrazí v grafu.

- Při najetí myší na řez v grafu se zobrazí počet objednávek a procento vybrané nabídky ve srovnání s celkovým počtem objednávek ve všech nabídkách.
- Ve sloupcovém grafu se zobrazují trendy po měsících. Sloupce představují Počet zrušených objednávek podle názvu nabídky. Pomocí posuvníku v horní části grafu se můžete posunout doprava a doleva podél osy x a soustředit se na konkrétní datové body. Graf trendu můžete zobrazit tak, že vyberete konkrétní položku v legendě.

### <a name="order-details-table"></a>Tabulka podrobností objednávky

Tabulka Podrobnosti objednávky zobrazuje číslovaný seznam 1000 hlavních objednávek seřazených podle data pořízení.

- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do souboru TSV, pokud je počet záznamů menší než 1000.
- Pokud zaznamenává číslo přes 1000, vyexportovaná data se asynchronně nasadí na stránku ke stažení po dobu příštích 30 dnů.
- Filtry lze použít v **tabulce podrobností objednávky** k zobrazení pouze těch dat, která vás zajímají. Data je možné filtrovat podle země, typu licence Azure, typu licence Marketplace, typu nabídky, stavu objednávky, volných stop, ID předplatného Marketplace, ID zákazníka a názvu společnosti.
- Vzhledem k tomu, že SaaS nabídky zakoupené prostřednictvím Azure Marketplace nebo AppSource nevyžadují předplatné Azure, ID předplatného na webu Marketplace se v části **podrobné údaje o objednávkách** zobrazí jako 00000000-0000-0000-0000-000000000000.

#### <a name="orders-page-filters"></a>Filtry stránek objednávky

Tyto filtry jsou aplikovány na úrovni stránky.

Můžete vybrat více filtrů pro vykreslení grafu pro kritéria, která chcete zobrazit, a data, která se mají zobrazit v **podrobném pořadí dat** a exportu. Filtry se aplikují na extrahovaná data pro rozsah dat, který jste vybrali v pravém horním rohu stránky objednávky.

- Typy nabídek a názvy nabídek jsou uvedeny pouze pro nabídky, které máte v průběhu vybraného rozsahu kalendářních dat. Názvy nabídek v seznamu se zobrazí pro nabídky typy, které jste v seznamu vybrali.
- Použité filtry zobrazují celkový počet metrik v rámci jednotlivých výběrů pro každý vybraný filtr. Použité filtry se nezobrazí, pokud je zvolen výchozí výběr.
- Pokud je pro jeden z rozevíracích seznamů vybraná možnost **vše** , budou agregované všechny metriky vybrané stránky. Například: "All" v možnosti filtru typů nabídek znamená, že byly vybrány všechny typy nabídek. Toto je výchozí výběr rozevíracích seznamů. Při výběru **všech** použitých filtrů se nezobrazí vše.
- **Výběr více hodnot**: všechny metriky na stránce budou agregovány pro všechny výběry provedené v rozevíracím seznamu. Pokud provedete více výběrů, použije se pro filtr počet všech provedených výběrů. Informace najdete v obrázku níže.

    ![Partnerské centrum analyzuje pořadí s více hodnotami použitými na filtr](./media/filters-applied.png)

- **Výběr jedné hodnoty**: Pokud je vybraná jedna hodnota, použije se filtr, ve kterém se zobrazí počet vybraných filtrů. Odkaz najdete níže v části obrázek.

     ![Centrum pro partnery analyzuje pořadí s jednou hodnotou použitou k filtrování](./media/filters-applied-single.png)

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).
