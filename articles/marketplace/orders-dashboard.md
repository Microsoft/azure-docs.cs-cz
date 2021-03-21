---
title: Řídicí panel objednávky partnerského centra v analytickém obchodě na komerčních webu | Microsoft AppSource a Azure Marketplace
description: Naučte se, jak získat přístup k analytickým sestavám na vaše komerční nabídky na webu Marketplace v grafickém a ke stažení.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561377"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Objednávky v analýzách komerčního marketplace

Tento článek poskytuje informace o řídicím panelu objednávky v partnerském centru. Tento řídicí panel zobrazuje informace o vašich objednávkách, včetně trendů růstu, prezentovaných ve formátu grafického a ke stažení.

Pokud chcete získat přístup k řídicímu panelu objednávky v partnerském centru, vyberte v části **komerční Marketplace** možnost **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **objednávky**.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [terminologie a běžné otázky k komerčním](./analytics-faq.md)analýzám na webu Marketplace.

## <a name="orders-dashboard"></a>Řídicí panel objednávek

Řídicí panel objednávky zobrazuje aktuální objednávky všech nabídek software jako služba (SaaS). Můžete zobrazit grafické reprezentace následujících položek:

- Trend objednávek
- Objednávky na pracovní stanici a trend webu
- Objednávky podle nabídek a SKU
- Objednávky podle geografického
- Tabulka podrobných objednávek
- Filtry stránek objednávky

> [!NOTE]
> Maximální latence mezi nákupem zákazníků a vykazováním v partnerském centru je 48 hodin.

## <a name="elements-of-the-orders-dashboard"></a>Prvky řídicího panelu objednávky

Následující části popisují, jak používat řídicí panel objednávky a jak číst data.

### <a name="month-range"></a>Rozsah měsíce

Výběr rozsahu měsíce můžete najít v pravém horním rohu každé stránky. Upravte výstup grafů stránky **objednávky** výběrem rozsahu měsíce na základě posledních 6 nebo 12 měsíců nebo výběrem vlastního rozsahu měsíců s maximální dobou trvání 12 měsíců. Výchozí rozsah měsíce (perioda výpočtu) je šest měsíců.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Ukazuje filtry měsíce na řídicím panelu objednávky.":::

> [!NOTE]
> Všechny metriky v widgetech a sestavách vizualizace jsou v době výpočtu vybrané uživatelem.

### <a name="orders-trend"></a>Trend objednávek

V této části najdete graf **objednávek** , který zobrazuje trend aktivních a zrušených objednávek pro vybrané období výpočtu. Metriky a trendy nárůstu jsou reprezentovány spojnicovým grafem a zobrazí hodnotu pro každý měsíc přesunutím ukazatele myši na čáru v grafu. Procentuální hodnota pod metrikami objednávek v widgetu představuje množství nárůstu nebo poklesu během vybraného období výpočtu.

Existují dva objednávky: _aktivní_ a _zrušeno_.

- **Aktivní** se rovná počtu objednávek aktuálně používaných zákazníky v průběhu vybraného rozsahu kalendářních dat.
- **Canceled** se rovná počtu objednávek, které se dřív nakoupily a pak zrušily během vybraného rozsahu dat.

[![Znázorňuje widget Orders na řídicím panelu objednávky, který zobrazuje trend aktivních a zrušených objednávek.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Objednávky podle místa na pracovišti a trendu webu

**Objednávky podle pracovní stanice a** spojnicového grafu založené na lokalitách představují metriku a trend SaaS na pracovišti a SaaS objednávky zakoupené zákazníky (Tento graf obsahuje zrušené objednávky).

[![Znázorňuje widget Orders na řídicím panelu objednávky, který ukazuje objednávky na pracovní stanici a trend lokality.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

Nabídky SaaS můžou používat jeden ze dvou modelů cen s každým plánem: paušální sazba (založená na lokalitě) nebo na uživatele (na základě pracovních stanic).

- **Paušální sazba**: Povolte přístup k vaší nabídce s jednou měsíční nebo roční sazbou za paušální cenu. Tato situace se někdy označuje jako ceny na základě lokality.
- **Na uživatele**: Povolte přístup k vaší nabídce s cenou na základě počtu uživatelů, kteří mají přístup k nabídce nebo zabírat křesla. Pomocí tohoto modelu založeného na využití můžete nastavit minimální a maximální počet uživatelů, které plán podporuje. Můžete vytvořit více plánů pro konfiguraci různých cenových bodů na základě počtu uživatelů. Tato pole jsou volitelná. Pokud je ponecháno bez výběru, počet uživatelů bude interpretován jako neomezený (minimální počet 1 a maximální počet, jak může vaše služba podporovat). Tato pole je možné upravovat v rámci aktualizace vašeho plánu.
- **Měřená fakturace**: podle ceny za paušální cenu. Pomocí tohoto cenového modelu můžete volitelně definovat měřené plány, které používají rozhraní API služby pro měření softwaru Marketplace k účtování zákazníků na využití, na které se nevztahuje paušální sazba.

Další informace o tom, jak na pracovišti, na webu a v účtovaném měření, najdete v tématu [plánování nabídky SaaS pro komerční tržiště](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Objednávky podle nabídek a SKU

Graf ORDER by nabídky a SKU zobrazuje míry a trendy všech nabídek:

- Horní nabídky se zobrazují v grafu a ostatní nabídky jsou seskupené jako **zbývající vše**.
- V legendě můžete vybrat konkrétní nabídky a zobrazit pouze tuto nabídku a přidružené SKU v grafu.
- Najetí myší na řez v grafu zobrazuje počet objednávek a procento této nabídky ve srovnání s celkovým počtem objednávek napříč všemi nabídkami.
- **Trend podle nabídky ORDER by** zobrazuje trendy růstu po měsících. Sloupec month (měsíc) představuje počet objednávek podle názvu nabídky. Spojnicový graf zobrazuje trend procenta nárůstu vykreslený na ose z.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Znázorňuje graf Orders by nabídky na řídicím panelu objednávky.":::

Můžete vybrat jakoukoli nabídku a maximálně tři SKU nabídky pro zobrazení trendu po měsících pro nabídku, SKU a křesla.

### <a name="orders-by-geography"></a>Objednávky podle geografického

Pro vybrané období výpočtu heatmapu zobrazuje celkový počet objednávek a procento růstu nově přidaných objednávek v rámci geografické oblasti.  Světlá barva světla na mapě představuje nízkou a vysokou hodnotu počtu zákazníků. Vyberte záznam v tabulce pro přiblížení konkrétní země nebo oblasti.

[![Znázorňuje geografickou dvojstránku na řídicím panelu objednávky.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Všimněte si, že:

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek a normalizovaných hodinách používání pro konkrétní umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Vraťte se k původnímu zobrazení výběrem tlačítka **Domů** na mapě.

### <a name="orders-details-table"></a>Tabulka podrobností objednávky

Tabulka Podrobnosti objednávky zobrazuje číslovaný seznam 1 000 hlavních objednávek seřazených podle data pořízení.

- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do. Sdílený svazek clusteru nebo. Soubor TSV, pokud je počet záznamů menší než 1 000.
- Pokud zaznamenává číslo přes 1 000, vyexportovaná data se asynchronně nasadí na stránku ke stažení po dobu příštích 30 dnů.
- Použijte filtry v tabulce **Podrobnosti objednávky** , abyste zobrazili jenom data, která vás zajímají. Filtrovat podle země/oblasti, typu licence Azure, typu licence komerčního tržiště, typu nabídky, stavu objednávky, volných stop, ID předplatného komerčního tržiště, ID zákazníka a názvu společnosti.
- Když je objednávka zakoupena chráněným zákazníkem, informace v **objednávkách jsou podrobná data** maskována (* * * * * * * * * * *).

***Tabulka 1: slovník datových podmínek***

| Název sloupce v<br>uživatelské rozhraní | Název atributu | Definice | Název sloupce v programovém programu<br>přístup k sestavám |
| ------------ | ------------- | ------------- | ------------- |
| ID předplatného Marketplace | ID předplatného Marketplace | Jedinečný identifikátor přidružený k předplatnému Azure, který zákazník použil k nákupu vaší nabídky na komerčním webu Marketplace. V případě nabídek infrastruktury se jedná o identifikátor GUID předplatného Azure zákazníka. V případě nabídek SaaS se tato hodnota zobrazuje jako nuly, protože nákupy SaaS nevyžadují předplatné Azure. | ID předplatného Marketplace |
| MonthStartDate | Počáteční datum měsíce | Počáteční datum měsíce představuje měsíc nákupu. Formát je rrrr-mm-dd. | MonthStartDate |
| Typ nabídky | Typ nabídky | Typ nabídky komerčního tržiště. | OfferType |
| Typ licence Azure | Typ licence Azure | Typ licenční smlouvy používané zákazníky k nákupu Azure. Označuje se také jako kanál. Možné hodnoty jsou:<ul><li>Program Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise prostřednictvím prodejce</li><li>Průběžné platby</li></ul> | AzureLicenseType |
| Typ licence Marketplace | Typ licence Marketplace | Způsob fakturace nabídky komerčního obchodu na webu Marketplace. Různé hodnoty jsou:<ul><li>Účtuje se prostřednictvím Azure.</li><li>Přineste si vlastní licenci</li><li>Free</li><li>Microsoft jako prodejce</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Plán přidružený k nabídce | SKU |
| Země zákazníka | Země nebo oblast zákazníka | Název země nebo oblasti, kterou zadal zákazník. Země nebo oblast se může lišit od země nebo oblasti v předplatném Azure zákazníka. | CustomerCountry |
| Je verze Preview SKU | Je verze Preview SKU | Tato hodnota vám umožní zjistit, jestli jste SKU označili jako "Preview". Hodnota bude "Ano", pokud je odpovídajícím způsobem označena skladová jednotka a pouze předplatná Azure, která jsou autorizována, můžete nasadit a použít tuto bitovou kopii. Pokud nebyla položka SKU identifikována jako "Preview", bude hodnota "ne". | IsPreviewSKU |
| ID objednávky | ID objednávky | Jedinečný identifikátor objednávky zákazníka pro vaši komerční službu Marketplace Nabídky založené na využití virtuálních počítačů nejsou přidruženy k objednávce. | OrderId |
| Order Quantity | Order Quantity | Počet prostředků přidružených k ID objednávky pro aktivní objednávky | OrderQuantity |
| Název cloudové instance | Název cloudové instance | Microsoft Cloud, ve kterém došlo k nasazení virtuálního počítače. | CloudInstanceName |
| Je nový zákazník | Je nový zákazník | Hodnota určuje, zda nový zákazník poprvé získal jednu nebo více nabídek. Hodnota bude "Ano", pokud se nachází ve stejném kalendářním měsíci pro "datum získání". Pokud zákazník zakoupil nějaké nabídky před nahlášeným kalendářním měsícem, bude hodnota "ne". | IsNewCustomer |
| Order Status | Order Status | Stav obchodní objednávky na webu Marketplace v době poslední aktualizace dat. | OrderStatus |
| Datum zrušení objednávky | Datum zrušení objednávky | Datum, kdy se objednávka komerčního tržiště zrušila | OrderCancelDate |
| Název společnosti zákazníka | Název společnosti zákazníka | Název společnosti poskytnutý zákazníkem Název může být jiný než město v předplatném Azure zákazníka. | CustomerCompanyName |
| Datum nákupu objednávky | Datum nákupu objednávky | Datum vytvoření objednávky komerčního tržiště Formát je rrrr-mm-dd. | OrderPurchaseDate |
| Název nabídky | Název nabídky | Název nabídky komerčního tržiště. | OfferName |
| Datum ukončení zkušební verze | Datum ukončení zkušební verze | Datum ukončení zkušebního období pro tuto objednávku skončí nebo skončí. | TrialEndDate |
| ID zákazníka | ID zákazníka | Jedinečný identifikátor přiřazený k zákazníkovi. Zákazník může mít nula nebo více Azure Marketplace předplatných. | CustomerId |
| ID fakturačního účtu | ID fakturačního účtu | Identifikátor účtu, ve kterém se vygenerovala faktura Mapování **ID fakturačního účtu** na **CustomerID** pro připojení sestavy transakcí výběr se sestavami zákazník, objednávka a využití. | BillingAccountId |
| AssetCount | Počet assetů | Počet prostředků přidružených k ID objednávky. | Zastaralé |
|||||

### <a name="orders-page-filters"></a>Filtry stránek objednávky

Filtry stránky **objednávky** jsou aplikovány na úrovni stránky objednávky. Můžete vybrat jeden nebo více filtrů pro vykreslení grafu pro kritéria, která se rozhodnete zobrazit, a data, která chcete zobrazit v mřížce/exportu podrobných objednávek dat. Filtry se aplikují na extrahovaná data pro rozsah měsíců, který jste vybrali v pravém horním rohu stránky objednávky.

> [!TIP]
> Pomocí ikony stáhnout v pravém horním rohu libovolného widgetu můžete data stáhnout. Svůj názor na každé widgety můžete poskytnout kliknutím na ikonu "palec nahoru" nebo "palec".

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na komerčním webu Marketplace najdete v tématu [přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./partner-center-portal/downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení & kontroly řídicího panelu Analytics v partnerském centru](./partner-center-portal/ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [terminologie a běžné otázky ke komerčním analýzám na webu Marketplace](./analytics-faq.md).