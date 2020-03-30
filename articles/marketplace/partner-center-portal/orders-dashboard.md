---
title: Řídicí panel Objednávky partnerského centra v analýze komerčního tržiště
description: Přečtěte si, jak získat přístup k analytickým přehledům o objednávkách nabídky na trhu v grafickém formátu a formátu ke stažení.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d0db6553a774a69bb8a55538cbd2b4a333be9316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281404"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Objednávky v analýze komerčního tržiště

Tento článek obsahuje informace na **řídicím panelu Objednávky** v Centru partnerů. Tento řídicí panel zobrazuje informace o vašich objednávkách v grafickém formátu a formátu ke stažení.

Chcete-li získat přístup k **řídicímu panelu Objednávky** v analytických nástrojích Centra partnerů, otevřete **[řídicí panel Analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v části Komerční tržiště.

>[!NOTE]
> Podrobné definice terminologie analýzy naleznete v [tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).

## <a name="orders-dashboard"></a>Řídicí panel objednávek

**Řídicí panel Objednávky** v nabídce **Analyzovat** zobrazuje aktuální objednávky pro všechny nabídky SaaS. Můžete zobrazit grafické znázornění následujících položek:

- [Souhrn objednávky](#order-summary)
- [Objednávky podle geografie](#orders-by-geography)
- [Objednávky podle nabídek](#orders-by-offers)
- [Trend objednávek na web oproti místu](#orders-trend-per-site-versus-per-seat)
- [Objednávky podle SKU](#orders-by-skus)
- [Trend objednávek a sedadel](#orders-and-seats-trend)
- [Tabulka podrobností objednávky](#order-details-table)

> [!NOTE]
> Existují rozdíly mezi zobrazením analytických sestav na portálu partnerů cloudu (CPP) a novým programem Commercial Marketplace v Centru partnerů. Jedním z konkrétních způsobů je, že **Přehledy prodejců** v CPP mají kartu **Objednávky & použití,** která zobrazuje data pro nabídky založené na využití a nabídky nezaložené na využití. V Centru partnerů má stránka **Objednávky** samostatnou kartu pro nabídky SaaS.

## <a name="order-dashboard-details"></a>Podrobnosti řídicího panelu objednávky

Tato část podrobněji popisuje analytické sestavy.

### <a name="order-summary"></a>Souhrn objednávky

V části Souhrn objednávek se zobrazuje počet všech zakoupených objednávek (kromě zrušených objednávek), zrušených objednávek a míst.

Procentuální hodnota vedle položky Celkové objednávky představuje velikost růstu vybraného období.

![Souhrn objednávek Analýzy partnerského centra](./media/order-summary.png)

- Zelený trojúhelník směřující nahoru naznačuje pozitivní růstový trend.
- Červený trojúhelník směřující dolů označuje negativní růstový trend ve srovnání s předchozím měsícem.
- Růstové trendy jsou reprezentovány mikrosloupcovými grafy. Hodnotu pro každý měsíc můžete zobrazit tak, že najedete na sloupce v grafu.
- Zrušené objednávky jsou počet objednávek, které byly dříve zakoupeny a poté zrušeny během vybraného období.
- Počet míst vytvořených během zvoleného období.

### <a name="orders-by-geography"></a>Objednávky podle geografie

**Heatmapa Objednávky podle zeměpisu** zobrazuje počet vašich objednávek na mapě světa a zobrazuje místa mapovaná na základě země zákazníka. Tato heatmap funguje stejně jako **[zákazník podle geografie heatmap](./customer-dashboard.md#customer-by-geography)**.

![Partnerské centrum Analyzovat objednávky podle zeměpisné polohy](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Objednávky podle nabídek

**Objednávky podle nabídek** donut graf organizuje objednávky (včetně zrušených objednávek) podle jejich nabídky jména.

- Horní nabídky jsou zobrazeny v grafu a ostatní nabídky jsou seskupeny jako "Rest All".
- V legendě můžete vybrat konkrétní nabídky, které zobrazí pouze nabídky v grafu.
- Když najedete přes výseč v grafu, zobrazí se počet objednávek a procento této nabídky ve srovnání s celkovým počtem objednávek ve všech nabídkách.
- Trendy růstu **podle nabídek** zobrazují meziměsíční trendy růstu. Sloupec měsíc představuje počet objednávek podle názvu nabídky. Spojnicový graf zobrazuje trend procenta růstu vykreslený na ose z.
- Pomocí posuvníku v horní části grafu můžete posouvat doprava a doleva podél osy x a zaměřit se na konkrétní datové body.
- Graf trendů můžete zobrazit výběrem konkrétní položky v legendě.
- Můžete také zobrazit trendy a data pro **zrušené objednávky**. Graf bude fungovat stejným způsobem jako **objednávky podle nabídek** grafu.

### <a name="orders-trend-per-site-versus-per-seat"></a>Trend objednávek na web oproti místu

Graf **na lokalitu a na sedadlo** představuje rozpis objednávek SaaS na webu a na sedadlo SaaS zakoupených zákazníky (tento graf obsahuje zrušené objednávky). Sloupcový graf představuje trend objednávek SaaS na pracovišti a na sedadlo SaaS zakoupených zákazníky (tento graf obsahuje zrušené objednávky).

### <a name="orders-by-skus"></a>Objednávky podle SKU

Graf **Objednávky podle skladových jednotek** představuje trend objednávek na úrovni skladové jednotky (Skladová jednotka) pro všechny vaše nabídky (včetně zrušených objednávek). Graf koblihy představuje rozpis prvních pěti objednávek skladových bodů a sloupcový graf představuje trend objednávek pro prvních pět skladových bodů.

### <a name="orders-and-seats-trend"></a>Trend objednávek a sedadel

Graf **trendů objednávek a sedadel** představuje 50 nejlepších nabídek s nejvyšším počtem objednávek. Ty jsou zobrazeny na žebříčku a jsou seřazeny podle nejvyššího počtu objednávek a procenta objednávky.

- **Objednávky podle sku**: Vyberte nabídku pro zobrazení rozpisu počtu objednávek pro prvních pět SKU v grafu.
- **Sedadla sku :** Měsíční trend sedadel pro prvních pět SKU. Pokud vybraná nabídka není nabídkou pro každou sedátko, v tomto plošném grafu se nezobrazí žádná data.

### <a name="canceled-orders-by-offers"></a>Zrušené objednávky podle nabídek

**Výsečový graf Zrušené objednávky podle nabídek** uspořádá všechny zrušené objednávky podle názvů jejich nabídek. Horní nabídky jsou zobrazeny v grafu a ostatní nabídky jsou seskupeny jako "Rest All". V legendě můžete vybrat konkrétní nabídky, které se zobrazí v grafu.

- Když najedete přes výseč v grafu, zobrazí se počet objednávek a procento vybrané nabídky v porovnání s celkovým počtem objednávek ve všech nabídkách.
- Sloupcový graf zobrazuje trendy měsíc po měsíci. Sloupce představují počet zrušených objednávek podle názvu nabídky. Pomocí posuvníku v horní části grafu můžete posouvat doprava a doleva podél osy x a zaměřit se na konkrétní datové body. Graf trendů můžete zobrazit výběrem konkrétní položky v legendě.

### <a name="order-details-table"></a>Tabulka podrobností objednávky

Tabulka Podrobnosti objednávky zobrazuje číslovaný seznam 1000 nejlepších objednávek seřazených podle data pořízení.

- Každý sloupec v mřížce je seřaditelný.
- Data lze extrahovat do souboru TSV, pokud je počet záznamů menší než 1000.
- Pokud je počet záznamů vyšší než 1000, exportovaná data budou asynchronně umístěna na stránce stahování po dobu následujících 30 dnů.
- Filtry lze použít v **tabulce Podrobnosti objednávky** a zobrazit pouze data, která vás zajímají. Data lze filtrovat podle země, typu licence Azure, typu licence Marketplace, typu nabídky, stavu objednávky, bezplatných tras, ID předplatného Marketplace, ID zákazníka a názvu společnosti.

#### <a name="orders-page-filters"></a>Filtry stránek objednávek

Tyto filtry jsou použity na úrovni stránky.

Můžete vybrat více filtrů, chcete-li vykreslit graf pro kritéria, která chcete zobrazit, a data, která chcete zobrazit v mřížce **Podrobných dat objednávky** nebo exportu. Filtry se použijí na data extrahovaná pro oblast dat, kterou jste vybrali v pravém horním rohu stránky objednávky.

- Typy nabídek a názvy nabídek jsou uvedeny pouze u nabídek, které mají objednávky během vybraného období. Názvy nabídek v seznamu se zobrazí pro typy nabídek, které jste vybrali v seznamu.
- Použité filtry zobrazují celkové metriky v rámci každého výběru (výběrů) pro každý vybraný filtr. Použité filtry se nezobrazí, když je vybrán výchozí výběr.
- Pokud je pro jeden z rozevíracích seznamů vybrána možnost **Vše,** budou agregovány všechny metriky na vybrané stránce. Například: Možnost "Vše" v možnosti filtru Typů nabídek znamená, že byly vybrány všechny typy nabídek. Toto je výchozí výběr pro rozevírací seznamy. Použité filtry se zobrazí, když je **vybrána možnost Vše.**
- **Výběr více hodnot**: Všechny metriky na stránce budou agregovány pro všechny výběry provedené v rozevíracím seznamu. Pokud je provedeno více výběrů, použitý filtr zobrazí počet všech provedených výběrů. Viz obrázek níže pro referenci.

    ![Pořadí analýzy centra partnerů s více hodnotami použitými pro filtrování](./media/filters-applied.png)

- **Výběr jedné hodnoty**: Pokud je vybrána jedna hodnota, použitý filtr zobrazí počet jednoho vybraného filtru. Viz níže obrázek pro referenci.

     ![Pořadí analýzy centra partnerů s jedinou hodnotou použitou pro filtr](./media/filters-applied-single.png)

## <a name="next-steps"></a>Další kroky

- Přehled analytických přehledů dostupných na komerčním trhu Partnerského centra najdete v [článku Analýza pro komerční tržiště v Centru partnerů](./analytics.md).
- Grafy, trendy a hodnoty souhrnných dat, které shrnují aktivitu na marketplace pro vaši nabídku, najdete [v tématu Souhrnný řídicí panel v analýze komerčního webu Marketplace](./summary-dashboard.md).
- Pro virtuální počítač (VM) nabízí využití a účtované metriky účtované podle objemu dat, najdete [v tématu Využití řídicího panelu v komerčních Marketplace analytics](./usage-dashboard.md).
- Podrobné informace o zákaznících, včetně trendů růstu, najdete [v tématu Řídicí panel zákazníka v analytice na komerčním trhu](./customer-dashboard.md).
- Seznam žádostí o stažení za posledních 30 dní najdete [v tématu Panel stahování v analytice na komerčním webu Marketplace](./downloads-dashboard.md).
- Konsolidované zobrazení zpětné vazby od zákazníků u nabídek na Azure Marketplace a AppSource najdete [v tématu Hodnocení a recenze na řídicím panelu Commercial Marketplace analytics](./ratings-reviews.md).
- Nejčastější dotazy týkající se analýzy na komerčním marketplace a komplexní slovník datových termínů naleznete [v tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).
