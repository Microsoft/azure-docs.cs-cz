---
title: Vytváření mobilních aplikací s Xamarin a Azure Cosmos DB | Microsoft Docs
description: Kurz, který vytvoří Xamarin iOS, Android nebo formuláře aplikace pomocí Azure Cosmos DB. Azure Cosmos DB je fast, planetu škálování, cloudu databáze pro mobilní aplikace.
services: cosmos-db
documentationcenter: .net
author: arramac
manager: monicar
editor: ''
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 0cbe9a8463e8b471e3e4b0b4403c3a5d01ed59a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Vytváření mobilních aplikací s Xamarin a Azure Cosmos DB

Většina mobilních aplikací potřebovat k ukládání dat v cloudu a Azure Cosmos databáze je databáze cloudu pro mobilní aplikace. Obsahuje všechny objekty, které musí vývojář mobilní. Je plně spravovaná databáze jako služba, která je škálovatelná na vyžádání. Ho můžete Oživte svoje data do vaší aplikace transparentně, bez ohledu na umístění uživatele po celém světě. Pomocí [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md), můžete povolit mobilní aplikace Xamarin pracovat přímo s Azure Cosmos DB bez střední vrstvy.

Tento článek obsahuje návod pro vytváření mobilních aplikací s Xamarin a Azure Cosmos DB. Můžete najít úplný zdrojový kód pro tento kurz v [Xamarin a Azure DB Cosmos na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), včetně toho, jak můžete spravovat uživatele a oprávnění.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Možnosti Azure Cosmos DB pro mobilní aplikace
Azure Cosmos DB poskytuje následující klíčové funkce pro vývojáře mobilních aplikací:

![Možnosti Azure Cosmos DB pro mobilní aplikace](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Bohaté dotazy nad datovým. Azure Cosmos DB ukládá data jako schemaless dokumenty JSON v heterogenní kolekce. Nabízí [bohatý a rychlé dotazy](sql-api-sql-query.md) bez nutnosti starat o schémata nebo indexů.
* Rychlé propustnost. Jak dlouho trvá jen několik milisekund číst a zapisovat dokumentů s Azure Cosmos DB. Vývojářům můžete určit, které potřebují, propustnosti a Azure Cosmos DB ho ctí s SLA 99,99 % dostupnost pro všechny účty jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % číst dostupnosti pro všechny účty databáze více oblast .
* Neomezený škálování. Kolekce Azure Cosmos DB [růst s růstem aplikace](partition-data.md). Můžete spustit s malou velikost dat a propustnost stovky požadavků za sekundu. Kolekce můžou růst až po petabajty dat a libovolně velké propustnosti se stovkami miliony požadavků za sekundu.
* Globálně distribuované. Uživatelé mobilních aplikací jsou na cestách, často po celém světě. Je Azure Cosmos DB [globálně distribuované databáze](distribute-data-globally.md). Klikněte na mapě tak, aby vaše data mají uživatelé přístup.
* Předdefinované bohaté autorizace. S Azure Cosmos databáze, můžete snadno implementovat oblíbených vypadají podobně jako [uživatelská data](https://aka.ms/documentdb-xamarin-todouser) nebo s více uživateli sdílet data, bez komplexní vlastní autorizační kód.
* Geoprostorové dotazy. Mnoha mobilních aplikacích nabídku geograficky kontextové prostředí ještě dnes. S prvotřídní podporu pro [geoprostorové typy](geospatial.md), vytváření tyto činnosti snadno plnit díky Azure Cosmos DB.
* Binární přílohy. Data aplikací často obsahují binární objekty BLOB. Nativní podpora pro přílohy je jednodušší použít Azure Cosmos DB jako centralizované pro data aplikace.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Kurz pro Azure Cosmos DB a Xamarin
Následující kurz ukazuje, jak vytvářet mobilních aplikací pomocí Xamarin a Azure Cosmos DB. Můžete najít úplný zdrojový kód pro tento kurz v [Xamarin a Azure DB Cosmos na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Začínáme
Je snadné začít s Azure Cosmos DB. Přejděte na portál Azure a vytvořit nový účet Azure Cosmos DB. Klikněte **úvodní** kartě. Stažení ukázky seznamu úkolů Xamarin Forms, který je již připojen k účtu Azure Cosmos DB. 

![Azure Cosmos DB rychlý start pro mobilní aplikace](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Nebo pokud máte existující aplikaci Xamarin, můžete přidat [balíčku NuGet pro Azure Cosmos DB](sql-api-sdk-dotnet-core.md). Podporuje Azure Cosmos DB Xamarin.IOS, Xamarin.Android, a Xamarin Forms sdílené knihovny.

### <a name="work-with-data"></a>Práce s daty
Záznamy dat jsou uloženy v databázi Azure Cosmos jako schemaless dokumenty JSON v heterogenní kolekce. Dokumenty s jinou struktury můžete uložit ve stejné kolekci:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

V Xamarin projekty můžete použít dotazy na language-integrated nad datovým:

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
Jako mnoho získat Začínáme ukázky, Azure Cosmos DB ukázku, kterou jste stáhli ověřuje službě s použitím pevně zakódované hlavní klíč v kódu aplikace. Toto výchozí nastavení není vhodné pro aplikace, které chcete spustit kdekoliv s výjimkou na vaše místní emulátor. Pokud neoprávněný uživatel získat hlavního klíče, může dojít k ohrožení všechna data v účtu Azure Cosmos DB. Místo toho chcete aplikaci pro přístup k záznamů pouze pro přihlášeného uživatele. Azure Cosmos DB umožňuje vývojářům udělit aplikaci pro čtení nebo oprávnění pro čtení a zápis do kolekce, sada dokumenty seskupené podle klíč oddílu, nebo určitého dokumentu. 

Tyto pokyny slouží k úpravě aplikace seznamu úkolů do aplikace seznamu úkolů s více uživateli: 

  1. Přidání přihlášení do aplikace pomocí Facebooku, služby Active Directory nebo jiného poskytovatele.

  2. Vytvořte kolekci Azure Cosmos DB UserItems s **/UserId** jako klíč oddílu. Zadávání klíče oddílů pro vaše kolekce umožňuje Azure DB Cosmos škálování nekonečnou roste počet uživatelů vaší aplikace, můžete nadále nabízejí rychlý dotazy.

  3. Přidejte Token zprostředkovatele prostředků Azure Cosmos DB. Tento jednoduchý webového rozhraní API ověří uživatele a problémy krátkodobou tokeny pro přihlášeného uživatele s přístup jenom k dokumentům v rámci svého oddílu. V tomto příkladu je Token zprostředkovatele prostředků hostované ve službě App Service.

  4. Upravte aplikace k ověření tokenu zprostředkovatele prostředků se sítí Facebook a žádosti o tokeny prostředků pro přihlášeného uživatele služby Facebook. Potom můžete přistupovat k datům v kolekci UserItems.  

Najdete kompletní příklad tohoto vzoru v [tokenu zprostředkovatele prostředků na Githubu](http://aka.ms/documentdb-xamarin-todouser). Tento diagram znázorňuje řešení:

![Zprostředkovatel uživatele a oprávnění Azure Cosmos DB](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Pokud chcete dva uživatelé měli přístup k seznamu úkolů, můžete přidat další oprávnění k přístupu k tokenu v tokenu zprostředkovatele prostředků.

### <a name="scale-on-demand"></a>Škálování na vyžádání
Azure Cosmos DB je spravovaný databáze jako služba. S růstem vaší uživatelské základny, nemusíte si dělat starosti o zřizování virtuálních počítačů nebo zvýšení jader. Všechny budete muset zadat databázi Cosmos Azure je vaše aplikace musí kolik operací za sekundu (propustnost). Můžete zadat propustnost prostřednictvím **škálování** karta pomocí míru propustnosti názvem jednotky žádosti (ruština) za sekundu. Například operace čtení pro 1 KB dokumentu vyžaduje 1 RU. Můžete také přidat výstrahy, které **propustnost** metrika se používá k monitorování provozu růstu a prostřednictvím kódu programu změnit propustnost jako výstrahy ještě efektivněji.

![Azure Cosmos DB škálování propustnosti na vyžádání](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Přejděte planetu škálování
Jako oblíbenosti zvýšení vaší aplikace může získat uživatelé po celém světě. Nebo možná chcete připravit pro nepředpokládaného události. Přejděte na portál Azure a otevřete váš účet Azure Cosmos DB. Klikněte na tlačítko mapu, aby vaše data nepřetržitě replikace pro libovolný počet oblastech po celém světě. Tato funkce zpřístupňuje data, kde jsou vaši uživatelé. Můžete také přidat zásady převzetí služeb při selhání, abyste byli připraveni pro událostí odpovídajících odvětvím.

![Azure Cosmos DB škálování napříč zeměpisné oblasti](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Blahopřejeme. Byly dokončeny řešení a mít mobilní aplikaci s Xamarin a Azure Cosmos DB. Postupujte podle podobným způsobem můžete vytvářet aplikace Cordova pomocí sady Azure Cosmos DB JavaScript SDK a nativní aplikace iOS nebo Android pomocí rozhraní API REST Azure Cosmos DB.

## <a name="next-steps"></a>Další postup
* Zobrazit zdrojový kód pro [Xamarin a Azure DB Cosmos na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Stažení [Cosmos Azure DB .NET Core SDK](sql-api-sdk-dotnet-core.md).
* Najít další ukázky kódu pro [aplikací .NET](sql-api-dotnet-samples.md).
* Další informace o [Azure Cosmos DB bohaté dotazování možnosti](sql-api-sql-query.md).
* Další informace o [geoprostorové podpory v Azure Cosmos DB](geospatial.md).



