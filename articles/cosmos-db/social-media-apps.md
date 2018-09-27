---
title: 'Vzor návrhu služby Azure Cosmos DB: aplikace sociálních médií | Dokumentace Microsoftu'
description: Další informace o vzoru návrhu pro sociální sítě s využitím flexibilní úložiště služby Azure Cosmos DB a dalšími službami Azure.
keywords: aplikace sociálních médií
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maquaran
ms.openlocfilehash: 5c916f847bf5098145c3ed14fad87c7669d916c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222688"
---
# <a name="going-social-with-azure-cosmos-db"></a>Sociální funkce s Azure Cosmos DB
Žijete ve vysoce propojených společnosti znamená, že v určitém okamžiku v životě stanou součástí **sociálních sítí**. Sociální sítě používáte zachovat ve spojení s přáteli, kolegům, řada, nebo někdy také sdílet s lidmi s společné zájmy vaše nadšení.

Jako technici a vývojáři, pravděpodobně vás zajímá, jak tyto sítě úložiště a propojení vašich dat, nebo může mít byl úkol i k vytvoření nebo navrhovat sami sobě nové sociálních sítí pro konkrétní volné místo na trhu. Kdy vzniká významné Otázka: jak jsou tato data uložena?

Předpokládejme, že vytváříte nové a shiny sociální síť, kde vaši uživatelé můžete publikovat články s související média jako obrázky, videa nebo dokonce music. Uživatelé mohou přidávat komentáře na příspěvky a poskytují body pro hodnocení. Bude kanál, které příspěvky, které se zobrazí uživatelům a mohli pracovat na cílové stránce hlavní webové stránky. Toto není znít komplexní (zpočátku), ale z důvodu zjednodušení, Pojďme zastavit existuje (můžete delve do vlastní uživatelské kanály ovlivněny relace, ale přesahuje cílem tohoto článku).

Takže jak to můžete ukládat a kde?

Řada z vás možná prostředí u databází SQL nebo mít aspoň pojem [relační modelování dat](https://en.wikipedia.org/wiki/Relational_model) a můžete mít tendenci spustit vykreslování vypadat přibližně takto:

![Diagram ilustrující relativní relační model](./media/social-media-apps/social-media-apps-sql.png) 

Nemusíte zajistit dokonalou normalizovaná a přehlednou datová struktura... který nemá škálování. 

Nezískávat mě chybný, jsem pracovala s databázemi SQL všechny životnost, jsou skvělé, ale jako platforem vzor, postupů a softwaru není ideální pro každý scénář.

Proč SQL není v tomto scénáři o nejlepší volbu? Podívejme se na struktuře jeden příspěvek kdybych chtěl k zobrazení tohoto příspěvku na webu nebo aplikace, že bude nutné provést dotaz s... Zobrazíte jednu jeden příspěvek, nyní obrázek streamovaných příspěvky, které dynamicky načíst a zobrazit na obrazovce a může se zobrazit kde teď předvedu právě osm spoje tabulky platná (!).

Můžete, samozřejmě, použít obrovské instance SQL s dostatek power vyřešit tisíce dotazů s těmito mnoho spojení k poskytování obsahu, ale skutečně, proč byste, pokud mezi doménami existuje jednodušší řešení?

## <a name="the-nosql-road"></a>Silniční NoSQL
Tento článek vás provede do modelování dat sociální platforma databáze NoSQL Azure [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) v nákladově efektivní způsob přitom můžete využívat další služby Azure Cosmos DB funkce, například [rozhraní Gremlin API](../cosmos-db/graph-introduction.md). Použití [NoSQL](https://en.wikipedia.org/wiki/NoSQL) přístup, ukládání dat ve formátu JSON a použití [denormalizace](https://en.wikipedia.org/wiki/Denormalization), dříve složité příspěvek je možné transformovat do jednoho [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

A s pomocí jediného dotazu a žádná spojení lze získat. To je mnohem více jednoduché a nekomplikované a budget-wise, vyžaduje méně prostředků k dosažení lepších výsledků.

Azure Cosmos DB zajišťuje, že všechny vlastnosti jsou indexovány pomocí jeho automatické indexování, které můžou být [přizpůsobené](indexing-policies.md). Přístup bez schémat umožňují ukládání dokumentů s jinou a dynamické struktur, možná zítra chcete příspěvků na seznam kategorií nebo k nim má přiřazené hashtagy, Cosmos DB bude zpracovávat nové dokumenty s přidané atributy se žádná další práce vyžaduje od nás.

Další příspěvky k nadřazené vlastnosti (zjednodušuje mapování pracovního objektu) lze považovat za komentářů na příspěvek. 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

A všechny sociální interakce může být uložená na samostatný objekt jako čítače:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Vytváření kanálů stačí vytváření dokumentů, které může obsahovat seznam ID příspěvku s danou relevance pořadí:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Můžete mít "posledního" datový proud s příspěvky seřazené podle data vytvoření, "Nejaktivnější" datový proud takových příspěvků s více rád za posledních 24 hodin, dokonce je možné implementovat vlastní datový proud pro každého uživatele na základě logiky jako sledujících a zájmy a stále by šlo seznam  příspěvky. Je na vás o tom, jak vytvářet tyto seznamy, ale zůstane nerušený výkonu čtení. Jakmile získáte jednu z těchto seznamů, vydáte pomocí jediného dotazu pomocí služby Cosmos DB [v operátoru](sql-api-sql-query.md#WhereClause) k získání stránek příspěvků v čase.

Informační kanály datové proudy může být postavená pomocí [Azure App Services](https://azure.microsoft.com/services/app-service/) procesy na pozadí: [Webjobs](../app-service/web-sites-create-web-jobs.md). Po vytvoření příspěvek zpracování na pozadí můžete aktivovat pomocí [služby Azure Storage](https://azure.microsoft.com/services/storage/) [fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) a aktivovat pomocí Webjobs [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementující Publikovat šíření uvnitř streamů v závislosti na vlastní logiku. 

Body a lajky přes příspěvek mohou být zpracovány odložené způsobem pomocí Tento stejný postup k vytvoření konzistentní prostředí.

Sledujících jsou trickier. Cosmos DB má omezení velikosti maximální dokumentu a čtení/zápis velkých dokumentů může mít vliv na škálovatelnost aplikace. Takže si promyslet ukládání sledujících jako dokument s touto strukturou:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

To může fungovat pro uživatele s několika tisíc sledující, ale pokud některé celebrit připojí rozměry, tento přístup se vést k velikost velkých dokumentu a nakonec pravděpodobně dojde k limit velikosti dokumentu.

Tento problém vyřešit, je použít smíšené přístup. Jako část dokumentu statistiky uživatelů můžete ukládat počet sledujících:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

A skutečné graf sledující můžete ukládat pomocí služby Azure Cosmos DB [rozhraní Gremlin API](../cosmos-db/graph-introduction.md), chcete-li vytvořit [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) pro každého uživatele a [hrany](http://mathworld.wolfram.com/GraphEdge.html) , Udržovat "A-způsobem – B" relace. Rozhraní Gremlin API nevztahují teď můžete nejen získat sledujících určitý uživatel ale vytvářet složitější dotazy na dokonce navrhnout lidí v běžných. Pokud chcete přidat do grafu, jako jsou kategorie obsahu, který lidé nebo využijte, zahájením tkaní prostředí, které zahrnují inteligentní zjišťování obsahu, návrhy obsahu, která se, které sledujete, třeba nebo vyhledání lidé, se kterými můžete mít mnoho společných.

Dokument statistické údaje o uživatelích stále slouží k vytvoření karty v uživatelského rozhraní nebo náhledy rychlé profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Adaptivní žebříček přenosových rychlostí" model a data duplicity
Jak možná jste si všimli v dokumentu JSON, který odkazuje na příspěvek, existují více výskytů jednoho uživatele. A jste by mít uhodnout správné, že to znamená, že informace, které představuje uživatele, daný denormalizace, může být k dispozici více než jednom místě.

Aby bylo možné povolit pro rychlejší dotazy, se vám účtovat duplicitních dat. Problém s vedlejším účinkem je, že pokud některé akce, změny dat uživatele, je potřeba najít všechny aktivity mu nikdy nebyla a je všechny aktualizovat. Není zvukové praktické, hned?

Chystáte se vyřešit určením atributů klíč uživatele, který se zobrazí v aplikaci pro každou aktivitu. Pokud vizuálně zobrazit příspěvek ve vaší aplikaci a zobrazit pouze tvůrce příslušného názvu a obrázek, proč ukládání všech dat uživatele v atributu "createdBy"? Pokud pro každý komentáře stačí zobrazit obrázek uživatele, nepotřebujete zbývající jeho informace. Který přichází něco že můžu volat "adaptivní žebříček přenosových rychlostí vzor" do hry.

Pojďme se na informace o uživateli jako příklad:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Zobrazením tyto informace může rychle zjistit, což je důležité informace a který není, tedy vytvoření "Přenosových rychlostí":

![Diagram vzoru adaptivní žebříček přenosových rychlostí](./media/social-media-apps/social-media-apps-ladder.png)

Nejmenší kroku se říká UserChunk, minimální část informace identifikující uživatele a používá se k replikaci dat. Snížením množství duplicitních dat pouze informace o vás bude "Zobrazit" snížení rizika vzniku obrovských aktualizace.

Střední krok se nazývá uživatelem, je úplná data, která se použije na většinu závislé na výkonu dotazů ve službě Cosmos DB, přístupu a kritické. Obsahuje informace UserChunk představované.

Největší je rozšířená uživatele. Obsahuje všechny informace o důležitých uživatele a další data, která nevyžaduje, aby skutečně rychle čtení nebo jeho použití je konečné (např. proces přihlášení). Tato data mohou být uloženy mimo Cosmos DB v Azure SQL Database nebo úložiště tabulek Azure.

Proč by i tyto informace ukládaly na různých místech a rozdělit uživatele? Protože z hlediska výkonu, tím větší dokumenty, costlier dotazy. Zachovat dokumenty tenký správné informace, které se všechny vaše dotazy závislé na výkonu pro sociální sítě a uložit další informace pro konečné scénáře, jako jsou úpravy celých profilů, přihlášení, a to i dolování dat pro analýzy využití a velké objemy dat iniciativy. Ve skutečnosti není vás v případě, shromažďování dat pro dolování dat je pomalejší, protože běží na Azure SQL Database, budete mít problém ale aby uživatelé měli rychlý a tenký prostředí. Uživatel, uložené ve službě Cosmos DB, bude vypadat takto:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

A příspěvek vypadat nějak takto:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

A když úpravy nastane, pokud jeden z atributů u bloku je ovlivněno, je snadné pro vyhledání ovlivněných dokumenty pomocí dotazů, které odkazují na indexované atributy (vybrat * FROM příspěvky p WHERE p.createdBy.id == "edited_user_id") a následnou aktualizací bloky dat.

## <a name="the-search-box"></a>Do vyhledávacího pole
Uživatelé budou generovat naštěstí příliš mnoho obsahu. By měl být schopný poskytnout možnost vyhledávání a najdete obsah, který nemusí být přímo v jejich obsahu datové proudy, pravděpodobně protože neodpovídají autoři a možná se právě pokoušíte najít, staré příspěvek jste před šesti měsíci.

Naštěstí a vzhledem k tomu, že používáte služby Azure Cosmos DB, je možné snadno implementovat pomocí modulu vyhledávání [Azure Search](https://azure.microsoft.com/services/search/) za pár minut a nemusíte psát jediný řádek kódu (jiné než samozřejmě, proces vyhledávání a uživatelského rozhraní).

Proč je to tak jednoduché?

Služba Azure Search implementuje co volají [indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx), procesy na pozadí tohoto volání v úložištích dat a automagically přidání, aktualizace nebo odebrání objektů v indexy. Podporují taky [indexování Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexování objektů BLOB Azure](../search/search-howto-indexing-azure-blob-storage.md) a naštěstí [indexerů Azure Cosmos DB](../search/search-howto-index-documentdb.md). Přechod informace ze služby Cosmos DB do služby Azure Search je jednoduché, jako i ukládání informací ve formátu JSON, stačí [vytvořit Index](../search/search-create-index-portal.md) mapování, které atributy z vašich dokumentů chcete indexovat a je to, během několika minut (závisí na velikosti dat), váš obsah bude k dispozici pro hledání na nejlepší řešení vyhledávání jako služby v infrastruktuře cloudu. 

Další informace o službě Azure Search, můžete navštívit [Hitchhiker od průvodce hledání](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Základní znalosti
Po uložení všech tento obsah, který zvyšuje nebo každým dnem roste, může pro vás přemýšlení: Co mohu využít tento proud informací z mých uživatelů?

Odpověď je jednoduchý: je začít pracovat a Učte se z něj.

Ale co je další? Zahrnují několik příkladů snadno [analýzu subjektivního hodnocení](https://en.wikipedia.org/wiki/Sentiment_analysis), obsahu, doporučení na základě předvolby uživatele nebo dokonce automatizované content moderatoru, který zajistí, že veškerý obsah publikovaný sociálních sítí je bezpečný pro rodinu.

Teď, když mám tady připojeny, vám dovolí přemýšlet pravděpodobně potřebujete některé titul pH.d. v matematické vědy extrahovat tyto vzory a informace z jednoduchého databáze a soubory, ale by byl nesprávný.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)patří [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), je plně spravovaná Cloudová služba, která umožňuje vytvářet pracovní postupy pomocí algoritmů v jednoduché rozhraní pro přetahování myší, kód vlastní algoritmy v [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)) nebo používat některé z již integrované a připravené k použití rozhraní API, jako: [rozhraní Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator, nebo [doporučení](https://gallery.azure.ai/Solution/Recommendations-Solution).

K dosažení některého z těchto scénářů Machine Learning, můžete použít [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) ingestovat informace z různých zdrojů a použijte [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) pro zpracování informací a generovat výstup, která se dá zpracování Azure Machine Learning.

K dispozici další možností je použít [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) k analýze obsahu; nejen můžete je jim lépe porozumět uživatelům (prostřednictvím analýza zapisovaly s [rozhraní Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale může zjišťovat nežádoucí nebo až po zralé obsah a příslušně na ně s [rozhraní API pro počítačové zpracování obrazu](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Služby cognitive Services zahrnuje mnoho out-of-the-box řešení, které nevyžadují jakýkoli druh znalostní báze Machine Learning k použití.

## <a name="a-planet-scale-social-experience"></a>Sociální prostředí globálním měřítku
Je last, ale ne nejméně důležité článku můžu musí řešit: **škálovatelnost**. Při návrhu architektury, je důležité, že jednotlivé komponenty můžete škálovat sama o sobě, buď protože je potřeba zpracovat víc dat, nebo protože budete chtít mít větší zeměpisném pokrytí (nebo obojí!). Naštěstí je dosažení složitý úkol **předpřipravené prostředí** pomocí služby Cosmos DB.

Cosmos DB podporuje [dynamické dělení na oddíly](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of-the-box protože automaticky vytvoří oddíly na základě danou **klíč oddílu** (definované jako jeden z atributů v dokumentech). Definování správného klíče oddílu je třeba provést v době návrhu a dodržujte při tom [osvědčené postupy](../cosmos-db/partition-data.md#designing-for-partitioning) k dispozici; v případě sociálních prostředí, musí být zarovnány strategii dělení do toho, jak dotazovat (čtení v rámci stejného oddíl je žádoucí) a zápis (vyhnout "hotspotů" tím, že rozprostírá zápisy na několik oddílů). Některé možnosti jsou: oddíly na základě dočasné klíče (den/měsíc/týden), podle obsahu kategorie, podle zeměpisné oblasti, podle uživatele. všechno ve skutečnosti závisí na jak budete zadávat dotazy na data a zobrazte ji v sociálních sítích prostředí. 

Jeden je zajímavé bod za zmínku, že Cosmos DB se spustí vaše dotazy (včetně [agregace](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) na všechny oddíly transparentně, nemusíte přidávat žádné logiku růst objemu dat.

Čas, bude nakonec nárůstu provozu a využití prostředků (měřeno v [ru](request-units.md), nebo jednotek žádostí) se zvýší. Bude číst a zapisovat častěji vaší uživatelské základně roste a mohou začít vytváření a čtení více obsahu; schopnost **škálování propustnosti** je důležité. Zvýšení vaší jednotky ru je jednoduché, můžete to provést pomocí několika kliknutí na portálu Azure portal nebo pomocí [vydávání příkazů pomocí rozhraní API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Vertikální navýšení kapacity a definovat klíč oddílu](./media/social-media-apps/social-media-apps-scaling.png)

Co se stane, když věci stále zlepšovaly a uživatele z jiné oblasti, země nebo kontinent, Všimněte si, že vaše platforma a začít používat, jaké velké překvapení!

Ale počkejte... brzy realizaci svých zkušeností s vaší platformě není ideální; jsou zatím od provozní oblasti, že latence je ještěrů a samozřejmě nechcete, aby jejich ukončení. Pokud pouze došlo snadný způsob **rozšíření globálního dosahu**... ale existuje!

Cosmos DB umožňuje [replikujte data globálně](../cosmos-db/tutorial-global-distribution-sql-api.md) a transparentně pomocí několika kliknutí a automaticky vybrat z dostupných oblastí z vaší [klientský kód](../cosmos-db/tutorial-global-distribution-sql-api.md). To také znamená, že můžete mít [více oblastí převzetí služeb při selhání](regional-failover.md). 

Replikujte data globálně, budete muset Ujistěte se, že vaši klienti mohou využívat jejich výhod. Pokud používáte webový front-end nebo přístup k rozhraní API z mobilních klientů, můžete nasadit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) a klonování služby Azure App Service na všechny požadované oblasti pomocí konfigurace výkon pro podporu vašich rozšířené globální pokrytí. Pokud vaši klienti získat přístup k rozhraní API nebo front-endu, budou směrovány do nejbližší App Service, který se pak připojí k místní replika služby Cosmos DB.

![Přidání globální pokrytí sociální platforma](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Závěr
V tomto článku, pokusí se zbavit některé světlo do alternativy zcela vytváření sociálních sítí v Azure se službami s nízkými náklady a poskytování skvělé výsledky tím, že podporují použití více vrstvách úložiště řešení a data distribuci nazývá "Adaptivní žebříček přenosových rychlostí".

![Diagram interakcí mezi službami Azure pro sociální sítě](./media/social-media-apps/social-media-apps-azure-solution.png)

Pravdy je bez stříbrné odrážek pro tento druh scénářů, je synergii vytvořené kombinací skvělé služby, které umožňuje vytvářet skvělé prostředí: rychlost a volnosti služby Azure Cosmos DB zajistit skvělé aplikace sociálních sítí, inteligentní funkce za prvotřídní hledání řešení, jako je Azure Search, pružnosti Azure App Services k hostování aplikací není ještě jazykově nezávislé, ale procesy na pozadí výkonné a rozšiřitelné služby Azure Storage a Azure SQL Database pro ukládání velké objemy dat a Analytický výkon služby Azure Machine Learning k vytváření znalostní báze a inteligentních funkcí, které můžete poskytnout zpětnou vazbu k vašim procesům a Pomozte nám posílat správný obsah správným uživatelům.

## <a name="next-steps"></a>Další postup
Další informace o případech použití pro službu Cosmos DB najdete v tématu [Cosmos DB běžné případy použití](use-cases.md).
