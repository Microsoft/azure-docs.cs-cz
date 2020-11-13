---
title: Řídicí panel využití v komerčních obchodech na webu Marketplace | Azure Marketplace
description: Naučte se, jak získat přístup ke všem metrikám využití a měření fakturace pro nabídky publikované do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 5e2bdec8b3c628f1d470acb3e27bb18eabbc71ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565534"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Využití v analýzách komerčního marketplace

Tento článek poskytuje informace o řídicím panelu využití v partnerském centru. Tento řídicí panel zobrazuje všechny virtuální počítače (VM), které nabízí normalizované využití, nezpracované využití a metriky účtované podle objemu dat na třech oddělených kartách: normalizované využití virtuálních počítačů, nezpracované využití virtuálních počítačů a využití účtované fakturace.

Pokud chcete získat přístup k řídicímu panelu využití v partnerském centru, vyberte v části **komerční Marketplace** možnost **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **využití**.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [terminologie a běžné otázky k komerčním](./partner-center-portal/faq-terminology.md)analýzám na webu Marketplace.

## <a name="usage-dashboard"></a>Řídicí panel využití

Řídicí panel **využití** v nabídce **analyzovat** zobrazuje aktuální objednávky všech nabídek software jako služba (SaaS). Můžete zobrazit grafické reprezentace následujících položek:

- Trend využití
- Normalizované využití pomocí nabídek
- Normalizované využití jinými dimenzemi: velikost virtuálního počítače, prodejní kanály a typy nabídek
- Využití podle geografie
- Podrobná tabulka použití
- Filtry stránek objednávky

> [!NOTE]
> Maximální latence mezi generováním událostí využití a vytvářením sestav v partnerském centru je 48 hodin.

## <a name="elements-of-the-usage-dashboard"></a>Prvky řídicího panelu využití

Následující části popisují, jak použít řídicí panel využití a jak číst data.

### <a name="month-range"></a>Rozsah měsíce

Výběr rozsahu měsíce můžete najít v pravém horním rohu každé stránky. Výstup grafu stránky **využití** si přizpůsobte tak, že vyberete rozsah měsíců na základě posledních 6 nebo 12 měsíců nebo vyberete vlastní rozsah měsíců s maximální dobou trvání 12 měsíců. Výchozí rozsah měsíce (perioda výpočtu) je šest měsíců.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Ukazuje filtry měsíce na řídicím panelu využití.":::

### <a name="usage-trend"></a>Trend využití

V této části najdete celkové hodiny a trend využití všech nabídek, které vaši zákazníci během vybraného období výpočtu spotřebují. Metriky a trendy růstu představují spojnicový graf. Umožňuje zobrazit hodnotu pro každý měsíc přesunutím ukazatele myši na čáru v grafu. Procentuální hodnota pod metrikami využití v widgetu představuje množství nárůstu nebo poklesu během vybraného období výpočtu.

K dispozici jsou dvě reprezentace hodin používání: normalizované využití virtuálních počítačů a nezpracovaného využití virtuálního počítače.

- Normalizované hodiny používání jsou definovány jako hodiny využití normalizované na účet pro počet jader virtuálních počítačů ([počet jader virtuálních počítačů] × [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů].
- Nezpracované hodiny využití jsou definovány jako doba, po kterou byly virtuální počítače spuštěny v hodinách.

[![Ukazuje normalizované využití a nezpracovaná data o využití na řídicím panelu využití.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Normalizované využití pomocí nabídek

V této části najdete celkovou dobu využití a trend pro vaše nabídky založené na používání v Azure Marketplace. Tabulka normalizovaného využití pomocí nabídky je popsána níže.

- **Normalizované využití pomocí** skládaného sloupcového grafu zobrazuje rozpis normalizovaných hodin využití na prvních 5 nabídek v závislosti na vybraném období výpočtu. V grafu se zobrazí pět nejlepších nabídek, zatímco zbytek je seskupený do kategorie **zbývající vše** .
- Skládaný sloupcový graf znázorňuje trend růstu od měsíců po měsících pro vybraný rozsah dat. Sloupce měsíc představují hodiny využití z nabídek s nejvyšším hodinou využití pro příslušný měsíc. Spojnicový graf znázorňuje trend procenta nárůstu vykreslený na sekundární ose Y.
- V legendě můžete vybrat konkrétní nabídky, aby se zobrazily pouze nabídky v grafu.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Znázorňuje normalizované využití dat na řídicím panelu využití.":::

Můžete vybrat jakoukoli nabídku a maximálně tři SKU této nabídky a zobrazit trend využití v měsíci po měsících pro nabídku a vybrané SKU.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Znázorňuje normalizované nabídky využití a data SKU na řídicím panelu využití.":::

### <a name="orders-by-offers-and-skus"></a>Objednávky podle nabídek a SKU

Graf **objednávky podle nabídky a SKU** zobrazuje míry a trendy všech nabídek. Všimněte si, že:

- Horní nabídky se zobrazují v grafu a ostatní nabídky jsou seskupené jako **zbývající vše**.
- V legendě můžete vybrat konkrétní nabídky, aby se zobrazily pouze nabídky v grafu.
- Najetí myší na řez v grafu zobrazuje počet objednávek a procento této nabídky ve srovnání s celkovým počtem objednávek napříč všemi nabídkami.
- **Trend podle nabídky ORDER by** zobrazuje trendy růstu po měsících. Sloupec month (měsíc) představuje počet objednávek podle názvu nabídky. Spojnicový graf zobrazuje trend procenta nárůstu vykreslený na ose z.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Znázorňuje příkazy ORDER by nabídky a SKU na řídicím panelu využití.":::

Můžete vybrat jakoukoli nabídku a maximálně tři SKU nabídky pro zobrazení trendu po měsících pro nabídku, SKU a křesla.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Znázorňuje příkazy ORDER by nabídky a SKU na řídicím panelu využití. Zobrazí se trend nabídky, trend SKU a trend sedadel.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Normalizované využití jinými dimenzemi: velikost virtuálního počítače, prodejní kanály a typ nabídky

Pro dimenze jsou k dispozici tři karty: velikost virtuálního počítače, prodejní kanály a typ nabídky. Můžete zobrazit metriky využití a trend měsíčního měsíce proti každé z těchto dimenzí.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Znázorňuje normalizované využití v grafu jiných dimenzí na řídicím panelu využití.":::

### <a name="usage-by-geography"></a>Využití podle geografie

Pro vybrané období výpočtu zobrazuje heatmapu celkové využití na geografickou dimenzi. Světlá barva světla na mapě představuje nízkou a vysokou hodnotu počtu zákazníků. Vyberte záznam v tabulce pro přiblížení země nebo oblasti.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Znázorňuje normalizovaný graf země využití na řídicím panelu využití.":::

Všimněte si, že:

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek a normalizovaných hodinách použití v určitém umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Vraťte se k původnímu zobrazení výběrem tlačítka **Domů** na mapě.

### <a name="usage-details-table"></a>Tabulka s podrobnostmi o využití

Tabulka s **podrobnostmi o využití** zobrazuje číslovaný seznam prvních 1 000 záznamů o využití seřazených podle využití. Všimněte si, že:

- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do. TSV nebo. Soubor CSV, pokud je počet záznamů menší než 1 000.
- Pokud počet záznamů překročí 1 000, exportují se data asynchronně na stránce ke stažení, která bude k dispozici po dobu následujících 30 dnů.
- Použijte filtry na **podrobná data o využití** , abyste zobrazili jenom data, která vás zajímají. Filtrovat data podle země/oblasti, prodejního kanálu, typu licence Marketplace, typu použití, názvu nabídky, typu nabídky, bezplatných zkušebních verzí, ID předplatného Marketplace, ID zákazníka a název společnosti.

_**Tabulka 1: slovník datových podmínek**_

| Název sloupce | Název atributu | Definice |
| ------------ | ------------- | ------------- |
| ID předplatného Marketplace | ID předplatného Marketplace | Jedinečný identifikátor přidružený k předplatnému Azure, který zákazník použil k nákupu vaší nabídky na komerčním webu Marketplace. IDENTIFIKÁTORem byl dřív identifikátor GUID předplatného Azure. |
| MonthStartDate | Počáteční datum měsíce | Počáteční datum měsíce představuje měsíc nákupu. |
| Typ nabídky | Typ nabídky | Typ nabídky komerčního tržiště. |
| Typ licence Azure | Typ licence Azure | Typ licenční smlouvy používané zákazníky k nákupu Azure. Označuje se také jako kanál. Možné hodnoty jsou:<ui><li>Program Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise prostřednictvím prodejce</li><li>Průběžné platby</li></ul> |
| Typ licence Marketplace | Typ licence Marketplace | Způsob fakturace nabídky komerčního obchodu na webu Marketplace. Možné hodnoty jsou:<ul><li>Účtuje se prostřednictvím Azure.</li><li>Přineste si vlastní licenci</li><li>Free</li><li>Microsoft jako prodejce</li></ul> |
| Skladová položka | Skladová položka | Plán přidružený k této nabídce |
| Země zákazníka | Země nebo oblast zákazníka | Název země nebo oblasti, kterou zadal zákazník. Země nebo oblast se může lišit od země nebo oblasti v předplatném Azure zákazníka. |
| Je verze Preview SKU | Je verze Preview SKU | Hodnota se zobrazí, pokud jste SKU označili jako "Preview". Hodnota bude "Ano", pokud je odpovídajícím způsobem označena skladová jednotka a pouze předplatná Azure, která jsou autorizována, můžete nasadit a použít tuto bitovou kopii. Pokud nebyla položka SKU identifikována jako "Preview", bude hodnota "ne". |
| Typ fakturace SKU | Typ fakturace SKU | Typ fakturace přidružený k jednotlivým položkám SKU v nabídce Možné hodnoty jsou:<ul><li>Free</li><li>Hrazen</li></ul> |
| Velmi interní | Zastaralé | Zastaralé |
| Velikost virtuálního počítače | Velikost virtuálního počítače | U typů nabídek založených na virtuálních počítačích označuje tato entita velikost virtuálního počítače přidruženého k SKU nabídky. |
| Název cloudové instance | Název cloudové instance | Microsoft Cloud, ve kterém došlo k nasazení virtuálního počítače. |
| ServicePlanName | Zastaralé | Zastaralé (stejná definice jako SKU) |
| Název nabídky | Název nabídky | Název nabídky komerčního tržiště. |
| DeploymentMethod | Zastaralé | Zastaralé (stejná definice jako typ nabídky)
 |
| Název společnosti zákazníka | Název společnosti zákazníka | Název společnosti poskytnutý zákazníkem Název může být jiný než město v předplatném Azure zákazníka. |
| Datum využití | Datum využití | Datum generování události využití pro prostředky založené na využití. |
| IsMultisolution | Je více řešení | Označuje, zda je nabídka typu nabídky s více řešeními. |
| Je nový zákazník | Zastaralé | Zastaralé |
| Základní velikost | Základní velikost | Počet jader přidružených k nabídce založené na virtuálním počítači. |
| Typ využití | Typ využití | Označuje, zda je událost využití přidružená k této nabídce jedním z následujících:<ul><li>Normalizované využití</li><li>Nezpracované využití</li><li>Měření využití</li></ul> |
| Datum ukončení zkušební verze | Datum ukončení zkušební verze | Datum ukončení zkušebního období pro tuto objednávku skončí nebo skončí. |
| Zákaznická měna (CC) | Měna zákazníka | Měna, kterou zákazník používá pro transakci komerčního tržiště. |
| Cena (CC) | Cena | Jednotková cena skladové jednotky zobrazená v měně zákazníka |
| Měna výběr (PC) | Měna výběr | Vydavatel se hradí za události využití přidružené k assetu v měně nakonfigurované vydavatelem. |
| Odhadovaná cena (PC) | Odhadovaná cena | Jednotková cena skladové položky v měně konfigurované vydavatelem |
| Odkaz na použití | Odkaz na použití | Zřetězený identifikátor GUID, který se používá k připojení sestavy o využití (na komerčním webu Marketplace) se sestavou transakce výběr. Odkaz na použití je propojen s poli ČísloObjednávky a LineItemId v sestavě transakce výběr. |
| Jednotka využití | Jednotka využití | Jednotka spotřeby přidružená k SKU |
| ID zákazníka | ID zákazníka | Jedinečný identifikátor přiřazený k zákazníkovi. Zákazník může mít nula nebo více Azure Marketplace předplatných. |
| ID fakturačního účtu | ID fakturačního účtu | Identifikátor účtu, ve kterém se vygenerovala faktura Mapování **ID fakturačního účtu** na **CustomerID** pro připojení sestavy transakcí výběr se sestavami zákazník, objednávka a využití. |
| Množství využití | Množství využití | Celkové jednotky využití spotřebované Assetem, který je nasazený zákazníkem.<br>To je založené na položce typu použití. Například pokud je typ použití normalizované využití, pak je množství využití pro normalizované využití. |
| NormalizedUsage | Normalizované využití | Celkový počet normalizovaných jednotek využití spotřebovaných Assetem, který je nasazen zákazníkem.<br>Normalizované hodiny používání jsou definovány jako hodiny využití normalizované na účet pro počet jader virtuálních počítačů ([počet jader virtuálních počítačů] × [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů]. |
| MeteredUsage | Měření využití | Celkové jednotky využití spotřebované měřiči, které jsou nakonfigurovány s nabídkou, která je nasazena zákazníkem. |
| RawUsage | Nezpracované využití | Celkový počet nezpracovaných jednotek využití spotřebovaných Assetem, který je nasazený zákazníkem.<br>Nezpracované hodiny využití se definují jako doba, po kterou jsou virtuální počítače spuštěné z hlediska jednotek využití. |
| Odhadované rozšířené poplatky (CC) | Odhadované rozšířené náklady v měně zákazníka | Označuje poplatky spojené s využitím. Sloupec je produktem ceny (CC) a množství využití. |
| Odhadované rozšířené poplatky (PC) | Odhadované rozšířené poplatky v měně pro výběr | Označuje poplatky spojené s využitím. Sloupec je součinem odhadovaného cenového (POČÍTAČového) a množství využití. |
||||

### <a name="usage-page-filters"></a>Filtry stránek využití

Filtry stránky **použití** jsou aplikovány na úrovni stránky objednávky. Můžete vybrat jeden nebo více filtrů pro vykreslení grafu pro kritéria, která se rozhodnete zobrazit, a data, která chcete zobrazit v mřížce využití a exportu dat. Filtry se aplikují na extrahovaná data pro rozsah měsíců, který jste vybrali v pravém horním rohu stránky používání.

Sestavy widgetů a exportu pro nezpracované využití virtuálních počítačů se podobají normalizovanému využití virtuálních počítačů s následujícími odlišnostmi:

- Normalizované hodiny používání jsou definovány jako hodiny využití normalizované na účet pro počet jader virtuálních počítačů ([počet jader virtuálních počítačů] × [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů].
- Nezpracované hodiny využití se definují jako doba, po kterou jsou virtuální počítače spuštěné z hlediska jednotek využití.

### <a name="metered-billing-usage"></a>Využití měřené fakturace

Karta **měření využití** zobrazuje informace o využití pro typy nabídek, kde je využití měřeno podle dimenze měřiče. Nadlimitní typ SaaS nabídky se teď prezentuje. Karta zobrazuje grafické reprezentace nadlimitních trendů pro využití SaaS měření využívání dat:

- Trend nadlimitního **využití podle dimenze měřiče** : zobrazí měsíční trend nadlimitku pro vybranou dimenzi nabídky. Osa X představuje měsíc a osa Y představuje překročené množství využití. Jednotka měření vlastního měřiče se zobrazí také na ose Y.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Znázorňuje graf normalizovaného využití virtuálních počítačů na řídicím panelu využití.":::

- Trend nadlimitního využití **podle SKU** : představuje trend množství využití vybrané dimenze měřiče podle SKU nebo plánů. Zobrazí se pět nejlepších plánů s největším množstvím využití vybrané nabídky.

- **Trend** nadlimitního využití zákazníky: přední deska zákazníka představuje skládaný seznam zákazníků s nejvyšším počtem hodin používání a zobrazuje se na _vodicí desce_ , seřazené podle nejvyššího využití vlastního měřiče. Vyberte zákazníka na vedoucím panelu a zobrazte trend využití nadlimitního využití vybrané dimenze měřiče.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Ukazuje graf měření využití podle zákazníků na řídicím panelu využití.":::

Pokud máte více nabídek, které používají vlastní měřiče, zobrazí se v sestavě využití měřených faktur informace o využití pro všechny vaše nabídky, a to podle jejich vlastních dimenzí měřičů.

> [!TIP]
> Pomocí ikony stáhnout v pravém horním rohu libovolného widgetu můžete data stáhnout. Svůj názor na každé widgety můžete poskytnout kliknutím na ikonu "palec nahoru" nebo "palec".

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na komerčním webu Marketplace najdete v tématu [přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu [objednávky na řídicím panelu objednávky na komerčním webu Marketplace](./orders-dashboard.md)
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](usage-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./partner-center-portal/downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a Microsoft AppSource, přečtěte si téma [hodnocení & recenze řídicího panelu Analytics v partnerském centru](./partner-center-portal/ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [terminologie a běžné otázky ke komerčním analýzám na webu Marketplace](./partner-center-portal/faq-terminology.md).
