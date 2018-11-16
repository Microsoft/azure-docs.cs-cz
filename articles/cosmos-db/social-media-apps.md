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
ms.date: 11/14/2018
ms.author: maquaran
ms.openlocfilehash: a65ec0a92b8fc245c77ce67c80c1202f73a3ec66
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711867"
---
# <a name="going-social-with-azure-cosmos-db"></a>Sociální funkce s Azure Cosmos DB

Žijete ve vysoce propojených společnosti znamená, že v určitém okamžiku v životě stanou součástí **sociálních sítí**. Sociální sítě používáte zachovat ve spojení s přáteli, kolegům, řada, nebo někdy také sdílet s lidmi s společné zájmy vaše nadšení.

Jako technici a vývojáři pravděpodobně vás zajímá, jak tyto sítě úložiště a vzájemné propojení, vaše data. Nebo vás může mít i byl úkol vytvářet nebo upravovat architekturu, když nové sociálních sítí pro konkrétní volné místo na trhu. Kdy vzniká významné Otázka: jak jsou tato data uložena?

Předpokládejme, že vytváříte nové a shiny sociální síti kde vaši uživatelé můžete publikovat články s související média, jako jsou obrázky, videa nebo dokonce music. Uživatelé mohou přidávat komentáře na příspěvky a poskytují body pro hodnocení. Bude kanál, které příspěvky, které budou uživatelé vidí a pracovat na cílové stránce hlavní webové stránky. Tato metoda není zvukové komplexní na první, ale z důvodu zjednodušení, Pojďme zastavit existuje. (Můžete delve do vlastní uživatelské kanály ovlivněny relace, ale přesahuje cílem tohoto článku.)

Ano jak můžete ukládat data a kde?

Možná máte prostředí u databází SQL nebo hodnoty z [relační modelování dat](https://en.wikipedia.org/wiki/Relational_model). Můžete začít kreslení přibližně takto:

![Diagram ilustrující relativní relační model](./media/social-media-apps/social-media-apps-sql.png)

Nemusíte zajistit dokonalou normalizovaná a přehlednou datová struktura..., který nemá škálování.

Nezískávat mě chybný, že jsem pracovala s databázemi SQL všechny život. Hodí se ale stejně jako každý vzorek, postupů a softwaru platformy, není ideální pro každý scénář.

Proč SQL není v tomto scénáři o nejlepší volbu? Podívejme se na struktuře jeden příspěvek. Kdybych chtěl zobrazení příspěvku na webu nebo aplikaci, by musím dotaz s... spojením osm tables(!) pouze k zobrazení jedné jeden příspěvek. Nyní obrázek streamovaných příspěvky, které dynamicky načíst a na obrazovce a může se zobrazit, kde teď předvedu.

Můžete použít instanci SQL obrovské s dostatek power vyřešit tisíce dotazů s mnoha spojení k poskytování obsahu. Ale proč byste, pokud mezi doménami existuje jednodušší řešení?

## <a name="the-nosql-road"></a>Silniční NoSQL

Tento článek vás provede do modelování dat sociální platforma databáze NoSQL Azure [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) za výhodnou cenu. Také zjistíte, jak používat další funkce služby Azure Cosmos DB, jako [rozhraní Gremlin API](../cosmos-db/graph-introduction.md). Použití [NoSQL](https://en.wikipedia.org/wiki/NoSQL) přístup, ukládání dat ve formátu JSON a použití [denormalizace](https://en.wikipedia.org/wiki/Denormalization), dříve složité příspěvek je možné transformovat do jednoho [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):

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

A může být získali s pomocí jediného dotazu a žádná spojení. Tento dotaz je velmi jednoduché a nekomplikované a budget-wise, vyžaduje méně prostředků k dosažení lepších výsledků.

Azure Cosmos DB zajišťuje, že všechny vlastnosti jsou indexovány pomocí automatického indexování. Automatické indexování můžou být [přizpůsobené](index-policy.md). Přístup bez schémat umožňují ukládání dokumentů s jinou a dynamické struktury. Zítra možná budete potřebovat příspěvků na seznam kategorií nebo k nim má přiřazené hashtagy? Cosmos DB bude zpracovávat nové dokumenty s přidané atributy bez další práce potřebné od nás.

Další příspěvky k nadřazené vlastnosti lze považovat za komentářů na příspěvek. (Tento postup usnadňuje mapování pracovního objektu.)

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

Vytváření kanálů stačí vytváření dokumentů, které může obsahovat seznam příspěvek ID se daný relevance pořadí:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Můžete mít "posledního" datový proud s příspěvky seřazené podle data vytvoření. Nebo můžete mít "Nejaktivnější" datový proud takových příspěvků s další lajků za posledních 24 hodin. Dokonce je možné implementovat vlastní datový proud pro každého uživatele na základě logiky jako sledujících a zájmy. Stále je seznam příspěvků. Je na vás o tom, jak vytvářet tyto seznamy, ale zůstává nerušený výkonu čtení. Jakmile získáte jednu z těchto seznamů, vydáte pomocí jediného dotazu pomocí služby Cosmos DB [v operátoru](sql-api-sql-query.md#WhereClause) k získání stránek příspěvků v čase.

Informační kanály datové proudy může být postavená pomocí [Azure App Services](https://azure.microsoft.com/services/app-service/) procesy na pozadí: [Webjobs](../app-service/web-sites-create-web-jobs.md). Po vytvoření příspěvek zpracování na pozadí můžete aktivovat pomocí [služby Azure Storage](https://azure.microsoft.com/services/storage/) [fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) a aktivovat pomocí Webjobs [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementující Publikovat šíření uvnitř streamů v závislosti na vlastní logiku.

Body a lajky přes příspěvek mohou být zpracovány odložené způsobem pomocí Tento stejný postup k vytvoření konzistentní prostředí.

Sledujících jsou trickier. Cosmos DB má omezení velikosti dokumentu a čtení/zápis velkých dokumentů může mít vliv na škálovatelnost aplikace. Takže si promyslet ukládání sledujících jako dokument s touto strukturou:

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

Tato struktura může fungovat pro uživatele s několika tisíc sledujícími. Pokud některé celebrit připojí pořadí, ale tento přístup může vést k velikost velkých dokumentu a se nakonec pravděpodobně dojde limit velikosti dokumentu.

Chcete-li tento problém vyřešit, je použít smíšené přístup. Jako část dokumentu statistiky uživatelů můžete ukládat počet sledujících:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Skutečné graf sledující využívající službu Azure Cosmos DB můžete ukládat [rozhraní Gremlin API](../cosmos-db/graph-introduction.md) vytvořit [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) pro každého uživatele a [hrany](http://mathworld.wolfram.com/GraphEdge.html) , Udržovat "A-způsobem – B" relace. Pomocí rozhraní Gremlin API můžete načíst sledující určitého uživatele a vytvářet složitější dotazy pro návrh lidí v běžných. Pokud chcete přidat do grafu, jako jsou kategorie obsahu, který lidé nebo využijte, můžete spustit tkaní prostředí, které zahrnují inteligentní zjišťování obsahu, návrhy obsahu, že tito uživatelé postupujte stejně jako nebo zjištění osoby, které může mít mnoho společně s.

Dokument statistické údaje o uživatelích stále slouží k vytvoření karty v uživatelského rozhraní nebo náhledy rychlé profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Adaptivní žebříček přenosových rychlostí" model a data duplicity

Jak možná jste si všimli v dokumentu JSON, který odkazuje na příspěvek, existují mnoho výskytů uživatele. A jste by mít uhodnout správné, že tyto duplikáty znamená, že informace popisující uživatel zadaný denormalizace, může být k dispozici více než jednom místě.

Povolit pro rychlejší dotazy, se vám účtovat duplicitních dat. Problém s tomuto vedlejšímu efektu je, že pokud některé akce, změny dat uživatele, je potřeba najít všechny aktivity nikdy nebyla a uživatele je všechny aktualizovat. Není zvukové praktické, hned?

Budete k jeho řešení Identifikujte klíčové atributy uživatele, který se zobrazí v aplikaci pro každou aktivitu. Pokud vizuálně zobrazit příspěvek ve vaší aplikaci a zobrazit pouze tvůrce příslušného názvu a obrázek, proč ukládání všech dat uživatele v atributu "createdBy"? Pokud pro každý komentáře že stačí zobrazit obrázek uživatele, nepotřebujete zbývající informace uživatele. Je to, kde se stane něco že můžu volat "adaptivní žebříček přenosových rychlostí vzor" zahrnuty.

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

Nejmenší kroku se říká UserChunk, minimální část informace identifikující uživatele a používá se k replikaci dat. Zmenšením velikosti duplicitní data na pouze informace "zobrazíte" snížení rizika vzniku obrovských aktualizace.

Střední krok se nazývá uživatelem. Je úplná data, která se použije na většinu závislé na výkonu dotazů ve službě Cosmos DB, přístupu a kritické. Obsahuje informace UserChunk představované.

Největší je rozšířená uživatele. Obsahuje informace o důležitých uživatele a další data, která nemusí být rychle přečíst nebo má konečnou využití, jako je proces přihlašování. Tato data mohou být uloženy mimo Cosmos DB v Azure SQL Database nebo úložiště tabulek Azure.

Proč by i tyto informace ukládaly na různých místech a rozdělit uživatele? Protože z hlediska výkonu, tím větší dokumenty, costlier dotazy. Zachovejte dokumenty tenký správné informace, které se všechny vaše dotazy závislé na výkonu pro sociální sítě. Store Další informace pro konečné scénáře, jako jsou úpravy celých profilů, přihlášení a dolování dat pro analýzy využití a velké objemy dat iniciativy. Ve skutečnosti není stojíte pokud shromažďování dat pro dolování dat je pomalejší, protože běží na Azure SQL Database. Můžete mít problém ale aby uživatelé měli rychlý a tenký prostředí. Uživatelské uložené ve službě Cosmos DB může vypadat třeba takto:

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

Pokud úpravy vznikne, kde je ovlivněna atribut bloků dat, můžete snadno vyhledat ovlivněné dokumenty. Stačí použít dotazy, které odkazují na indexované atributy, jako například `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`a pak aktualizujte bloky dat.

## <a name="the-search-box"></a>Do vyhledávacího pole

Uživatelé budou generovat naštěstí příliš mnoho obsahu. By měl být schopný poskytnout možnost vyhledávání a najdete obsah, který nemusí být přímo v jejich obsahu datové proudy, pravděpodobně protože neodpovídají autoři a možná se právě snažíte najít, staré příspěvek jste před šesti měsíci.

Vzhledem k tomu, že používáte služby Azure Cosmos DB, je možné snadno implementovat pomocí modulu vyhledávání [Azure Search](https://azure.microsoft.com/services/search/) za pár minut, aniž by museli zadávat žádný kód, než je proces hledání a uživatelského rozhraní.

Tento proces je tak snadné

Služba Azure Search implementuje co volají [indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx), procesy na pozadí tohoto volání v úložištích dat a automagically přidání, aktualizace nebo odebrání objektů v indexy. Podporují taky [indexování Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexování objektů BLOB Azure](../search/search-howto-indexing-azure-blob-storage.md) a naštěstí [indexerů Azure Cosmos DB](../search/search-howto-index-documentdb.md). Přechod informace ze služby Cosmos DB do služby Azure Search je jednoduché. Obě technologie ukládat informace ve formátu JSON, takže stačí [vytvořit Index](../search/search-create-index-portal.md) a mapování atributů z vašich dokumentů chcete indexovat. A to je vše! V závislosti na velikosti dat bude váš obsah k dispozici pro hledání na během několika minut nejlepší řešení vyhledávání jako služby v infrastruktuře cloudu.

Další informace o službě Azure Search, můžete navštívit [Hitchhiker od průvodce hledání](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Základní znalosti

Po uložení všech tento obsah, který zvyšuje nebo každým dnem roste, může pro vás přemýšlení: Co mohu využít tento proud informací z mých uživatelů?

Odpověď je jednoduchý: je začít pracovat a Učte se z něj.

Ale co je další? Zahrnují několik příkladů snadno [analýzu subjektivního hodnocení](https://en.wikipedia.org/wiki/Sentiment_analysis), obsah doporučení na základě zálib uživatelů, nebo dokonce automatizované content moderatoru, který zajišťuje, že obsah publikovaný sociálních sítí je bezpečný pro rodinu.

Teď, když mám tady připojeny, vám dovolí přemýšlet pravděpodobně potřebujete některé titul pH.d. v matematické vědy extrahovat tyto vzory a informace z jednoduchého databáze a soubory, ale by byl nesprávný.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)patří [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), je plně spravovaná Cloudová služba, která umožňuje vytvářet pracovní postupy pomocí algoritmů v jednoduché rozhraní pro přetahování myší, kód vlastní algoritmy v [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)), nebo použít některé z již integrované a připravené k použití rozhraní API, jako: [rozhraní Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator, nebo [doporučení](https://gallery.azure.ai/Solution/Recommendations-Solution).

Chcete-li dosáhnout některého z těchto scénářů Machine Learning, můžete použít [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) k ingestování informace z různých zdrojů. Můžete také použít [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) pro zpracování informací a generovat výstup, který může být zpracována Azure Machine Learning.

K dispozici další možností je použít [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) k analýze obsahu; nejen můžete je jim lépe porozumět uživatelům (prostřednictvím analýza zapisovaly s [rozhraní Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale může také zjistit nežádoucí nebo až po zralé obsah a příslušně na ně s [rozhraní API pro počítačové zpracování obrazu](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Služby cognitive Services zahrnuje mnoho out-of-the-box řešení, které nevyžadují jakýkoli druh znalostní báze Machine Learning k použití.

## <a name="a-planet-scale-social-experience"></a>Sociální prostředí globálním měřítku

Je last, ale ne nejméně důležité článku můžu musí řešit: **škálovatelnost**. Při návrhu architekturu jednotlivé komponenty by se měly škálovat sama o sobě. Nakonec bude potřeba zpracovávat další data, nebo chcete mít větší zeměpisném pokrytí. Naštěstí je dosažení obě úlohy **předpřipravené prostředí** pomocí služby Cosmos DB.

Cosmos DB podporuje [dynamické dělení na oddíly](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of-the-box. Automaticky vytvoří oddíly na základě danou **klíč oddílu**, který je definován jako atribut v dokumentech. Definování správného klíče oddílu je třeba provést v době návrhu. Další informace najdete v tématu [zvolit klíč oddílu správné](partitioning-overview.md#choose-partitionkey) článku.

Sociálních prostředí které musí být v souladu strategii dělení do toho, jak dotazovat a zápis. (Například čtení v rámci stejného oddílu jsou žádoucí a vyhnout se "hotspotů" tím, že rozprostírá zápisy na několik oddílů.) Některé možnosti jsou: oddíly na základě dočasné klíče (den/měsíc/týden), kategorie obsahu, geografické oblasti nebo uživatele. Všechno ve skutečnosti závisí na jak budete zadávat dotazy na data a zobrazit data ve vašem prostředí sociálních sítí.

Cosmos DB se spustí vaše dotazy (včetně [agregace](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) na všechny oddíly transparentně, takže není nutné přidat jakékoli logiky růst objemu dat.

Čas, bude nakonec nárůstu provozu a využití prostředků (měřeno v [ru](request-units.md), nebo jednotek žádostí) se zvýší. Můžete se čtení a zápis víc často s růstem uživatelské základny. Vytváření a čtení další obsah se spustí uživatelskou základnu. Takže schopnost **škálování propustnosti** je důležité. Zvýšení vaší jednotky ru je snadné. Můžete to provést pomocí několika kliknutí na portálu Azure portal nebo pomocí [vydávání příkazů pomocí rozhraní API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Vertikální navýšení kapacity a definovat klíč oddílu](./media/social-media-apps/social-media-apps-scaling.png)

Co se stane, když stále zlepšovaly věci? Předpokládejme, že uživatelé z jiné oblasti, země nebo kontinentem Všimněte si, že vaše platforma a začít ji používat. Jaké skvělé překvapení!

Ale Počkejte! Brzy zjistíte, že se vaše platforma nejsou optimálním řešením. Jsou to zatím od provozní oblasti, že je ještěrů latence. Samozřejmě nechcete, aby jejich ukončení. Pokud pouze došlo snadný způsob **rozšíření globálního dosahu**? Existuje!

Cosmos DB umožňuje [replikujte data globálně](../cosmos-db/tutorial-global-distribution-sql-api.md) a transparentně pomocí několika kliknutí a automaticky vybrat z dostupných oblastí z vaší [klientský kód](../cosmos-db/tutorial-global-distribution-sql-api.md). Tento proces také znamená, že můžete mít [více oblastí převzetí služeb při selhání](high-availability.md).

Replikujte data globálně, budete muset Ujistěte se, že vaši klienti mohou využívat jejich výhod. Pokud používáte webový front-end nebo přístup k rozhraní API z mobilních klientů, můžete nasadit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) a klonování služby Azure App Service na všechny požadované oblasti pomocí konfigurace výkon pro podporu vašich rozšířené globální pokrytí. Pokud vaši klienti získat přístup k rozhraní API nebo front-endu, bude směrovat na nejbližší App Service, který se pak připojí k místní replika služby Cosmos DB.

![Přidání globální pokrytí sociální platforma](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Závěr

Tento článek některé přibližuje do alternativy zcela vytváření sociálních sítí v Azure se službami s nízkými náklady. poskytuje výsledky tím, že podporují použití více vrstvách úložiště řešení a data distribuci nazývá "Adaptivní žebříček přenosových rychlostí".

![Diagram interakcí mezi službami Azure pro sociální sítě](./media/social-media-apps/social-media-apps-azure-solution.png)

Pravdy je, že neexistuje žádná stříbrné odrážky pro tento druh scénářů. Je synergii vytvořené kombinací skvělé služby, které umožňuje vytvářet skvělé prostředí: rychlost a volnosti služby Azure Cosmos DB zajistit skvělé aplikace sociálních sítí, intelligence za prvotřídní hledání řešení, jako je Azure Search pružnost služby Azure App Services k hostování aplikací není ještě jazykově nezávislé, ale procesy na pozadí výkonné a rozšiřitelné služby Azure Storage a Azure SQL Database pro ukládání velkých objemů dat a Analytický výkon služby Azure Machine Learning k Vytvoření znalostní báze a inteligentních funkcí, které můžete poskytnout zpětnou vazbu k vašim procesům a Pomozte nám posílat správný obsah správným uživatelům.

## <a name="next-steps"></a>Další postup

Další informace o případech použití pro službu Cosmos DB najdete v tématu [Cosmos DB běžné případy použití](use-cases.md).
