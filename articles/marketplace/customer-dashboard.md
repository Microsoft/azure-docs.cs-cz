---
title: Řídicí panel zákazníci v Microsoft Commercial Marketplace Analytics na partnerském centru, Azure Marketplace a Microsoft AppSource
description: Naučte se, jak získat přístup k informacím o vašich zákaznících, včetně trendů růstu, pomocí řídicího panelu Customers Analytics na komerčním webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c5c50787ef3e287d164e051ece26da4e83199d47
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555631"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel zákazníci na komerčním webu Marketplace Analytics

Tento článek poskytuje informace o řídicím panelu zákazníci v partnerském centru. Tento řídicí panel zobrazuje informace o vašich zákaznících, včetně trendů růstu, prezentovaných ve formátu grafického a ke stažení.

Pokud chcete získat přístup k řídicímu panelu zákazníci v partnerském centru, vyberte v části **komerční Marketplace** možnost **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **zákazníky**.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [terminologie a běžné otázky k komerčním](./analytics-faq.md)analýzám na webu Marketplace.

## <a name="customers-dashboard"></a>Řídicí panel zákazníků

Řídicí panel zákazníci zobrazuje data pro zákazníky, kteří získali vaše nabídky. Můžete zobrazit grafické reprezentace následujících položek:

- Trend aktivních a měněných zákazníků
- Trend růstu zákazníků, včetně stávajících, nových a nezměněných zákazníků
- Zákazníci podle objednávek a využití
- Percentil zákazníků 
- Typ zákazníka podle objednávek a využití
- Zákazníci podle geografie
- Tabulka s podrobnostmi o zákaznících
- Filtry stránky pro zákazníky

> [!NOTE]
> Maximální latence mezi nákupem zákazníků a vykazováním v partnerském centru je 48 hodin.

## <a name="elements-of-the-customers-dashboard"></a>Prvky řídicího panelu Customers

Následující části popisují, jak používat řídicí panel zákazníci a jak číst data.

### <a name="month-range"></a>Rozsah měsíce

Výběr rozsahu měsíce můžete najít v pravém horním rohu každé stránky. Upravte výstup grafů stránky **Customers** tak, že vyberete rozsah měsíců na základě posledních 6 nebo 12 měsíců nebo vyberete vlastní rozsah měsíců s maximální dobou trvání 12 měsíců. Výchozí rozsah měsíce (perioda výpočtu) je šest měsíců.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Znázorňuje měsíční filtry na stránce Customers.":::

> [!NOTE]
> Všechny metriky v widgetech a sestavách vizualizace jsou v době výpočtu vybrané uživatelem.

### <a name="active-and-churned-customers-trend"></a>Trend aktivních a měněných zákazníků

V této části najdete trend růstu vašich zákazníků pro vybrané období výpočtu. Metriky a trendy nárůstu jsou reprezentovány spojnicovým grafem a zobrazují hodnotu pro každý měsíc přesunutím ukazatele myši na čáru v grafu. Procentuální hodnota níže **aktivní zákazníci** v widgetu představuje množství růstu během vybraného období výpočtu. Například následující snímek obrazovky ukazuje nárůst 0,92% pro vybrané období výpočtu.

[![Znázorňuje widgety zákazníků na stránce Customers.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Existují tři _typy zákazníků_: nový, existující a změněný.

- Nový zákazník získal jednu nebo více vašich nabídek poprvé ve vybraném měsíci.
- Stávající zákazník získal jednu nebo více nabídek před vybraným měsícem.
- Změněný zákazník zrušil všechny nabídky, které jste si už koupili. Ovlivnění zákazníci jsou v widgetu trendu zastoupeni na záporné ose.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Trend růstu zákazníků, včetně stávajících, nových a nezměněných zákazníků

V této části najdete trend a počet všech zákazníků, včetně nových, stávajících a nezměněných, s nárůstem trendu po měsících.

- Spojnicový graf představuje celkové procento růstu zákazníka.
- Sloupec month (měsíc) představuje počet zákazníků vydaných novými, stávajícími a změněnými zákazníky.
- Počet ovlivněných zákazníků se zobrazí v záporném směru osy X.
- Můžete vybrat konkrétní položky legendy pro zobrazení podrobnějších zobrazení. Vyberte například možnost Noví zákazníci z legendy a zobrazí se pouze noví zákazníci.
- Najetí myší na sloupec v grafu zobrazí podrobnosti pouze pro tento měsíc.

[![Znázorňuje widget trendů zákazníků na stránce Customers.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Zákazníci podle objednávek/využití

Tabulka **zákazníci podle objednávek/používání** obsahuje tři karty: objednávky, normalizované využití a nezpracované využití. Vyberte kartu **objednávky** pro zobrazení podrobností o objednávce.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Znázorňuje kartu objednávky zákazníků podle objednávky a pomůcky používání na stránce zákazníci.":::

Všimněte si, že:

- Vedoucí deska uvádí podrobnosti o zákaznících seřazené podle počtu objednávek. Po výběru zákazníka se podrobnosti o zákaznících zobrazí v částech sousedících "Podrobnosti", "objednávky podle SKU" a "SKU podle míst".
- Podrobnosti o profilu zákazníka se zobrazí v tomto prostoru, když se vydavatelé přihlásí pomocí role vlastníka. Pokud jsou vydavatelé přihlášení pomocí role přispěvatele, podrobnosti v této části nebudou k dispozici.
- Prstencový graf **ORDER by SKU** zobrazuje Rozpis objednávek koupených pro plány. Zobrazí se pět 5 plánů s nejvyšším počtem objednávek, zatímco zbytek objednávek je seskupený pod položkou **REST All**.
- Graf **SKU podle míst** prstenec zobrazuje rozpis sedadel seřazených pro plány. Zobrazí se horní pět plánů s nejvyššími místy, zatímco zbytek objednávek je seskupený pod položkou **REST All**.

Můžete také vybrat kartu **normalizované využití** nebo **nezpracované využití** a zobrazit podrobnosti o využití.

- Vedoucí deska uvádí podrobnosti o zákaznících seřazené podle počtu hodin využití. Po výběru zákazníka se podrobnosti o zákazníkovi zobrazí v sousedících "podrobnostech", "normalizovaném využití", které nabízí "a" normalizované využití podle velikosti virtuálního počítače (VM) ".
- Podrobnosti o profilu zákazníka se zobrazí v tomto prostoru, když se vydavatelé přihlásí pomocí role vlastníka. Pokud jsou vydavatelé přihlášení pomocí role přispěvatele, podrobnosti v této části nebudou k dispozici.
- **Normalizované využití pomocí nabídky** prstencový graf zobrazuje rozpis využití spotřebovaného nabídkami. Zobrazí se horní pět plánů s nejvyšším počtem použití, zatímco zbývající nabídky jsou seskupeny do části **vše zbývající**.
- **Normalizované využití podle velikosti virtuálních počítačů** : prstencový graf zobrazuje rozpis využití spotřebovaného různými VELIKOSTMI virtuálních počítačů. Zobrazí se 5 nejoblíbenějších velikostí virtuálních počítačů s nejvyšším normalizovaným využitím, zatímco zbytek použití je seskupený v části **zbývající vše**.

### <a name="top-customers-percentile"></a>Percentil hlavních zákazníků

Graf s **Nejdůležitějšími zákazníky** obsahuje tři karty, "objednávky", normalizované využití a "nezpracované využití". _Horní percentil zákazníka_ se zobrazuje podél osy x podle počtu objednávek. Osa y zobrazuje počet objednávek zákazníka. Sekundární osa y (spojnicový graf) zobrazuje kumulativní procento celkového počtu objednávek. Podrobnosti můžete zobrazit tak, že najedete myší na body podél spojnicového grafu.

[![Znázorňuje kartu objednávky na horním widgetu percentilu zákazníka na stránce Customers.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Typ zákazníka podle objednávek a využití

Graf **Order/Usage podle zákaznického typu** zobrazuje počet objednávek, normalizované využití a hodiny nezpracovaných hodin, které se rozdělí mezi nové zákazníky a stávající zákazníci. na základě výběru objednávek, normalizovaného a nezpracovaného využití v grafu.

Tento graf znázorňuje následující:

- Nový zákazník získal jednu nebo více nabídek nebo vykázaných použití poprvé v rámci stejného kalendářního měsíce (osa y).
- Stávající zákazník předtím získal nabídku od vás nebo oznámil použití před uvedením kalendářního měsíce (na ose y).

[![Znázorňuje kartu objednávky na ovládacím prvku objednávky podle typu zákazníka na stránce zákazníci.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Zákazníci podle geografie

Pro vybrané období výpočtu heatmapu zobrazuje celkový počet zákazníků a procento nově přidaných zákazníků na geografickou dimenzi. Světlá barva světla na mapě představuje nízkou a vysokou hodnotu počtu zákazníků. Vyberte záznam v tabulce pro přiblížení země nebo oblasti.

[![Znázorňuje kartu objednávky na widgetu Orders by geografie na stránce Customers.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Všimněte si, že:

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek, normalizovaných hodinách využití v určitém umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Vraťte se k původnímu zobrazení výběrem tlačítka **Domů** na mapě.

### <a name="customer-details-table"></a>Tabulka s podrobnostmi o zákaznících

Tabulka **Podrobnosti o zákazníkovi** zobrazuje číslovaný seznam prvních 1 000 zákazníků seřazených podle data prvního získání jedné z vašich nabídek. Oddíl můžete rozbalit tak, že na pásu karet podrobnosti vyberete ikonu rozšíření.

Všimněte si, že:

- Osobní údaje zákazníka budou k dispozici jenom v případě, že zákazník poskytl souhlas. Tyto informace můžete zobrazit pouze v případě, že jste se přihlásili s úrovní oprávnění role vlastníka.
- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do. Sdílený svazek clusteru nebo. Soubor TSV, pokud je počet záznamů menší než 1 000.
- Pokud je počet záznamů větší než 1 000, vyexportovaná data se asynchronně nasadí na stránku ke stažení na následujících 30 dnů.
- Použijte filtry na tabulku, abyste zobrazili jenom data, která vás zajímají. Filtrovat data podle názvu společnosti, ID zákazníka, ID předplatného Marketplace, typu licence Azure, datum získání, ztracené datum, E-mail zákazníka, země zákazníka/oblast/stav/Město/PSČ, jazyk zákazníka atd.
- Když je nabídka koupena chráněným zákazníkem, informace v **podrobnostech o zákazníkovi** budou maskovány (* * * * * * * * * * * *).
- Podrobnosti o dimenzi zákazníka, jako je název společnosti, jméno zákazníka a E-mail zákazníka, jsou na úrovni ID organizace, ne na Azure Marketplace nebo Microsoft AppSource na úrovni transakce.

_**Tabulka 1: slovník datových podmínek**_

| Název sloupce v<br>uživatelské rozhraní | Název atributu | Definice | Název sloupce v programovém programu<br>přístup k sestavám |
| ------------ | ------------- | ------------- | ------------- |
| ID předplatného Marketplace | ID předplatného Marketplace | Jedinečný identifikátor přidružený k předplatnému Azure, který zákazník použil k nákupu vaší nabídky na komerčním webu Marketplace. V případě nabídek infrastruktury se jedná o identifikátor GUID předplatného Azure zákazníka. V případě nabídek SaaS se tato hodnota zobrazuje jako nuly, protože nákupy SaaS nevyžadují předplatné Azure. | MarketplaceSubscriptionId |
| DateAcquired | Datum získání | První datum, kdy zákazník koupil jakoukoli nabídku, kterou jste publikovali. | DateAcquired |
| DateLost | Datum ztráty | Poslední datum, kdy zákazník zrušil poslední z předchozích zakoupených nabídek. | DateLost |
| Název poskytovatele | Název poskytovatele | Název zprostředkovatele zapojeného v relaci mezi společností Microsoft a zákazníkem. Pokud zákazník je podnikem prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele Cloud Solution Provider (CSP), bude to CSP. | ProviderName |
| E-mail poskytovatele | E-mail poskytovatele | E-mailová adresa poskytovatele zapojeného v relaci mezi společností Microsoft a zákazníkem. Pokud zákazník je podnikem prostřednictvím prodejce, bude to prodejce. Pokud se jedná o poskytovatele Cloud Solution Provider (CSP), bude to CSP. | ProviderEmail |
| FirstName | Křestní jméno zákazníka | Křestní jméno zadané zákazníkem. Název může být jiný než název uvedený v předplatném Azure zákazníka. | FirstName |
| LastName | Příjmení zákazníka | Příjmení zadané zákazníkem Název může být jiný než název uvedený v předplatném Azure zákazníka. | LastName |
| E-mail | E-mail zákazníka | E-mailová adresa poskytnutá koncovým zákazníkem E-mail může být jiný než e-mailová adresa v předplatném Azure zákazníka. | E-mail |
| Název společnosti zákazníka | Název společnosti zákazníka | Název společnosti poskytnutý zákazníkem Název může být jiný než město v předplatném Azure zákazníka. | Název CustomerCompany |
| CustomerCity | Město zákazníka | Název města, který zadal zákazník. Město může být jiné než město v předplatném Azure zákazníka. | CustomerCity |
| Poštovní směrovací číslo zákazníka | Poštovní směrovací číslo zákazníka | Poštovní směrovací číslo poskytnuté zákazníkem Kód může být jiný než poštovní směrovací číslo poskytované v rámci předplatného Azure zákazníka. | CustomerPostal kód |
| CustomerCommunicationCulture | Jazyk zákaznické komunikace | Jazyk upřednostňovaný zákazníkem pro komunikaci. | CustomerCommunicationCulture |
| CustomerCountryRegion | Země nebo oblast zákazníka | Název země nebo oblasti, kterou zadal zákazník. Země nebo oblast se může lišit od země nebo oblasti v předplatném Azure zákazníka. | CustomerCountryRegion |
| AzureLicenseType | Typ licence Azure | Typ licenční smlouvy používané zákazníky k nákupu Azure. Označuje se také jako _kanál_. Možné hodnoty jsou:<ul><li>Program Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise prostřednictvím prodejce</li><li>Průběžné platby</li></ul> | AzureLicenseType |
| PromotionalCustomers | Je propagační kontaktní osoba v | Tato hodnota vám umožní zjistit, jestli se zákazník aktivně přihlásil k propagačnímu kontaktu od vydavatelů. V tuto chvíli Neprezentujeme možnost pro zákazníky, takže jsme na vývěsce uvedli "ne". Po nasazení této funkce se odpovídajícím způsobem spustí aktualizace. | PromotionalCustomers |
| CustomerState | Stav zákazníka | Stav pobytu poskytnutého zákazníkem Stav může být jiný než stav uvedený v předplatném Azure zákazníka. | CustomerState |
| CommerceRootCustomer | Kořenový zákazník pro Commerce | Jedno ID fakturačního účtu může být přidruženo k několika zákaznickým ID.<br>Jedna kombinace ID fakturačního účtu a ID zákazníka může být přidružená k několika předplatným komerčního tržiště.<br>Kořenový zákazník pro obchodní oddělení označuje název zákazníka předplatného. | CommerceRootCustomer |
| ID zákazníka | ID zákazníka | Jedinečný identifikátor přiřazený k zákazníkovi. Zákazník může mít nula nebo více Azure Marketplace předplatných. | CustomerId |
| ID fakturačního účtu | ID fakturačního účtu | Identifikátor účtu, ve kterém se vygenerovala faktura Mapování **ID fakturačního účtu** na **CustomerID** pro připojení sestavy transakcí výběr se sestavami zákazník, objednávka a využití. | BillingAccountId |
|||||

### <a name="customers-page-filters"></a>Filtry stránky pro zákazníky

Filtry stránky Customers jsou aplikovány na úrovni stránky Customers. Můžete vybrat více filtrů pro vykreslení grafu pro kritéria, která se rozhodnete zobrazit, a data, která chcete zobrazit v mřížce a exportu podrobných objednávek. Filtry se aplikují na extrahovaná data pro rozsah měsíců, který jste vybrali v pravém horním rohu stránky Customers.

> [!TIP]
> Pomocí ikony stáhnout v pravém horním rohu libovolného widgetu můžete data stáhnout. Svůj názor na každé widgety můžete poskytnout kliknutím na ikonu "palec nahoru" nebo "palec".

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na komerčním webu Marketplace najdete v tématu [přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./partner-center-portal/downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a Microsoft AppSource, přečtěte si téma [hodnocení & recenze řídicího panelu Analytics v partnerském centru](./partner-center-portal/ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [terminologie a běžné otázky ke komerčním analýzám na webu Marketplace](./analytics-faq.md).
