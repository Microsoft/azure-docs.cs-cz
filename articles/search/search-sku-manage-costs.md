---
title: Odhad kapacity a nákladů
titleSuffix: Azure Cognitive Search
description: Přečtěte si Rady k odhadu kapacity a správě nákladů na službu Search, včetně infrastruktury a nástrojů v Azure, a osvědčených postupů pro efektivnější používání prostředků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577640"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>Jak odhadnout kapacitu a náklady na službu Azure Kognitivní hledání

V rámci plánování kapacity pro Azure Kognitivní hledání vám následující doprovodné materiály pomůžou snížit náklady nebo efektivněji spravovat náklady:

+ Vytvořte všechny prostředky ve stejné oblasti nebo v několika oblastech, abyste minimalizovali nebo vyloučili poplatky za šířku pásma.

+ Konsolidujte všechny služby do jedné skupiny prostředků, jako je například Azure Kognitivní hledání, Cognitive Services a jakékoli další služby Azure používané ve vašem řešení. V Azure Portal Najděte skupinu prostředků a pomocí **cost Managementch** příkazů si projděte informace o skutečné a předpokládané útratě.

+ Zvažte webovou aplikaci Azure pro front-end aplikaci, aby žádosti a odpovědi zůstaly v rámci hranice datového centra.

+ Nahorizontální navýšení operací náročných na prostředky, jako je indexování, a následná změna nastavení pro běžné úlohy dotazů. Začněte s minimální konfigurací pro Azure Kognitivní hledání (jeden SU tvořený jedním oddílem a jednou replikou) a pak sledujte aktivitu uživatelů, abyste mohli identifikovat vzorce používání, které by znamenaly nutnost větší kapacity. Pokud existuje předvídatelný vzor, může být možné synchronizovat škálování s aktivitou (pro automatizaci musíte napsat kód).

Fakturovatelné události, vzorec fakturace a Fakturovatelná sazba jsou vysvětleny v tématu [Výběr cenové úrovně](search-sku-tier.md). Kromě toho můžete navštívit [fakturační a nákladovou správu](../cost-management-billing/cost-management-billing-overview.md) pro integrované nástroje a funkce týkající se útraty.

Vypínání vyhledávací služby na dočasné bázi není možné. Vyhrazené prostředky jsou vždycky v provozu a jsou vyhrazené pro vaše výhradní použití po dobu života vaší služby. Odstranění služby je trvalé a zároveň odstraní přidružená data.

V rámci samotné služby je jediným způsobem, jak snížit vaše vyúčtování, je snížit počet replik a oddílů na úroveň, která stále poskytuje přijatelný výkon a [dodržování předpisů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), nebo vytvořit službu na nižší úrovni (hodinové sazby S1 jsou nižší než ceny S2 a S3). Za předpokladu, že jste službu zařídili na konci vašich projekce zatížení, můžete při zvětšování služby vytvořit druhou větší službu, znovu sestavit indexy na druhou službu a pak odstranit první z nich.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak vyhodnotit požadavky na kapacitu

Ve službě Azure Kognitivní hledání je kapacita strukturovaná jako *repliky* a *oddíly*.

+ Repliky jsou instance služby Search Service. Každá replika hostuje jednu kopii indexu s vyrovnáváním zatížení. Například služba se šesti replikami má šest kopií každého indexu načteného ve službě.

+ Oddíly ukládají indexy a automaticky rozdělí hledaná data. Dva oddíly rozdělí rejstřík na polovinu, tři oddíly se rozdělí na třetí a tak dále. V souvislosti s kapacitou je *velikost oddílu* primární odlišná funkce mezi vrstvami.

> [!NOTE]
> Všechny úrovně optimalizované pro Standard a úložiště podporují [flexibilní kombinace replik a oddílů](search-capacity-planning.md#chart) , takže můžete [optimalizovat systém pro rychlost nebo úložiště](search-performance-optimization.md) změnou zůstatku. Úroveň Basic nabízí až tři repliky pro vysokou dostupnost, ale má jenom jeden oddíl. Bezplatné úrovně neposkytují vyhrazené prostředky: výpočetní prostředky sdílí více odběratelů.

### <a name="evaluating-capacity"></a>Vyhodnocování kapacity

Kapacita a náklady na provoz služby se dostanou rukou. Vrstvy ukládají omezení na dvou úrovních: úložiště a obsah (například počet indexů). Měli byste si myslet na oba, protože podle toho, co se vám limit dosahuje, je platný limit.

Obchodní požadavky obvykle určují počet indexů, které budete potřebovat. Například je možné, že budete potřebovat globální index pro velké úložiště dokumentů. Případně je možné, že budete potřebovat více indexů na základě oblastí, aplikací nebo Business mezery.

Chcete-li určit velikost indexu, je třeba [ho sestavit](search-what-is-an-index.md). Jeho velikost bude založena na importovaných datech a konfiguraci indexu, například na tom, jestli povolíte moduly pro návrhy, filtrování a řazení.

Pro fulltextové vyhledávání je primární strukturou dat [obrácená struktura indexu](https://en.wikipedia.org/wiki/Inverted_index) , která má jiné charakteristiky než zdrojová data. U převráceného indexu se velikost a složitost určují podle obsahu, nikoli nutně podle množství dat, která do něj zadáte. Velký zdroj dat s vysokou redundancí může mít za následek menší index než menší datová sada, která obsahuje vysoce proměnlivý obsah. Je proto možné pouze odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhaduje budoucí potřeby indexů a úložiště, může to vypadat stejně jako v případě vysokého odhadu. Pokud je kapacita vrstvy příliš nízká, budete muset zřídit novou službu na vyšší úrovni a pak [znovu načíst své indexy](search-howto-reindex.md). Neexistuje žádný místní upgrade služby z jedné úrovně na jinou.
>

## <a name="estimate-with-the-free-tier"></a>Odhad s úrovní Free

Jedním z přístupů k odhadu kapacity je začít s úrovní Free. Mějte na paměti, že bezplatná služba nabízí až tři indexy, 50 MB úložiště a 2 minuty času indexování. Může být náročné odhadnout velikost předpokládaného indexu s těmito omezeními, ale postup je následující:

+ [Vytvořte bezplatnou službu](search-create-service-portal.md).
+ Připravte malou, reprezentativní datovou sadu.
+ [Sestavte počáteční index na portálu](search-get-started-portal.md) a poznamenejte si jeho velikost. Funkce a atributy mají dopad na úložiště. Například přidání návrhů (dotazy hledání podle typu) zvýší požadavky na úložiště. Pomocí stejné datové sady můžete zkusit vytvořit několik verzí indexu s různými atributy každého pole, abyste viděli, jak se požadavky na úložiště liší. Další informace najdete v části ["dopady na úložiště" v tématu Vytvoření základního indexu](search-what-is-an-index.md#index-size).

V případě hrubého odhadu se může tato částka zdvojnásobit do rozpočtu pro dva indexy (vývoj a produkce) a odpovídajícím způsobem zvolit svou úroveň.

## <a name="estimate-with-a-billable-tier"></a>Odhad s fakturovatelnou úrovní

Vyhrazené prostředky můžou sloužit k většímu počtu vzorkování a zpracování za účelem realističtějších odhadů množství indexu, velikosti a svazků dotazů během vývoje. Někteří zákazníci najdou přímo v rámci Fakturovatelné úrovně a pak se znovu vyhodnotí jako vývoj projektu vývoje.

1. [Zkontrolujte omezení služeb na jednotlivých úrovních](./search-limits-quotas-capacity.md#index-limits) , abyste zjistili, jestli nižší úrovně můžou podporovat potřebný počet indexů. V úrovních Basic, S1 a S2 jsou limity indexu 15, 50 a 200, v uvedeném pořadí. Optimalizovaná úroveň úložiště má omezení 10 indexů, protože je navržena tak, aby podporovala nízký počet velmi rozsáhlých indexů.

1. [Vytvoření služby v Fakturovatelné úrovni](search-create-service-portal.md):

    + Pokud si nejste jisti plánovaným zatížením, začněte na stránce Basic nebo S1 nízká.
    + Pokud víte, že budete mít rozsáhlé indexování a načítání dotazů, začněte vysokou, v S2 nebo dokonce S3.
    + Pokud indexuje velké množství dat a zatížení dotazů je relativně nízké, stejně jako u interní obchodní aplikace, začněte s optimalizovaným úložištěm, v L1 nebo L2.

1. [Sestavte počáteční index](search-what-is-an-index.md) pro určení způsobu, jakým zdrojová data přecházejí do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. Na portálu [monitorujte úložiště, omezení služeb, svazek dotazů a latenci](search-monitor-usage.md) . Na portálu se zobrazí dotazy za sekundu, omezené dotazy a latence hledání. Všechny tyto hodnoty vám pomohou při rozhodování, zda jste vybrali správnou úroveň. 

Číslo a velikost indexu jsou stejně důležité pro vaši analýzu. Důvodem je to, že se dosáhne maximálního využití úložiště (oddílů) nebo podle maximálního počtu prostředků (indexy, indexery a tak dále), podle toho, co nastane dřív. Portál vám pomůže sledovat obojí, zobrazuje aktuální využití a maximální limity vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště můžou být neploché, pokud dokumenty obsahují nadbytečné údaje. V ideálním případě dokumenty obsahují pouze data, která potřebujete pro vyhledávání. Binární data se nedají prohledávat a měla by se ukládat samostatně (možná v tabulce Azure nebo v úložišti objektů BLOB). Pole by pak mělo být přidáno do indexu, aby obsahovalo odkaz na adresu URL externích dat. Maximální velikost jednotlivého dokumentu je 16 MB (nebo méně, pokud hromadně nahráváte více dokumentů v jednom požadavku). Další informace najdete v tématu [omezení služby v Azure kognitivní hledání](search-limits-quotas-capacity.md).
>

**Požadavky na svazky dotazů**

Dotazy za sekundu (QPS) jsou důležitou metrikou při vyladění výkonu, ale obecně platí pouze pro zvážení vrstvy, pokud očekáváte, že na začátku budete očekávat velký objem dotazů.

Úrovně Standard můžou poskytovat rovnováhu mezi replikami a oddíly. Můžete zvýšit vyřízení dotazu přidáním replik pro vyrovnávání zatížení nebo přidat oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

Pokud na začátku očekáváte velké svazky dotazů, měli byste zvážit vyšší úrovně Standard, která je zajištěná výkonnějším hardwarem. Pak můžete převzít oddíly a repliky offline, nebo dokonce přepnout na službu nižší úrovně, pokud k těmto svazkům dotazu nedochází. Další informace o tom, jak vypočítat propustnost dotazů, najdete v článku o [výkonu a optimalizaci Azure kognitivní hledání](search-performance-optimization.md).

Vrstvy optimalizované pro úložiště jsou užitečné pro úlohy s velkým objemem dat, které podporují komplexnější úložiště indexů pro dobu, kdy jsou požadavky na latenci dotazů méně důležité. Stále byste měli používat další repliky pro vyrovnávání zatížení a další oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

**Smlouvy o úrovni služeb**

Funkce bezplatné úrovně a verze Preview neposkytují [smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny Fakturovatelné úrovně se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA musíte mít dvě nebo víc replik. Musíte mít tři nebo víc replik pro dotazování a indexování (pro čtení i zápis) SLA. Počet oddílů nemá vliv na SLA.

## <a name="tips-for-tier-evaluation"></a>Tipy pro vyhodnocování vrstev

+ Umožňuje, aby metriky sestavily dotazy a shromažďovat data o vzorech používání (dotazy během pracovní doby, indexování v době mimo špičku). Tato data slouží k informování o rozhodnutích zajišťování služeb. I když to není praktické při hodinovém nebo denním tempo, můžete dynamicky upravovat oddíly a prostředky, aby se na svazcích dotazů vešly plánované změny. Můžete také přizpůsobit neplánované, ale trvalé změny, pokud úrovně dostatečně dostatečně dostatečně neopravňují k zajištění, aby bylo možné provádět akce.

+ Mějte na paměti, že jediným nevýhodouem zřizování je, že pokud jsou skutečné požadavky větší než vaše předpovědi, možná budete muset tuto službu vytrhnout. Aby nedošlo k přerušení služeb, vytvořili byste novou službu na vyšší úrovni a běžela vedle sebe, dokud všechny aplikace a požadavky necílí na nový koncový bod.

## <a name="next-steps"></a>Další kroky

Začněte s bezplatnou úrovní a sestavte počáteční index pomocí podmnožiny vašich dat, abyste pochopili jeho charakteristiky. Struktura dat v Azure Kognitivní hledání je obrácená struktura indexu. Velikost a složitost obráceného indexu se určují podle obsahu. Mějte na paměti, že vysoce redundantní obsah obvykle vede k menšímu indexu než vysoce nepravidelný obsah. Takže charakteristiky obsahu, a ne velikost datové sady, určují požadavky na úložiště indexu.

Jakmile budete mít počáteční odhad velikosti indexu, [zajistěte si Fakturovatelné služby](search-create-service-portal.md) na jedné z vrstev popsaných v tomto článku: Basic, Standard nebo Storage optimalizované. Uvolněte veškerá umělá omezení velikosti dat a [znovu sestavte index](search-howto-reindex.md) tak, aby zahrnoval všechna data, která chcete prohledávat.

Podle potřeby [přidělte oddíly a repliky](search-capacity-planning.md) , abyste získali výkon a škálování, které požadujete.

Pokud máte výkon a kapacitu v pořádku, budete hotovi. V opačném případě znovu vytvořte vyhledávací službu na jiné úrovni, která bude lépe zarovnána podle vašich potřeb.

> [!NOTE]
> Pokud máte otázky, pošlete příspěvek na [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [kontaktujte podporu Azure](https://azure.microsoft.com/support/options/).