---
title: Řídicí panel zákazníka v Microsoft Commercial Marketplace Analytics na partnerském centru, Azure Marketplace a Microsoft AppSource
description: Naučte se, jak získat přístup k informacím o vašich zákaznících, včetně trendů růstu, pomocí řídicího panelu zákazníka na komerčním webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 44e992bb1445e1e58f42ec150a8e2c0682eea98b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317582"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Zákazníci v analýzách komerčního marketplace

Tento článek poskytuje informace o **řídicím panelu zákazníka** v partnerském centru. Tento řídicí panel zobrazuje informace o vašich zákaznících, včetně trendů růstu, prezentovaných ve formátu grafického a ke stažení.

Pokud chcete získat přístup k **řídicímu panelu zákazníka**, otevřete řídicí panel **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** v rámci komerčního tržiště.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).

## <a name="customer-dashboard"></a>Řídicí panel zákazníků

**Řídicí panel zákazníka** v nabídce **analyzovat** zobrazuje data pro zákazníky, kteří získali vaše nabídky. Můžete zobrazit grafické reprezentace následujících položek:

- [Shrnutí zákazníka](#customer-summary)
- [Zákazník podle geografie](#customer-by-geography)
- [Trendy zákazníků](#customer-trends)
- [Zákazníci podle objednávek a využití](#customers-by-orders-and-usage)
- [Zákazníci podle plánů](#customers-by-plans)
- [Objednávky a využití podle typu zákazníka](#orders-and-usage-by-customer-type)
- [Tabulka s podrobnostmi o zákaznících](#customer-details-table)
- [Filtry stránek zákazníka](#customer-page-filters)

Maximální latence mezi nákupem zákazníků a vykazováním v partnerském centru je 48 hodin.

### <a name="customer-summary"></a>Shrnutí zákazníka

V části Customer Summary (souhrn zákazníka) se zobrazuje počet všech zákazníků, včetně nových, stávajících a nezměněných dat, ve vybraném období.

- Celkový počet zákazníků je definován jako počet všech zákazníků, kteří si nabídku zakoupili, a mají alespoň jednu objednávku, která nebyla zrušena.
- Procentuální podíl růstu v porovnání s předchozím měsícem je označen číslem a zeleným indikátorem zeleného nebo klesajícího indikátoru červenou barvou.
- Trendy růstu jsou znázorněny pomocí pruhových grafů a zobrazí hodnotu pro každý měsíc přesunutím ukazatele myši na sloupce grafu.

Existují tři **typy zákazníků**: nový, existující a změněný.

- Nový zákazník získal jednu nebo více vašich nabídek poprvé ve vybraném měsíci.
- Stávající zákazník získal jednu nebo více nabídek před vybraným měsícem.
- Změněný zákazník zrušil všechny nabídky, které jste si už koupili.

### <a name="customer-by-geography"></a>Zákazník podle geografie

Graf **zákazníka podle geografického** grafu zobrazuje počty všech zákazníků a zákazníků získaných během vybraného rozsahu kalendářních dat, které jsou mapovány na základě země nebo oblasti zákazníka. Světlá barva světla na mapě představuje nízkou a vysokou hodnotu počtu zákazníků. Vyberte záznam v tabulce pro přiblížení země nebo oblasti.

Heat Mapa zobrazuje počet zákazníků a procento podle země/oblasti zákazníka. Mapu můžete přesunout tak, aby se zobrazilo přesné umístění a přiblížení do konkrétního umístění. Tato mapa má doplňkovou mřížku, která vám umožní zobrazit% zákazníků podle umístění a také nově přidaných zákazníků do tohoto umístění.

### <a name="customer-trends"></a>Trendy zákazníků

Graf **trendů zákazníků** zobrazuje počet všech zákazníků, včetně nových, stávajících a nezměněných, s nárůstem trendu po měsících.

- Spojnicový graf představuje celkové procento růstu zákazníka.
- Sloupec month (měsíc) představuje počet zákazníků vydaných novými, stávajícími a změněnými zákazníky.
- Počet ovlivněných zákazníků se zobrazí na záporném směru osy Y.
- Můžete vybrat konkrétní položky legendy pro zobrazení podrobnějších zobrazení. Vyberte například možnost Noví zákazníci z legendy, aby se zobrazily pouze noví zákazníci.
- Pomocí posuvníku v horní části grafu se můžete posunout doprava a doleva na ose x a soustředit se na konkrétní datové body, které se mají zobrazit podrobněji.
- Po najetí myší na sloupec grafu se zobrazí podrobnosti jenom pro tento měsíc.

### <a name="customers-by-orders-and-usage"></a>Zákazníci podle objednávek a využití

Tabulka **zákazníci podle objednávek/používání** obsahuje tři karty, "objednávky", normalizované využití a nezpracované využití. " **Horní percentil zákazníka** se zobrazuje podél osy x podle počtu objednávek. Osa y zobrazuje počet objednávek zákazníka. Sekundární osa y (spojnicový graf) zobrazuje kumulativní procento celkového počtu objednávek. Podrobnosti můžete zobrazit tak, že najedete myší na body podél spojnicového grafu.

Příklad najdete v následujícím grafu pro normalizované využití: horní 30. percentil zákazníků přispívá k 87% normalizovaného využití na základě součtu. 30. percentil zákazníků přispívá jenom 1 570 000 hodin využití.

### <a name="customers-by-plans"></a>Zákazníci podle plánů

Grafy **zákazníci podle plánů nebo použití** jsou popsány níže.

1. Vedoucí deska zobrazuje podrobnosti o hlavních 50 zákaznících seřazených podle počtu objednávek. Po výběru zákazníka se podrobnosti o zákazníkovi zobrazí v oddílech 2, 3 a 4 tohoto vedoucího senátu.
2. Podrobnosti o profilu zákazníka se zobrazí v tomto prostoru, když se vydavatelé přihlásí pomocí role vlastníka. Pokud jsou vydavatelé přihlášení pomocí role přispěvatele, podrobnosti v této části nebudou k dispozici.
3. Prstencový graf **Orders by plánuje** zobrazit rozpis objednávek koupených pro plány. Zobrazí se horní pět plánů s nejvyšším počtem objednávek, zatímco zbytek objednávek je seskupený pod položkou "REST All".
4. Prstencový graf **křesla podle plánů** zobrazuje rozpis sedadel seřazených pro plány. Zobrazí se horní pět plánů s nejvyššími místy, zatímco zbytek objednávek je seskupený pod položkou REST ALL.

### <a name="orders-and-usage-by-customer-type"></a>Objednávky a využití podle typu zákazníka

Graf **Order/Usage podle zákaznického typu** zobrazuje počet objednávek, normalizované využití a hodiny nezpracovaných hodin, které se rozdělí mezi nové zákazníky a stávající zákazníci. na základě výběru objednávek, normalizovaného a nezpracovaného využití v grafu.

- Nový zákazník získal jednu nebo více nabídek nebo vykázaných použití poprvé v rámci stejného kalendářního měsíce (osa y).
- Stávající zákazník předtím získal nabídku od vás nebo oznámil použití před uvedením kalendářního měsíce (na ose y).

### <a name="customer-details-table"></a>Tabulka s podrobnostmi o zákaznících

Tabulka **Podrobnosti o zákazníkovi** zobrazuje číslovaný seznam prvních 1000 zákazníků seřazených podle data prvního získání jedné z vašich nabídek.

- Osobní údaje zákazníka budou k dispozici jenom v případě, že zákazník poskytl souhlas. Tyto informace můžete zobrazit pouze v případě, že jste přihlášeni s úrovní oprávnění role vlastníka. Přečtěte si další informace o uživatelských rolích a oprávněních.
- Každý sloupec v mřížce lze seřadit.
- Data je možné extrahovat do souboru TSV, pokud je počet záznamů menší než 1000.
- Pokud je počet záznamů větší než 1000, exportovaná data se asynchronně nasadí na stránku ke stažení na následujících 30 dnů.
- Použijte filtry na tabulku, abyste zobrazili jenom data, která vás zajímají. Filtrovat data podle názvu společnosti, ID zákazníka, ID předplatného Marketplace, typu licence Azure, datum získání, ztracené datum, E-mail zákazníka, země zákazníka/oblast/stav/Město/PSČ, jazyk zákazníka atd.
- Když je nabídka koupena chráněným zákazníkem, informace v **podrobnostech o zákazníkovi** budou maskovány (* * * * * * * * * * * *).
- Podrobnosti o dimenzi zákazníka, jako je název společnosti, jméno zákazníka a E-mail zákazníka, jsou na úrovni ID organizace, nikoli na Azure Marketplace nebo na úrovni transakce AppSource.

### <a name="customer-page-filters"></a>Filtry stránek zákazníka

Filtry **stránky Customers** jsou aplikovány na úrovni stránky Customers. Můžete vybrat více filtrů pro vykreslení grafu pro kritéria, která se rozhodnete zobrazit, a data, která chcete zobrazit v mřížce/exportu podrobných objednávek dat. Filtry se aplikují na extrahovaná data pro rozsah dat, který jste vybrali v pravém horním rohu stránky objednávky.

>[!NOTE]
> Podrobné definice pro každé pole v mřížce zákazníka, filtry stránky a jejich možné výběry jsou umístěny v níže uvedeném článku s nejčastějšími dotazy.

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na obchodním tržišti partnerského centra najdete v tématu [analýzy pro komerční tržiště v partnerském centru](./analytics.md).
- Grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaši nabídku, najdete v tématu [souhrnný řídicí panel v části komerční analýza na webu Marketplace](./summary-dashboard.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](./orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](./usage-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení a recenze řídicího panelu na komerčních analýzách na webu Marketplace](./ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [Nejčastější dotazy a terminologie pro komerční analýzy na webu Marketplace](./faq-terminology.md).
