---
title: Azure Cosmos DB – model prostředků a koncepty | Dokumentace Microsoftu
description: Přečtěte si o Azure Cosmos DB hierarchický model databází, kolekcí, uživatelem definované funkce (UDF), dokumenty, oprávnění ke správě prostředků a další.
keywords: Hierarchický model, služby cosmos DB, azure, Microsoft azure
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0869881ace689d12272affb38d3689965e107e8f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050928"
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Hierarchický model prostředků a základní koncepty databáze Azure Cosmos

Entity databáze, které spravuje Azure Cosmos DB se označují jako **prostředky**. Každý prostředek je jednoznačně identifikují pomocí logického identifikátoru URI. Můžete pracovat s prostředky pomocí standardní příkazy HTTP, hlaviček požadavků a odpovědí a stavové kódy. 

Tento článek obsahuje odpovědi na následující otázky:

* Jaký je model prostředků služby Azure Cosmos DB?
* Co jsou systému definovány prostředky na rozdíl od uživatelsky definovaných prostředků?
* Jak můžu řešit prostředek?
* Jak můžu pracovat s kolekcí?
* Jak fungují s uložených procedur, triggerů a uživatelsky definovaných funkcí (UDF)

## <a name="hierarchical-resource-model"></a>Hierarchický model prostředků
Jak ukazuje následující diagram, Azure Cosmos DB hierarchické **model prostředků** se skládá ze sady prostředků v rámci účtu databáze, každý adresovatelný prostřednictvím logické a stabilní identifikátor URI. Sada prostředků se označují jako **kanálu** v tomto článku. 

> [!NOTE]
> Azure Cosmos DB nabízí vysoce účinného protokol TCP, který je také RESTful v jeho komunikaci modelu, k dispozici prostřednictvím [SQL .NET API klienta](sql-api-sdk-dotnet.md).
> 
> 

![Azure Cosmos DB hierarchický model prostředků][1]  
**Hierarchický model prostředků**   

Pokud chcete začít pracovat s prostředky, je nutné [vytvoření databázového účtu](create-sql-api-dotnet.md) pomocí svého předplatného Azure. Databázový účet se může skládat ze sady **databází**, každá obsahuje několik **kolekce**každá zase obsahovat **uložené procedury, aktivační události, funkce UDF, dokumenty a související přílohy**. Databázi jsou také přiřazeni **uživatelé**, každý s sadu **oprávnění** pro přístup k kolekcí, uložených procedur, aktivačních událostí, funkce UDF, dokumenty nebo přílohy. Databáze, uživatelé, oprávnění a kolekce jsou systémem definované prostředky s dobře známými schématy, dokumenty a přílohy obsahují libovolný, uživatelem definovaný obsah JSON.  

| Prostředek | Popis |
| --- | --- |
| Databázový účet |Databázový účet souvisí s počtem databází a pevné velikosti úložiště objektů blob pro přílohy. Můžete vytvořit jeden nebo více účtů databáze pomocí svého předplatného Azure. Další informace najdete [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Databáze |Databáze je logický kontejner úložiště dokumentů rozděleného mezi kolekcemi. Je také kontejner uživatelů. |
| Uživatel |Logické obor názvů pro nastavení rozsahu oprávnění. |
| Oprávnění |Autorizační token přidružit k uživateli pro přístup ke konkrétnímu prostředku. |
| Kolekce |Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace. Kolekce mohou pokrývat jeden nebo více oddílů nebo serverů a lze je škálovat tak, aby zvládaly prakticky neomezené objemy úložišť a propustnosti. |
| Uložená procedura |Logika aplikace napsané v jazyce JavaScript, který je registrován s kolekcí a transakčně spouštět přímo z databázového stroje. |
| Trigger |Logika aplikace napsané v jazyce JavaScript spustit před nebo po buď vložení, nahraďte nebo operace odstranění. |
| UDF |Logika aplikace napsané v jazyce JavaScript. UDF umožňují modelovat vlastní operátor a tím rozšiřovat základní rozhraní SQL API dotazovací jazyk. |
| Dokument |Uživatelem definovaný (libovolný) obsah JSON. Ve výchozím nastavení musí se definovat žádné schéma ani se sekundárními indexy musí být zadané pro všechny dokumenty přidána do kolekce. |
| Příloha |Přílohu, která je speciální dokument obsahující odkazy a související metadata pro externí objekt blob/media. Vývojář můžete mít objekt blob spravované službou Cosmos DB nebo úložiště pomocí poskytovatele služeb externích objektů blob, jako je OneDrive, Dropbox atd. |

## <a name="system-vs-user-defined-resources"></a>Systém a uživatelsky definovaných prostředků
Prostředky, jako jsou účty databází, databází, kolekcí, uživatelé, oprávnění, uložené procedury, aktivační události a UDF – mít všechny pevné schéma a jsou volány systémových prostředků. Prostředky, jako jsou dokumenty a přílohy naproti tomu máte bez omezení na schéma a jsou příkladem uživatelsky definovaných prostředků. Ve službě Cosmos DB jsou systém a uživatelsky definovaných prostředků reprezentované a spravovat jako vyhovující standardu JSON. Všechny prostředky, systému nebo uživatelem definované, mají společné následující vlastnosti:

> [!NOTE]
> Všechny vlastnosti vygenerované systémem v prostředku začínají podtržítkem (_) v jejich reprezentaci JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Vlastnost</strong></p></td>
            <td valign="top"><p><strong>Nastavitelná při čekání uživatele nebo generována?</strong></p></td>
            <td valign="top"><p><strong>Účel</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Systémem vygenerované, jedinečné a hierarchické identifikátor prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>etag prostředku vyžadovaného pro optimistického řízení souběžnosti</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Časové razítko poslední aktualizace prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Vygenerované systémem</p></td>
            <td valign="top"><p>Jedinečný identifikátor URI adresovatelný prostředku</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Buď</p></td>
            <td valign="top"><p>Uživatelem definované jedinečný název prostředku (stejnou hodnotu klíče oddílu). Pokud uživatel nezadá id, id je generována</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Síťové vyjádření prostředků
Cosmos DB nenutí všechny vlastní rozšíření JSON standardní nebo speciální kódování; funguje to standardní kompatibilní dokumentů JSON.  

### <a name="addressing-a-resource"></a>Adresování prostředku
Všechny prostředky jsou adresovatelné identifikátoru URI. Hodnota **_self** vlastnost prostředku, který představuje relativní identifikátor URI prostředku. Formát identifikátoru URI se skládá z /\<kanálu\>/ segmenty cesty {_rid}:  

| Hodnota _self | Popis |
| --- | --- |
| /DBS |Informační kanál databází v rámci účtu databáze |
| /DBS/ {%{dbname/} |Databáze s id odpovídající hodnotě {%{dbname/} |
| /colls/ /DBS/ {%{dbname/} |Informační kanál kolekcí v databázi |
| /colls/ /DBS/ {%{dbname/} {collName} |Kolekce s id odpovídající hodnotě {collName} |
| /colls/ /DBS/ {%{dbname/} {collName} / docs |Informační kanál dokumentů v kolekci |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Dokument s id odpovídající hodnotě {doc} |
| složku /users/ /DBS/ {%{dbname/} |Kanál, které uživatelům a v databázi |
| /dbs/{dbName}/users/{userId} |Uživatel s id odpovídající hodnotě {user} |
| složku /users/ /DBS/ {%{dbname/} {userId} / oprávnění |Informační kanál o oprávnění podle uživatele |
| složku /users/ {userId} /permissions/ /DBS/ {%{dbname/} {permissionId} |Oprávnění s id odpovídající hodnotě {oprávnění} |

Každý prostředek má jedinečný název definovaný uživatelem zveřejněné přes vlastnost id. Poznámka: pro dokumenty, pokud uživatel nezadá id, sady SDK automaticky generovat jedinečný identifikátor pro dokument. Id je uživatelem definovaný řetězec, až 256 znaků, které je jedinečný v rámci konkrétní nadřazený prostředek. 

Každý prostředek má také systémem generovaný identifikátor hierarchické prostředku (také označované jako identifikátorů RID), která je dostupná prostřednictvím vlastnosti _rid. Identifikátor RID kóduje celou hierarchii daný prostředek a je vhodné vnitřní reprezentaci používá k vynucení referenční integrity distribuovaných způsobem. Identifikátor RID je jedinečný v rámci účtu databáze a Cosmos DB je používán interně pro efektivní směrování, bez nutnosti vyhledávání napříč oddílu. Hodnoty vlastností _rid a _self jsou alternativní a canonical reprezentace prostředku. 

Rozhraní REST API podporují prostředků adresování a směrování žádostí podle id a _rid vlastnosti.

## <a name="database-accounts"></a>Účty databáze
Můžete zřídit jednu nebo více účty Cosmos DB databáze pomocí svého předplatného Azure.

Můžete vytvářet a spravovat účty databáze Cosmos DB pomocí webu Azure portal na [ http://portal.azure.com/ ](https://portal.azure.com/). Vytváření a správa databázový účet vyžaduje oprávnění správce a můžete provést pouze v rámci vašeho předplatného Azure. 

### <a name="database-account-properties"></a>Vlastnosti účtu databáze
Jako součást zřizovat a spravovat účet databáze můžete nakonfigurovat a přečtěte si následující vlastnosti:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Název vlastnosti</strong></p></td>
            <td valign="top"><p><strong>Popis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Zásady konzistence</p></td>
            <td valign="top"><p>Nastavte tuto vlastnost nakonfigurovat výchozí úroveň konzistence pro všechny kolekce v rámci vašeho účtu databáze. Můžete přepsat úrovně konzistence na základě požadavku na pomocí hlavičky žádosti [x-ms--úrovně konzistence]. <p><p>Tato vlastnost se vztahuje pouze na <i>uživatelsky definovaných prostředků</i>. Všechny systémové prostředky definované jsou konfigurovány pro podporu čtení nebo dotazů se silnou konzistenci.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Autorizace klíče</p></td>
            <td valign="top"><p>Primární a sekundární hlavní větev a jen pro čtení klíče, které poskytují přístup pro správu na všechny prostředky v rámci účtu databáze.</p></td>
        </tr>
    </tbody>
</table>

Kromě zřízení, konfigurace a správa vašeho účtu databáze na webu Azure Portal, můžete také programově vytvořit a spravovat účty databáze Cosmos DB s použitím [REST API služby Azure Cosmos DB](/rest/api/cosmos-db/) a také [klientské sady SDK](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Databáze
Databáze Cosmos DB je logický kontejner jedné nebo více kolekcí a uživatelů, jak je znázorněno v následujícím diagramu. Můžete vytvořit libovolný počet databází v rámci účtu databáze Cosmos DB v souladu s limity nabídky.  

![Databázový účet a kolekce hierarchický model][2]  
**Databáze je logický kontejner, uživatelů a kolekce**

Databáze může obsahovat neomezené úložiště dokumentů rozdělit na oddíly v rámci kolekce.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastické škálování databáze Azure Cosmos DB
Je ve výchozím nastavení – od několika GB až po petabajty úložiště dokumentů se zálohováním SSD a zřízené propustnosti elastické databáze Cosmos DB. 

Na rozdíl od databáze v tradiční relační databázový systém není v oboru databáze ve službě Cosmos DB na jednom počítači. Pomocí služby Cosmos DB podle potřeb škálování vaší aplikace k růstu vytvoříte více kolekcí a databází. Ve skutečnosti různých první strany aplikací v rámci Microsoftu jste dosud používali Azure Cosmos DB ve velkém měřítku příjemce vytvořením extrémně velké databáze Azure Cosmos DB každý obsahující tisíce kolekcí s terabajtů úložiště dokumentů. Může zvětšovat nebo zmenšovat databáze přidáním nebo odebráním kolekce pro splnění požadavků škálování vaší aplikace. 

Můžete vytvořit libovolný počet kolekcí v databázi v souladu s nabídky. Každá kolekce, nebo sadu kolekce (v databázi), má disky SSD úložiště a propustnosti zřízené pro vás v závislosti na vybrané nabídky.

Databáze Azure Cosmos DB je také kontejner uživatelů. Uživatel, naopak, je logický obor názvů pro sadu oprávnění, která poskytuje jemně autorizace a přístupu do kolekcí, dokumentů a příloh.  

Jak se ostatními prostředky v modelu prostředků Azure Cosmos DB, databáze se dají vytvářet, nahradí, odstranit, číst, nebo Výčtový snadno buď pomocí [rozhraní REST API](/rest/api/cosmos-db/) nebo některý z [klientské sady SDK](sql-api-sdk-dotnet.md). Azure Cosmos DB zaručuje silnou konzistenci pro čtení nebo dotazování metadat databáze prostředků. Odstranění databáze automaticky zajistí, že nemůžete použít žádnou z kolekce nebo uživatelé jsou v něm obsaženy.   

## <a name="collections"></a>Kolekce
Kolekce Cosmos DB je kontejner dokumentů JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastické využívající disky SSD úložiště dokumentů
Kolekce je vnitřně elastické – automaticky roste a zmenšuje při přidání nebo odebrání dokumentů. Kolekce jsou logické prostředky, které můžou využívat jeden nebo více fyzických oddílů nebo serverů. Počet oddílů přiřazena ke kolekci je určena na základě velikosti úložiště Cosmos DB a zajištěné propustnosti pro kolekce nebo sady kolekcí. Každý oddíl v databázi Cosmos DB má pevnou velikost úložiště zálohují na disky SSD s ním spojená a se replikují pro zajištění vysoké dostupnosti. Oddíl správy je plně spravovaná služba Azure Cosmos DB a není potřeba psát složitý kód nebo spravovat oddíly. Kolekce cosmos DB jsou **neomezené** z hlediska úložiště a propustnosti. 

### <a name="automatic-indexing-of-collections"></a>Automatické indexování kolekce
Azure Cosmos DB je systém true databáze bez schémat. Se nepředpokládá ani vyžadují žádné schéma dokumentů JSON. Přidejte do kolekce dokumentů, Azure Cosmos DB automaticky indexuje a jsou k dispozici pro vás k dotazování. Automatické indexování dokumentů, bez nutnosti schématu nebo sekundárních indexů je klíčová dovednost, pomocí služby Azure Cosmos DB a zajišťuje optimalizovaný pro zápis, bez zámku a strukturované protokolu index údržby techniky. Azure Cosmos DB podporuje trvalý objem mimořádně rychlému zápisu a stále současné obsluhování konzistentních dotazů. Úložiště dokumentů a index se používá k výpočtu úložiště spotřebovávané každé z kolekcí. Můžete řídit úložiště a výkon kompromisy spojené s tím, že nakonfigurujete zásady indexování kolekce indexování. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurace zásady indexování kolekce
Zásady indexování každé kolekce umožňuje zajistit výkon a úložiště kompromisy spojené s indexování. Následující možnosti jsou k dispozici jako součást konfiguraci indexování:  

* Zvolte, zda kolekce automaticky indexuje všechny dokumenty nebo ne. Ve výchozím nastavení jsou všechny dokumenty automaticky indexován. Můžete vypnout automatické indexování a selektivně přidejte pouze konkrétní dokumentů do indexu. Naopak můžete si zvolit vyloučit pouze konkrétní dokumenty. Můžete dosáhnout nastavením automatickou vlastnost, která má být true nebo false na indexingPolicy kolekce a pomocí hlavičky žádosti [x-ms-indexingdirective] při vkládání, nahrazení nebo odstranění dokumentu.  
* Zvolte, jestli se má zahrnout nebo vyloučit konkrétní cesty nebo vzory v dokumenty z indexu. Můžete dosáhnout tím includedPaths nastavení a excludedPaths na indexingPolicy kolekce v uvedeném pořadí. Můžete také nakonfigurovat úložiště a výkon kompromisy za dotazy na rozsah a hodnotu hash pro konkrétní cesty vzory. 
* Výběr mezi synchronní (stejně jako) a aktualizace asynchronní (opožděné) indexu. Ve výchozím nastavení index se při každém vložení, nahrazení nebo odstranění dokumentu ke kolekci aktualizuje synchronně. To umožňuje dotazům případném dalším sdílení dodržovat stejné úrovně konzistence, jako čtení dokumentu. Azure Cosmos DB je optimalizovaná zápisu, která podporuje trvalý objem zapíše dokument synchronní index údržby a poskytování konzistentního dotazů, můžete nakonfigurovat určité kolekce k aktualizaci jejich rejstřík laxně. Opožděné indexování zvyšuje rychlost zápisu dále a je ideální pro scénáře hromadné ingestování hlavně kolekcí náročná na výkon pro čtení.

Zásady indexování lze změnit pomocí provádí PUT v kolekci. To může být buď prostřednictvím dosáhnout [Klientská sada SDK](sql-api-sdk-dotnet.md), [webu Azure portal](https://portal.azure.com), nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="querying-a-collection"></a>Dotazování na kolekci
Dokumenty v kolekci může mít libovolný schémata a můžete dát dotaz na dokumenty v kolekci bez zadání jakéhokoli schématu nebo sekundárních indexů předem. Můžete zadávat dotazy pomocí kolekce [Reference k syntaxi SQL služby Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn782250.aspx), která nabízí bohaté hierarchické, relačních a prostorových operátory a rozšiřitelnost prostřednictvím uživatelem definovanými funkcemi založené na jazyce JavaScript. Gramatika JSON umožňuje modelování dokumentů JSON jako stromové struktury s popisky jako uzly stromu. To je zneužít, jak pomocí rozhraní SQL API automatických technikách indexování, jakož i dialekt SQL služby Azure Cosmos DB. Dotazovací jazyk SQL se skládá ze tří hlavních aspekty:   

1. Malou sadu operace dotazů, které se mapují přirozeně do stromové struktury, včetně hierarchické dotazy a projekce. 
2. Podmnožinu relační operací včetně složení, filter, projekce, agregace a vlastní spojení. 
3. Uživatelem definovanými funkcemi, které pracují s (1) a (2) založené na jazyce JavaScript čistě.  

Model dotazování služby Azure Cosmos DB se pokusí hledají rovnováhu mezi funkcí, efektivitu a jednoduchost. Databázový stroj služby Azure Cosmos DB nativně zkompiluje a spustí příkazy dotazu SQL. Můžete dát dotaz na kolekce používat [rozhraní REST API](/rest/api/cosmos-db/) nebo některý z [klientské sady SDK](sql-api-sdk-dotnet.md). Sady .NET SDK obsahuje poskytovatele jazyka LINQ.

> [!TIP]
> Můžete vyzkoušet rozhraní SQL API a spouštět dotazy SQL proti naší datové sadě v [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transakce s několika dokumenty
Databázové transakce poskytují bezpečné a předvídatelné programovací model pro práci s souběžných změny v datech. V relační databázový systém, je napsat tradiční způsob, jak psát obchodní logiky **uložené procedury** a/nebo **triggery** a odešle ho do databázového serveru pro spouštění transakcí. V relační databázový systém a programátor aplikace je potřeba řešit dvou různých programovacích jazycích: 

* (Netransakční) aplikačního programovacího jazyka (například JavaScript, Python, C#, Java atd.)
* T-SQL, transakční programovací jazyk, který je nativně proveden v databázi

Tím, že jeho oddáni až po JavaScript a JSON přímo uvnitř databázového stroje, služby Azure Cosmos DB poskytuje výsledkem je intuitivní programovací model pro provádění jazyka JavaScript založené na aplikační logiku přímo na kolekce z hlediska uložených procedur a aktivační události. Díky tomu pro obě z následujících akcí:

* Efektivní provádění souběžnosti řídit, obnovení, automatické indexování grafů objektů JSON přímo v databázovém stroji
* Přirozeně vyjádření tok řízení, proměnné rozsahu, přiřazení a integrace primitivních elementů s transakcemi databáze přímo z hlediska programovací jazyk JavaScript zpracování výjimek

Logiky JavaScript zaregistrované na úrovni kolekce můžete pak provádět databázové operace s dokumenty ze zadané kolekce. Azure Cosmos DB implicitně zabalí založené na jazyce JavaScript uložených procedur a aktivačních událostí v rámci ambientní transakci ACID s izolací snímku mezi dokumenty v kolekci. Pokud během spouštění JavaScript vyvolá výjimku, je celá transakce zrušena. Výsledný programovací model je jednoduchý výkonné. Při stále použití jejich konstrukce dobře známého jazyka a knihovny primitivy vývojáře v JavaScriptu se zobrazí "trvalý" programovací model.   

Spouštění jazyka JavaScript přímo uvnitř databázového stroje ve stejném adresním prostoru jako fond vyrovnávacích pamětí umožňuje výkonné a transakční provádění databázové operace proti dokumentům v kolekci. Kromě toho databázový stroj Cosmos DB umožňuje oddáni ve formátu JSON a JavaScript eliminuje jakákoli neshoda vzniklé vzájemné napětí mezi systémy typ aplikace a databáze.   

Po vytvoření kolekce, můžete zaregistrovat uložené procedury, triggery a uživatelem definovanými funkcemi pomocí kolekce [rozhraní REST API](/rest/api/cosmos-db/) ani [klientské sady SDK](sql-api-sdk-dotnet.md). Po registraci můžete odkazovat a spouštět je. Vezměte v úvahu následující uložené procedury, které jsou vytvořené zcela v JavaScriptu, následující kód přebírá dva argumenty (název knihy a jméno autora) a vytvoří nový dokument, dotazuje na dokument a pak aktualizuje ji – vše v rámci implicitní transakci ACID. Kdykoli během provádění Pokud je vyvolána výjimka jazyka JavaScript, celý přerušení transakce.

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

Klient "zaslat" výše logiky JavaScript do databáze pro transakční spuštění přes HTTP POST. Další informace o použití metody HTTP, naleznete v tématu [RESTful interakce s prostředky Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

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


Všimněte si, že vzhledem k tomu, že databáze nativně podporuje JSON a JavaScript, neexistuje žádná neshoda typu systému, "OR mapování" nebo magic generování kódu vyžaduje.   

Uložené procedury a triggery interakci s kolekci a dokumenty v kolekci prostřednictvím dobře definovaných objektového modelu, který zveřejňuje kontextu aktuální kolekce.  

Nejde vytvořit kolekce v rozhraní SQL API, odstraní, čtení nebo výčtu, buď pomocí snadno [rozhraní REST API](/rest/api/cosmos-db/) nebo některý z [klientské sady SDK](sql-api-sdk-dotnet.md). Rozhraní SQL API vždy poskytuje silnou konzistenci pro čtení nebo dotazů na metadata kolekce. Odstranění kolekce automaticky zajistí, že nemůžete použít žádnou z dokumentů, přílohy, uložené procedury, triggery a uživatelem definovanými funkcemi, jsou v něm obsaženy.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Uložené procedury, triggery a uživateli definované funkce (UDF)
Jak je popsáno v předchozí části, můžete psát logiku aplikace spustit přímo v rámci transakce v rámci služby databázového stroje. Aplikace logiky můžete vytvořené zcela v JavaScriptu a můžete modelovat jako uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI. Kód jazyka JavaScript v rámci uložené procedury nebo aktivační událost může vložit, nahraďte, odstranit, číst nebo dotazování dokumentů v kolekci. Na druhé straně jazyka JavaScript v rámci uživatelem definovanou FUNKCI nelze vložit, nahradí nebo odstranit dokumenty. Uživatelem definovanými funkcemi výčet dokumenty sadu výsledků dotazu a vytvořit další sadu výsledků. Pro více tenantů vynucuje Azure Cosmos DB zásady správného řízení striktní prostředků na základě rezervace. Každý uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI získá pevné quantum prostředků operačního systému ke své práci. Kromě toho uložené procedury, triggery nebo UDF nelze propojit s externí knihovny jazyka JavaScript a jsou na seznamu zakázaných adres při překročení prostředků rozpočty přidělené k nim. Můžete zaregistrovat, zrušit registraci uložené procedury, triggery nebo UDF s kolekcí pomocí rozhraní REST API.  Při registraci se uložené procedury, aktivační události nebo uživatelem definovanou FUNKCI předem zkompilovat a uloží jako bajtový kód, který se provede později. Následující části ukazuje, jak můžete používat Azure Cosmos DB JavaScript SDK na vytvářet, spouštět a rušit registraci uložené procedury, aktivační události a uživatelem definovanou FUNKCI. Sada JavaScript SDK je jednoduchá Obálka nad [rozhraní REST API](/rest/api/cosmos-db/). 

### <a name="registering-a-stored-procedure"></a>Registrace uložené procedury
Registrace uložená procedura vytvoří nový prostředek uloženou proceduru v kolekci přes HTTP POST.  

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
Provádění uložené procedury se provádí pomocí metody POST protokolu HTTP pro existující prostředek uloženou proceduru předáním parametry do procedury v textu požadavku.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Zrušení registrace uložené procedury
Zrušení registrace uloženou proceduru se provádí pomocí požadavku HTTP DELETE se proti existující prostředek uloženou proceduru.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrace předběžné aktivační události
Registrace aktivační události se provádí tak, že vytvoříte nový prostředek aktivační události u kolekce přes HTTP POST. Určíte-li aktivační událost je předběžná nebo aktivační událost post a typ operace můžou být spojené s (například vytvořit, Replace, Delete nebo všechny).   

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

### <a name="executing-a-pre-trigger"></a>Provádění aktivační události před
Spuštění aktivační události se provádí tak, že zadáte název aktivační událost existující v době vydání žádost POST, PUT nebo odstranění prostředků dokumentů pomocí hlavičky žádosti.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Zrušení registrace předběžné aktivační události
Zrušení registrace aktivační událost se provádí prostřednictvím vydání požadavku HTTP DELETE se proti existující prostředek aktivační události.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrace UDF
Registrace systém souborů UDF se provádí tak, že vytvoříte nový prostředek systému souborů UDF v kolekci přes HTTP POST.  

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

### <a name="executing-a-udf-as-part-of-the-query"></a>Spuštění UDF jako součást dotazu
Uživatelem definovanou FUNKCI lze zadat jako součást příkazu jazyka SQL a slouží jako způsob, jak rozšířit základní [dotazovací jazyk SQL](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Zrušení registrace UDF
Zrušení registrace systém souborů UDF jednoduše stačí vydání požadavku HTTP DELETE se proti existující prostředek UDF.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

I když fragmenty kódu výše jsme si ukázali, registrace (POST), zrušení registrace (PUT), čtení nebo jejich výpisu (GET) a spuštění (POST) prostřednictvím [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), můžete použít také [rozhraní REST API](/rest/api/cosmos-db/) nebo jiných [klientské sady SDK](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumenty
Můžete vložit, nahraďte, odstranit, číst, výčet a dotazování na libovolné dokumenty JSON v kolekci. Azure Cosmos DB nenutí žádné schéma a nevyžaduje, aby sekundární indexy za účelem podpory dotazování na dokumenty v kolekci. Maximální velikost pro dokument je 2 MB.   

Jsou skutečně otevřené databázová služba Azure Cosmos DB není vytvářet žádné speciální datové typy (například datum a čas) nebo konkrétní kódování pro dokumenty JSON. Azure Cosmos DB nevyžaduje žádné speciální konvence JSON a kodifikovat vztahy mezi různými dokumenty; Syntaxe jazyka SQL ve službě Azure Cosmos DB poskytuje výkonné hierarchické a relační dotazů, operátory pro dokumenty dotazu a projekt bez jakékoli speciální poznámky nebo nutnost kodifikovat vztahy mezi dokumenty pomocí rozlišující vlastnosti.  

Jak se všemi ostatními prostředky dokumentů mohou být vytvořeny, nahradí, odstranit, číst, vytvořit výčet a dotazovat snadno pomocí rozhraní REST API nebo některou z [klientské sady SDK](sql-api-sdk-dotnet.md). Odstranění dokumentu okamžitě uvolní kvóty odpovídající všechny vnořené přílohy. Úroveň konzistence čtení dokumentů se řídí zásadami konzistence účtu databáze. Tuto zásadu lze přepsat na základě jednotlivých žádostí v závislosti na požadavcích konzistence dat vaší aplikace. Při dotazování na dokumenty, následuje konzistence čtení indexování režim nastavený na kolekci. Pro "konzistentní" to se řídí zásadami konzistence účtu. 

## <a name="attachments-and-media"></a>Přílohy a média
Azure Cosmos DB umožňuje ukládat binární objekty BLOB nebo média buď pomocí služby Azure Cosmos DB (maximálně 2 GB za účet) nebo médium vzdáleného úložiště. Také umožňuje představuje metadata média z hlediska speciálním dokumentu s názvem přílohy. Příloha ve službě Azure Cosmos DB je speciální dokument (JSON), který odkazuje média nebo objekt blob uložená na jiném místě. Přílohu, která je jednoduše speciální dokument, který zachycuje metadata (například umístění, autora atd.) médií ve službě storage vzdálené média. 

Vezměte v úvahu aplikace sociálních sítí čtení, která používá službu Azure Cosmos DB k ukládání rukopisné poznámky a metadata, včetně komentáře, zvýrazní záložky, hodnocení, líbí nebo nelíbí přidružené e-knihy daného uživatele atd.   

* Obsah adresáře samotné jsou uložena v úložišti media buď k dispozici jako součást účtu databáze Azure Cosmos DB nebo médium vzdáleného úložiště. 
* Aplikace může ukládat každý uživatel metadat jako odlišné dokument – například Michalův metadata pro Sešit1 jsou uložena v dokumentu odkazují /colls/joe/docs/book1. 
* Přejdete na stránky obsahu daného adresáře uživatele, přílohy jsou uloženy v odpovídající dokumentu, například /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 atd. 

Příklady uvedené výše pro vyjádření hierarchie prostředků použijte popisný ID. Prostředky jsou přístupné přes rozhraní REST API přes ID prostředků jedinečné. 

Vlastnost _médium přílohy pro média, který je spravovaný službou Azure Cosmos DB, odkazuje médium pomocí jeho identifikátoru URI. Azure Cosmos DB zajistíte k uvolnění paměti média získaná při všech zbývajících odkazů se zahodí. Azure Cosmos DB automaticky generuje přílohu při nahrání nové médium a naplní _médium tak, aby odkazoval na nově přidaných médium. Pokud se rozhodnete k ukládání médií v úložišti objektů blob vzdálené spravujete sami (třeba OneDrive, Azure Storage, DropBox atd.), můžete stále použít přílohy tak, aby odkazovaly média. V tomto případě vytvoříte přílohu sami a jeho vlastnost _médium naplnit.   

Jako se všemi ostatními prostředky, můžete vytvořit přílohy, nahradí, odstranit, číst nebo výčtu snadno pomocí rozhraní REST API nebo některou z klientské sady SDK. Stejně jako u dokumentů, úrovně konzistence čtení příloh se řídí zásadami konzistence účtu databáze. Tuto zásadu lze přepsat na základě jednotlivých žádostí v závislosti na požadavcích konzistence dat vaší aplikace. Při dotazování na přílohy, následuje konzistence čtení indexování režim nastavený na kolekci. Pro "konzistentní" to se řídí zásadami konzistence účtu. 
 

## <a name="users"></a>Uživatelé
Uživatele služby Azure Cosmos DB představuje logické obor názvů pro seskupení oprávnění. Uživatele služby Azure Cosmos DB může odpovídat na uživatele v roli předdefinované aplikace nebo na systém správy identit. Pro službu Azure Cosmos DB uživatel jednoduše představuje abstrakci pro seskupení sady oprávnění v databázi.   

Pro implementaci více tenantů v aplikaci, můžete vytvořit uživatele v Azure Cosmos DB, která odpovídá vašim uživatelům skutečný nebo tenanti vaší aplikace. Potom můžete vytvořit oprávnění pro daného uživatele, které odpovídají řízení přístupu prostřednictvím různých kolekcí, dokumentů, přílohy, atd.   

Jak vaše aplikace potřebuje škálovat podle růstu vašeho uživatele, můžete přijmout různé způsoby, jak horizontálních oddílů, vaše data. Můžete modelovat se může každý z vašich uživatelů následujícím způsobem:   

* Každý uživatel mapuje na databázi.
* Každý uživatel mapuje na kolekci. 
* Dokumenty odpovídá více uživatelů, přejděte na kolekci vyhrazená. 
* Dokumenty odpovídá více uživateli přejít na sadu kolekcí.   

Bez ohledu na to strategie horizontálního dělení konkrétní zvolíte, můžete modelovat skutečné uživatele jako uživatele v databázi Azure Cosmos DB a přidružit k podrobné oprávnění jednotlivým uživatelům.  

![Kolekce uživatelů][3]  
**Strategie horizontálního dělení a modelování uživatelů**

Stejně jako všechny ostatní prostředky uživatelů ve službě Azure Cosmos DB je možné vytvořit, nahradí, odstraněn, přečíst nebo výčtu snadno pomocí rozhraní REST API nebo některou z klientských sad SDK. Azure Cosmos DB vždy poskytuje silnou konzistenci pro čtení nebo dotazování metadat prostředek uživatele. Je zmínku, že odstranění uživatele automaticky zajistí, že nemůžete použít žádnou z oprávnění jsou v něm obsaženy. I v případě, že služby Azure Cosmos DB uvolňuje kvóty oprávnění jako součást odstraněného uživatele na pozadí, odstraněné oprávnění jsou k dispozici okamžitě znovu k použití.  

## <a name="permissions"></a>Oprávnění
Z hlediska řízení přístupu, jsou považovány za prostředky, jako jsou účty databáze, databáze, uživatelů a oprávnění *správu* prostředky, protože se vyžadují oprávnění správce. Na druhé straně prostředky, včetně kolekcí, dokumentů, přílohy, uložené procedury, triggery, a uživatelem definovanými funkcemi jsou s rozsahem v rámci dané databáze a považován za *prostředky aplikace*. Odpovídající dva typy prostředků a rolí, ve kterých přistupovat k nim (konkrétně správce a uživatele), modelu autorizace definuje dva typy *přístupové klíče*: *hlavní klíč* a  *klíč prostředku*. Hlavní klíč je součástí účtu, databáze a je k dispozici pro vývojáře (nebo správci) který probíhá zřizování účtu databáze. Tento hlavní klíč má sémantiku správce, v tom, že je možné k autorizaci přístupu k prostředkům pro správu i aplikace. Klíč prostředku je naopak detailní přístupový klíč, který umožňuje přístup k *konkrétní* prostředků aplikace. Díky tomu se zaznamená vztah mezi uživatelem databáze a oprávnění, která má uživatel pro určitý prostředek (například kolekce, dokument, přílohy, uložené procedury, aktivační události nebo UDF).   

Jediný způsob, jak získat klíč prostředku je tak, že vytvoříte prostředek oprávnění daného uživatele. Pokud chcete vytvořit nebo načíst oprávnění, musí být hlavní klíč prezentovány v hlavičce autorizace. Prostředek oprávnění sváže prostředek, jeho přístup a uživatele. Po vytvoření oprávnění prostředků, uživatel se musí předložit klíč přidružený prostředek cílem získat přístup k odpovídající zdroj. Klíč prostředku, takže lze zobrazit jako logický reprezentací compact prostředků oprávnění.  

Jako se všemi ostatními prostředky, můžete vytvořit oprávnění ve službě Azure Cosmos DB, nahradí, odstranit, číst nebo výčtu snadno pomocí rozhraní REST API nebo některou z klientské sady SDK. Azure Cosmos DB vždy poskytuje silnou konzistenci pro čtení nebo dotazování metadat oprávnění. 

## <a name="next-steps"></a>Další postup
Další informace o práci s nimi pomocí příkazů HTTP v [RESTful interakce s prostředky Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

