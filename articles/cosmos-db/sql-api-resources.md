---
title: Azure Cosmos DB model prostředků a koncepty | Microsoft Docs
description: Další informace o databázi Cosmos Azure hierarchické model databáze, kolekce, uživatelem definované funkce (UDF), dokumentů, oprávnění ke správě prostředků a další.
keywords: Model hierarchické, cosmosdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0fc8a977a172a859d6691a5b587135caf14e03f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Hierarchický model prostředků a základní koncepty databáze Azure Cosmos

Databáze entitami, které spravuje databázi Cosmos Azure jsou označovány jako **prostředky**. Každý prostředek je jedinečně identifikovaný logického identifikátoru URI. Můžete pracovat s prostředky pomocí standardních operací protokolu HTTP, hlaviček požadavků a odpovědí a stavové kódy. 

Tento článek obsahuje odpovědi na tyto otázky:

* Co je Azure Cosmos DB model prostředků?
* Co jsou systémové definované prostředky a uživatelem definované prostředky?
* Jak řeší prostředku?
* Jak funguje s kolekcí?
* Jak funguje s uložené procedury, triggery a uživatelem definované funkce (UDF)?

V následujícím videu Azure manažer programu DB Cosmos Andrew Liu vás provede procesem model prostředků Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/luWFgTP0IL4]
>
>

## <a name="hierarchical-resource-model"></a>Model hierarchické prostředků
Jak ukazuje následující diagram, Azure DB Cosmos hierarchické **model prostředků** se skládá ze sady prostředků v rámci účtu databáze, každý adresovatelné prostřednictvím logické a stabilní identifikátoru URI. Sadu prostředků jsou označovány jako **kanálu** v tomto článku. 

> [!NOTE]
> Azure Cosmos DB nabízí vysoce efektivní protokol TCP, který je také dosáhl standardu RESTful v jeho komunikace modelu, k dispozici prostřednictvím [klient SQL .NET API](sql-api-sdk-dotnet.md).
> 
> 

![Model hierarchické prostředků Azure Cosmos DB][1]  
**Model hierarchické prostředků**   

Chcete-li začít pracovat s prostředky, je potřeba [vytvoření databázového účtu](create-sql-api-dotnet.md) pomocí svého předplatného Azure. Databázový účet se může skládat z sadu **databáze**, každá obsahuje několik **kolekce**, každý naopak obsahovat ** uložené procedury, aktivuje, funkce UDF, dokumenty a související  **přílohy**. Databáze také přiřazeni **uživatelé**, každý s sadu **oprávnění** pro přístup k kolekce, uložené procedury, triggery, funkce UDF, dokumenty nebo přílohy. Databáze, uživatelé, oprávnění a kolekce jsou systémem definované prostředky s dobře známými schématy, dokumenty a přílohy obsahují libovolný, uživatelem definovaný obsah JSON.  

| Prostředek | Popis |
| --- | --- |
| Databázový účet |Databázový účet je přidružen sadu databází a pevné velikosti úložiště objektů blob pro přílohy. Můžete vytvořit jeden nebo více účtů databáze pomocí svého předplatného Azure. Další informace najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Databáze |Databáze je logický kontejner úložiště dokumentů rozděleného mezi kolekcemi. Je také kontejner uživatelé. |
| Uživatel |Logické obor názvů pro obor oprávnění. |
| Oprávnění |Autorizační token přidružit k uživateli pro přístup k určitému zdroji. |
| Kolekce |Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace. Kolekce je fakturovatelná entita, kde [náklady](performance-levels.md) je určen podle úrovně výkonu přidružené ke kolekci. Kolekce mohou pokrývat jeden nebo více oddílů nebo serverů a lze je škálovat tak, aby zvládaly prakticky neomezené objemy úložišť a propustnosti. |
| Uložená procedura |Logiku aplikace napsané v jazyce JavaScript, který je registrován s kolekcí a transakčně provést v rámci databázového stroje. |
| Trigger |Logiku aplikace napsané v jazyce JavaScript provést před nebo po buď typu vložení, nahraďte nebo operace odstranění. |
| UDF |Logiku aplikace napsané v jazyce JavaScript. Funkce UDF umožňují modelu operátor vlastního dotazu a tím rozšiřovat základní rozhraní SQL API dotazovací jazyk. |
| Dokument |Uživatelem definovaný (libovolný) obsah JSON. Ve výchozím nastavení musí být definováno žádné schéma ani udělat sekundární indexy, které musí být zadané pro všechny dokumenty do kolekce přidána. |
| Příloha |Přílohu je speciální dokument obsahující odkazy a související metadata pro externí objektů blob nebo médium. Vývojář můžete mít objekt blob spravuje Cosmos DB nebo jej uložte u poskytovatele služeb externí objekt blob, jako je například OneDrive, Dropbox, atd. |

## <a name="system-vs-user-defined-resources"></a>Systém oproti uživatelem definované prostředky
Prostředkům, například účty databáze, databáze, kolekce, uživatelé, oprávnění, uložené procedury, aktivační události a UDF – všechny mají pevného schématu a se označují jako systémové prostředky. Naproti tomu prostředkům, například dokumentů a příloh mít žádná omezení na schéma a jsou příklady uživatelem definované prostředky. V systému Cosmos databáze jsou systému i uživatelem definované prostředky reprezentované a spravovat jako kompatibilní se standardem standard JSON. Všechny prostředky, systém nebo uživatelsky definované, mají následující běžné vlastnosti:

> [!NOTE]
> Všechny vlastnosti vygenerované systémem v prostředku začínají podtržítkem (_) v jejich reprezentace JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Vlastnost</strong></p></td>
            <td valign="top"><p><strong>Nastavit uživatele nebo vygenerované systémem?</strong></p></td>
            <td valign="top"><p><strong>Účel</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Generované systémem, jedinečný a hierarchické identifikátor prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Značka Etag požadované pro optimistické řízení souběžného prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Časového razítka posledního aktualizované prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Jedinečný identifikátor URI adresovatelné prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Buď</p></td>
            <td valign="top"><p>Uživatelem definované jedinečný název prostředku (s stejnou hodnotu klíče oddílu). Pokud uživatel není uveden id, id je generováno systémem</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Síťové vyjádření prostředků
Cosmos DB nenutí všechny vlastní rozšíření JSON standardní nebo speciální kódování; funguje s standardní kompatibilní dokumentů JSON.  

### <a name="addressing-a-resource"></a>Adresování prostředku
Všechny prostředky jsou adresovatelné identifikátor URI. Hodnota **_self** vlastnost prostředku představuje relativní identifikátor URI prostředku. Formát identifikátoru URI se skládá z /\<kanálu\>/ {_rid} segmenty cesty:  

| Hodnota _self | Popis |
| --- | --- |
| /DBS |Informační kanál databází pod účtem databáze |
| /DBS/ {dbName} |Databáze s id odpovídající hodnotě {dbName} |
| /colls/ /DBS/ {dbName} |Informační kanál kolekcí v databázi |
| /colls/ /DBS/ {dbName} {collName} |Kolekce s id odpovídající hodnotě {collName} |
| /colls/ /DBS/ {dbName} {collName} / docs |Informační kanál dokumentů v kolekci |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Dokumentů s id odpovídající hodnotě {doc} |
| /users/ /DBS/ {dbName} |Informační kanál uživatele v databázi |
| /dbs/{dbName}/users/{userId} |Uživatel s id odpovídající hodnota {uživatele} |
| /users/ /DBS/ {dbName} {userId} nebo oprávnění |Informační kanál oprávnění pod uživatelským |
| /permissions/ /users/ {userId} /DBS/ {dbName} {permissionId} |Oprávnění s id odpovídající hodnotě {oprávnění} |

Každý prostředek má jedinečný název definovaný uživatelem, zveřejňovány prostřednictvím vlastnost id. Poznámka: pro dokumenty, pokud uživatel není uveden id, sady SDK automaticky generovat jedinečný identifikátor pro dokument. Id je řetězec definovaný uživatelem, až 256 znaků, které je jedinečné v rámci konkrétní nadřazený prostředek. 

Každý prostředek, má také identifikátor hierarchické prostředků vygenerované systémem (také označované jako identifikátorů RID), která je dostupná přes vlastnost _rid. Identifikátor RID kóduje celou hierarchii daný prostředek a je vhodné interního vyjádření slouží k vynucení referenční integrity distribuovaných způsobem. Identifikátor RID je jedinečná v rámci účtu databáze a Cosmos DB je používán interně pro efektivní směrování, bez nutnosti hledání křížové oddílu. Hodnoty vlastností _rid a _self jsou alternativní i kanonický reprezentace prostředku. 

Rozhraní REST API podporují adresování prostředků a směrování požadavků id i _rid vlastnosti.

## <a name="database-accounts"></a>Databáze účtů
Můžete zřídit jeden nebo více Cosmos DB databáze účtů pomocí svého předplatného Azure.

Můžete vytvořit a spravovat účty pro databázi Cosmos DB prostřednictvím portálu Azure v [ http://portal.azure.com/ ](https://portal.azure.com/). Vytváření a správa databázový účet vyžaduje přístup pro správu a lze provést pouze v rámci vašeho předplatného Azure. 

### <a name="database-account-properties"></a>Vlastnosti účtu databáze
V rámci zřizování a správa databázového účtu můžete nakonfigurovat a přečtěte si následující vlastnosti:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Název vlastnosti</strong></p></td>
            <td valign="top"><p><strong>Popis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Konzistence zásad</p></td>
            <td valign="top"><p>Nastavte tuto vlastnost nakonfigurovat výchozí úroveň konzistence pro všechny kolekce v rámci účtu databáze. Můžete přepsat úroveň konzistence na základě žádosti pomocí hlaviček požadavku [x-ms--úroveň konzistence]. <p><p>Tato vlastnost se vztahuje pouze na <i>uživatelem definované prostředky</i>. Všechny systému definované prostředky jsou konfigurovány pro podporu čtení či dotazy s silnou konzistenci.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Autorizace klíče</p></td>
            <td valign="top"><p>Primární a sekundární hlavní a jen pro čtení klíčů, které poskytují přístup správce ke všem prostředkům v rámci účtu databáze.</p></td>
        </tr>
    </tbody>
</table>

Kromě zřizování, konfigurace a Správa účtu databáze z portálu Azure můžete také programově vytvořit a spravovat účty Cosmos DB databáze pomocí [rozhraní API REST Azure Cosmos DB](/rest/api/cosmos-db/) i [klientskou sadu SDK](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Databáze
Cosmos DB databáze je logický kontejner jeden nebo více kolekcí a uživatelů, jak je znázorněno v následujícím diagramu. Můžete vytvořit libovolný počet databází pod účtem databáze Cosmos DB vztahují omezení nabídky.  

![Účet a kolekce hierarchické model databáze][2]  
**Databáze je logický kontejner uživatelů a kolekce**

Databáze může obsahovat úložiště prakticky neomezené dokumentů rozděleného v rámci kolekce.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastické škálování databáze Azure Cosmos DB
Je ve výchozím nastavení – od několik GB až po petabajty SSD zálohovaný dokumentu úložiště a zřízené propustnosti elastické databáze Cosmos DB. 

Na rozdíl od databáze v tradiční relační databáze v databázi Cosmos není v oboru pro jeden počítač. S Cosmos DB jako škálování aplikace je potřeba růst, můžete více kolekcí a databází vytvořit. Ve skutečnosti různé první strany aplikací v rámci Microsoft již používáte Azure Cosmos DB škálované příjemce vytvořením velmi velké databáze Azure Cosmos DB každý obsahující tisíce kolekce s terabajtů úložiště dokumentů. Může zvětšovat a zmenšovat databáze přidáním nebo odebráním kolekcí, aby splňovaly požadavky rozsahu vaší aplikace. 

Můžete vytvořit libovolný počet kolekcí v databázi v závislosti nabídku. Každá kolekce má založenou na SSD úložiště a zřízené pro vás v závislosti na vybrané výkonu vrstvy propustnosti.

Databázi Azure Cosmos DB je také kontejner uživatelů. Uživatel, naopak, je logický obor názvů pro sadu oprávnění, která poskytuje jemně odstupňovaných autorizace a přístupu do kolekcí, dokumentů a příloh.  

Jako s další prostředky ve model prostředků Azure Cosmos DB databáze se dají vytvářet, nahrazen, odstranit, čtení, nebo vytvořit její výčet snadno buď pomocí [rozhraní REST API](/rest/api/cosmos-db/) ani v žádné z [klientskou sadu SDK](sql-api-sdk-dotnet.md). Azure Cosmos DB zaručuje silnou konzistenci pro čtení nebo dotazování metadata databáze prostředků. Odstranění databáze automaticky zajistí, že nemůžete použít žádnou z kolekce nebo uživatelé jsou v něm obsažena.   

## <a name="collections"></a>Kolekce
Cosmos DB kolekce je kontejner dokumentů JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastické SSD zálohovaný úložiště dokumentů
Kolekce je vnitřně elastické – automatické zvětšování a zmenší tak, jak přidat nebo odebrat dokumenty. Kolekce jsou logické prostředky a může mít rozsah jeden nebo více fyzických oddílů nebo serverů. Počet oddílů v rámci kolekce je dáno DB Cosmos na základě velikosti úložiště a zřízené propustnosti vaší kolekce. Každý oddíl v Cosmos DB má pevně stanovený objem zálohovaná na SSD úložiště s ním spojená a se replikují pro vysokou dostupnost. Oddíl správy je plně spravovat Azure Cosmos DB a není nutné zapsat složitý kód nebo spravovat vaše oddíly. Cosmos DB kolekce jsou **prakticky neomezené** z hlediska úložiště a propustnosti. 

### <a name="automatic-indexing-of-collections"></a>Automatické indexování kolekcí
Azure Cosmos DB je systém true databáze bez schémat. Se nepředpokládá ani nevyžaduje žádné schéma dokumentů JSON. Při přidávání dokumentů do kolekce, Azure Cosmos DB automaticky indexuje a jsou k dispozici pro vás k dotazování. Automatické indexování dokumentů, aniž byste museli schématu nebo sekundární indexy je klíčové funkce Azure Cosmos DB a je povolen jako techniky údržby optimalizovaný pro zápis, uvolněte zámku a protokolu strukturovaná index. Azure Cosmos DB podporuje dlouhodobě svazku extrémně rychlou zápisů při poskytování stále konzistentní dotazů. Dokument a index úložiště se použije k výpočtu úložiště spotřebovávají každou kolekci. Můžete ovládat, úložiště a výkon kompromisy přidružené k indexování tak, že nakonfigurujete zásady indexování pro kolekci. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurace zásady indexování kolekce
Zásady indexování jednotlivých kolekcí můžete výkonu a úložiště kompromis přidružené k indexování. K dispozici jako součást indexování konfigurace jsou následující možnosti:  

* Zvolte, zda kolekce automaticky indexuje všechny dokumenty nebo ne. Ve výchozím nastavení jsou všechny dokumenty automaticky indexovány. Můžete vypnout automatické indexování a selektivně přidat pouze konkrétní dokumentů do indexu. Naopak selektivně můžete vyloučit jenom konkrétní dokumenty. Můžete dosáhnout nastavení automatického vlastnost, která má být true nebo false na indexingPolicy kolekce a použitím hlaviček požadavku [x-ms-indexingdirective] při vložení, nahrazení nebo odstranění dokumentu.  
* Vyberte, zda chcete zahrnout nebo vyloučit konkrétní cesty nebo vzory v dokumentech z indexu. Můžete dosáhnout to tak, že nastavení includedPaths a excludedPaths na indexingPolicy kolekce v uvedeném pořadí. Můžete také nakonfigurovat úložiště a výkon kompromisy pro dotazy na rozsah a hodnota hash pro vzory konkrétní cesty. 
* Výběr mezi synchronní (konzistentní) a aktualizace asynchronní indexu (lazy). Ve výchozím nastavení je synchronně aktualizace indexu na každý insert, replace nebo odstranění dokumentu do kolekce. To umožňuje dotazy vyhovět stejnou úroveň konzistence jako čtení dokumentu. Zatímco Azure Cosmos DB je optimalizovaná zápisu která podporuje dlouhodobě svazky zápisů dokumentu společně s synchronní indexu údržby a poskytování konzistentní dotazů, můžete nakonfigurovat určité kolekce k aktualizaci svého indexu líné. Opožděné indexování součást zvyšuje výkon zápisu další a je ideální pro hromadné přijímání scénáře pro především pro čtení náročné kolekce.

Zásady indexování lze změnit spuštěním PUT na kolekci. To může být buď prostřednictvím dosáhnout [klienta SDK](sql-api-sdk-dotnet.md), [portál Azure](https://portal.azure.com) nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="querying-a-collection"></a>Dotazování na kolekci
Dokumenty v rámci kolekce může obsahovat libovolný schémata a můžete dát dotaz na dokumenty v rámci kolekce bez zadání žádné schéma nebo předem sekundárních indexů. Můžete zadat dotaz na kolekce pomocí [reference syntaxe Azure Cosmos DB SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx), která nabízí bohaté hierarchické, relační a prostorových operátory a rozšiřitelnost prostřednictvím bázi jazyka JavaScript UDF. Gramatika JSON umožňuje modelování dokumentů JSON jako stromy s popisky formě uzlů stromu. To je zneužití, jak rozhraní SQL API automatických technikách indexování, jakož i dialekt Azure Cosmos DB SQL. Dotazovací jazyk SQL se skládá ze tří hlavních aspekty:   

1. Malá sada operace dotazů, které mapují přirozeně do stromové struktury, včetně hierarchické dotazy a projekce. 
2. Podmnožinu relační operacím, včetně složení, filtr, projekce, agregace a vlastní spojení. 
3. Čistý JavaScript na základě UDF, které pracují s (1) a (2).  

Model Azure Cosmos DB dotazu se pokusí vytvořit rovnováhu mezi funkce, jednoduchost a efektivitu. Databázový stroj Azure Cosmos DB nativně kompilovaný a provede příkazy dotaz SQL. Můžete dát dotaz na kolekce pomocí [rozhraní REST API](/rest/api/cosmos-db/) nebo [klientskou sadu SDK](sql-api-sdk-dotnet.md). .NET SDK se dodává s LINQ zprostředkovatele.

> [!TIP]
> Můžete vyzkoušet rozhraní SQL API a spouštění dotazů SQL na našem datovou sadu v [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transakcí několika dokumentů
Databázové transakce zadejte bezpečné a předvídatelné programovací model pro práci s souběžných změny v datech. V RDBMS, je tradičním způsobem, jakým zapisovat obchodní logiku napsat **uložené procedury** nebo **aktivační události** a odešlete databázový server pro spouštění transakcí. V RDBMS programátorů aplikace je potřeba řešit dva různorodých programovací jazyky: 

* Aplikace (netransakční) programovací jazyk (například JavaScript, Python, C#, Java, atd.)
* T-SQL, transakcí programovací jazyk, který je nativně provedený databáze

Na základě jeho hloubkové závazků a JSON JavaScript přímo v rámci databázový stroj, Azure Cosmos DB poskytuje intuitivní programovací model pro provádění JavaScript na základě logiky aplikace přímo na kolekcích z hlediska uložené procedury a aktivační události. To umožňuje, aby obě z následujících akcí:

* Efektivní provádění souběžnosti řídit, obnovení, automatické indexování grafy objekt JSON přímo v databázovém stroji
* Přirozeně vyjadřující tok řízení, proměnné rozsahu, přiřazení a integrace primitiv s databázové transakce přímo z hlediska programovací jazyk JavaScript zpracování výjimek

Logiky Javascriptové registrované na úrovni kolekce potom můžete vydat databázových operací s dokumenty dané kolekce. Azure Cosmos DB implicitně zabalí založené na jazyce JavaScript uložené procedury a triggery v rámci vedlejším transakce ACID s izolací snímku na dokumentech v rámci kolekce. Pokud během spouštění JavaScript vyvolá výjimku, je celá transakce zrušena. Výsledný programovací model je velmi jednoduchý ještě výkonné. Vývojáři JavaScript získat "trvanlivý" programovací model při stále pomocí svého známé jazykové konstrukty a knihovna primitiv.   

Možnost spuštění JavaScript přímo v rámci databázového stroje v adresním prostoru stejné jako fondu vyrovnávací paměti umožňuje původce a transakční provádění databázové operace proti dokumentům v kolekci. Kromě toho Cosmos DB databázový stroj usnadňuje hloubkové závazek do formátu JSON a JavaScript eliminuje jakákoli neshoda odpor mezi systémy typu aplikace a databáze.   

Po vytvoření kolekce, můžete zaregistrovat uložené procedury, triggery a UDF pomocí kolekce [rozhraní REST API](/rest/api/cosmos-db/) nebo [klientskou sadu SDK](sql-api-sdk-dotnet.md). Po registraci můžete odkazovat a jejich provedení. Vezměte v úvahu následující uložené procedury vytvořené zcela v JavaScriptu, následující kód má dva argumenty (název adresáře a jméno autora) a vytvoří nový dokument, dotazy pro dokument a pak ho – vše v rámci implicitní transakci ACID aktualizuje. Kdykoli během provádění Pokud je vyvolána výjimka JavaScript, celá transakce zruší.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Klient se dají "dodávat" výše logiky JavaScript pro databázi spouštění transakcí přes HTTP POST. Další informace o použití metod HTTP najdete v tématu [RESTful interakce s prostředky Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Všimněte si, že vzhledem k tomu, že databáze nativně funguje s technologií JSON a JavaScript, neexistuje žádný systém Neshoda typu, "OR mapování" nebo magic generování kódu vyžaduje.   

Uložené procedury a triggery komunikovat s kolekce a dokumenty v kolekci prostřednictvím dobře definovaný objekt modelu, který zveřejňuje aktuální kontext kolekce.  

Kolekce v rozhraní SQL API lze vytvořit, odstraní, čtení nebo výčtové snadno buď pomocí [rozhraní REST API](/rest/api/cosmos-db/) ani v žádné z [klientskou sadu SDK](sql-api-sdk-dotnet.md). Rozhraní SQL API vždy poskytuje silnou konzistenci pro čtení nebo dotazování metadata kolekce. Odstranění kolekce automaticky zajistí, že nemůžete použít žádnou dokumenty, přílohy, uložené procedury, triggery a jsou v něm obsažena UDF.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Uložené procedury, triggery a uživatel definované funkce (UDF)
Jak je popsáno v předchozí části, můžete napsat aplikační logiku a spustit přímo v rámci transakce uvnitř databázového stroje. Aplikace logiky můžete vytvořené zcela v JavaScriptu a můžete modelován jako uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI. Kód jazyka JavaScript v rámci uložené procedury nebo aktivační událost lze vložit, nahradí, odstranit, číst nebo dotazování dokumentů v rámci kolekce. Na druhé straně JavaScript v rámci uživatelem definovanou FUNKCI nelze vložit, nahradí nebo odstranit dokumenty. Funkce UDF výčet dokumenty sadu výsledků dotazu a vytvořit jinou sadu výsledků. Víceklientský Azure Cosmos DB vynucuje přísné založené na vyhrazené prostředků řízení. Každý uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI získá pevné quantum operačního systému prostředků ke své práci. Kromě toho uložené procedury, aktivační události nebo UDF nemůže propojit s externí knihovny jazyka JavaScript a jsou zakázány, pokud se překročí rozpočty prostředků přidělené k nim. Můžete zaregistrovat, zrušení registrace uložené procedury, aktivační události nebo UDF v kolekci pomocí rozhraní REST API.  Po registraci se uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI předem zkompilovat a uložené jako bajtové kód, který se provede později. Následující ssection illustrateshow Azure Cosmos DB JavaScript SDK můžete použít k registraci, spouštění a zrušit registraci uložené procedury, aktivační události a UDF. JavaScript SDK je jednoduché obálku nad [rozhraní REST API](/rest/api/cosmos-db/). 

### <a name="registering-a-stored-procedure"></a>Registrace uložené procedury
Registrace uložená procedura vytvoří nový prostředek uložené procedury na kolekci přes HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Provedení uložené procedury
Provádění uložené procedury je potřeba vydání předáním parametrů na postup v textu požadavku HTTP POST na existující prostředek uložené procedury.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Zrušení registrace uložené procedury
Zrušení registrace uložené procedury je jednoduše potřeba vydání HTTP DELETE se proti existující prostředek uložené procedury.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrace předběžné aktivační událost
Registrace aktivační událost se provádí tak, že vytvoříte nový prostředek aktivační události u kolekce přes HTTP POST. Můžete zadat, pokud je aktivační událost po předem nebo aktivační událost post a typ operace může být přidružen (například vytvořit, Replace, Delete nebo všechny).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Provádění aktivační události starší než
Provádění aktivační události je potřeba zadat název existující aktivační události v době vydání požadavku POST, PUT nebo odstranění prostředku dokumentu prostřednictvím hlavičky žádosti.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Zrušení registrace předběžné aktivační událost
Zrušení registrace aktivační událost se jednoduše provádí prostřednictvím vydání HTTP DELETE se proti existující prostředek aktivační události.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrace UDF
Registrace UDF se provádí tak, že vytvoříte nový prostředek UDF na kolekci přes HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Provádění UDF jako součást dotazu
Uživatelem definovanou FUNKCI lze zadat jako součást dotazu SQL a slouží jako způsob, jak rozšířit základní [dotazovací jazyk SQL](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Zrušení registrace UDF
Zrušení registrace UDF jednoduše provádí vydáním HTTP DELETE se proti existující prostředek UDF.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

I když výše uvedené fragmenty kódu vám ukázal registrace (POST), zrušení registrace (PUT), pro čtení nebo jejich výpisu (GET) a provádění (POST) prostřednictvím [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), můžete použít také [rozhraní REST API](/rest/api/cosmos-db/) nebo jiných [klientskou sadu SDK](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumenty
Můžete vložit, nahradí, odstranit, číst, výčet a dotaz na libovolné dokumenty JSON v kolekci. Azure Cosmos DB nenutí žádné schéma a nevyžaduje sekundární indexy za účelem podpory dotazování s dokumenty v kolekci. Maximální velikost pro dokument je 2 MB.   

Probíhá skutečně otevřenou databázi služby, Azure Cosmos DB není skladová žádné speciální datové typy (například datum a čas) nebo konkrétní kódování pro dokumenty JSON. Azure Cosmos DB nevyžaduje žádné speciální konvence JSON do kodifikovat vztahy mezi různé dokumenty; syntaxe SQL Azure Cosmos DB poskytuje velmi výkonný hierarchické a relační dotazu, operátory dotazu a projekt dokumenty bez jakékoli speciální poznámky nebo potřeba kodifikovat vztahy mezi dokumenty pomocí rozlišující vlastnosti.  

Jak se všemi ostatními prostředky dokumentů mohou být vytvořeny, nahrazen, odstranit, číst, výčet a dotazovat snadno pomocí rozhraní REST API nebo některou z [klientskou sadu SDK](sql-api-sdk-dotnet.md). Odstranění dokumentu okamžitě uvolní kvótu odpovídající všechny vnořené přílohy. Úroveň konzistenci čtení dokumentů odpovídá zásad konzistence na databázového účtu. Tuto zásadu lze přepsat na základě požadavků v závislosti na požadavcích konzistence dat vaší aplikace. Při dotazování dokumentů, následuje konzistenci čtení indexování režim nastavený na kolekci. Pro "konzistentní" což odpovídá zásad konzistence účtu. 

## <a name="attachments-and-media"></a>Přílohy a média
Azure Cosmos DB umožňuje ukládání binární objekty BLOB nebo médium buď pomocí Azure DB Cosmos (maximum 2 GB každý účet) nebo do vlastního médium vzdáleného úložiště. Také umožňuje představuje metadata médií z hlediska speciální dokument s názvem přílohy. Přílohy v databázi Cosmos Azure je speciální dokument (JSON), který odkazuje média nebo objekt blob uložená na jiném místě. Příloha je jednoduše speciální dokument, který zachycuje metadata médií uložených v médium vzdáleného úložiště (například umístění, Autor atd.). 

Zvažte sociálních čtení aplikace, která používá Azure Cosmos DB k uložení neobsahovaly a metadata včetně komentář, označuje záložky, hodnocení, líbí nebo nelíbí atd přidružené pro zde elektronickou knihu daného uživatele.   

* Obsah knihy samotné je uložen v úložišti média buď jako součást účet Azure Cosmos DB databáze nebo médium vzdáleného úložiště k dispozici. 
* Aplikace může ukládat metadata každého uživatele jako odlišné dokument – například Michalův metadata pro Sešit1 se ukládají v dokumentu odkazuje /colls/joe/docs/book1. 
* Přílohy odkazující na obsah, který stránky daného adresáře uživatele, jsou uloženy v odpovídající dokumentu například, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 atd. 

Příklady uvedené výše použijte popisný ID k vyjádření hierarchie prostředků. Jsou přístup k prostředkům prostřednictvím rozhraní REST API prostřednictvím ID prostředků jedinečné. 

Pro média, která spravuje databázi Cosmos Azure vlastnost _media přílohu odkazuje médium pomocí jeho identifikátoru URI. Azure Cosmos DB zajistí do paměti shromažďování médium, když všechny odkazy na zbývající vyřadit. Azure Cosmos DB automaticky generuje přílohu při nahrávání nové médium a naplní _media tak, aby odkazovaly na nově přidané médium. Pokud se rozhodnete ukládat média v úložišti objektů blob vzdálené spravované vámi (například OneDrive, Azure Storage, atd. DropBox), stále můžete přílohy tak, aby odkazovaly média. V tomto případě vytvoříte přílohu sami a naplnit její _media vlastnost.   

Jako se všemi ostatními prostředky, můžete vytvořit přílohy, nahradit, odstranit, čtení nebo vytvořit její výčet snadno pomocí rozhraní REST API nebo některou z klienta sady SDK. Stejně jako u dokumenty, odpovídá konzistenci čtení úroveň přílohy zásad konzistence na databázového účtu. Tuto zásadu lze přepsat na základě požadavků v závislosti na požadavcích konzistence dat vaší aplikace. Při dotazování pro přílohy, následuje konzistenci čtení indexování režim nastavený na kolekci. Pro "konzistentní" což odpovídá zásad konzistence účtu. 
 

## <a name="users"></a>Uživatelé
Uživatel s Azure Cosmos DB představuje logické obor názvů pro seskupování oprávnění. Uživatel s Azure Cosmos DB nemusí odpovídat uživatele v systému správy protokolu identity nebo předdefinované aplikační role. Pro Azure DB Cosmos uživatel jednoduše představuje abstrakci k seskupení sady oprávnění v databázi.   

Pro implementaci víceklientský ve vaší aplikaci, můžete vytvořit uživatele v Azure DB Cosmos, který odpovídá skutečných uživatelů nebo klienti vaší aplikace. Potom můžete vytvořit oprávnění pro daného uživatele, které odpovídají řízení přístupu prostřednictvím různých kolekcí, dokumentů, přílohy, atd.   

Jak vaše aplikace potřebují ke škálování se vaše uživatele růst, můžete přijmout různé způsoby, jak horizontálního oddílu vaše data. Můžete model každému uživateli následujícím způsobem:   

* Každý uživatel se mapuje na databázi.
* Každý uživatel se mapuje na kolekci. 
* Dokumenty odpovídající více uživatelů přejděte na vyhrazené kolekce. 
* Dokumenty odpovídající více uživatelů přejděte na sadu kolekcí.   

Bez ohledu na konkrétní horizontálního dělení strategie si zvolíte, můžete model skutečné uživatelům jako uživatele v Azure Cosmos DB databázi a přidružit podrobné oprávnění pro každého uživatele.  

![Kolekce uživatelů][3]  
**Strategie horizontálního dělení a uživatelé modelování**

Podobně jako všechny ostatní prostředky uživatele v Azure Cosmos DB lze vytvořit, nahradit, odstranit, číst nebo uvedené snadno pomocí rozhraní REST API nebo některou z klientské sady SDK. Azure Cosmos DB vždy poskytuje silnou konzistenci pro čtení nebo dotazování metadata prostředek uživatele. Je vhodné odkazující odstranění uživatele automaticky zajistí, že nemůžete použít žádnou z oprávnění jsou v něm obsažena. I když Azure Cosmos DB získá kvótu oprávnění v rámci Odstraněný uživatel na pozadí, odstraněné oprávnění opět k dispozici okamžitě budete muset používat.  

## <a name="permissions"></a>Oprávnění
Z hlediska řízení k přístupu, jsou považovány za prostředkům, například účty databáze, databáze, uživatelů a oprávnění *správu* prostředky, protože vyžadují oprávnění správce. Na druhé straně prostředků, včetně kolekcí, dokumentů, přílohy, uložené procedury, triggery, a UDF jsou obor v části na danou databázi a považovány za *prostředky aplikace*. Odpovídající dva typy prostředků a rolí, které přistupovat k nim (konkrétně správce a uživatele), modelu autorizace definuje dva typy *přístupové klíče*: *hlavní klíč* a  *klíč prostředku*. Hlavní klíč je součástí databázového účtu a je k dispozici pro vývojáře (nebo správce) kdo je zřizování databázového účtu. Tento hlavní klíč musí správce sémantiku v tom, že může sloužit k autorizaci přístupu k prostředkům pro správu a aplikace. Klíč prostředku spočívá v tom, podrobné přístupový klíč, který umožňuje přístup k *konkrétní* prostředků aplikace. Proto zaznamená vztah mezi uživatelem databáze a oprávnění, která má uživatel pro konkrétní zdroje (například kolekce, dokument, přílohy, uložené procedury, aktivační události nebo UDF).   

Jediný způsob, jak získat klíč prostředku je vytvoření prostředku oprávnění v rámci daného uživatele. Všimněte si, že abyste mohli vytvořit nebo načíst oprávnění, hlavní klíč musí být uvedené v hlavičce autorizace. Prostředek oprávnění sváže prostředek, přístup a uživatele. Po vytvoření oprávnění prostředků, uživatel se musí předložit klíč přidružený prostředku s cílem získat přístup k prostředku relevantní. Klíč prostředku proto lze zobrazit jako logické a compact reprezentace prostředku oprávnění.  

Jako se všemi ostatními prostředky, můžete vytvořit oprávnění v Azure Cosmos DB, nahradit, odstranit, čtení nebo ve výčtu snadno pomocí rozhraní REST API nebo některou z klienta sady SDK. Azure Cosmos DB vždy poskytuje silnou konzistenci pro čtení nebo dotazování metadata oprávnění. 

## <a name="next-steps"></a>Další postup
Další informace o práci s prostředky pomocí příkazů HTTP v [RESTful interakce s prostředky Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

