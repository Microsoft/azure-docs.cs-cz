---
title: Co je Azure Cosmos DB analytické úložiště (Preview)?
description: Přečtěte si o Azure Cosmos DB transakční (založený na řádku) a analytickém (sloupcovém) úložišti. Výhody analytického úložiště, dopad na výkon pro rozsáhlé úlohy a automatickou synchronizaci dat z transakčního úložiště do analytického úložiště
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: fdaffef6c682bd1f9c81f14af6cd949816f7555a
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505518"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Co je Azure Cosmos DB analytické úložiště (Preview)?

> [!IMPORTANT]
> Azure Cosmos DB analytické úložiště je momentálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Azure Cosmos DB analytické úložiště je plně izolované úložiště sloupců, které umožňuje rozsáhlou škálu analýz dat v rámci vašeho Azure Cosmos DB, aniž by to mělo dopad na vaše transakční úlohy.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Problémy s velkými objemy analýz provozních dat

Provozní data ve více modelech v kontejneru Azure Cosmos DB se interně ukládají do transakčního úložiště s indexovanými řádky. Formát úložiště řádků je navržen tak, aby umožňoval rychlou transakční čtení a zápisy v časech odezvy v řádu milisekund a provozní dotazy. Pokud vaše datová sada roste velké, složité analytické dotazy mohou být nákladné z hlediska zřízené propustnosti dat uložených v tomto formátu. Vysoká spotřeba zajištěné propustnosti má dopad na výkon transakčních úloh, které používají aplikace a služby v reálném čase.

Tradičně pro analýzu velkých objemů dat se provozní data extrahují z transakčního úložiště Azure Cosmos DB a ukládají se do samostatné datové vrstvy. Data jsou například ukládána v datovém skladu nebo data Lake v vhodném formátu. Tato data se později používají pro rozsáhlou analýzu a analyzují se pomocí výpočetního modulu, jako jsou Apache Spark clustery. Tato oddělení analytických úložišť a výpočetních vrstev z provozních dat má za následek další latenci, protože kanály ETL (extrakce, transformace, zatížení) jsou spouštěny méně často, aby se minimalizoval potenciální dopad na transakční úlohy.

Kanály ETL jsou také složité při zpracování aktualizací provozních dat ve srovnání s zpracováním jenom nově zpracovaných provozních dat. 

## <a name="column-oriented-analytical-store"></a>Sloupcově orientované analytické úložiště

Azure Cosmos DB analytické úložiště řeší problémy se složitostí a latencí, ke kterým dochází v tradičních kanálech ETL. Azure Cosmos DB analytické úložiště může automaticky synchronizovat vaše provozní data do samostatného úložiště sloupce. Formát úložiště sloupců je vhodný pro analytické analytické dotazy, které jsou optimalizované tak, aby se zvýšila latence těchto dotazů.

Pomocí odkazu na Azure synapse teď můžete vytvářet HTAP řešení bez ETL tím, že přímo propojíte s Azure Cosmos DB analytické úložiště z analýzy synapse. Umožňuje provozovat rozsáhlé analýzy v reálném čase pro vaše provozní data.

## <a name="analytical-store-details"></a>Podrobnosti o analytickém úložišti

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

Automatická synchronizace odkazuje na plně spravovanou funkci Azure Cosmos DB, kde se vložení, aktualizace a odstranění dat na provozní data automaticky synchronizují z transakčního úložiště do analytického úložiště téměř v reálném čase do 5 minut.

Funkce automatické synchronizace společně s analytickým úložištěm nabízí tyto klíčové výhody:

#### <a name="scalability--elasticity"></a>Pružnost & škálovatelnosti

Při použití horizontálního dělení Azure Cosmos DB transakční úložiště může elasticky škálovat úložiště a propustnost bez výpadků. Horizontální dělení do transakčního úložiště poskytuje škálovatelnost & flexibility při automatické synchronizaci, aby se zajistilo, že se data synchronizují s analytickým úložištěm téměř v reálném čase. Synchronizace dat probíhá bez ohledu na propustnost transakčních přenosů, ať už je 1000 operací za sekundu nebo 1 000 000 operací/s, a nemá vliv na zřízenou propustnost v transakčním úložišti. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatické zpracování aktualizací schématu

Azure Cosmos DB transakční úložiště je nezávislá schématu a umožňuje iterovat na transakčních aplikacích bez nutnosti zabývat se správou schématu nebo indexu. Na rozdíl od toho Azure Cosmos DB analytické úložiště optimalizuje výkon dotazů v analytických dotazech. Díky funkci automatické synchronizace Azure Cosmos DB spravuje odvození schématu prostřednictvím nejnovějších aktualizací z transakčního úložiště.  Spravuje také reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů.

V případě vývoje schématu, kde se v průběhu času přidávají nové vlastnosti, analytické úložiště automaticky prezentuje sjednocené schéma ve všech historických schématech v transakčním úložišti.

Pokud všechna provozní data v Azure Cosmos DB řídí dobře definované schéma pro analýzy, pak se schéma automaticky odvozuje a v analytickém úložišti se reprezentuje správně. Pokud jsou správně definované schéma pro analýzu, jak je definováno níže, porušuje určité položky, nebudou zahrnuty do analytického úložiště. Pokud máte scénáře blokované v důsledku dobře definovaného schématu definice analýz, pošlete [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com)e-mail.

Dobře definované schéma pro analýzu je definováno s následujícími požadavky:

* Vlastnost má vždycky stejný typ napříč několika položkami.

  * Například `{"a":123} {"a": "str"}` nemá správně definované schéma, protože `"a"` je někdy řetězec a někdy číslo. 
  
    V tomto případě analytické úložiště zaregistruje datový typ `“a”` jako datový typ `“a”` v první vyskytující položce po dobu života kontejneru. Položky, ve kterých se datový typ liší, nebudou `“a”` zahrnuty do analytického úložiště.
  
    Tento stav se nevztahuje na vlastnosti null. Například `{"a":123} {"a":null}` je stále správně definován.

* Typy polí musí obsahovat jeden opakovaný typ.

  * Nejedná se například `{"a": ["str",12]}` o dobře definované schéma, protože pole obsahuje kombinaci typů Integer a String.

* Existuje maximálně 200 vlastností na libovolné úrovni vnoření schématu a maximální hloubka vnořování 5.

  * Položka s 201 vlastnostmi na nejvyšší úrovni nemá dobře definované schéma.

  * Položka s více než pěti vnořenými úrovněmi ve schématu také nemá dobře definované schéma. Například `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`.

* Názvy vlastností jsou v porovnání s rozlišováním velkých a malých písmen jedinečné.

  * Například následující položky nemají dobře definované schéma `{"Name": "fred"} {"name": "john"}` – `"Name"` a `"name"` jsou stejné při porovnání s rozlišováním malých a velkých písmen

### <a name="cost-effective-archival-of-historical-data"></a>Nákladově efektivní archivace historických dat

Vrstvení dat odkazuje na oddělení dat mezi infrastrukturami úložiště optimalizovanými pro různé scénáře. Tím se zlepší celkový výkon a cenová efektivita kompletního datového zásobníku. Díky analytickému úložišti teď Azure Cosmos DB nyní podporuje automatické vrstvení dat z transakčního úložiště do analytického úložiště s různými rozloženími dat. V případě analytického úložiště optimalizovaného z hlediska nákladů na úložiště v porovnání s transakčním úložištěm umožňuje zachovat mnohem delší časové horizonty provozních dat pro účely historických analýz.

Po povolení analytického úložiště založeného na potřebě uchovávání dat v transakčních úlohách můžete nastavit vlastnost transakčního úložiště Time to Live (transakční čas) na hodnotu automaticky odstranit záznamy z transakčního úložiště po určitém časovém období. Podobně "analytické úložiště v reálném čase (analytická hodnota TTL)" umožňuje spravovat životní cyklus dat uchovávaných v analytickém úložišti nezávisle na transakčním úložišti. Když povolíte analytické úložiště a nakonfigurujete vlastnosti TTL, můžete hladce a definovat dobu uchovávání dat pro tyto dva obchody.

### <a name="global-distribution"></a>Globální distribuce

Pokud máte globálně distribuovaný Azure Cosmos DB účet, pak po povolení analytického úložiště pro kontejner bude k dispozici ve všech oblastech tohoto účtu.  Jakékoli změny provozních dat se globálně replikují ve všech oblastech. Analytické dotazy můžete efektivně spouštět proti nejbližší místní kopii vašich dat v Azure Cosmos DB.

### <a name="security"></a>Zabezpečení

Ověřování pomocí analytického úložiště je stejné jako transakční úložiště pro danou databázi. K ověřování můžete použít hlavní klíče nebo klíče jen pro čtení. V synapse studiu můžete využít propojenou službu k tomu, aby se zabránilo vkládání klíčů Azure Cosmos DB do notebooků Spark. Přístup k této propojené službě je k dispozici všem uživatelům, kteří mají přístup k pracovnímu prostoru.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Podpora více běhových prostředí Azure synapse Analytics

Analytické úložiště je optimalizováno pro zajištění škálovatelnosti, pružnosti a výkonu pro analytické úlohy bez jakékoli závislosti na výpočetních časech. Technologie úložiště je samoobslužně spravovaná pro optimalizaci vašich úloh analýzy bez ručního úsilí.

Díky oddělení analytického úložného systému z výpočetního systému se data v Azure Cosmos DB analytickém úložišti dají dotazovat souběžně z různých běhových modulů, které Azure synapse Analytics podporuje. Od dnešního dne podporuje synapse Analytics Apache Spark a SQL Server bez Azure Cosmos DB analytického úložiště.

> [!NOTE]
> Z analytického úložiště se dá číst jenom pomocí synapse analýzy běhu. Data můžete zapsat zpátky do transakčního úložiště jako obsluhující vrstvu.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Stanov

Analytické úložiště sleduje cenový model založený na spotřebě, kde se vám účtují poplatky za:

* Storage (úložiště): objem dat uchovávaných v analytickém úložišti každý měsíc, včetně historických dat definovaných analytickou hodnotou TTL.

* Analytické operace zápisu: plně spravovaná synchronizace aktualizací provozních dat do analytického úložiště z transakčního úložiště (Automatická synchronizace)

* Analytické operace čtení: operace čtení provedené na analytickém úložišti z synapse Analytics Spark a za běhu bez SQL serveru.

> [!NOTE]
> Služba Azure Cosmos DB Analytical Store je aktuálně dostupná ve verzi Public Preview bez jakýchkoli poplatků.

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
*   Zatímco transakční hodnotu TTL je možné nastavit na úrovni kontejneru nebo položky, analytická hodnota TTL se dá na úrovni kontejneru nastavit jenom teď.
*   Můžete dosáhnout delšího uchovávání provozních dat v analytickém úložišti nastavením analytického TTL >= transakční hodnota TTL na úrovni kontejneru.
*   Analytické úložiště je možné provést pro zrcadlení transakčního úložiště nastavením analytického TTL = transakční hodnota TTL.

Když povolíte anaytical Store na kontejneru:
 * pomocí webu Azure Portal je analytická hodnota TTL nastavená na výchozí hodnotu-1. Tuto hodnotu můžete změnit na n sekund, a to tak, že přejdete na nastavení kontejneru v části Průzkumník dat. 
 
 * pomocí sady Azure SDK nebo prostředí PowerShell nebo rozhraní příkazového řádku (CLI) můžete analytickou hodnotu TTL povolit tak, že ji nastavíte na hodnotu-1 nebo n. 

Další informace najdete v tématu [Postup konfigurace analytického TTL na kontejneru](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Odkaz na Azure synapse pro Azure Cosmos DB](synapse-link.md)

* [Začínáme s Azure Synapse Linkem pro Azure Cosmos DB](configure-synapse-link.md)

* [Nejčastější dotazy k Synapse Linku pro Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Případy použití Azure Synapse Linku pro Azure Cosmos DB](synapse-link-use-cases.md)
