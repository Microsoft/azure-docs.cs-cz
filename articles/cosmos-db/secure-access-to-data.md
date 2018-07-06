---
title: Zjistěte, jak zabezpečit přístup k datům ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o konceptech správy přístupů ovládacího prvku ve službě Azure Cosmos DB, včetně hlavních klíčů, klíče jen pro čtení, uživatelů a oprávnění.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: sngun
ms.openlocfilehash: eddfce08711043f81cee0b1c8d7ee8c6c02f6a45
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858734"
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Zabezpečení přístupu k datům služby Azure Cosmos DB
Tento článek obsahuje základní informace o zabezpečení přístupu k datům uloženým v [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB používá dva typy klíče k ověřování uživatelů a získání přístupu k jeho datům a prostředkům. 

|Typ klíče|Zdroje a prostředky|
|---|---|
|[Hlavní klíče](#master-keys) |Používá pro správu prostředků: databáze účtů, databází, uživatelů a oprávnění|
|[Tokeny prostředků](#resource-tokens)|Používá pro prostředky aplikace: kontejnery, dokumenty, přílohy, uložené procedury, triggery a uživatelem definovanými funkcemi|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hlavní klíče 

Hlavní klíče poskytují přístup k všech správce prostředků pro účet databáze. Hlavní klíče:  
- Poskytnutí přístupu k účtům, databází, uživatelů a oprávnění. 
- Nelze použít k poskytování granulární přístup ke kontejnerům a dokumenty.
- Jsou vytvořeny během vytváření účtu.
- Může být znovu vygenerován, kdykoli.

Každý účet se skládá ze dvou hlavních klíčů: primární klíč a sekundární klíč. Účelem dva klíče je tak, aby znovu, nebo vrátit klíče, poskytuje nepřetržitý přístup k účtu a data. 

Kromě dva hlavní klíče pro účet služby Cosmos DB jsou k dispozici dva klíče jen pro čtení. Tyto klíče jen pro čtení povolí jenom operace čtení pro účet. Klíče jen pro čtení se neposkytuje přístup ke čtení zdrojů oprávnění.

Jen pro čtení primární, sekundární, a je možné načíst hlavní klíče pro čtení i zápis a znovu vygenerován pomocí webu Azure portal. Pokyny najdete v tématu [zobrazení, kopírování a obnovení přístupových klíčů](manage-account.md#keys).

![Řízení přístupu (IAM) na webu Azure Portal – ukázka zabezpečení databáze typu NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces otočení hlavní klíč je jednoduché. Přejděte na web Azure Portal k načtení sekundární klíč a primární klíč nahradit sekundární klíč ve vaší aplikaci a pak proveďte otočení primárního klíče na webu Azure Portal.

![Obměna hlavního klíče na webu Azure Portal – ukázka zabezpečení databáze typu NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Ukázka kódu použít hlavní klíč

Následující vzorový kód ukazuje, jak vytvořit instanci DocumentClient a vytvořit databázi pomocí koncový bod účtu služby Cosmos DB a hlavní klíč. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokeny prostředků

Tokeny prostředků poskytují přístup k prostředkům aplikace v databázi. Tokeny prostředků:
- Poskytuje přístup ke konkrétní kontejnery, klíče oddílů, dokumenty, přílohy, uložené procedury, triggery a uživatelem definovanými funkcemi.
- Se vytvoří, pokud [uživatele](#users) udělením [oprávnění](#permissions) ke konkrétnímu prostředku.
- Jsou opětovně vytvořit, když prostředek oprávnění je datová služba vykoná po volání POST, GET nebo PUT.
- Pomocí tokenu prostředku hash vytvořená speciálně pro uživatele, prostředků a oprávnění.
- Když svázán s dobou platnosti přizpůsobitelné. Platná hodnota timespan výchozím nastavení je jedna hodina. Životnost tokenu, však může být explicitně zadán, až do maximálního počtu pět hodin.
- Poskytnout bezpečné alternativu k poskytnutí hlavní klíč. 
- Povolte klientům čtení, zápisu a odstraňování prostředků v účtu služby Cosmos DB podle oprávnění, který byl udělen.

Můžete použít token prostředku (tak, že vytvoříte uživatele služby Cosmos DB a oprávnění) Pokud chcete poskytnout přístup k prostředkům v účtu služby Cosmos DB do klienta, který nemůže být důvěryhodný s hlavním klíčem.  

Tokeny prostředků služby cosmos DB poskytují bezpečné alternativy, která umožňuje klientům pro čtení, zápisu a odstraňování prostředků v účtu služby Cosmos DB podle oprávnění, které jste poskytli a taky nemusejí pro hlavní nebo pouze klíče pro čtení.

Zde je typický návrhový vzor, kterým může požadovaná tokenech prostředků a způsob jejich vygeneruje a doručí do klientů:

1. Střední vrstvě služby je nastavený pro obsluhu mobilních aplikací sdílet fotky uživatele. 
2. Střední úroveň služby má hlavního klíče účtu služby Cosmos DB.
3. Fotografie aplikace se nainstaluje na mobilních zařízeních koncových uživatelů. 
4. Při přihlášení vytvoří aplikace fotky identitu uživatele pomocí služby střední vrstvy. Tento mechanismus identity zařízení je čistě až po aplikace.
5. Po vytvoření identity služby střední vrstvy požádá o oprávnění na základě identity.
6. Služba střední vrstvy odešle token prostředku zpět do aplikace.
7. Aplikace můžete dál používat token prostředku přímo k prostředkům služby Cosmos DB na oprávnění definované pomocí tokenu prostředku a intervalu dovolují tokenu prostředku. 
8. Když vyprší platnost tokenu prostředku, následné žádosti přijímat 401 neoprávněný výjimky.  V tomto okamžiku aplikace znovu naváže identitu a požádá o nový token prostředku.

    ![Pracovní postup tokeny prostředků Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Generování tokenů prostředků a řízení se zpracovává souborem nativní klientské knihovny Cosmos DB; však použití REST musí vytvořit záhlaví žádosti o/ověření. Další informace o vytváření ověřovací hlavičky pro REST najdete v tématu [řízení přístupu na prostředky databáze Cosmos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) nebo [zdrojový kód pro naše sady SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Příklad používá k vygenerování nebo zprostředkování tokenů prostředku služby střední vrstvy, najdete v článku [ResourceTokenBroker aplikace](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Uživatelé
Cosmos DB uživatelů k databázi Cosmos DB.  Každá databáze může obsahovat nula nebo více uživatelů služby Cosmos DB.  Následující příklad kódu ukazuje, jak vytvořit prostředek uživatele služby Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Každý uživatel služby Cosmos DB má PermissionsLink vlastnost, která slouží k načtení seznamu [oprávnění](#permissions) přidružené k uživateli.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Oprávnění
Oprávnění prostředek Cosmos DB je přidružená k uživateli služby Cosmos DB.  Každý uživatel může obsahovat nula nebo více oprávnění služby Cosmos DB.  Prostředek oprávnění poskytuje přístup k tokenu zabezpečení, který uživatel potřebuje při pokusu o přístup k prostředku konkrétní aplikaci.
Existují dvě úrovně přístupu k dispozici, které mohou být k dispozici prostředek oprávnění:

* All: Uživatel má oprávnění k úplnému prostředku.
* Čtení: Uživatel může jenom číst obsah prostředek, ale nemůže provádět zápis, aktualizace nebo odstranění operací s prostředkem.

> [!NOTE]
> Chcete-li spustit služby Cosmos DB uložené procedury, které uživatel musí mít všechna oprávnění ke kontejneru, ve kterém se spustí uloženou proceduru.
> 
> 

### <a name="code-sample-to-create-permission"></a>Vzorový kód k vytvoření oprávnění

Následující příklad kódu ukazuje, jak prostředek oprávnění vytvářet, číst tokenu prostředku prostředku oprávnění a oprávnění pomocí spojení [uživatele](#users) vytvořili výše.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Pokud jste zadali klíče oddílu pro kolekci, pak oprávnění pro kolekci, dokument a přílohy prostředky také uvést ResourcePartitionKey kromě ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Ukázka kódu pro oprávnění ke čtení pro uživatele

Snadno získáte všechna oprávnění prostředky spojené s konkrétní uživatele, Cosmos DB přináší oprávnění informačního kanálu pro každý objekt uživatele.  Následující fragment kódu ukazuje, jak načíst oprávnění přidružené k uživateli vytvořili výše, vytvoření seznamu oprávnění a vytvoření nové instance DocumentClient instance jménem uživatele.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Další postup
* Další informace o zabezpečení databáze Cosmos DB najdete v tématu [Cosmos DB: databáze zabezpečení](database-security.md).
* Další informace o správě klíče hlavní a jen pro čtení, naleznete v tématu [jak spravovat účet služby Azure Cosmos DB](manage-account.md#keys).
* Zjistěte, jak vytvořit službu Azure Cosmos DB autorizačních tokenech, najdete v článku [řízení přístupu na prostředky Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
