---
title: 'Vzor návrhu Azure Cosmos DB: sociálních médií aplikace | Microsoft Docs'
description: Další informace o vzoru návrhu pro sociální sítě s využitím flexibilitu úložiště Azure Cosmos DB a jinými službami Azure.
keywords: Aplikace v sociálních sítích
services: cosmos-db
author: ealsur
manager: kfile
documentationcenter: ''
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: maquaran
ms.openlocfilehash: 53abefd4f3dd1f8da60b8b8efed1e7070b471383
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="going-social-with-azure-cosmos-db"></a>Budete sociálních s Azure Cosmos DB
Žijí v společnosti massively propojeny znamená, že v určitém okamžiku v životnosti stane součástí **sociálních sítí**. Pro komunikaci s kolegy přátel, rodiny nebo někdy nasdílejte naše nadšení pro osoby s společné zájmy používáme sociálních sítí.

Jako technici nebo vývojáře, jsme může mít zajímalo jak tyto sítě úložiště a propojení naše data, nebo může mít byla musí i k vytvoření nebo architektury nové sociální sítě pro konkrétní volné místo na trhu, sami sobě. Kdy vzniká big Otázka: jak jsou tato data uložena?

Předpokládejme, že vytváříme nové a lesklý sociálních sítí, kde můžete naši uživatelé odesílat články s související média jako obrázky, videa nebo i Hudba. Uživatelé komentář k příspěvkům a poskytují body pro hodnocení. Bude příspěvky, které uživatelé uvidí informační kanál a moci komunikovat s na cílovou stránku hlavní webové stránky. Toto není zvukových skutečně komplexní (na první), ale z důvodu zjednodušení umožňuje zastavit existuje (jsme může pustíte do vlastní uživatelské kanály vliv na relace, ale jeho velikost překračuje limit cílem v tomto článku).

Ano jak to nám uložit a kde?

Řada z vás mohl být prostředí v databázích SQL nebo mít aspoň představu o [relační modelování dat](https://en.wikipedia.org/wiki/Relational_model) a může být rozhodnout spustit kreslení přibližně takto:

![Diagram ilustrující relativní relační modelu](./media/social-media-apps/social-media-apps-sql.png) 

Dokonale normalizovaný a velmi datová struktura... které nemá škálování. 

Nezískávat mi nesprávný I jste pracovali s databází SQL všechny život, jsou skvělé, ale jako každé platformě vzor, postupů a softwaru není ideální pro každý scénář.

Proč není SQL nejlepší volbou v tomto scénáři? Podívejme se na strukturu jeden POST, pokud chci zobrazit tento příspěvek ve webu nebo aplikace, že bude nutné provést dotaz s... Chcete-li zobrazit jednu jeden post, teď obrázek proud příspěvky dynamicky načíst a zobrazí na obrazovce a může dojít, kde bude jenom 8 spoje tabulky platná (!).

Může samozřejmě používáme ABC instance SQL s dostatek power vyřešit tisíce dotazy pomocí těchto mnoho spojení poskytovat náš obsah, ale skutečně, proč jsme by, pokud existuje jednodušší řešení?

## <a name="the-nosql-road"></a>Silniční NoSQL
Tento článek vám pomohou do modelování sociální platforma dat pomocí Azure, je databáze NoSQL [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) v nákladově efektivní způsob, jak při využívání dalších Azure DB Cosmos funkce, jako [Gremlin Graph API](../cosmos-db/graph-introduction.md). Použití [NoSQL](https://en.wikipedia.org/wiki/NoSQL) přístup, ukládání dat ve formátu JSON a použití [denormalization](https://en.wikipedia.org/wiki/Denormalization), lze je transformovat naše dříve složitá post do jednoho [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):


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

A jej můžete získat pomocí jednoho dotazu a s žádné spojení. Toto je mnohem víc jednoduchá a přímočará a budget-wise, vyžaduje méně prostředků zajistit lepší výsledek.

Azure Cosmos DB zajišťuje, že všechny vlastnosti jsou indexované jeho automatické indexování, může být i [přizpůsobené](indexing-policies.md). Přístup bez schémat umožňují nám ukládat dokumenty s jiné a dynamické struktury, možná zítra chceme příspěvcích tak, aby měl seznam kategorií nebo hashtags s nimi spojených, Cosmos DB, bude zpracovávat nové dokumenty s přidaných atributů s žádné další práci vyžaduje nás.

Komentáře v příspěvku na lze považovat za právě dalších bodů se nadřazený klíč (zjednodušuje naše mapování objektu). 

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

A všechny sociálních interakce může být uložený na samostatný objekt jako čítače:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Vytváření informační kanály stačí vytváření dokumenty, které mohou být uloženy seznam ID post s danou relevance pořadí:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Máme může "posledního" datového proudu s hraniční seřazené podle data vytvoření, "nejprodávanějších" datový proud s těchto příspěvcích s více líbí za posledních 24 hodin, může i implementaci vlastního datového proudu pro každého uživatele na základě logiky jako délky a zájmů a stále je seznam p osts. Bude stačit o tom, jak vytvářet tyto seznamy, ale pořád nerušený výkonu čtení. Jakmile jsme získat jeden z těchto seznamů, jsme vydávat jediný dotaz pomocí Cosmos DB [v operátoru](sql-api-sql-query.md#WhereClause) získat stránky příspěvcích najednou.

Datové proudy informačního kanálu může být postavená pomocí [Azure App Services](https://azure.microsoft.com/services/app-service/) procesy na pozadí: [Webjobs](../app-service/web-sites-create-web-jobs.md). Po vytvoření příspěvku na zpracování na pozadí můžete spustit pomocí [Azure Storage](https://azure.microsoft.com/services/storage/) [fronty](../storage/queues/storage-dotnet-how-to-use-queues.md) a spustí pomocí webové úlohy [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementující POST šíření uvnitř streamů v závislosti na vlastní vlastní logiky. 

Body a líbí na příspěvek lze zpracovat odložené způsobem pomocí této stejné techniky k vytvoření prostředí, nakonec byl konzistentní.

Délky jsou trickier. Cosmos databáze může mít dokument maximální velikost a čtení/zápis velkých dokumentů může mít vliv na škálovatelnost vaší aplikace. Proto si promyslet ukládání délky jako dokument s tuto strukturu:

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

To může fungovat pro uživatele s několika tisíc délky, ale pokud některé celebrit spojí naše pořadí, bude tento přístup vést k velikost velké dokumentu a může nakonec dosáhl limitu velikosti dokumentu.

Chcete-li tento problém vyřešit, můžeme použít smíšený přístup. Jako součást dokumentu statistické údaje o uživatelích ukládáme počet délky:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

A skutečný graf délky mohou být uloženy pomocí Azure Cosmos DB [Gremlin Graph API](../cosmos-db/graph-introduction.md), chcete-li vytvořit [bodů uchycení](http://mathworld.wolfram.com/GraphVertex.html) pro každého uživatele a [okraje](http://mathworld.wolfram.com/GraphEdge.html) , Udržovat "A-způsobem B" vztahy. Rozhraní Graph API umožňuje můžete nejen získat délky určitého uživatele, ale vytvořit složitější dotazy i společné navrhovat osoby. Pokud přidat do grafu, pro který lidé kategorie obsah jako nebo získejte, můžeme začít tkaní prostředí, které zahrnují zjišťování inteligentní obsahu, k obsahu, která se, budeme postupovat podle jako, nebo při hledání osob, se kterými může máme mnohem společné.

Statistické údaje o uživatelích dokumentu pořád můžou použít k vytvoření karty v uživatelského rozhraní nebo rychlé profil funkce Preview.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplikace "Žebříku" vzor a data
Jak je možná jste si všimli v dokumentu JSON, který odkazuje na metodu POST směřující, se vyskytuje víckrát uživatele. A jste by mít uhádnout správné, že to znamená, že informace, který reprezentuje uživatele, danou tento denormalization, může být k dispozici více než jednom místě.

Aby bylo možné rychlejší dotazy, budeme způsobit odstranění duplicitních dat. Problém s vedlejším účinkem je, pokud některé akce, změny dat uživatele, je potřeba najít všechny aktivity že někdy nebyla a všechny jejich aktualizaci. Není zvuk velmi praktické, pravé?

Přidáme vyřešit tím, že určíte klíč atributy uživatele, který ukážeme v naší aplikaci pro každou aktivitu. Pokud jsme vizuálně zobrazit metodu POST směřující v naší aplikaci a zobrazit pouze nástroj creator název a obrázek, proč ukládat všechna data uživatele do atribut "vytvořená"? Pokud pro každý komentář jsme právě uživatele obrázek zobrazit, jsme nepotřebujeme zbývající jeho informace. To je, kde něco že můžu volat "žebříku vzor" dodává do play.

Podívejme se na informace o uživateli, jako například:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Pohledem na tyto informace jsme můžete rychle zjistit, což je důležité informace a které není, čímž vytvoříte "Žebříku":

![Diagram žebříku vzor](./media/social-media-apps/social-media-apps-ladder.png)

Nejmenší krok se nazývá UserChunk, minimální část informace, které identifikuje uživatele a používá se pro odstranění duplicitních dat. Snížením velikosti duplicitní data na pouze informace "ukážeme" jsme omezení případných masivní aktualizace.

Střední krok se nazývá uživatele, je úplná data, která se použije na většinu závislé na výkonu dotazů na Cosmos databáze, přístupu a kritické. Obsahuje informace reprezentována UserChunk.

Největší je uživatele rozšířeno. Obsahuje všechny informace o důležitých uživatele a další data, která nevyžaduje skutečně rychlé čtení nebo jeho využití je případný (např. proces přihlášení). Tato data mohou být uloženy mimo Cosmos databáze, v Azure SQL Database nebo úložiště tabulek Azure.

Proč by jsme rozdělit uživatele a i tyto informace ukládaly na různých místech? Protože z hlediska výkonu, tím větší dokumenty, tím costlier dotazy. Zachovat dokumenty tenký správné informace, které se všechny dotazy závislé na výkonu pro sociální sítě a uložit doplňující informace pro případné scénáře jako úplné profil úpravy, přihlášení, a to i dolování dat pro analýzy využití a velké objemy dat iniciativy. Jsme skutečně nezajímá je, pokud pro dolování dat shromažďování dat je pomalejší, protože je spuštěna v databázi SQL Azure, budeme mít se týkají ale že naši uživatelé mají rychlé a tenký prostředí. Uživatel, uložený na Cosmos databáze, bude vypadat takto:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

A bude vypadat příspěvku na:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

A pokud upravíte vznikne, kde jeden z atributů bloku má vliv, se dají snadno najít ovlivněných dokumenty pomocí dotazů, které odkazují na indexované atributy (vybrat * FROM odešle p kde p.createdBy.id == "edited_user_id") a potom aktualizaci bloky dat.

## <a name="the-search-box"></a>Do vyhledávacího pole
Uživatelé budou naštěstí vygeneroval velké množství obsahu. A jsme musí být schopen poskytnout možnost vyhledávání a vyhledávat obsah, který nemusí být přímo v jejich obsahu datové proudy, možná vzhledem k tomu, že jsme si podle tvůrci, nebo možná právě Snažíme se najít, staré post, jsme se před 6 měsíců.

Naštěstí a protože používáme Azure Cosmos DB, můžeme snadno implementovat modul vyhledávání pomocí [Azure Search](https://azure.microsoft.com/services/search/) během několika minut a aniž by museli zadávat jeden řádek kódu (jiné než samozřejmě, proces vyhledávání a uživatelského rozhraní).

Proč je to tak snadno?

Služba Azure Search implementuje co volání [indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx), pozadí zpracovává této háku v datových úložišť a automagically přidání, aktualizace nebo odebrání vašich objektů v indexy. Podporují [Azure SQL Database indexery](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [objektů BLOB služby Azure indexery](../search/search-howto-indexing-azure-blob-storage.md) a naštěstí [indexery Azure Cosmos DB](../search/search-howto-index-documentdb.md). Převod informace z databáze Cosmos do služby Azure Search je jednoduché, jako i informace o úložišti ve formátu JSON, potřebujeme [vytvořit Index](../search/search-create-index-portal.md) a mapovat atributy, které z našich dokumentů chceme indexované a je to, v několika minut (závisí na velikosti našich dat), náš obsah bude k dispozici pro vyhledat natolik, nejlepší řešení vyhledávání jako služby v infrastruktuře cloudu. 

Další informace o službě Azure Search, můžete navštívit [Hitchhiker na Průvodce vyhledávání](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Základní znalosti
Po uložení tohoto obsahu, který zvětšování a zvětšování každý den, nám může najít označována myslím: jak s tímto proudem informací z mé uživatelé?

Odpověď je jednoduchý: pro něj fungovat a zjistěte z něj.

Ale co jsme další? Několik snadno příklady [postojích analysis](https://en.wikipedia.org/wiki/Sentiment_analysis)obsahu doporučení na základě předvoleb uživatele nebo i automatizované obsahu moderátora, který zajišťuje, že veškerý obsah naše sociálních sítí, které zveřejnil bezpečné pro rodinu.

Teď, když se zobrazí chybové jste připojili, budete pravděpodobně domníváte, že budete potřebovat některé aplikace PhotoDraw ve vědě matematické extrahovat tyto vzory a informace o jednoduché databáze a soubory, ale by být nesprávné.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), jsou součástí [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), je plně spravovaná Cloudová služba, která umožňuje vytvářet pracovní postupy pomocí algoritmů v jednoduché rozhraní přetahování myší, kód vlastní algoritmy v [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) nebo používat některé z už sestavené a připravené k použití rozhraní API, jako například: [Analýza textu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [obsahu moderátora](https://www.microsoft.com/moderator) nebo [doporučení](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

K dosažení těchto scénářů Machine Learning, můžeme použít [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) ingestování informace z různých zdrojů a používat [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) zpracovat informace a generovat výstup, který můžete pomocí Azure Machine Learning zpracovat.

K dispozici další možností je použít [kognitivní služby Microsoft](https://www.microsoft.com/cognitive-services) k analýze obsahu; nejen můžete chápeme je lépe naši uživatelé (prostřednictvím analýza zapisují s [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale nemůžeme může také rozpoznání nežádoucí nebo vyspělá obsahu a odpovídajícím způsobem fungují s [počítače vize API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Kognitivní služby zahrnují spoustu out-of-the-box řešení, které nevyžadují jakýkoli druh Machine Learning znalostmi k použití.

## <a name="a-planet-scale-social-experience"></a>V sociálních rozhraní planetu škálování
Je poslední, ale zase důležité tématu I, musí řešit: **škálovatelnost**. Při navrhování architekturu, kterou je třeba, že jednotlivé komponenty můžete škálovat sama o sobě, buď protože je potřeba zpracovat více dat, nebo protože chceme mít větší zeměpisném pokrytí (nebo oba!). Naštěstí je dosažení složité úlohy **připraveného prostředí** s Cosmos DB.

Podporuje cosmos DB [dynamické rozdělení](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of-the-box automaticky vytvořením oddíly na základě danou **klíč oddílu** (definován jako jeden z atributů v dokumentech). Definování klíč správné oddílu je třeba provést v době návrhu a dodržíte [osvědčené postupy](../cosmos-db/partition-data.md#designing-for-partitioning) není k dispozici; v případě sociálních prostředí, musí být zarovnána strategie dělení se způsobem dotazu (čtení v téže oddíl je žádoucí) a zápis (vyhnout "aktivní body" tak, že se zápisy na více oddílů). Některé možnosti jsou: oddíly založené na dočasný klíč (den/měsíc/týden), podle obsahu kategorie podle zeměpisné oblasti uživatelem; všechny ve skutečnosti závisí na tom, jak budete zadávat dotazy na data a zobrazit v sociálních prostředí. 

Jeden je zajímavé bod důležité zmínit, Cosmos DB bude spuštěn své dotazy (včetně [agregace](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) pro všechny oddíly transparentně, nemusíte přidejte veškeré logiky s růstem vaše data.

S časem, můžete se nakonec zvýší v provozu a vaší spotřeby prostředků (měřeno v [RUs](request-units.md), nebo jednotky žádosti) se zvýší. Bude číst a zapisovat častěji, jako vaše userbase zvětšování a začnou vytváření a čtení víc obsahu; schopnost **škálování vašeho propustnost** je životně důležité. Zvýšení naše RUs je velmi snadné, jsme můžete provést pomocí několika kliknutí na portálu Azure nebo pomocí [vydávání příkazů prostřednictvím rozhraní API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Škálování a definování klíč oddílu](./media/social-media-apps/social-media-apps-scaling.png)

Co se stane, když věcí zachovat zlepšuje a uživatelé z jiné oblasti, země nebo kontinentě, Všimněte si vaši platformu a začít používat ji, skvělé neočekávaném!

Počkejte..., ale brzy zjistíte, své zkušenosti s vaši platformu není optimální; jsou dosavadní od vaší provozní oblasti latence je strašlivých, a samozřejmě nechcete, aby uživatelům ukončit. Pokud jenom došlo snadný způsob **rozšíření globální sítě**... ale!

Cosmos DB umožňuje [replikovat data globálně](../cosmos-db/tutorial-global-distribution-sql-api.md) a transparentně pomocí několika kliknutí a automaticky vybrat mezi dostupné oblasti z vaší [kód klienta](../cosmos-db/tutorial-global-distribution-sql-api.md). To také znamená, že můžete mít [více oblastí převzetí služeb při selhání](regional-failover.md). 

Při replikaci dat globálně, musíte zajistit, že vaši klienti mohou využít výhod ho. Pokud používáte front-endu webové nebo accesing rozhraní API z mobilních klientů, můžete nasadit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) a klonování Azure App Service na všechny požadované oblasti pomocí konfigurace výkonu pro podporu vašeho rozšířené globální pokrytí. Pokud vaši klienti přístup k rozhraní API nebo front-endu, budou směrovány na nejbližší App Service, který se pak připojí k místní repliky databáze Cosmos.

![Přidání globální pokrytí k sociálních platformě](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Závěr
Tento článek se pokusí přenesen některé light do alternativy úplně vytváření sociálních sítí v Azure s nízkými náklady službami a poskytuje skvělé výsledky tím, že podporují použití víceúrovňová úložiště řešení a data distribuční názvem "Žebříku".

![Diagram interakce mezi Azure services pro sociálních sítí](./media/social-media-apps/social-media-apps-azure-solution.png)

Správné je žádné stříbrným odrážka pro tento druh scénářů, je součinnosti vytvořené kombinace kvalitních služeb, které nám s cílem vytvořit skvělé umožňují: rychlostí a volnost Azure Cosmos DB zajistit skvělé sociálních aplikace, Intelligence za prvotřídní hledání řešení jako Azure Search, flexibilní aplikační služby Azure k hostování aplikací není i jazykově nezávislého ale procesy na pozadí výkonný a rozšíření Azure Storage a Azure SQL Database pro ukládání masivní objemy dat a analýzy výkonu Azure Machine Learning vytvořit znalostní báze a intelligence, která může poskytnout zpětnou vazbu pro naše procesy a Pomozte nám správné uživatelům poskytovat s příslušným obsahem.

## <a name="next-steps"></a>Další postup
Další informace o případy použití pro Cosmos DB, najdete v části [případy použití běžné DB Cosmos](use-cases.md).