---
title: Řídicí panel využití v Microsoft Commercial Marketplace Analytics, Azure Marketplace a Microsoft AppSource
description: Přečtěte si, jak získat přístup ke všem VIRTUÁLNÍm počítačům nabízí metriky fakturace využití a měření. V partnerském centru v části komerční web Marketplace přejdete na řídicí panel využití.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9b8432a54aa90b7d500898b2f6959d075ac89460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245328"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Řídicí panel využití v Microsoft Commercial Marketplace Analytics

Tento článek poskytuje informace o řídicím panelu využití v partnerském centru. Tento řídicí panel zobrazuje všechny virtuální počítače, které nabízí metriky využití a měření fakturace na dvou oddělených kartách: využití virtuálních počítačů a využití účtované fakturace.

Pokud chcete získat přístup k řídicímu panelu využití, otevřete řídicí panel **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v rámci **komerčního tržiště**.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).

## <a name="usage-dashboard"></a>Řídicí panel využití

Řídicí panel využití představuje metriky pro všechny virtuální počítače (VM) nabízí využití a měření využití fakturace. Tyto metriky se nacházejí na dvou oddělených kartách: využití virtuálních počítačů a využití účtované fakturace.

Na kartě využití virtuálního počítače jsou k dispozici grafické reprezentace následujících položek:

- [Souhrn využití](#usage-summary)
- [Využití podle geografie](#usage-by-geography)
- [Využití podle nabídek](#usage-by-offers)
- [Využití trendů podle nabídek a plánů](#usage-trend-by-offers-and-plans)
- [Využití podle typu nabídky](#usage-by-offer-type)
- [Využití podle velikosti virtuálního počítače](#usage-by-vm-size)
- [Využití podle prodejního kanálu](#usage-by-sales-channel)
- [Podrobná data o využití](#detailed-usage-data)

Maximální latence mezi generováním událostí využití a vytvářením sestav v partnerském centru je 48 hodin.

### <a name="usage-summary"></a>Souhrn využití

Tabulka Souhrn využití zobrazuje hodiny používání zákazníka pro všechny nabídky, které si zakoupili.

- Normalizované hodiny používání jsou definovány jako hodiny využití normalizované na účet pro počet jader virtuálních počítačů ([počet jader virtuálních počítačů] × [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů].
- Hodiny hrubého využití se definují jako počet spuštěných virtuálních počítačů v řádu hodin.
- Procentuální hodnota představuje změnu nárůstu využití pro vybraný rozsah dat ([využití za poslední měsíc – první měsíc použití])/první měsíc použití).
- Zelené trojúhelníky ukazující nahoru znamenají změnu růstu.
- Červený trojúhelník ukazující dolů znamená změnu negativního nárůstu vzhledem k předchozímu měsíci.
- Mikropruhové grafy reprezentují měsíční hodnoty. Můžete zobrazit hodnotu pro každý měsíc přesunutím ukazatele myši na sloupce v grafu.

### <a name="usage-by-geography"></a>Využití podle geografie

**Normalizované využití podle geografického** heatho mapy zobrazuje hodiny využití mapované na základě země nebo oblasti zákazníka. Barevná variace země/oblasti představuje normalizovanou koncentraci využití. Kliknutím na tlačítko **Domů** na mapě se vraťte k původnímu zobrazení.

### <a name="usage-by-offers"></a>Využití podle nabídek

- **Normalizované využití pomocí** výsečového grafu zobrazuje rozpis normalizovaných hodin využití nabídek podle vybraného rozsahu kalendářních dat. V grafu se zobrazí pět nejlepších nabídek, zatímco zbytek je seskupený do kategorie **zbývající vše** .
- Pruhový graf znázorňuje trend růstu od měsíců po měsících pro vybraný rozsah dat. Sloupce měsíc představují hodiny využití z nabídek s nejvyšším hodinou využití pro příslušný měsíc. Spojnicový graf znázorňuje trend procenta nárůstu vykreslený na sekundární ose Y.
- Pomocí posuvníku v horní části grafu se posunete doprava doleva podél osy x nebo se zaměřte na konkrétní datové body.

### <a name="usage-trend-by-offers-and-plans"></a>Využití trendů podle nabídek a plánů

Tento graf znázorňuje trend normalizovaného využití pro vybrané plány (dříve nazývané SKU) nabídky. Tabulek výsledků nabídky zobrazuje nejvyšší 50 nabídky s nejvyšším využitím, seřazené podle hodin využití. Tabulek výsledků plánu zobrazuje horních 50 plánů s nejvyšším využitím vybrané nabídky.

### <a name="usage-by-offer-type"></a>Využití podle typu nabídky

- Výsečový graf **využití podle typu nabízí** uspořádání využití podle typu nabídky.
- Horní nabídky se zobrazují v grafu a ostatní nabídky jsou seskupené jako zbývající vše.
- Graf **trendu** zobrazuje trendy růstu po měsících. Sloupec month představuje využití v nejvyšší nabídce v daném měsíci.

### <a name="usage-by-vm-size"></a>Využití podle velikosti virtuálního počítače

Tento graf znázorňuje trend využití vybraných velikostí virtuálních počítačů (maximálně pět) pro všechny vaše nabídky a plány. Sloupcový graf je skládaný s hodinami využití vybraných velikostí virtuálních počítačů.

Tabulek výsledků zobrazuje nejvyšší 50 velikosti virtuálních počítačů s nejvyšším využitím a seřazenými podle hodin využití.

### <a name="usage-by-sales-channel"></a>Využití podle prodejního kanálu

- Výsečový graf využití podle prodejních kanálů uspořádává využití podle prodejního kanálu.
- Nejvyšší prodejní kanál s nejvyšším využitím se zobrazuje v grafu a zbytek prodejního kanálu se seskupuje jako "REST All".
- Sloupec month (měsíc) představuje využití nejdůležitějším prodejním kanálem v daném měsíci.
- Funkce tohoto grafu jsou stejné jako u grafu "využití podle nabídek".

### <a name="detailed-usage-data"></a>Podrobná data o využití

**Tabulka s podrobnostmi o využití** zobrazuje číslovaný seznam prvních 1000 záznamů o využití seřazených podle využití.

- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do souboru CSV, pokud je počet záznamů menší než 1000.
- Pokud počet záznamů překročí 1000, exportují se data asynchronně na stránce ke stažení, která bude k dispozici po dobu následujících 30 dnů.
- Použijte filtry na **podrobná data o využití** , abyste zobrazili jenom data, která vás zajímají. Filtrovat data podle země/oblasti, prodejního kanálu, typu licence Marketplace, typu použití, názvu nabídky, typu nabídky, bezplatných zkušebních verzí, ID předplatného Marketplace, ID zákazníka a název společnosti.

> [!NOTE]
> Vyberte **typ použití** ve filtru stránky pro zobrazení grafů na stránce v zobrazení "normalizované zobrazení" nebo "raw". Výchozím zobrazením pro tyto grafy je "normalizované zobrazení".

**Filtry stránky použití** jsou aplikovány na úrovni stránky. Můžete vybrat více filtrů pro vykreslení grafu pro kritéria, která chcete zobrazit, a požadovaná data se zobrazí v mřížce/exportu podrobných dat o využití. Filtry se aplikují na extrahovaná data pro rozsah dat, který jste vybrali v pravém horním rohu stránky objednávky.

- **Typy nabídek** a **názvy nabídek** jsou uvedeny pouze pro nabídky, které jste získali během vybraného rozsahu dat. Názvy nabídek v seznamu se zobrazí pro typy nabídek, které jsou vybrány ze seznamu.
- Výchozí výběr je "All" pro každou z možností filtrování, s výjimkou **typu použití**. Výchozí volbou pro **typ využití** je normalizované využití. Chcete-li zobrazit nezpracované využití v grafech, vyberte možnost "hrubá spotřeba".
- Použité filtry zobrazují výběr počtu pro výběry filtru, které byly provedeny. Použité filtry nejsou zobrazeny pro výchozí výběry.

> [!NOTE]
> Podrobná definice každého pole v mřížce "detailní objednávka data", filtry stránky a všechny možné výběry jsou definovány v části slovník dat v článku [Nejčastější dotazy a terminologie](link needed) .

Karta **využití měřené fakturace** prezentuje informace o použití pro typy nabídek, kde využití se měří podle dimenze měřiče. Nadlimitní typ SaaS nabídky se teď prezentuje. Karta zobrazuje grafické reprezentace nadlimitních trendů pro využití SaaS měření využívání dat:

- Trend nadlimitního **využití podle dimenze měřiče**: zobrazí měsíční trend nadlimitku pro vybranou dimenzi nabídky. Osa X představuje měsíc a osa Y představuje množství využití. Jednotka měření vlastního měřiče se zobrazí také na ose Y.
- Nadlimitní **trend podle plánu**: představuje trend množství využití vybrané dimenze měřiče podle plánů. Zobrazené plány budou představovat pět nejlepších plánů s největším množstvím využití vybrané nabídky.
- Trend nadlimitního využití **od největších 50 zákazníků**: horní 50 nabídky s nejvyšším počtem hodin používání se zobrazují na ***kartě vedoucí*** a jsou seřazené podle nejvyššího využití vlastního měřiče. Vyberte zákazníka v tabulek výsledků a zobrazte trend využití vybrané dimenze měřiče.
- Trend nadlimitního využití **v rámci hlavních zákazníků**: prezentuje nejlepší percentily zákazníků, které přispívají k celkovému využití%. Horní percentil zákazníka se zobrazuje podél osy X a je určený množstvím využití zákazníka. Osa Y zobrazuje množství využití. Podrobnosti můžete zobrazit tak, že najedete myší na body podél spojnicového grafu.

Pokud máte více nabídek, které používají vlastní měřiče, zobrazí se v sestavě využití měřených faktur informace o využití pro všechny vaše nabídky, a to podle jejich vlastních dimenzí měřičů.

> [!NOTE]
> Podrobnosti o využití a všechny grafy na této stránce se zobrazí pro každou dimenzi měřiče, která je vybrána pro filtr stránky.

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](./customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Microsoft AppSource a Azure Marketplace, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).
