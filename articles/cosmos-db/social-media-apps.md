---
title: 'Model návrhu Azure Cosmos DB: aplikace pro sociální média'
description: Seznamte se s návrhovým vzorem pro sociální sítě, a to využitím flexibility úložiště Azure Cosmos DB a dalších služeb Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 7118a12a5a92912c51bb35d8b516d5b8e2f45388
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478146"
---
# <a name="going-social-with-azure-cosmos-db"></a>Spolupráce s Azure Cosmos DB

Živý ve vysoce propojené společnosti znamená, že v určitém okamžiku se stanete součástí **sociální sítě**. Pomocí sociálních sítí se budete moci spojit s přáteli, kolegy, rodinou nebo občas sdílet své zaujetí s lidmi se společnými zájmy.

Jako technici nebo vývojáři můžete mít přemýšleli, jak tyto sítě ukládají a propojovat vaše data. Nebo jste dokonce mohli vytvořit nebo vytvořit architekta nové sociální sítě pro konkrétní mezery trh. To je v situaci, kdy se jedná o významnou otázku: jak jsou všechna tato data uložena?

Předpokládejme, že vytváříte novou a lesklou sociální síť, ve které mohou uživatelé publikovat články se souvisejícími médii, jako jsou obrázky, videa nebo dokonce hudba. Uživatelé mohou komentovat příspěvky a dávat body za hodnocení. K dispozici jsou informační kanály pro příspěvky, které uživatelé uvidí a budou s nimi pracovat na úvodní stránce na hlavním webu. Tato metoda není v první době složitá, ale z důvodu jednoduchosti ji zastavíme. (Můžete se rozznačit do vlastních uživatelských kanálů, které jsou ovlivněné vztahy, ale překročí rámec tohoto článku.)

Jak tedy ukládáte tato data a kde?

Je možné, že máte zkušenosti s databázemi SQL nebo máte pojem [relační modelování dat](https://en.wikipedia.org/wiki/Relational_model). Můžete začít kreslit něco, jak je znázorněno níže:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="Diagram znázorňující relativní relační model" border="false":::

Dokonale normalizovaná a velmi poměrně datová struktura... To se neškáluje.

Nedaří se mi znovu, již jsem pracoval s databázemi SQL a vše můj život. Jsou skvělé, ale podobně jako při každém vzoru, praxi a softwarové platformě není ideální pro každý scénář.

Proč není SQL nejlepší volbou v tomto scénáři? Pojďme se podívat na strukturu jednoho příspěvku. Pokud bych chtěl Zobrazit příspěvek na webu nebo v aplikaci, musím udělat dotaz s... připojením osmi tabulek (!) pouze k zobrazení jednoho příspěvku. Teď nahrajte datový proud příspěvků, které se dynamicky načítají a zobrazují na obrazovce, a můžete si všimnout, kde se to dělá.

Můžete použít obrovský instanci SQL s dostatečnou silou pro řešení tisíců dotazů s mnoha spojeními, které slouží k poskytování obsahu. Ale proč byste měli, když existuje jednodušší řešení?

## <a name="the-nosql-road"></a>NoSQL cesta

Tento článek vás provede jednotlivými modelováním dat na sociálních platformách pomocí Azure NoSQL [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) Database a efektivně. Také se dozvíte, jak používat jiné funkce Azure Cosmos DB, jako je [GREMLIN API](../cosmos-db/graph-introduction.md). S využitím přístupu [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , ukládání dat ve formátu JSON a použití [denormalizace](https://en.wikipedia.org/wiki/Denormalization), se dřív komplikovaný příspěvek dá transformovat do jednoho [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):

```json
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
```

A k tomu může dojít s jediným dotazem a bez spojení. Tento dotaz je mnohem jednoduchý a jasný a je z rozpočtu potřeba méně prostředků, aby bylo dosaženo lepšího výsledku.

Azure Cosmos DB zajistí, aby byly všechny vlastnosti indexovány pomocí automatického indexování. Automatické indexování lze dokonce [přizpůsobit](index-policy.md). Přístup bez schémat umožňuje uložit dokumenty s různou a dynamickou strukturou. Možná zítra budete chtít, aby příspěvky měly seznam kategorií nebo hashtagů, které jsou k nim přidružené? Cosmos DB zpracuje nové dokumenty pomocí přidaných atributů bez další práce, kterou nám vyžaduje.

Komentáře na příspěvku lze považovat za jiné příspěvky s nadřazenou vlastností. (Tento postup zjednodušuje mapování objektů.)

```json
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
```

A všechny sociální interakce mohou být uloženy na samostatném objektu jako čítače:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

Vytváření informačních kanálů je jenom věcí k vytváření dokumentů, které můžou obsahovat seznam ID příspěvků s daným pořadím významnosti:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

Můžete mít "poslední" datový proud s příspěvky seřazenými podle data vytvoření. Nebo můžete mít datový proud "nejžhavějších" s těmito příspěvky většími než za posledních 24 hodin. Můžete dokonce implementovat vlastní datový proud pro každého uživatele na základě logiky, jako je sledující a zájmy. Stále se jedná o seznam příspěvků. Je to způsob, jak tyto seznamy sestavit, ale výkon čtení zůstává nerušený. Po získání jednoho z těchto seznamů vydáte jeden dotaz, který Cosmos DB pomocí [klíčového slova in](sql-query-keywords.md#in) získat stránky příspěvků.

Datové proudy informačního kanálu lze sestavit pomocí procesů [Azure App Services '](https://azure.microsoft.com/services/app-service/) na pozadí: [WebJobs](../app-service/webjobs-create.md). Po vytvoření příspěvku se zpracování na pozadí dá aktivovat pomocí [Azure Storage](https://azure.microsoft.com/services/storage/) [front](../storage/queues/storage-dotnet-how-to-use-queues.md) a WebJobs aktivovaných pomocí [sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), která implementuje šíření po rozbalení v datových proudech na základě vlastní logiky.

Body a jako v příspěvku lze zpracovat odvoditelné způsobem pomocí stejné techniky pro vytvoření prostředí s konečnou konzistencí.

Sledující jsou trickier. Cosmos DB má omezení velikosti dokumentu a čtení a zápis velkých dokumentů může mít vliv na škálovatelnost vaší aplikace. Můžete tedy zvážit ukládání sledujících dokumentů jako dokumentu s touto strukturou:

```json
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
```

Tato struktura může fungovat pro uživatele s několika tisíci sledujícími. Pokud se celebrit spojí s pořadím, ale tento přístup povede k velké velikosti dokumentu a může nakonec dosáhnout limitu velikosti dokumentu.

Chcete-li tento problém vyřešit, můžete použít smíšený přístup. V rámci dokumentu statistiky uživatele můžete ukládat Počet sledujících:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

Můžete si uložit skutečný graf sledující pomocí Azure Cosmos DB [rozhraní Gremlin API](../cosmos-db/graph-introduction.md) k vytváření [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) pro každého uživatele a [hrany](http://mathworld.wolfram.com/GraphEdge.html) , které udržují relace "A" za B ". Pomocí rozhraní Gremlin API můžete získat sledující konkrétního uživatele a vytvořit složitější dotazy pro návrh lidí. Pokud přidáte do grafu kategorie obsahu, které lidé chtějí nebo chtějí využít, můžete začít tkaní prostředí, která zahrnují inteligentní zjišťování obsahu, navrhovat obsah, který sledují lidé, jako je, nebo najít lidi, které mohou být v podstatě běžné.

K vytváření karet v uživatelském rozhraní nebo v náhledech rychlých profilů můžete použít dokument s statistikou uživatelů.

## <a name="the-ladder-pattern-and-data-duplication"></a>Vzor "žebřík" a duplikace dat

Jak jste si všimli v dokumentu JSON, který odkazuje na příspěvek, existuje mnoho výskytů uživatele. A měli byste se rozhodnout, že tyto duplicity znamenají, že informace, které popisují uživatele, se můžou vyskytovat ve více než jednom místě.

Pokud chcete pro rychlejší dotazy použít více dotazů, bude se vám zabývat duplicity dat. Tento problém s tímto účinkem spočívá v tom, že v případě, že se data uživatele změní, je nutné najít všechny aktivity, které uživatel někdy uskutečnil, a aktualizovat je všechny. Nejedná se o praktickou a pravou?

Chystáte se ho vyřešit určením klíčových atributů uživatele, který zobrazíte v aplikaci pro každou aktivitu. Pokud ve své aplikaci vizuálně ukážete příspěvek a chcete zobrazit jenom jméno autora a obrázek, proč ukládat všechna data uživatele do atributu "createdBy"? Pokud se u každého komentáře zobrazuje jenom obrázek uživatele, nepotřebujete opravdu zbývající informace o uživateli. To je místo, kde se zavolá "vzor žebříku".

Pojďme získat informace o uživateli jako příklad:

```json
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
```

Když si tyto informace prohlížíte, můžete rychle zjistit, které z nich jsou důležité a které nejsou, takže se vytvoří "žebřík":

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="Diagram znázorňující relativní relační model" border="false":::

Nejmenší krok se nazývá UserChunk, minimální část informací, která identifikuje uživatele a používá se pro duplikaci dat. Zmenšením velikosti duplicitních dat na jenom informace, které se zobrazí, snížíte tak možnost obrovských aktualizací.

Prostřední krok se nazývá uživatel. K dispozici jsou všechna data, která se použijí pro většinu dotazů závislých na výkonu na Cosmos DB, nejpravděpodobnějších a kritických. Obsahuje informace reprezentované UserChunk.

Největší je rozšířený uživatel. Obsahuje důležité informace o uživateli a další data, která nemusejí být rychle čtena nebo mají konečné použití, například proces přihlašování. Tato data můžou být uložená mimo Cosmos DB v tabulkách Azure SQL Database nebo Azure Storage.

Proč byste mohli uživatele rozdělit a dokonce uložit tyto informace na různých místech? Vzhledem k tomu, že se z hlediska výkonu zobrazuje větší počet dokumentů, costlier dotazy. Udržujte si dokumenty na tenké straně se správnými informacemi, abyste mohli provádět všechny dotazy závislé na výkonu pro vaši sociální síť. Ukládejte Další Další informace pro případné scénáře, jako jsou úplné úpravy profilů, přihlášení a dolování dat pro účely analýzy využití a testování velkých objemů dat. Nezáleží na tom, jestli je shromažďování dat pro dolování dat pomalejší, protože běží na Azure SQL Database. Máte obavy, že uživatelé mají rychlý a tenký zážitek. Uživatel uložený v Cosmos DB by vypadal jako tento kód:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

A příspěvek by vypadal jako:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

Když dojde k úpravě, kde je ovlivněn atribut bloku dat, můžete snadno najít ovlivněné dokumenty. Stačí použít dotazy, které odkazují na indexované atributy, jako například `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` , a poté aktualizovat bloky dat.

## <a name="the-search-box"></a>Vyhledávací pole

Uživatelé vygenerují, donovanovo, mnoho obsahu. A měli byste být schopni umožnit vyhledávání a hledání obsahu, který nemusí být přímo v datových proudech obsahu, protože nedodržujete tvůrce, nebo možná jste se pokusili zjistit původní příspěvek, který jste před šesti měsíci.

Vzhledem k tomu, že používáte Azure Cosmos DB, můžete snadno implementovat vyhledávací modul pomocí [Azure kognitivní hledání](https://azure.microsoft.com/services/search/) během několika minut, aniž byste museli psát kód, který je jiný než proces vyhledávání a uživatelské rozhraní.

Proč je tento proces tak snadný?

Azure Kognitivní hledání implementuje, co volají [indexery](/rest/api/searchservice/Indexer-operations), procesy na pozadí, které jsou zapojené do úložišť dat, a automagic přidávají, aktualizují nebo odstraňují objekty v indexech. Podporují [Azure SQL Database indexery](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search), [indexery Azure BLOBs](../search/search-howto-indexing-azure-blob-storage.md) a naštěstí, [Azure Cosmos DB indexery](../search/search-howto-index-cosmosdb.md). Přechod informací z Cosmos DB do Azure Kognitivní hledání je jednoduchý. Obě technologie ukládají informace ve formátu JSON, takže potřebujete pouze [vytvořit index](../search/search-what-is-an-index.md) a namapovat atributy z dokumentů, které chcete indexovat. A to je vše! V závislosti na velikosti dat bude k dispozici veškerý obsah, který bude prohledán během několika minut od nejlepšího řešení hledání jako služby v cloudové infrastruktuře.

Další informace o službě Azure Kognitivní hledání najdete v [příručce hitchhiker](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search), kde můžete hledat.

## <a name="the-underlying-knowledge"></a>Základní znalostní báze

Po uložení veškerého tohoto obsahu, který se postupně rozroste a roste, se můžete setkat s tím, co se dá dělat s veškerým datovým proudem informací z mých uživatelů.

Odpověď je jednoduchá: uložte ji a Naučte se ji používat.

Ale k čemu se můžete dozvědět? Mezi jednoduché příklady patří [Analýza mínění](https://en.wikipedia.org/wiki/Sentiment_analysis), doporučení k obsahu založené na uživatelských preferencích nebo dokonce i automatizovaný moderátor obsahu, který zajišťuje, aby byl obsah publikovaný vaší sociální sítí bezpečný pro rodinu.

Teď, když jste se připojili, pravděpodobně budete potřebovat některé PhD v matematické oblasti pro extrakci těchto vzorků a informací z jednoduchých databází a souborů, ale je to ale chybné.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), která je součástí [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), je plně spravovaná cloudová služba, která umožňuje vytvářet pracovní postupy pomocí algoritmů v jednoduchém rozhraní přetažení, kódovat vlastní algoritmy v jazyce [R](https://en.wikipedia.org/wiki/R_\(programming_language\))nebo použít některé z již sestavených a připravených k používání rozhraní api, jako jsou například: [Analýza textu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), Content moderator nebo [doporučení](https://gallery.azure.ai/Solution/Recommendations-Solution).

Chcete-li dosáhnout některého z těchto scénářů Machine Learning, můžete použít [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) k ingestování informací z různých zdrojů. Můžete také použít [u-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) ke zpracování informací a vygenerování výstupu, který lze zpracovat pomocí Azure Machine Learning.

Další dostupnou možností je použít k analýze obsahu uživatelů službu [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) ; nejenom je můžete lépe pochopit (analýzou toho, co zapisuje pomocí [rozhraní API pro analýzu textu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale můžete taky zjistit nežádoucí nebo vyzrálější obsah a pracovat s ním [rozhraní API pro počítačové zpracování obrazu](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services obsahuje mnoho předem připravených řešení, která nevyžadují, aby se používaly žádné Machine Learning znalosti.

## <a name="a-planet-scale-social-experience"></a>Prostředí globálním pro sociální škálování

K dispozici je jen poslední, ale nejméně důležitý článek, který je potřeba řešit: **škálovatelnost**. Při návrhu architektury by se každá součást měla škálovat samostatně. Nakonec budete muset zpracovat více dat nebo budete chtít mít větší geografické pokrytí. Naštěstí, které jsou v obou úlohách, je **prostředí klíč** s Cosmos DB.

Cosmos DB podporuje dynamické dělení předem. Automaticky vytvoří oddíly na základě daného **klíče oddílu**, který je definován jako atribut v dokumentech. Definování správného klíče oddílu je nutné provést v době návrhu. Další informace najdete v tématu [dělení v Azure Cosmos DB](partitioning-overview.md).

Pro sociální prostředí je nutné sjednotit strategii dělení pomocí způsobu dotazování a zápisu. (Například čtení v rámci stejného oddílu je žádoucí a nepoužívejte "aktivní body" rozšíříte zápisy na více oddílů.) Mezi možnosti patří: oddíly založené na dočasném klíči (den/měsíc/týden), podle kategorie obsahu, podle zeměpisné oblasti nebo podle uživatele. Všechno ve skutečnosti záleží na způsobu, jakým se dotazuje na data a zobrazují data v sociálním prostředí.

Cosmos DB spustí vaše dotazy (včetně [agregací](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) ve všech vašich oddílech transparentně, takže nemusíte přidávat žádnou logiku, protože vaše data roste.

V čase budete nakonec růst provozu a spotřebu prostředků (měřené v [ru](request-units.md)nebo jednotkách žádosti) se zvýší. Při zvětšování uživatelské základny budete číst a zapisovat častěji. Uživatelskou základnu začne vytvářet a číst další obsah. Schopnost **škálování propustnosti** je proto důležitá. Zvýšení ru je snadné. Můžete to udělat několika kliknutími na Azure Portal nebo vyvoláním [příkazů prostřednictvím rozhraní API](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Diagram znázorňující relativní relační model":::

Co se stane, když všechno pořád ještě lepší? Předpokládejme, že uživatelé z jiné oblasti, země nebo kontinentu si vyvšimli vaši platformu a začnou ji používat. Co Skvělé neočekávaně!

Ale počkejte! Brzy zjistíte, že své zkušenosti s platformou nejsou optimální. Jsou zatím mimo vaši provozní oblast, že latence je ještěrů. Zjevně nechcete, aby se ukončily. Pokud je k dispozici pouze snadný způsob **rozšíření globálního dosahu**? K dispozici je!

Cosmos DB umožňuje globálně a transparentně [replikovat data](../cosmos-db/tutorial-global-distribution-sql-api.md) několika kliknutími a automaticky vybírat z dostupných oblastí z vašeho [klientského kódu](../cosmos-db/tutorial-global-distribution-sql-api.md). Tento proces také znamená, že můžete mít [více oblastí převzetí služeb při selhání](high-availability.md).

Při globální replikaci dat je potřeba zajistit, aby ji vaši klienti mohli využít. Pokud používáte webový front-end nebo přístup k rozhraním API z mobilních klientů, můžete nasadit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) a klonovat Azure App Service ve všech požadovaných oblastech pomocí konfigurace výkonu pro podporu vašeho rozšířeného globálního pokrytí. Když klienti přistupují k front-endu nebo rozhraním API, budou přesměrováni na nejbližší App Service, která se zase připojí k místní replice Cosmos DB.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Diagram znázorňující relativní relační model" border="false":::

## <a name="conclusion"></a>Závěr

Tento článek se podrobněji přenese do alternativních možností vytváření sociálních sítí, které jsou zcela v Azure, s nižšími náklady. poskytuje výsledky tím, že povzbudí použití řešení úložiště s více vrstvami a distribuce dat s názvem "žebřík".

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Diagram znázorňující relativní relační model" border="false":::

Pravdy je, že pro tento druh scénářů není k dispozici žádná stříbrné odrážka. Jedná se o synergii vytvořenou kombinací skvělých služeb, které nám umožňují sestavovat Skvělé prostředí: rychlost a volnost Azure Cosmos DB poskytování Skvělé sociální aplikace, inteligentních řešení pro vyhledávání, jako je Azure Kognitivní hledání, flexibility Azure App Services pro hostování nenezávislách aplikací pro jazyky, ale výkonné procesy na pozadí a rozšiřitelné Azure Storage a Azure SQL Database pro ukládání velkých objemů dat a analytické síly počítačů Azure Naučte se vytvářet poznatky a inteligentní informace, které vám poskytnou zpětnou vazbu vašim procesům a pomáhají zajistit správnému obsahu správným uživatelům.

## <a name="next-steps"></a>Další kroky

Další informace o případech použití Cosmos DB najdete v tématu [běžné případy použití Cosmos DB](use-cases.md).