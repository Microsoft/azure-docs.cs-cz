---
title: 'Vzor návrhu Azure Cosmos DB: Aplikace sociálních médií'
description: Seznamte se s návrhovým vzorem pro sociální sítě využitím flexibility úložiště Azure Cosmos DB a dalších služeb Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827303"
---
# <a name="going-social-with-azure-cosmos-db"></a>Sociální sítě s Azure Cosmos DB

Život v masivně propojené společnosti znamená, že se v určitém okamžiku života stanete součástí **sociální sítě**. Pomocí sociálních sítí můžete zůstat v kontaktu s přáteli, kolegy, rodinou nebo někdy sdílet svou vášeň s lidmi se společnými zájmy.

Jako inženýři nebo vývojáři jste se mohli divit, jak tyto sítě ukládají a propojují vaše data. Nebo jste dokonce byli pověřeni vytvořit nebo architekt nové sociální sítě pro konkrétní mezeru na trhu. To je, když vyvstává významná otázka: Jak jsou všechna tato data uložena?

Předpokládejme, že vytváříte novou a lesklou sociální síť, kde mohou uživatelé zveřejňovat články se souvisejícími médii, jako jsou obrázky, videa nebo dokonce hudba. Uživatelé mohou komentovat příspěvky a udávat body za hodnocení. Na vstupní stránce hlavního webu bude k dispozici informační kanál příspěvků, které uživatelé uvidí a budou s nimi pracovat. Tato metoda zpočátku nezní složitě, ale kvůli jednoduchosti se zastavme. (Můžete se ponořit do vlastních uživatelských kanálů ovlivněných vztahy, ale přesahuje cíl tohoto článku.)

Takže, jak ukládáte tato data a kde?

Můžete mít zkušenosti s databázemi SQL nebo máte představu o [relačním modelování dat](https://en.wikipedia.org/wiki/Relational_model). Můžete začít kreslit něco takto:

![Diagram znázorňující relativní relační model](./media/social-media-apps/social-media-apps-sql.png)

Dokonale normalizovaná a hezká datová struktura... to se nezvětšuje.

Nechápejte mě špatně, pracoval jsem s SQL databázemi celý svůj život. Jsou skvělé, ale stejně jako každý vzor, praxe a softwarová platforma, to není ideální pro každý scénář.

Proč není SQL nejlepší volbou v tomto scénáři? Podívejme se na strukturu jednoho příspěvku. Kdybych chtěl ukázat příspěvek na webových stránkách nebo v aplikaci, musel bych udělat dotaz s ... spojením osmi tabulek (!) jen proto, aby se zobrazil jeden příspěvek. Nyní obrázek proud příspěvků, které dynamicky načíst a objeví se na obrazovce, a můžete vidět, kam jdu.

Můžete použít obrovskou instanci SQL s dostatkem energie k vyřešení tisíců dotazů s mnoha spojeními, které slouží vašemu obsahu. Ale proč byste, když existuje jednodušší řešení?

## <a name="the-nosql-road"></a>Silnice NoSQL

Tento článek vás provede modelováním dat sociální platformy pomocí azure's NoSQL database [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) nákladově efektivním. Také vám řekne, jak používat jiné funkce Azure Cosmos DB, jako je [rozhraní GREMLIN API](../cosmos-db/graph-introduction.md). Pomocí [nosql](https://en.wikipedia.org/wiki/NoSQL) přístupu, ukládání dat, ve formátu JSON a použití [denormalizace](https://en.wikipedia.org/wiki/Denormalization), dříve složitý příspěvek může být transformován do jednoho [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

A to lze získat s jedním dotazem, a bez spojení. Tento dotaz je velmi jednoduchý a přímočarý a z hlediska rozpočtu vyžaduje méně prostředků k dosažení lepšího výsledku.

Azure Cosmos DB zajišťuje, že všechny vlastnosti jsou indexovány s jeho automatické indexování. Automatické indexování lze dokonce [přizpůsobit](index-policy.md). Přístup bez schématu nám umožňuje ukládat dokumenty s různými a dynamickými strukturami. Možná, že zítra chcete, aby příspěvky měly seznam kategorií nebo hashtagů s nimi spojených? Cosmos DB bude zpracovávat nové dokumenty s přidanými atributy bez další práce, kterou požadujeme.

Komentáře k příspěvku lze považovat za jiné příspěvky s nadřazenou vlastností. (Tento postup zjednodušuje mapování objektů.)

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

A všechny sociální interakce mohou být uloženy na samostatný objekt jako čítače:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Vytváření informačních kanálů je pouze otázkou vytváření dokumentů, které mohou obsahovat seznam ID příspěvků s daným pořadím relevance:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Můžete mít "nejnovější" proud s příspěvky seřazenými podle data vytvoření. Nebo byste mohli mít "nejžhavější" proud s těmito příspěvky s více lajky za posledních 24 hodin. Můžete dokonce implementovat vlastní datový proud pro každého uživatele na základě logiky, jako jsou následovníci a zájmy. Pořád by to byl seznam příspěvků. Je to otázka, jak vytvořit tyto seznamy, ale čtení výkon zůstává bez překážek. Jakmile získáte jeden z těchto seznamů, vydáte jeden dotaz do Cosmos DB pomocí [klíčového slova IN](sql-query-keywords.md#in) k získání stránek příspěvků najednou.

Datové proudy informačních kanálů lze sestavit pomocí procesů na pozadí [služby Azure App Services:](https://azure.microsoft.com/services/app-service/) [Webjobs](../app-service/webjobs-create.md). Po vytvoření příspěvku se zpracování na pozadí dá aktivovat pomocí [front](../storage/queues/storage-dotnet-how-to-use-queues.md) [úložiště Azure](https://azure.microsoft.com/services/storage/) a webových úloh spouštěných pomocí sady [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)a implementujete šíření příspěvku uvnitř datových proudů na základě vlastní logiky.

Body a lajky nad příspěvkem mohou být zpracovány odloženým způsobem pomocí stejné techniky k vytvoření nakonec konzistentního prostředí.

Následovníci jsou složitější. Cosmos DB má limit velikosti dokumentu a čtení/zápis velkých dokumentů může mít vliv na škálovatelnost vaší aplikace. Takže můžete přemýšlet o ukládání následovníků jako dokument s touto strukturou:

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

Tato struktura může fungovat pro uživatele s několika tisíci následovníky. Pokud se však k hodnostem připojí nějaká celebrita, povede tento přístup k velké velikosti dokumentu a nakonec může zasáhnout limit velikosti dokumentu.

Chcete-li tento problém vyřešit, můžete použít smíšený přístup. Jako součást dokumentu Statistika uživatelů můžete uložit počet sledujících:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Můžete uložit skutečný graf následovníků pomocí rozhraní API Azure Cosmos DB [Gremlin](../cosmos-db/graph-introduction.md) k vytvoření [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) pro každého uživatele a [hrany,](http://mathworld.wolfram.com/GraphEdge.html) které udržují vztahy "A-follows-B". Pomocí rozhraní Gremlin API můžete získat stoupence určitého uživatele a vytvářet složitější dotazy, které navrhnou společné lidi. Pokud do grafu přidáte kategorie obsahu, které se lidem líbí nebo které se líbí, můžete začít tkalit prostředí, která zahrnují inteligentní zjišťování obsahu, navrhnout obsah, který se vám líbí, nebo najít lidi, se kterými byste mohli mít mnoho společného.

Dokument Statistika uživatelů lze stále použít k vytvoření karet v uživatelském rozhraní nebo rychlých náhledů profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>Vzor "Žebřík" a duplikace dat

Jak jste si možná všimli v dokumentu JSON, který odkazuje na příspěvek, existuje mnoho výskytů uživatele. A vy byste uhodli správně, tyto duplikáty znamenají, že informace, které popisují uživatele, vzhledem k této denormalizaci, mohou být přítomny na více než jednom místě.

Chcete-li povolit rychlejší dotazy, dochází k duplikaci dat. Problém s tímto vedlejším účinkem je, že pokud nějakou akcí se změní data uživatele, musíte najít všechny aktivity, které uživatel kdy udělal, a aktualizovat je všechny. To nezní prakticky, že?

Vyřešíte tak, že identifikujete klíčové atributy uživatele, které zobrazíte v aplikaci pro každou aktivitu. Pokud vizuálně zobrazíte příspěvek ve vaší aplikaci a zobrazíte pouze jméno a obrázek autora, proč ukládat všechna data uživatele do atributu "createdBy"? Pokud u každého komentáře zobrazíte pouze obrázek uživatele, nepotřebujete zbytek informací o uživateli. To je místo, kde se zapojuje něco, čemu říkám "Žebříkový vzor".

Vezměme si jako příklad informace o uživateli:

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

Při pohledu na tyto informace, můžete rychle zjistit, které jsou důležité informace a které nejsou, čímž se vytvoří "Žebřík":

![Schéma vzoru žebříku](./media/social-media-apps/social-media-apps-ladder.png)

Nejmenší krok se nazývá UserChunk, minimální část informace, která identifikuje uživatele a používá se pro duplikaci dat. Snížením duplicitní velikosti dat pouze na informace, které budete "zobrazovat", snížíte možnost masivních aktualizací.

Prostřední krok se nazývá uživatel. Je to úplná data, která se budou používat na většině dotazů závislých na výkonu na Cosmos DB, nejvíce přístupná a kritická. Obsahuje informace reprezentované UserChunk.

Největší je rozšířený uživatel. Obsahuje důležité informace o uživateli a další data, která není nutné číst rychle nebo má konečné využití, jako je proces přihlášení. Tato data se můžou ukládat mimo Cosmos DB, v Azure SQL Database nebo V tabulkách úložiště Azure.

Proč byste rozdělit uživatele a dokonce ukládat tyto informace na různých místech? Vzhledem k tomu, že z hlediska výkonu, tím větší dokumenty, nákladnější dotazy. Udržujte dokumenty štíhlé a se správnými informacemi, které vám pomohou provést všechny dotazy závislé na výkonu pro vaši sociální síť. Uklápěte další další informace pro případné scénáře, jako jsou úplné úpravy profilu, přihlášení a dolování dat pro analýzu využití a iniciativy big data. Opravdu je jedno, jestli shromažďování dat pro dolování dat je pomalejší, protože běží na Azure SQL Database. Máte obavy, i když, že vaši uživatelé mají rychlý a tenký zážitek. Uživatel uložený v Cosmos DB bude vypadat jako tento kód:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

A příspěvek by vypadal takto:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Pokud dojde k úpravě, kde je ovlivněn atribut bloku, můžete snadno najít ovlivněné dokumenty. Stačí použít dotazy, které odkazují na indexované `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`atributy, například , a potom aktualizovat bloky dat.

## <a name="the-search-box"></a>Vyhledávací pole

Uživatelé budou generovat, naštěstí, hodně obsahu. A měli byste být schopni poskytnout možnost vyhledávat a najít obsah, který nemusí být přímo v jejich streamech obsahu, možná proto, že nesledujete tvůrce, nebo se možná jen snažíte najít ten starý příspěvek, který jste udělali před šesti měsíci.

Vzhledem k tomu, že používáte Azure Cosmos DB, můžete snadno implementovat vyhledávač pomocí [Azure Cognitive Search](https://azure.microsoft.com/services/search/) během několika minut bez zadání kódu, jiné než proces vyhledávání a ui.

Proč je tento proces tak snadný?

Azure Cognitive Search implementuje to, co nazývají [Indexery](https://msdn.microsoft.com/library/azure/dn946891.aspx), procesy na pozadí, které se zavěsí do úložišť dat a automaticky magicky přidávají, aktualizují nebo odeberou objekty v indexech. Podporují [indexery Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexery objektů Blobs Azure](../search/search-howto-indexing-azure-blob-storage.md) a naštěstí [indexery Azure Cosmos DB](../search/search-howto-index-documentdb.md). Přechod informací z Cosmos DB na Azure Cognitive Search je jednoduchý. Obě technologie ukládají informace ve formátu JSON, takže stačí [vytvořit index](../search/search-create-index-portal.md) a mapovat atributy z dokumentů, které chcete indexovat. A to je vše! V závislosti na velikosti vašich dat bude veškerý obsah k dispozici pro vyhledávání během několika minut pomocí nejlepšího řešení Hledat jako služba v cloudové infrastruktuře.

Další informace o Azure Cognitive Search najdete v [Průvodci stopařovým vyhledáváním](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Základní znalosti

Po uložení veškerého obsahu, který roste a roste každý den, můžete najít myšlení: Co mohu dělat se všemi těmito proudy informací od mých uživatelů?

Odpověď je přímočará: Dejte to do práce a poučit se z ní.

Ale co se můžete naučit? Mezi několik jednoduchých příkladů patří [analýza mínění](https://en.wikipedia.org/wiki/Sentiment_analysis), doporučení obsahu založená na předvolbách uživatele nebo dokonce automatický moderátor obsahu, který zajišťuje, že obsah publikovaný vaší sociální sítí je pro rodinu bezpečný.

Teď, když jsem tě zahákl, asi si budeš myslet, že potřebuješ doktorát z matematiky, abys získal tyhle vzorce a informace z jednoduchých databází a souborů, ale mýlil by ses.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), součást [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), je plně spravovaná cloudová služba, která umožňuje vytvářet pracovní postupy pomocí algoritmů v jednoduchém rozhraní pro přetahování, kódovat vlastní algoritmy v [R](https://en.wikipedia.org/wiki/R_\(programming_language\))nebo používat některá již vytvořená a připravená rozhraní API, jako jsou: [Analýza textu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Obsah obsahu nebo [doporučení](https://gallery.azure.ai/Solution/Recommendations-Solution).

K dosažení některého z těchto scénářů Machine Learning, můžete použít [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) k ingestování informací z různých zdrojů. Můžete také použít [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) ke zpracování informací a generovat výstup, který může být zpracována Azure Machine Learning.

Další dostupnou možností je použití [služby Azure Cognitive Services](https://www.microsoft.com/cognitive-services) k analýze obsahu uživatelů. nejen, že jim lépe rozumíte (prostřednictvím analýzy toho, co píší pomocí [rozhraní Api pro analýzu textu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale můžete také detekovat nežádoucí nebo zralý obsah a podle toho jednat s [rozhraním Api pro počítačové zpracování .](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) Služby Cognitive Services obsahují mnoho řešení, která jsou vzdálená a nevyžadují žádné znalosti strojového učení.

## <a name="a-planet-scale-social-experience"></a>Sociální zkušenost v měřítku planety

Tam je poslední, ale v neposlední řadě, důležitý článek musím řešit: **škálovatelnost**. Při návrhu architektury by měla každá součást škálovat samostatně. Nakonec budete muset zpracovat více dat, nebo budete chtít mít větší geografické pokrytí. Naštěstí dosažení obou úkolů je **zkušenost na klíč** s Cosmos DB.

Cosmos DB podporuje dynamické dělení out-of-the-box. Automaticky vytváří oddíly na základě daného **klíče oddílu**, který je definován jako atribut v dokumentech. Definování správného klíče oddílu musí být provedeno v době návrhu. Další informace najdete [v tématu Dělení v Azure Cosmos DB](partitioning-overview.md).

Pro sociální prostředí je nutné sladit strategii dělení se způsobem dotazování a psaní. (Například čtení v rámci stejného oddílu jsou žádoucí a vyhnout se "aktivní body" rozprostřením zápisy na více oddílů.) Některé možnosti jsou: oddíly založené na dočasném klíči (den/měsíc/týden), podle kategorie obsahu, podle zeměpisné oblasti nebo podle uživatele. Vše skutečně závisí na tom, jak budete dotazovat data a zobrazovat data ve vašem sociálním prostředí.

Cosmos DB spustí vaše dotazy (včetně [agregací)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)ve všech oddílech transparentně, takže nemusíte přidávat žádnou logiku, jak vaše data rostou.

S časem nakonec zvýšíte provoz a zvýší se vaše spotřeba zdrojů (měřená v [ru](request-units.md)nebo jednotek požadavků). Budete číst a psát častěji, jak vaše uživatelská základna roste. Uživatelská základna začne vytvářet a číst další obsah. Takže schopnost **škálování propustnosti** je zásadní. Zvýšení ru je snadné. Můžete to udělat s několika kliknutími na webu Azure Portal nebo [vydáváním příkazů prostřednictvím rozhraní API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Škálování a definování klíče oddílu](./media/social-media-apps/social-media-apps-scaling.png)

Co se stane, když se to bude zlepšovat? Předpokládejme, že uživatelé z jiné oblasti, země nebo kontinentu si vaší platformy všimnou a začnou ji používat. Jaké velké překvapení!

Ale počkejte! Brzy si uvědomíte, že jejich zkušenosti s vaší platformou nejsou optimální. Jsou tak daleko od vaší operační oblasti, že latence je hrozná. Očividně nechceš, aby skončili. Kdyby jen tam byl snadný způsob, jak **rozšířit svůj globální dosah?** Je!

Cosmos DB umožňuje [replikovat data globálně](../cosmos-db/tutorial-global-distribution-sql-api.md) a transparentně pomocí několika kliknutí a automaticky vybrat mezi dostupnými oblastmi z vašeho [klientského kódu](../cosmos-db/tutorial-global-distribution-sql-api.md). Tento proces také znamená, že můžete mít [více oblastí převzetí služeb při selhání](high-availability.md).

Při globální replikaci dat je třeba zajistit, aby je klienti mohli využít. Pokud používáte webový front-end nebo přistupujete k apim z mobilních klientů, můžete nasadit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) a naklonovat službu Azure App Service ve všech požadovaných oblastech pomocí konfigurace výkonu pro podporu rozšířeného globálního pokrytí. Když vaši klienti přístup front-endu nebo API, budou směrovány do nejbližší služby app, která se zase připojí k místní repliky Cosmos DB.

![Přidání globálního pokrytí do vaší sociální platformy](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Závěr

Tento článek vrhá světlo do alternativ vytváření sociálních sítí zcela v Azure s nízkonákladové služby. přináší výsledky podporou používání vícevrstvého úložného řešení a distribuce dat s názvem "Žebřík".

![Diagram interakce mezi službami Azure pro sociální sítě](./media/social-media-apps/social-media-apps-azure-solution.png)

Pravdou je, že pro takové scénáře neexistuje žádná stříbrná kulka. Je to synergie vytvořená kombinací skvělých služeb, které nám umožňují vytvářet skvělé zážitky: rychlost a svoboda Azure Cosmos DB, která poskytuje skvělou sociální aplikaci, inteligenci za prvotřídním vyhledávacím řešením, jako je Azure Cognitive Search, flexibilita Azure App Services pro hostování aplikací, které nejsou ani jazykově nezávislá, ale výkonné procesy na pozadí a rozšiřitelná azure storage a Azure SQL Database pro ukládání obrovského množství dat a analytické síly Azure Machine Learning vytvářet znalosti a inteligenci, které mohou poskytovat zpětnou vazbu vašim procesům a pomáhají nám poskytovat správný obsah správným uživatelům.

## <a name="next-steps"></a>Další kroky

Další informace o případech použití pro Cosmos DB naleznete [v tématu Common Cosmos DB případy použití](use-cases.md).
