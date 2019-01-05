---
title: Vytváření mobilních aplikací s Xamarinem a Azure Cosmos DB
description: Kurz, ve kterém se vytvoří aplikace Xamarin iOS, aplikace pro Android nebo formulářová aplikace s využitím Azure Cosmos DB. Azure Cosmos DB je rychlá cloudová databáze v globálním měřítku pro mobilní aplikace.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 0580129d8a1e8500a7be1b0728bacc947f4ece5a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043868"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Vytváření mobilních aplikací s Xamarinem a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Většina mobilních aplikací potřebuje ukládat data v cloudu a Azure Cosmos DB je cloudová databáze pro mobilní aplikace. Obsahuje vše, co vývojář mobilních aplikací potřebuje. Jedná se o plně spravovanou databázi jako službu, která se škáluje na vyžádání. Dokáže transparentně přinést data do aplikace, ať jsou uživatelé kdekoli na světě. Pomocí [sady SDK služby Azure Cosmos DB pro .NET Core](sql-api-sdk-dotnet-core.md) můžete mobilním aplikacím Xamarin umožnit přímou komunikaci se službou Azure Cosmos DB bez střední vrstvy.

Tento článek obsahuje kurz vytváření mobilních aplikací s Xamarinem a Azure Cosmos DB. Kompletní zdrojový kód pro tento kurz, včetně pokynů ke správě uživatelů a oprávnění, najdete v úložišti [Xamarin a Azure Cosmos DB na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Možnosti služby Azure Cosmos DB pro mobilní aplikace
Azure Cosmos DB poskytuje vývojářům mobilních aplikací následující klíčové funkce:

![Možnosti služby Azure Cosmos DB pro mobilní aplikace](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Bohaté dotazy nad daty bez schématu. Azure Cosmos DB ukládá data jako dokumenty JSON bez schématu v heterogenních kolekcích. Nabízí [bohaté a rychlé dotazy](how-to-sql-query.md), aniž byste si museli dělat starosti se schématy nebo indexy.
* Vysoká propustnost. Se službou Azure Cosmos DB trvá čtení a zápis dokumentů pouze několik milisekund. Vývojáři můžou určit požadovanou propustnost a Azure Cosmos DB ji bude respektovat se smlouvou SLA o 99,99% dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999% dostupnosti čtení pro všechny účty databáze ve více oblastech.
* Neomezené škálování. Vaše kolekce Azure Cosmos DB [rostou s vaší aplikací](partition-data.md). Můžete začít s malým množstvím dat a propustností v řádu stovek požadavků za sekundu. Vaše kolekce nebo databáze se můžou rozšířit na petabajty dat s libovolně velkou propustností v řádu stovek milionů požadavků za sekundu.
* Globální distribuce. Uživatelé mobilních aplikací jsou v pohybu, často po celém světě. Azure Cosmos DB je [globálně distribuovaná databáze](distribute-data-globally.md). Kliknutím na mapu zpřístupníte svá data uživatelům.
* Integrovaná bohatá autorizace. Azure Cosmos DB umožňujte snadnou implementaci oblíbených modelů, jako jsou [data jednotlivých uživatelů](https://aka.ms/documentdb-xamarin-todouser) nebo data sdílená několika uživateli bez potřeby vlastního složitého autorizačního kódu.
* Geoprostorové dotazy. Řada mobilních aplikací dnes nabízí prostředí využívající geografický kontext. Azure Cosmos DB usnadňuje vytváření takových prostředí díky prvotřídní podpoře [geoprostorových typů](geospatial.md).
* Binární přílohy. Data aplikací často zahrnují binární objekty blob. Nativní podpora příloh usnadňuje použití služby Azure Cosmos DB jako univerzálního úložiště pro data aplikací.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Kurz služby Azure Cosmos DB a Xamarinu
Následující kurz ukazuje, jak vytvořit mobilní aplikaci s využitím Xamarinu a služby Azure Cosmos DB. Kompletní zdrojový kód pro tento kurz najdete v úložišti [Xamarin a Azure Cosmos DB na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Začínáme
Začít se službou Azure Cosmos DB je snadné. Přejděte na web Azure Portal a vytvořte si nový účet služby Azure Cosmos DB. Klikněte na kartu **Rychlý start**. Stáhněte si ukázku seznamu úkolů Xamarin Forms, která je již propojená s vaším účtem služby Azure Cosmos DB. 

![Azure Cosmos DB pro mobilní aplikace – Rychlý start](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Případně pokud máte existující aplikaci Xamarin, můžete do ní přidat [balíček NuGet služby Azure Cosmos DB](sql-api-sdk-dotnet-core.md). Azure Cosmos DB podporuje sdílené knihovny Xamarin.IOS, Xamarin.Android a Xamarin Forms.

### <a name="work-with-data"></a>Práce s daty
Datové záznamy se ve službě Azure Cosmos DB ukládají jako dokumenty JSON bez schématu v heterogenních kolekcích. Do stejné kolekce můžete ukládat dokumenty s různými strukturami:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

V projektech Xamarin můžete k dotazování dat bez schématu využít dotazy integrované do jazyka:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Přidání uživatelů
Stejně jako většina úvodních ukázek se ukázka Azure Cosmos DB, kterou jste stáhli, ověřuje ve službě pomocí hlavního klíče pevně zakódovaného v kódu aplikace. Toto výchozí chování není vhodné pro aplikaci, kterou chcete spouštět kdekoli mimo místní emulátor. Kdyby neoprávněný uživatel získal hlavní klíč, mohlo by dojít k ohrožení všech dat v celém účtu služby Azure Cosmos DB. Místo toho by vaše aplikace měla mít přístup pouze k záznamům pro přihlášeného uživatele. Azure Cosmos DB umožňuje vývojářům udělit aplikaci oprávnění ke čtení nebo zápisu a čtení ke kolekci, k sadě dokumentů seskupených podle klíče oddílu nebo ke konkrétnímu dokumentu. 

Podle těchto pokynů upravte aplikaci seznamu úkolů na víceuživatelskou aplikaci seznamu úkolů: 

  1. Přidejte do aplikace přihlašování přes Facebook, Active Directory nebo jakéhokoli jiného zprostředkovatele.

  2. Vytvořte kolekci Azure Cosmos DB UserItems s **/userId** jako klíčem oddílu. Zadáním klíče oddílu pro kolekci umožníte neomezené škálování služby Azure Cosmos DB s ohledem na rostoucí počet uživatelů aplikace při zachování rychlých dotazů.

  3. Přidejte zprostředkovatele tokenu prostředku Azure Cosmos DB. Toto jednoduché webové rozhraní API ověřuje uživatele a vystavuje pro přihlášené uživatele krátkodobé tokeny s přístupem pouze k dokumentům v rámci příslušného oddílu. V tomto příkladu je hostitelem zprostředkovatele tokenu prostředku služba App Service.

  4. Upravte aplikaci tak, aby se ve zprostředkovateli tokenu prostředku ověřovala přes Facebook a odesílala požadavky na tokeny prostředků pro přihlášené uživatele Facebooku. Pak můžete přistupovat k jejich datům v kolekci UserItems.  

Kompletní vzorový kód tohoto modelu najdete v úložišti [zprostředkovatele tokenu prostředku na GitHubu](https://aka.ms/documentdb-xamarin-todouser). Toto řešení znázorňuje tento diagram:

![Uživatelé služby Azure Cosmos DB a zprostředkovatel oprávnění](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Pokud chcete, aby k jednomu seznamu úkolů měli přístup dva uživatelé, můžete do přístupového tokenu ve zprostředkovateli tokenu prostředku přidat další oprávnění.

### <a name="scale-on-demand"></a>Škálování na vyžádání
Azure Cosmos DB je spravovaná databáze jako služba. Když se bude vaše uživatelská základna rozrůstat, nemusíte si dělat starosti se zřizování virtuálních počítačů ani zvyšováním počtu jader. Stačí službě Azure Cosmos DB sdělit, kolik operací za sekundu (propustnost) vaše aplikace potřebuje. Propustnost můžete zadat na kartě **Škálování** pomocí míry propustnosti označované jako jednotky žádostí za sekundu (RU/s). Například operace čtení dokumentu o velikosti 1 kB vyžaduje 1 RU. Můžete také přidat upozornění na metriku **propustnosti** a monitorovat nárůst objemu provozu a prostřednictvím kódu programu měnit propustnost při aktivaci upozornění.

![Škálování propustnosti služby Azure Cosmos DB na vyžádání](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Škálování na globální úrovni
S ohledem na rostoucí oblíbenost aplikace můžete získávat uživatele po celém světě. Nebo možná chcete být připraveni na nepředvídatelné události. Přejděte na web Azure Portal a otevřete svůj účet služby Azure Cosmos DB. Kliknutím na mapu zahájíte průběžnou replikaci vašich dat do jakéhokoli počtu oblastí po celém světě. Tato funkce zpřístupní vaše data uživatelům, ať jsou kdekoli. Můžete také přidat zásady převzetí služeb při selhání, abyste byli připraveni na nepředvídatelné události.

![Škálování služby Azure Cosmos DB napříč geografickými oblastmi](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Blahopřejeme. Dokončili jste řešení a máte mobilní aplikaci s Xamarinem a Azure Cosmos DB. Podle podobných pokynů můžete vytvářet aplikace Cordova s využitím sady SDK služby Azure Cosmos DB pro JavaScript a nativní aplikace pro iOS nebo Android s využitím rozhraní REST API služby Azure Cosmos DB.

## <a name="next-steps"></a>Další postup
* Prohlédněte si zdrojový kód ukázky pro [Xamarin a Azure Cosmos DB na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Stáhněte si [sadu SDK služby Azure Cosmos DB pro .NET Core](sql-api-sdk-dotnet-core.md).
* Prohlédněte si další vzorové kódy [aplikací .NET](sql-api-dotnet-samples.md).
* Přečtěte si o [možnostech bohatých dotazů služby Azure Cosmos DB](how-to-sql-query.md).
* Přečtěte si o [podpoře geoprostorových funkcí ve službě Azure Cosmos DB](geospatial.md).



