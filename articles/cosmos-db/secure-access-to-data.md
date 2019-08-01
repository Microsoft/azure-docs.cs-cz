---
title: Naučte se zabezpečit přístup k datům v Azure Cosmos DB
description: Přečtěte si o konceptech řízení přístupu v Azure Cosmos DB, včetně hlavních klíčů, klíčů jen pro čtení, uživatelů a oprávnění.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: e321091f2abcff4c385b442ac6474c0592845e2e
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602030"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Zabezpečený přístup k datům v Azure Cosmos DB

Tento článek poskytuje přehled zabezpečení přístupu k datům uloženým v [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB používá dva typy klíčů k ověřování uživatelů a poskytování přístupu k jeho datům a prostředkům. 

|Typ klíče|Zdroje a prostředky|
|---|---|
|[Hlavní klíče](#master-keys) |Používá se pro prostředky pro správu: databázové účty, databáze, uživatele a oprávnění.|
|[Tokeny prostředků](#resource-tokens)|Používá se pro prostředky aplikace: kontejnery, dokumenty, přílohy, uložené procedury, triggery a UDF.|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hlavní klíče 

Hlavní klíče poskytují přístup ke všem prostředkům správy pro účet databáze. Hlavní klíče:  
- Poskytněte přístup k účtům, databázím, uživatelům a oprávněním. 
- Nelze použít k poskytnutí podrobného přístupu k kontejnerům a dokumentům.
- Se vytvoří během vytváření účtu.
- Může být kdykoli znovu vygenerována.

Každý účet se skládá ze dvou hlavních klíčů: primárního a sekundárního klíče. Účelem duálních klíčů je, abyste mohli znovu vygenerovat nebo obnovit klíče a zajistit tak nepřetržitý přístup k vašemu účtu a datům. 

Kromě dvou hlavních klíčů pro účet Cosmos DB jsou k dispozici dva klíče jen pro čtení. Tyto klíče jen pro čtení povolují operace čtení jenom na účtu. Klíče jen pro čtení neposkytují přístup k prostředkům oprávnění ke čtení.

Hlavní klíče primární, sekundární, jen pro čtení a pro čtení i zápis se dají načíst a znovu vygenerovat pomocí Azure Portal. Pokyny najdete v tématu [zobrazení, kopírování a obnovení přístupových klíčů](manage-with-cli.md#regenerate-account-key).

![Řízení přístupu (IAM) ve Azure Portal – demonstrace zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces otáčení hlavního klíče je jednoduchý. Přejděte na Azure Portal pro načtení sekundárního klíče a potom v aplikaci Nahraďte svůj primární klíč sekundárním klíčem a pak v Azure Portal Tento primární klíč natočit.

![Rotace hlavního klíče v Azure Portal-demonstruje zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Ukázka kódu pro použití hlavního klíče

Následující ukázka kódu ukazuje, jak použít koncový bod účtu Cosmos DB a hlavní klíč pro vytvoření instance objektu DocumentClient a vytvoření databáze. 

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

Tokeny prostředků poskytují přístup k prostředkům aplikace v rámci databáze. Tokeny prostředků:
- Poskytněte přístup ke konkrétním kontejnerům, klíčům oddílu, dokumentům, přílohám, uloženým procedurám, aktivačním událostem a UDF.
- Se vytvoří, když se [uživateli](#users) udělí [oprávnění](#permissions) ke konkrétnímu prostředku.
- Se znovu vytvoří, když se na základě metody POST, GET nebo PUT vrátí prostředek s oprávněním.
- Použijte token prostředku hash specificky vytvořený pro uživatele, prostředek a oprávnění.
- Je časová vazba s přizpůsobitelné období platnosti. Výchozí platný časový interval je jedna hodina. Životnost tokenu je však možné výslovně zadat, maximálně po dobu pěti hodin.
- Poskytněte zabezpečenou alternativu pro vytvoření hlavního klíče. 
- Umožněte klientům číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB v závislosti na oprávněních, která byly uděleny.

Můžete použít token prostředku (vytvořením Cosmos DB uživatelů a oprávnění), pokud chcete poskytnout přístup k prostředkům v účtu Cosmos DB ke klientovi, který nemůže být důvěryhodný k hlavnímu klíči.  

Cosmos DB tokeny prostředků poskytují bezpečnou alternativu, která klientům umožňuje číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB v závislosti na oprávněních, která jste udělili, a aniž byste museli zadat buď hlavní klíč, nebo klíč jen pro čtení.

Tady je typický vzor návrhu, který umožňuje vyžádat, vygenerovat a doručit tokeny prostředků klientům:

1. Služba střední vrstvy je nastavená na poskytování mobilní aplikace pro sdílení fotografií uživatele. 
2. Služba střední vrstvy má hlavní klíč Cosmos DB účtu.
3. Aplikace Photo je nainstalovaná na mobilních zařízeních koncových uživatelů. 
4. Při přihlášení aplikace Photo vytvoří identitu uživatele se službou střední vrstvy. Tento mechanismus vybudování identity je čistě až do aplikace.
5. Po navázání identity služba střední vrstvy požaduje oprávnění na základě identity.
6. Služba střední vrstvy pošle token prostředku zpátky do aplikace pro telefon.
7. Aplikace pro telefon může i nadále používat token prostředku k přímému přístupu k prostředkům Cosmos DB s oprávněními definovanými tokenem prostředku a v intervalu povoleném tokenem prostředku. 
8. Po vypršení platnosti tokenu prostředku obdrží další požadavky 401 neoprávněnou výjimku.  V tuto chvíli aplikace pro telefon znovu naváže identitu a požádá o nový token prostředku.

    ![Pracovní postup Azure Cosmos DB tokenů prostředků](./media/secure-access-to-data/resourcekeyworkflow.png)

Generování a Správa tokenů prostředků jsou zpracovávány nativními klientskými knihovnami Cosmos DB; Pokud však použijete REST, je nutné vytvořit hlavičky žádosti nebo ověřování. Další informace o vytváření ověřovacích hlaviček pro REST najdete v tématu [Access Control na Cosmos DBch](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) prostředcích nebo ve [zdrojovém kódu pro naše sady SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Příklad služby střední vrstvy, která se používá ke generování tokenů prostředků nebo jejich zprostředkovateli, najdete v [aplikaci ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Uživatelé
Cosmos DB uživatelé jsou přidruženi k databázi Cosmos DB.  Každá databáze může obsahovat nula nebo více Cosmos DB uživatelů.  Následující ukázka kódu ukazuje, jak vytvořit prostředek Cosmos DB uživatele.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Každý Cosmos DB uživatel má vlastnost PermissionsLink, která se dá použít k načtení seznamu [oprávnění](#permissions) přidružených k uživateli.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Oprávnění
Prostředek oprávnění Cosmos DB je přidružen k Cosmos DBmu uživateli.  Každý uživatel může obsahovat nula nebo více Cosmos DB oprávnění.  Prostředek oprávnění poskytuje přístup k tokenu zabezpečení, který uživatel potřebuje při pokusu o přístup k určitému prostředku aplikace.
Existují dvě dostupné úrovně přístupu, které mohou být poskytnuty prostředkem oprávnění:

* Všem Uživatel má k prostředku úplné oprávnění.
* Oprávnění Uživatel může číst obsah prostředku, ale nemůže provádět operace zápisu, aktualizace nebo odstranění na prostředku.

> [!NOTE]
> Aby bylo možné spustit Cosmos DB uložené procedury, musí mít uživatel na kontejneru, ve kterém bude uložená procedura spuštěna, všechna oprávnění.
> 
> 

### <a name="code-sample-to-create-permission"></a>Ukázka kódu pro vytvoření oprávnění

Následující ukázka kódu ukazuje, jak vytvořit prostředek oprávnění, přečtěte si token prostředku prostředku oprávnění a přidružte oprávnění k výše vytvořenému [uživateli](#users) .

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

Pokud jste pro kolekci zadali klíč oddílu, musí mít oprávnění pro prostředky kolekcí, dokumentů a příloh také kromě ResourceLinku taky ResourcePartitionKey.

### <a name="code-sample-to-read-permissions-for-user"></a>Ukázka kódu pro oprávnění ke čtení pro uživatele

Aby bylo možné snadno získat všechny prostředky oprávnění přidružené ke konkrétnímu uživateli, Cosmos DB zpřístupňuje kanál oprávnění pro každý objekt uživatele.  Následující fragment kódu ukazuje, jak načíst oprávnění přidružené k výše vytvořenému uživateli, vytvořit seznam oprávnění a vytvořit instanci nového DocumentClient jménem uživatele.

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

## <a name="add-users-and-assign-roles"></a>Přidat uživatele a přiřadit role

Chcete-li přidat ke svému uživatelskému účtu přístup ke čtečce účtu Azure Cosmos DB, požádejte vlastníka předplatného o provedení následujících kroků v Azure Portal.

1. Otevřete Azure Portal a vyberte svůj účet Azure Cosmos DB.
2. Klikněte na kartu **řízení přístupu (IAM)** a pak klikněte na **+ Přidat přiřazení role**.
3. V podokně **Přidat přiřazení role** v poli **role** vyberte **Cosmos DB role čtecího modulu účtu**.
4. V **poli přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
5. V adresáři vyberte uživatele, skupinu nebo aplikaci, ke kterým chcete udělit přístup.  Můžete hledat v adresáři podle zobrazovaného jména, e-mailové adresy nebo identifikátorů objektů.
    Vybraný uživatel, skupina nebo aplikace se zobrazí v seznamu vybrané členy.
6. Klikněte na **Uložit**.

Entita může nyní číst prostředky Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Odstranit nebo exportovat uživatelská data
Azure Cosmos DB umožňuje vyhledávat, vybírat, upravovat a odstraňovat osobní údaje nacházející se v databázi nebo kolekcích. Azure Cosmos DB poskytuje rozhraní API k hledání a odstraňování osobních údajů, ale vaše zodpovědnost za použití rozhraní API a definování logiky vyžadované k mazání osobních údajů. Každé rozhraní API pro více modelů (SQL, MongoDB, Gremlin, Cassandra, Table) poskytuje různé jazykové sady SDK, které obsahují metody pro hledání a odstraňování osobních údajů. Funkci [TTL (Time to Live)](time-to-live.md) můžete také povolit, aby se data po uplynutí určité doby automaticky odstranila, aniž by se musely účtovat žádné další náklady.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup
* Další informace o Cosmos DB zabezpečení databáze najdete v článku [Cosmos DB: Zabezpečení](database-security.md)databáze.
* Informace o tom, jak vytvořit Azure Cosmos DB autorizační tokeny, najdete v tématu [Access Control na Azure Cosmos DBch](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)prostředcích.
