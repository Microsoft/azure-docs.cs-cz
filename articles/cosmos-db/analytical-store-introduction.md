---
title: Co je Azure Cosmos DB analytické úložiště?
description: Přečtěte si o Azure Cosmos DB transakční (založený na řádku) a analytickém (sloupcovém) úložišti. Výhody analytického úložiště, dopad na výkon pro rozsáhlé úlohy a automatickou synchronizaci dat z transakčního úložiště do analytického úložiště
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: bca4eb7f5f266a639916c0f8e520f025d259c39b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577355"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Co je Azure Cosmos DB analytické úložiště?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB analytické úložiště je plně izolované úložiště sloupců, které umožňuje rozsáhlou analýzu před provozními daty v Azure Cosmos DB, aniž by to mělo dopad na vaše transakční úlohy. 

Azure Cosmos DB transakční úložiště je nezávislá schématu a umožňuje iterovat na transakčních aplikacích bez nutnosti zabývat se správou schématu nebo indexu. Na rozdíl od toho Azure Cosmos DB analytické úložiště optimalizuje výkon dotazů v analytických dotazech. Tento článek popisuje podrobné informace o analytickém úložišti.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Problémy s velkými objemy analýz provozních dat

Provozní data ve více modelech v kontejneru Azure Cosmos DB se interně ukládají do transakčního úložiště s indexovanými řádky. Formát úložiště řádků je navržen tak, aby umožňoval rychlou transakční čtení a zápisy v časech odezvy v řádu milisekund a provozní dotazy. Pokud vaše datová sada roste velké, složité analytické dotazy mohou být nákladné z hlediska zřízené propustnosti dat uložených v tomto formátu. Vysoká spotřeba zajištěné propustnosti má dopad na výkon transakčních úloh, které používají aplikace a služby v reálném čase.

Tradičně pro analýzu velkých objemů dat se provozní data extrahují z transakčního úložiště Azure Cosmos DB a ukládají se do samostatné datové vrstvy. Data jsou například ukládána v datovém skladu nebo data Lake v vhodném formátu. Tato data se později používají pro rozsáhlou analýzu a analyzují se pomocí výpočetního modulu, jako jsou Apache Spark clustery. Tato oddělení analytických úložišť a výpočetních vrstev z provozních dat má za následek další latenci, protože kanály ETL (extrakce, transformace, zatížení) jsou spouštěny méně často, aby se minimalizoval potenciální dopad na transakční úlohy.

Kanály ETL jsou také složité při zpracování aktualizací provozních dat ve srovnání s zpracováním jenom nově zpracovaných provozních dat. 

## <a name="column-oriented-analytical-store"></a>Sloupcově orientované analytické úložiště

Azure Cosmos DB analytické úložiště řeší problémy se složitostí a latencí, ke kterým dochází v tradičních kanálech ETL. Azure Cosmos DB analytické úložiště může automaticky synchronizovat vaše provozní data do samostatného úložiště sloupce. Formát úložiště sloupců je vhodný pro provádění rozsáhlých analytických dotazů optimalizovaným způsobem, což vede k lepší latenci takových dotazů.

Pomocí odkazu na Azure synapse teď můžete vytvářet HTAP řešení bez ETL tím, že přímo propojíte s Azure Cosmos DB analytické úložiště z Azure synapse Analytics. Umožňuje provozovat rozsáhlé analýzy v reálném čase pro vaše provozní data.

## <a name="features-of-analytical-store"></a>Funkce analytického úložiště 

Když v kontejneru Azure Cosmos DB povolíte analytické úložiště, nové úložiště sloupce se interně vytvoří na základě provozních dat ve vašem kontejneru. Toto úložiště sloupce je trvale zachované odděleně od transakčního úložiště orientovaného na řádky tohoto kontejneru. Vložení, aktualizace a odstranění provozních dat se automaticky synchronizují s analytickým úložištěm. K synchronizaci dat nepotřebujete kanál změn ani ETL.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Úložiště sloupců pro analytické úlohy s provozními daty

Analytické úlohy obvykle zahrnují agregace a sekvenční kontroly vybraných polí. Uložením dat v pořadí podle sloupců je v analytickém úložišti možné seskupit hodnoty pro každé pole, které mají být serializovány dohromady. Tento formát omezuje IOPS vyžadované pro kontrolu nebo výpočet statistik pro konkrétní pole. Výrazně vylepšuje dobu odezvy dotazů na kontroly nad velkými datovými sadami. 

Například pokud jsou provozní tabulky v následujícím formátu:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Ukázková provozní tabulka" border="false":::

Úložiště řádků uchovává výše uvedená data v serializovaném formátu na každém řádku na disku. Tento formát umožňuje rychlejší transakční čtení, zápisy a provozní dotazy, jako například "návratové informace o Product1". Jak ale datová sada roste a pokud chcete spouštět složité analytické dotazy na data, může být nákladné. Například pokud chcete získat "trendy prodeje produktu v kategorii s názvem" vybavení "napříč různými obchodními jednotkami a měsíci", je nutné spustit složitý dotaz. Velký počet kontrol této datové sady může být nákladný z hlediska zřízené propustnosti a může také ovlivnit výkon transakčních úloh, které pracují s aplikacemi a službami v reálném čase.

Analytické úložiště, které je úložištěm sloupců, je vhodnější pro takové dotazy, protože pro ně serializace podobná pole dat a snižuje počet vstupně-výstupních operací disku.

Následující obrázek znázorňuje transakční úložiště řádků vs. analytické úložiště sloupců v Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transakční úložiště řádků vs. analytické úložiště sloupců v Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Oddělitelné výkony pro analytické úlohy

V důsledku analytických dotazů neexistuje žádný vliv na výkon transakčních úloh, protože analytické úložiště je oddělené od transakčního úložiště.  Analytické úložiště nepotřebuje přidělit samostatné jednotky žádostí (ru).

### <a name="auto-sync"></a>Automatická synchronizace

Automatická synchronizace odkazuje na plně spravovanou funkci Azure Cosmos DB, kde se vložení, aktualizace a odstranění dat na provozní data automaticky synchronizují z transakčního úložiště do analytického úložiště téměř v reálném čase. Latence automatické synchronizace je obvykle do 2 minut. V případě sdílené databáze propustnosti s velkým počtem kontejnerů může být latence automatické synchronizace v jednotlivých kontejnerech vyšší a trvat až 5 minut. Rádi bychom se dozvěděli, jak tato latence vyhovuje vašim scénářům. V takovém případě se prosím obraťte na [tým Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Funkce automatické synchronizace společně s analytickým úložištěm nabízí tyto klíčové výhody:

### <a name="scalability--elasticity"></a>Pružnost & škálovatelnosti

Při použití horizontálního dělení Azure Cosmos DB transakční úložiště může elasticky škálovat úložiště a propustnost bez výpadků. Horizontální dělení do transakčního úložiště poskytuje škálovatelnost & flexibility při automatické synchronizaci, aby se zajistilo, že se data synchronizují s analytickým úložištěm téměř v reálném čase. Synchronizace dat probíhá bez ohledu na propustnost transakčních přenosů, ať už je 1000 operací za sekundu nebo 1 000 000 operací/s, a nemá vliv na zřízenou propustnost v transakčním úložišti. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatické zpracování aktualizací schématu

Azure Cosmos DB transakční úložiště je nezávislá schématu a umožňuje iterovat na transakčních aplikacích bez nutnosti zabývat se správou schématu nebo indexu. Na rozdíl od toho Azure Cosmos DB analytické úložiště optimalizuje výkon dotazů v analytických dotazech. Díky funkci automatické synchronizace Azure Cosmos DB spravuje odvození schématu přes nejnovější aktualizace z transakčního úložiště.  Spravuje také reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů.

Když se vaše schéma vyvíjí a v průběhu času se přidají nové vlastnosti, analytické úložiště automaticky prezentuje sjednocené schéma ve všech historických schématech v transakčním úložišti.

#### <a name="schema-constraints"></a>Omezení schématu

Následující omezení se vztahují na provozní data v Azure Cosmos DB, když povolíte analytické úložiště pro automatické odvození a reprezentaci schématu:

* Ve schématu můžete mít maximálně 1000 vlastností na úrovni vnoření a maximální hloubku vnoření 127.
  * V analytickém úložišti jsou reprezentovány pouze první vlastnosti 1000.
  * V analytickém úložišti jsou reprezentovány pouze první 127 vnořené úrovně.

* I když dokumenty JSON (a Cosmos DB kolekce/kontejnery) rozlišují velká a malá písmena z perspektivy jedinečnosti, analytické úložiště není.

  * **Ve stejném dokumentu:** Vlastnosti názvů ve stejné úrovni by měly být jedinečné při porovnání malých a velkých písmen. Například následující dokument JSON má "Name" a "Name" na stejné úrovni. I když je to platný dokument JSON, nesplňuje omezení jedinečnosti, a proto nebude plně reprezentovaný v analytickém úložišti. V tomto příkladu jsou názvy "Name" a "Name" stejné, pokud jsou porovnány způsobem, který nerozlišuje velká a malá písmena. Pouze `"Name": "fred"` bude reprezentovat v analytickém úložišti, protože se jedná o první výskyt. A `"name": "john"` nebudou zastoupeny vůbec.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **V různých dokumentech:** Vlastnosti na stejné úrovni a se stejným názvem, ale v různých případech, budou ve stejném sloupci reprezentovány pomocí formátu názvu prvního výskytu. Například následující dokumenty JSON mají `"Name"` a na `"name"` stejné úrovni. Vzhledem k tomu, že první formát dokumentu je `"Name"` , bude použit k reprezentaci názvu vlastnosti v analytickém úložišti. Jinými slovy, název sloupce v analytickém úložišti bude `"Name"` . `"fred"` `"john"` Ve sloupci budou zastoupeny i a `"Name"` .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* První dokument kolekce definuje počáteční schéma analytického úložiště.
  * Vlastnosti v první úrovni dokumentu se reprezentují jako sloupce.
  * Dokumenty s více vlastnostmi, než má počáteční schéma, budou generovat nové sloupce v analytickém úložišti.
  * Sloupce nelze odebrat.
  * Odstranění všech dokumentů v kolekci neresetuje schéma analytického úložiště.
  * Není k dispozici Správa verzí schématu. Poslední verzí odvozenou z transakčního úložiště je to, co se zobrazí v analytickém úložišti.

* V současné době nepodporujeme Azure synapse Spark pro čtení názvů sloupců, které obsahují prázdné znaky (prázdné znaky).

* V souvislosti s hodnotami očekáváte jiné chování `NULL` :
  * Fondy Spark ve službě Azure synapse budou tyto hodnoty číst jako 0 (nula).
  * Fondy bez SQL serveru ve službě Azure synapse budou tyto hodnoty číst jako `NULL` .

* V případě chybějících sloupců očekávat jiné chování:
  * Fondy Spark ve službě Azure synapse budou tyto sloupce zastupovat jako `undefined` .
  * Fondy bez SQL serveru ve službě Azure synapse budou tyto sloupce zastupovat jako `NULL` .

#### <a name="schema-representation"></a>Reprezentace schématu

V analytickém úložišti existují dva režimy reprezentace schématu. Tyto režimy dělají kompromisy mezi jednoduchostí sloupcové reprezentace, zpracováním polymorfních schémat a jednoduchostí dotazování:

* Dobře definovaná reprezentace schématu
* Reprezentace schématu s úplnou věrností

> [!NOTE]
> U účtů rozhraní API SQL (Core) je v případě, že je povolené analytické úložiště, správně definované výchozí reprezentace schématu v analytickém úložišti. Vzhledem k tomu, že Azure Cosmos DB API pro účty MongoDB, je výchozí reprezentace schématu v analytickém úložišti úplnou reprezentací schématu přesnosti. Pokud máte scénáře, které vyžadují jinou reprezentaci schématu, než je výchozí nabídka pro každé z těchto rozhraní API, umožněte vám [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com) .

**Dobře definovaná reprezentace schématu**

Dobře definovaná reprezentace schématu vytvoří jednoduché tabulkové vyjádření nezávislá dat schématu v transakčním úložišti. Dobře definovaná reprezentace schématu má následující požadavky:

* Vlastnost vždy má stejný typ v několika položkách.

  * Například `{"a":123} {"a": "str"}` nemá správně definované schéma, protože `"a"` je někdy řetězec a někdy číslo. V tomto případě analytické úložiště zaregistruje datový typ `"a"` jako datový typ `“a”` v první vyskytující položce po dobu života kontejneru. Dokument bude i nadále součástí analytického úložiště, ale položky, u kterých se datový typ `"a"` liší, nebudou.
  
    Tento stav se nevztahuje na vlastnosti null. Například `{"a":123} {"a":null}` je stále dobře definován.

* Typy polí musí obsahovat jeden opakovaný typ.

  * Například `{"a": ["str",12]}` není správně definované schéma, protože pole obsahuje kombinaci celočíselného a řetězcového typu.

> [!NOTE]
> Pokud Azure Cosmos DB analytické úložiště následuje dobře definovaná reprezentace schématu a výše uvedená specifikace je v rozporu s některými položkami, nebudou tyto položky součástí analytického úložiště.

**Reprezentace schématu s úplnou věrností**

Úplná reprezentace schématu přesnosti je navržená tak, aby zpracovávala celou škálu polymorfních schémat v provozních datech nezávislá schématu. V tomto vyjádření schématu nejsou žádné položky z analytického úložiště vynechány, i když nejsou porušena dobře definovaná omezení schématu (která nemají pole se smíšeným datovým typem ani pole se smíšeným datovým typem).

Toho dosáhnete tak, že přeložíte vlastnosti listu provozních dat do analytického úložiště s odlišnými sloupci na základě datového typu hodnot ve vlastnosti. Názvy vlastností listu se rozšiřují pomocí datových typů jako přípona ve schématu analytického úložiště tak, aby mohly být dotazy bez nejednoznačnosti.

Řekněme například, že se v transakčním úložišti vezme následující ukázkový dokument:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

Vlastnost List `streetNo` v rámci vnořeného objektu `address` bude reprezentována ve schématu analytického úložiště jako sloupec `address.object.streetNo.int32` . Datový typ se přidá jako přípona do sloupce. To znamená, že pokud se do transakčního úložiště přidá jiný dokument, kde hodnota vlastnosti listu `streetNo` je "123" (Všimněte si, že se jedná o řetězec), schéma analytického úložiště se automaticky rozvíjejí bez změny typu dříve zapsaného sloupce. Nový sloupec přidaný do analytického úložiště, ve `address.object.streetNo.string` kterém je tato hodnota "123" uložena.

**Mapování datového typu na mapu přípon**

Tady je mapa všech datových typů vlastností a jejich reprezentace přípon v analytickém úložišti:

|Původní datový typ  |Auditování  |Příklad  |
|---------|---------|---------|
| dvojité |  ". float64" |    24,99|
| Pole | ". Array" |    ["a", "b"]|
|Binární | . Binary |0|
|Logická hodnota    | . bool   |Ano|
|Int32  | . Int32  |123|
|Int64  | ". Int64"  |255486129307|
|Null   | ". null"   | null|
|Řetězec|    řetězec ". String" | "ABC"|
|Timestamp |    ". timestamp" |  Časové razítko (0, 0)|
|DateTime   |". Date"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". objectId"    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Dokument   |". Object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Nákladově efektivní archivace historických dat

Vrstvení dat odkazuje na oddělení dat mezi infrastrukturami úložiště optimalizovanými pro různé scénáře. Tím se zlepší celkový výkon a cenová efektivita kompletního datového zásobníku. Díky analytickému úložišti teď Azure Cosmos DB nyní podporuje automatické vrstvení dat z transakčního úložiště do analytického úložiště s různými rozloženími dat. V případě analytického úložiště optimalizovaného z hlediska nákladů na úložiště v porovnání s transakčním úložištěm umožňuje zachovat mnohem delší časové horizonty provozních dat pro účely historických analýz.

Po povolení analytického úložiště založeného na potřebě uchovávání dat v transakčních úlohách můžete nastavit vlastnost transakčního úložiště Time to Live (transakční čas) na hodnotu automaticky odstranit záznamy z transakčního úložiště po určitém časovém období. Podobně "analytické úložiště v reálném čase (analytická hodnota TTL)" umožňuje spravovat životní cyklus dat uchovávaných v analytickém úložišti nezávisle na transakčním úložišti. Když povolíte analytické úložiště a nakonfigurujete vlastnosti TTL, můžete hladce a definovat dobu uchovávání dat pro tyto dva obchody.

### <a name="global-distribution"></a>Globální distribuce

Pokud máte globálně distribuovaný Azure Cosmos DB účet, pak po povolení analytického úložiště pro kontejner bude k dispozici ve všech oblastech tohoto účtu.  Jakékoli změny provozních dat se globálně replikují ve všech oblastech. Analytické dotazy můžete efektivně spouštět proti nejbližší místní kopii vašich dat v Azure Cosmos DB.

### <a name="security"></a>Zabezpečení

Ověřování pomocí analytického úložiště je stejné jako transakční úložiště pro danou databázi. Pro ověřování můžete použít primární klíče nebo klíče jen pro čtení. V synapse studiu můžete využít propojenou službu k tomu, aby se zabránilo vkládání klíčů Azure Cosmos DB do notebooků Spark. Přístup k této propojené službě je k dispozici všem uživatelům, kteří mají přístup k pracovnímu prostoru.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Podpora více běhových prostředí Azure synapse Analytics

Analytické úložiště je optimalizováno pro zajištění škálovatelnosti, pružnosti a výkonu pro analytické úlohy bez jakékoli závislosti na výpočetních časech. Technologie úložiště je samoobslužně spravovaná pro optimalizaci vašich úloh analýzy bez ručního úsilí.

Díky oddělení analytického úložného systému z výpočetního systému se data v Azure Cosmos DB analytickém úložišti dají dotazovat souběžně z různých běhových modulů, které Azure synapse Analytics podporuje. Od dnešního dne podporuje Azure synapse Analytics Apache Spark fond SQL bez serveru s využitím Azure Cosmos DB analytického úložiště.

> [!NOTE]
> Z analytického úložiště se můžete číst jenom pomocí doby běhu Azure synapse Analytics. Data můžete zapsat zpátky do transakčního úložiště jako obsluhující vrstvu.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Stanov

Analytické úložiště sleduje cenový model založený na spotřebě, kde se vám účtují poplatky za:

* Storage (úložiště): objem dat uchovávaných v analytickém úložišti každý měsíc, včetně historických dat definovaných analytickou hodnotou TTL.

* Analytické operace zápisu: plně spravovaná synchronizace aktualizací provozních dat do analytického úložiště z transakčního úložiště (Automatická synchronizace)

* Analytické operace čtení: operace čtení prováděné s analytickým úložištěm z fondu Azure synapse Analytics Spark a bez serveru SQL pro provoz.

Ceny za analytické úložiště jsou oddělené od cenového modelu úložiště transakcí. V analytickém úložišti neexistuje koncept zřízené ru. Úplné podrobnosti o cenovém modelu pro analytické úložiště najdete na [stránce s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Abyste získali odhad nákladů na nejvyšší úroveň, abyste mohli analytické úložiště v kontejneru Azure Cosmos DB povolit, můžete použít [Plánovač kapacity Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) a získat odhad nákladů na analytické úložiště a operace zápisu. Náklady na operace čtení v analytickém režimu závisí na charakteristikách úloh analýzy, ale jako odhad vysoké úrovně, skenování 1 TB dat v analytickém úložišti obvykle vede k 130 000 operací analýzy čtení a výsledkem je cena $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytická hodnota TTL (Time to Live)

Analytická hodnota TTL pro kontejner určuje, jak dlouho se mají uchovávat data v analytickém úložišti. 

Pokud je analytické úložiště povolené, vkládání, aktualizace a odstraňování provozních dat se automaticky synchronizují z transakčního úložiště do analytického úložiště, bez ohledu na konfiguraci transakčního standardu TTL. Uchovávání těchto provozních dat v analytickém úložišti se dá řídit analytickou hodnotou TTL na úrovni kontejneru, jak je uvedeno níže:

Analytická hodnota TTL na kontejneru je nastavena pomocí `AnalyticalStoreTimeToLiveInSeconds` vlastnosti:

* Pokud je hodnota nastavená na 0, chybí (nebo je nastavená na null): analytické úložiště je zakázané a nereplikují se žádná data z transakčního úložiště do analytického úložiště.

* Je-li k dispozici a hodnota je nastavena na "-1": analytické úložiště uchovává všechna historická data bez ohledu na uchovávání dat v transakčním úložišti. Toto nastavení indikuje, že analytické úložiště nemá nekonečné uchovávání vašich provozních dat.

* Je-li k dispozici a hodnota je nastavena na nějaké kladné číslo "n", vyprší platnost položek z analytického úložiště "n" sekund po datu poslední změny v transakčním úložišti. Toto nastavení se dá využít, pokud chcete uchovávat provozní data po určitou dobu v analytickém úložišti, bez ohledu na uchovávání dat v transakčním úložišti.

Některé body ke zvážení:

*   Až bude analytické úložiště povolené s analytickou hodnotou TTL, dá se později aktualizovat na jinou platnou hodnotu. 
*   Zatímco transakční hodnotu TTL je možné nastavit na úrovni kontejneru nebo položky, analytická hodnota TTL se dá nastavit jenom na úrovni kontejneru v současnosti.
*   Můžete dosáhnout delšího uchovávání provozních dat v analytickém úložišti nastavením analytického TTL >= transakční hodnota TTL na úrovni kontejneru.
*   Analytické úložiště je možné provést zrcadlením transakčního úložiště nastavením analytického TTL = transakční hodnota TTL.

Pokud povolíte analytické úložiště na kontejneru:

* Z Azure Portal je možnost analytického TTL nastavena na výchozí hodnotu-1. Tuto hodnotu můžete změnit na n sekund, a to tak, že přejdete na nastavení kontejneru v části Průzkumník dat. 
 
* V rámci Azure SDK nebo PowerShellu nebo rozhraní příkazového řádku (CLI) je možné povolit možnost analytického TTL nastavením na hodnotu-1 nebo n. 

Další informace najdete v tématu [Postup konfigurace analytického TTL na kontejneru](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Azure Synapse Link pro Azure Cosmos DB](synapse-link.md)

* [Začínáme s Azure Synapse Linkem pro Azure Cosmos DB](configure-synapse-link.md)

* [Nejčastější dotazy k Synapse Linku pro Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Případy použití Azure Synapse Linku pro Azure Cosmos DB](synapse-link-use-cases.md)
