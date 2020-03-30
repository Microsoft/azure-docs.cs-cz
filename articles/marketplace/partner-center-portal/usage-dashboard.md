---
title: Řídicí panel využití v analýze Komerčnítržiště v Partnerském centru
description: Přečtěte si, jak získat přístup ke všem nabídkám virtuálních aplikací a metrikám fakturace účtovaných podle objemu dat.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0cd6614c1ccc51a2b25f115dccf0ee389075aa70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285125"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel využití v analýze komerčního tržiště

Tento článek obsahuje informace o řídicím panelu Využití v Centru partnerů. Tento řídicí panel zobrazuje všechny metriky využití virtuálních počítače a fakturace účtované podle objemu dat na dvou samostatných kartách: využití virtuálních počítače a využití fakturace účtované podle objemu dat.

Chcete-li získat přístup k řídicímu panelu Využití, otevřete řídicí panel **[Analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v části Komerční tržiště.

>[!NOTE]
> Podrobné definice terminologie analýzy naleznete v [tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).

## <a name="usage-dashboard"></a>Řídicí panel využití

Řídicí panel využití představuje metriky pro všechny nabídky virtuálních počítačů (VM) využití a využití fakturace účtované podle objemu dat. Ty se nacházejí na dvou samostatných kartách: využití virtuálních počítače a využití fakturace účtované podle objemu dat.

Na kartě Využití virtuálního počítače jsou grafické znázornění následujících položek:

- [Souhrn použití](#usage-summary)
- [Použití podle zeměpisné polohy](#usage-by-geography)
- [Použití podle nabídek](#usage-by-offers)
- [Trend využití podle nabídek a slok](#usage-trend-by-offers-and-skus)
- [Použití podle typu nabídky](#usage-by-offer-type)
- [Použití podle velikosti virtuálního počítače](#usage-by-vm-size)
- [Využití podle prodejního kanálu](#usage-by-sales-channel)
- [Podrobné údaje o využití](#detailed-usage-data)

> [!NOTE]
> Přehledy Analytics se v cloudových partnerských portálech (CPP) a partnerském centru zobrazují odlišně. **Přehledy prodejce** v CPP mají kartu Objednávky a využití, která zobrazuje data pro nabídky založené na využití i nabídky nezaložené na využití. V Centru partnerů se metriky využití zobrazují na samostatné stránce.

### <a name="usage-summary"></a>Souhrn využití

Souhrnná tabulka využití zobrazuje hodiny využití zákazníků pro všechny nabídky, které si zakoupili.

- Normalizované hodiny využití jsou definovány jako hodiny využití normalizované tak, aby zohledňovala počet jader virtuálních počítače ([počet jader virtuálních počítače] x [hodiny nezpracovaného využití]). Virtuální procesory označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako multiplikátor [počet jader virtuálních v.m.].
- Nezpracované hodiny využití jsou definovány jako doba, po kterou virtuální počítače běží z hlediska hodin.
- Procentuální hodnota představuje změnu růstu využití pro vybrané období ([využití posledního měsíce – využití za první měsíc])/ využití za první měsíc).
- Zelené trojúhelníky směřující nahoru označují změnu růstu.
- Červený trojúhelník směřující dolů označuje zápornou změnu růstu ve srovnání s předchozím měsícem.
- Mikrosloupcové grafy představují měsíční hodnoty. Hodnotu pro každý měsíc můžete zobrazit tak, že najedete na sloupce v grafu.

### <a name="usage-by-geography"></a>Použití podle zeměpisné polohy

**Normalizované využití podle geografické** heatmap zobrazuje hodiny využití mapované podle země zákazníka. Barevná variace země představuje normalizovanou koncentraci využití. Vraťte se k původnímu zobrazení stisknutím **domovského** tlačítka na mapě.

### <a name="usage-by-offers"></a>Použití podle nabídek

- **Normalizované využití pomocí nabídky** výsečového grafu zobrazuje rozpis normalizovaných hodin využití podle nabídek podle vybraného období. Prvních 5 nabídek je zobrazeno v grafu, zatímco zbytek je seskupen v kategorii "zbytek vše".
- Pruhový graf znázorňuje trend růstu meziměsíčně pro vybrané období. Sloupce měsíce představují hodiny využití z nabídek s nejvyšší dobou využití pro příslušný měsíc. Spojnicový graf znázorňuje trend procenta růstu vykreslený na vedlejší ose Y.
- Pomocí posuvníku v horní části grafu se můžete posouvat doprava podél osy x a/nebo se zaměřit na konkrétní datové body.

### <a name="usage-trend-by-offers-and-skus"></a>Trend využití podle nabídek a slok

Tento graf zobrazuje trend normalizovaného využití vybraných skum nabídky. Žebříček nabídek zobrazuje 50 nejlepších nabídek s nejvyšším využitím a jsou seřazeny podle hodin používání. Žebříček skladových položk zobrazuje 50 nejlepších skladových částek s nejvyšším využitím vybrané nabídky.

### <a name="usage-by-offer-type"></a>Použití podle typu nabídky

- **Využití podle nabídky typ** výsečového grafu uspořádá využití podle typu nabídky.
- Hlavní nabídky jsou zobrazeny v grafu a ostatní nabídky jsou seskupeny jako "Rest All".
- Graf **trendů** zobrazuje trendy růstu meziměsíčně. Sloupec měsíc představuje využití podle hlavních typů nabídek v tomto měsíci.

### <a name="usage-by-vm-size"></a>Použití podle velikosti virtuálního počítače

Tento graf představuje trend využití pro vybrané velikosti virtuálních her (max 5) všech nabídek/sku. Sloupcový graf je skládaný s hodinami využití vybraných velikostí virtuálních her.

Žebříček zobrazuje 50 nejlepších velikostí virtuálních počítače s nejvyšším využitím a seřazených podle hodin využití.

### <a name="usage-by-sales-channel"></a>Využití podle prodejního kanálu

- Využití podle prodejního kanálu výsečového grafu uspořádá využití podle prodejního kanálu
- V grafu se zobrazí nejvyšší prodejní kanál s nejvyšším využitím a zbytek prodejního kanálu je seskupen jako "Rest All".
- Sloupec měsíc představuje využití podle nejvyššího prodejního kanálu v tomto měsíci.
- Vlastnosti tohoto grafu jsou stejné jako graf "Využití nabídkami"

### <a name="detailed-usage-data"></a>Podrobné údaje o využití

Tabulka **podrobností o použití** zobrazuje číslovaný seznam 1000 nejlepších záznamů o využití seřazených podle použití.

- Každý sloupec v mřížce je seřaditelný.
- Data lze extrahovat do souboru CSV, pokud je počet záznamů menší než 1000.
- Pokud počet záznamů je více než 1000, export dat bude asynchronně umístěn na stránce ke stažení, která bude k dispozici pro příštích 30 dnů.
- Filtry lze použít na **podrobná data o využití** a zobrazit tak pouze data, která vás zajímají. Data lze filtrovat podle země, prodejního kanálu, typu licence Marketplace, typu využití, názvu nabídky, typu nabídky, bezplatných zkušebních verzí, ID předplatného Marketplace, ID zákazníka a názvu společnosti.

> [!NOTE]
> Výběrem **typu Použití** ve filtru stránky zobrazíte grafy na stránce v "Normalizovaném zobrazení" nebo "Nezpracovaném zobrazení". Výchozí zobrazení těchto grafů je "Normalizované zobrazení".

**Filtry stránky Využití** se použijí na úrovni stránky. Můžete vybrat více filtrů, které vykreslí graf pro kritéria, která chcete zobrazit, a data, která chcete zobrazit v mřížce/exportu podrobných dat o využití. Filtry se použijí na data extrahovaná pro oblast dat, kterou jste vybrali v pravém horním rohu stránky objednávky.

- **Typy nabídek** a **názvy nabídek** jsou uvedeny pouze pro nabídky, které jste získali během vybraného období. Názvy nabídek v seznamu jsou zobrazeny pro typy nabídek, které jsou vybrány ze seznamu.
- Výchozí výběr je "Vše" pro každou z možností filtru, s výjimkou **typu Použití**. Výchozí výběr pro **typ použití** je normalizované použití. Chcete-li zobrazit nezpracované využití v grafech, vyberte možnost Nezpracované využití.
- Použité filtry zobrazují výběry počtu pro provedené výběry filtrů. Použité filtry se nezobrazují pro výchozí výběry.

> [!NOTE]
> Podrobná definice každého z polí v mřížce "podrobná data objednávky", filtry stránek a všechny možné výběry jsou definovány v sekci datový slovník v často [kladených dotazech a terminologii.](link needed)

Karta **Využití fakturace podle objemu dat** zobrazuje informace o využití typů nabídek, kde se využití měří podle dimenze měřiče. SaaS nabídka typu nadsazení je uveden v současné době. Karta představuje grafické znázornění trendů nadbytku pro využití fakturace s měřením podle objemu SaaS:

- **Trend nadbytku podle dimenze měřiče**: Zobrazuje trend měsíčního nadbytku pro vybranou dimenzi měřiče nabídky. Osa X představuje měsíc a osa Y představuje množství využití. Jednotka měření vlastního měřiče je také zobrazena na ose Y.
- **Trend nadbytku podle skladové položky**: Představuje trend využití množství vybrané dimenze měřiče skladovými položkami. Zobrazené sku budou představovat 5 nejvyšších sku s nejvyšším množstvím využití vybrané nabídky.
- **Trend nadhodnocení top 50 zákazníků:** Top 50 nabídek s nejvyšší dobou používání jsou zobrazeny na ***žebříčku*** a jsou seřazeny podle nejvyššího využití vlastního měřiče. Vyberte zákazníka v žebříčku a zobrazte trend využití vybrané dimenze měřiče.
- **Trend nadbytku podle špičkových zákazníků**: Představuje nejlepší percentil (y) zákazníků, které přispívají k % celkového využití. Percentil nejvyššího zákazníka je zobrazen podél osy X a je určen množstvím využití zákazníka. Osa Y zobrazuje množství využití. Podrobnosti můžete zobrazit tak, že najedete přes body podél spojnicového grafu.

> [!NOTE]
> Podrobnosti o použití a všechny grafy na této stránce se zobrazí pro kteroukoli dimenzi měřiče, která je vybrána pro filtr stránky.

## <a name="next-steps"></a>Další kroky

- Přehled analytických přehledů dostupných na komerčním trhu Partnerského centra najdete v [článku Analýza pro komerční tržiště v Centru partnerů](./analytics.md).
- Grafy, trendy a hodnoty souhrnných dat, které shrnují aktivitu na marketplace pro vaši nabídku, najdete [v tématu Summary Dashboard in Commercial Marketplace analytics](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a formátu ke stažení najdete [v tématu Dashboard objednávek v analýze komerčního tržiště](./orders-dashboard.md).
- Podrobné informace o zákaznících, včetně trendů růstu, najdete [v tématu Řídicí panel zákazníků v analýze komerčního webu Marketplace](./customer-dashboard.md).
- Seznam žádostí o stažení za posledních 30 dní najdete v článku [Panel stahování v analýze komerčního tržiště](./downloads-dashboard.md).
- Konsolidované zobrazení zpětné vazby od zákazníků u nabídek na Azure Marketplace a AppSource najdete [v tématu Hodnocení a recenze na řídicím panelu Commercial Marketplace analytics](./ratings-reviews.md).
- Nejčastější dotazy týkající se analýzy na komerčním marketplace a komplexní slovník datových termínů naleznete [v tématu Nejčastější dotazy a terminologie pro analýzu komerčního tržiště](./faq-terminology.md).
