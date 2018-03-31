---
title: Zjistěte, jak zabezpečit přístup k datům v Azure Cosmos DB | Microsoft Docs
description: Seznamte se s koncepty řízení přístupu v Azure DB Cosmos, včetně hlavního klíče, klíče jen pro čtení, uživatelé a oprávnění.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 771c4a539d240a6bfdc9770adc7cfada01571939
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Zabezpečení přístupu k datům v Azure Cosmos DB
Tento článek obsahuje přehled zabezpečení přístupu k datům uloženým v [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB používá dva typy klíčů k ověření uživatelů a poskytovat přístup k jeho datům a prostředkům. 

|Typ klíče|Zdroje a prostředky|
|---|---|
|[Hlavní klíče](#master-keys) |Použít pro správu prostředků: databáze účty, databází, uživatelů a oprávnění|
|[Tokeny prostředků](#resource-tokens)|Použít pro prostředky aplikace: kolekcí, dokumentů, přílohy, uložené procedury, triggery a UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hlavní klíče 

Hlavní klíče poskytují přístup k všechny správce prostředky pro účet databáze. Hlavní klíče:  
- Poskytují přístup k účtům, databází, uživatelů a oprávnění. 
- Nelze použít k poskytování granulární přístupu do kolekcí a dokumenty.
- Při vytváření účtu se vytvoří.
- Může být kdykoli znovu vygenerovat.

Každý účet se skládá ze dvou hlavních klíčů: primární klíč a sekundární klíč. Účelem dva klíče je, abyste mohli znovu vygenerovat nebo vrátit klíče, zajištění nepřetržité přístupu pro váš účet a data. 

Kromě dva hlavní klíče pro účet Cosmos DB existují dva klíče jen pro čtení. Tyto klíče jen pro čtení povolí jenom operace čtení pro účet. Neposkytuje přístup ke čtení prostředků oprávnění jen pro čtení klíče.

Primární, sekundární, jen pro čtení, a pro čtení a zápis hlavního klíče se dá načíst a znovu vytvořit pomocí portálu Azure. Pokyny najdete v tématu [zobrazení, kopírování a opětovné vytváření přístupových klíčů](manage-account.md#keys).

![Řízení přístupu (IAM) na portálu Azure – ukázka zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces otáčení hlavní klíč je jednoduché. Přejděte na portál Azure k načtení sekundární klíč a potom primární klíč nahradit sekundární klíč v aplikaci a potom otočit primární klíč v portálu Azure.

![Otočení hlavního klíče na portálu Azure – ukázka zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Ukázka kódu pomocí hlavního klíče

Následující příklad kódu ukazuje, jak používat koncový bod účtu Cosmos DB a hlavní klíč k vytváření instancí DocumentClient a vytvořit databázi. 

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

Tokeny prostředků poskytovat přístup k prostředkům aplikace v databázi. Tokeny prostředků:
- Zadejte přístup ke konkrétní kolekce, klíče oddílů, dokumenty, přílohy, uložené procedury, triggery a UDF.
- Když jsou vytvořeny [uživatele](#users) jsou udělena [oprávnění](#permissions) pro konkrétní prostředek.
- Jsou vytvořeny při prostředek oprávnění je reagovali na ni na při volání metody POST, GET a PUT.
- Použijte token prostředků hash vytvořená speciálně pro uživatele, prostředků a oprávnění.
- Když vázán s dobou platnosti přizpůsobitelné. Výchozí platný časový interval je jedna hodina. Životnost tokenu, ale lze explicitně zadat až pět hodin.
- Zadejte jinou bezpečné alternativu k poskytnutí hlavního klíče. 
- Povolte klientům číst, zapisovat a odstraňte prostředky v účtu Cosmos DB podle oprávnění, která jste uděleno.

Token prostředku můžete použít (vytvořením Cosmos DB uživatele a oprávnění) Pokud chcete poskytnout přístup k prostředkům ve vašem účtu Cosmos DB klientovi, který nemůže být považován za důvěryhodný hlavní klíč.  

Tokeny prostředků cosmos DB zadejte bezpečné alternativy, která umožňuje klientům čtení, zápisu a odstraňování prostředky ve vašem účtu Cosmos DB podle oprávnění, která jste udělena a nevyžaduje pro hlavní nebo klíče jen pro čtení.

Zde je typické návrhový vzor, kterým může požadovaný prostředek tokeny a způsob jejich generované a předáním klientům:

1. Střední vrstvě služby jsou nastaveny na mobilní aplikaci pro sdílení fotografií uživatele. 
2. Střední vrstvě služby má hlavní klíč účtu Cosmos DB.
3. Fotografie aplikace je nainstalovaná na mobilní zařízení koncového uživatele. 
4. Přihlašování vytvoří aplikace fotografie identitu uživatele u služby střední vrstvě. Tento mechanismus identity zařízení je čistě až aplikace.
5. Po vytvoření identity služby střední vrstvě požadavků oprávnění na základě identity.
6. Střední vrstvě služby odešle token prostředků zpátky na telefonní aplikace.
7. Telefonní aplikace můžete nadále používat token prostředku přímý přístup k prostředkům Cosmos DB s oprávněními definována token prostředku a pro interval povolený token prostředku. 
8. Když vyprší platnost token prostředku, přijímat další požadavky 401 neoprávněný výjimka.  V tomto okamžiku telefonní aplikaci znovu naváže identitu a vyžádá nový token prostředku.

    ![Prostředek Azure Cosmos DB tokeny pracovního postupu](./media/secure-access-to-data/resourcekeyworkflow.png)

Generování tokenů prostředků a správy se zpracovává souborem nativní knihovny klienta Cosmos DB; ale pokud používáte REST je nutné vytvořit hlavičky požadavku nebo ověřování. Další informace o vytváření ověřovacím hlavičkám pro REST naleznete v tématu [řízení přístupu u prostředků DB Cosmos](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) nebo [zdrojový kód pro naše sady SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Příklad ke generování nebo zprostředkovatel prostředků tokeny služby střední vrstvy, naleznete v části [ResourceTokenBroker aplikace](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Uživatelé
Uživatelé DB cosmos jsou přidruženy k databázi Cosmos DB.  Každá databáze může obsahovat nula nebo více uživatelů Cosmos DB.  Následující příklad kódu ukazuje, jak vytvořit prostředek uživatele Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Každý uživatel Cosmos DB má PermissionsLink vlastnost, která slouží k načtení seznamu [oprávnění](#permissions) přidružené k uživateli.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Oprávnění
Prostředek Cosmos DB oprávnění je přidružen uživatele Cosmos DB.  Každý uživatel může obsahovat nula nebo více oprávnění Cosmos DB.  Prostředek oprávnění poskytuje přístup k tokenu zabezpečení, který uživatel musí při pokusu o přístup k prostředku konkrétní aplikaci.
Existují dvě úrovně přístupu k dispozici, které může být k dispozici prostředek oprávnění:

* All: Uživatel má úplná oprávnění k prostředku.
* Přečtěte si: Uživatel může číst pouze obsah prostředku, ale nemůže provádět zápis, operace aktualizace nebo odstranění na prostředku.

> [!NOTE]
> Aby bylo možné spustit Cosmos DB uložené procedury, uživatel musí mít oprávnění All na kolekci, ve kterém se spustí uloženou proceduru.
> 
> 

### <a name="code-sample-to-create-permission"></a>Ukázka kódu k vytvoření oprávnění

Následující příklad kódu ukazuje, jak vytvořit prostředek oprávnění, token prostředku prostředku oprávnění číst a přidružte oprávnění s [uživatele](#users) vytvořili výše.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Pokud jste zadali klíč oddílu pro svoji kolekci, pak tato oprávnění pro kolekce, dokumentů a příloh prostředky musí obsahovat také ResourcePartitionKey kromě ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Ukázka kódu ke čtení oprávnění pro uživatele

Snadno získat oprávnění všechny prostředky přidružené určitého uživatele, Cosmos DB zpřístupňují oprávnění kanálu pro každý objekt uživatele.  Následující fragment kódu ukazuje, jak načíst oprávnění přidružené k uživateli vytvořili výše, vytvořit seznam oprávnění a vytvořit nové instance DocumentClient jménem uživatele.

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
* Další informace o zabezpečení databáze Cosmos DB najdete v tématu [Cosmos databáze: databáze zabezpečení](database-security.md).
* Další informace o správě klíče hlavní a jen pro čtení, najdete v části [Správa účtu Azure Cosmos DB](manage-account.md#keys).
* Naučte se vytvořit databázi Cosmos Azure autorizace tokeny, najdete v tématu [řízení přístupu na prostředky Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
