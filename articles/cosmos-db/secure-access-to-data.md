---
title: Zjistěte, jak zabezpečit přístup k datům v Azure Cosmos DB
description: Další informace o konceptech řízení přístupu v Azure Cosmos DB, včetně hlavních klíčů, klíčů jen pro čtení, uživatelů a oprávnění.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251814"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Zabezpečený přístup k datům ve službě Azure Cosmos DB

Tento článek obsahuje přehled zabezpečení přístupu k datům uloženým v [databázi Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/)DB .

Azure Cosmos DB používá dva typy klíčů k ověření uživatelů a poskytují přístup k jeho data a prostředky. 

|Typ klíče|Prostředky|
|---|---|
|[Hlavní klávesy](#master-keys) |Používá se pro prostředky pro správu: databázové účty, databáze, uživatelé a oprávnění|
|[Tokeny prostředků](#resource-tokens)|Používá se pro aplikační prostředky: kontejnery, dokumenty, přílohy, uložené procedury, aktivační události a ufl|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hlavní klávesy

Hlavní klíče poskytují přístup ke všem prostředkům pro správu databázového účtu. Hlavní klávesy:

- Poskytněte přístup k účtům, databázím, uživatelům a oprávněním. 
- Nelze použít k poskytnutí podrobného přístupu k kontejnerům a dokumentům.
- Jsou vytvořeny při vytváření účtu.
- Lze regenerovat kdykoliv.

Každý účet se skládá ze dvou hlavních klíčů: primárního a sekundárního klíče. Účelem dvou klíčů je, abyste mohli znovu vygenerovat nebo rolovat klíče a poskytovat nepřetržitý přístup k vašemu účtu a datům.

Kromě dvou hlavních klíčů pro účet Cosmos DB existují dva klíče jen pro čtení. Tyto klíče jen pro čtení umožňují pouze operace čtení v účtu. Klíče jen pro čtení neposkytují přístup k prostředkům oprávnění ke čtení.

Primární, sekundární, jen pro čtení a hlavní klíče pro čtení a zápis lze načíst a obnovit pomocí portálu Azure. Pokyny naleznete v tématu [Zobrazení, kopírování a opětovné generování přístupových klíčů](manage-with-cli.md#regenerate-account-key).

![Řízení přístupu (IAM) na portálu Azure – prokázání zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces otáčení hlavního klíče je jednoduchý. Přejděte na portál Azure, načtěte sekundární klíč, pak nahraďte primární klíč sekundárním klíčem ve vaší aplikaci a pak otočte primární klíč na webu Azure Portal.

![Hlavní střídání klíčů na portálu Azure – ukázka zabezpečení databáze NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Ukázka kódu pro použití hlavního klíče

Následující ukázka kódu ukazuje, jak použít koncový bod účtu Cosmos DB a hlavní klíč k vytvoření instance DocumentClient a vytvoření databáze.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="resource-tokens"></a>Tokeny prostředků<a id="resource-tokens"></a>

Tokeny prostředků poskytují přístup k prostředkům aplikace v rámci databáze. Tokeny prostředků:

- Poskytněte přístup ke konkrétním kontejnerům, klíčům oddílů, dokumentům, přílohám, uloženým procedurám, aktivačním událostem a ufl.
- Jsou vytvořeny, když [je uživateli](#users) udělena [oprávnění](#permissions) k určitému prostředku.
- Jsou znovu vytvořeny při oprávnění prostředku je zpracována na post, GET, nebo PUT volání.
- Použijte token prostředku hash speciálně vytvořený pro uživatele, prostředek a oprávnění.
- Jsou časově vázány přizpůsobitelné doby platnosti. Výchozí platný časový rozsah je jedna hodina. Životnost tokenu však může být explicitně zadána, maximálně pět hodin.
- Poskytněte bezpečnou alternativu k rozdávání hlavního klíče.
- Umožněte klientům číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB podle oprávnění, která jim byla udělena.

Token prostředku (vytvořením uživatelů a oprávnění Cosmos DB) můžete použít, pokud chcete poskytnout přístup k prostředkům v účtu Cosmos DB klientovi, kterému nelze důvěřovat s hlavním klíčem.  

Tokeny prostředků Cosmos DB poskytují bezpečnou alternativu, která umožňuje klientům číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB podle udělených oprávnění a bez nutnosti hlavního klíče nebo klíče jen pro čtení.

Zde je typický vzor návrhu, kdy tokeny prostředků mohou být požadovány, generovány a doručeny klientům:

1. Služba střední vrstvy je nastavena tak, aby sloužila mobilní aplikaci pro sdílení uživatelských fotografií.
2. Služba střední vrstvy má hlavní klíč účtu Cosmos DB.
3. Fotografická aplikace je nainstalována na mobilních zařízeních koncových uživatelů.
4. Při přihlášení aplikace pro fotografie vytvoří identitu uživatele pomocí služby střední vrstvy. Tento mechanismus provozovny identity je čistě na žádosti.
5. Jakmile je identita vytvořena, služba střední vrstvy požaduje oprávnění na základě identity.
6. Služba střední vrstvy odešle token prostředku zpět do aplikace telefonu.
7. Telefonní aplikace můžete nadále používat token prostředku pro přímý přístup k prostředkům Cosmos DB s oprávněními definovanými tokenem prostředku a pro interval povolený tokenem prostředku.
8. Po vypršení platnosti tokenu prostředku následné požadavky obdrží 401 neoprávněné výjimky.  V tomto okamžiku aplikace pro telefon znovu vytvoří identitu a požádá o nový token prostředku.

    ![Pracovní postup tokenů prostředků Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Generování a správa tokenů prostředků je zpracována nativními klientskými knihovnami Cosmos DB; Pokud však používáte REST, je nutné vytvořit hlavičky požadavku/ověřování. Další informace o vytváření ověřovacích hlaviček pro rest naleznete [v tématu Řízení přístupu k prostředkům Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) nebo zdrojový kód pro naši [sadu .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) nebo [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Příklad služby střední vrstvy, která se používá ke generování nebo zprostředkovateli tokenů prostředků, naleznete v [aplikaci ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Uživatelé<a id="users"></a>

Uživatelé Azure Cosmos DB jsou přidruženi k databázi Cosmos.  Každá databáze může obsahovat nula nebo více uživatelů Cosmos DB. Následující ukázka kódu ukazuje, jak vytvořit uživatele Cosmos DB pomocí [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Každý uživatel Cosmos DB má ReadAsync() metodu, která lze použít k načtení seznamu [oprávnění](#permissions) spojených s uživatelem.

## <a name="permissions"></a>Oprávnění<a id="permissions"></a>

Prostředek oprávnění je přidružen k uživateli a přiřazen na úrovni kontejneru a klíče oddílu. Každý uživatel může obsahovat nula nebo více oprávnění. Prostředek oprávnění poskytuje přístup k tokenu zabezpečení, který uživatel potřebuje při pokusu o přístup k určitému kontejneru nebo datům v určitém klíči oddílu. Existují dvě dostupné úrovně přístupu, které mohou být poskytnuty prostředkem oprávnění:

- Vše: Uživatel má úplná oprávnění k prostředku.
- Čtení: Uživatel může číst pouze obsah prostředku, ale nemůže provádět operace zápisu, aktualizace nebo odstranění prostředku.

> [!NOTE]
> Aby bylo možné spustit uložené procedury uživatel musí mít všechna oprávnění na kontejneru, ve kterém bude spuštěna uložená procedura.

### <a name="code-sample-to-create-permission"></a>Ukázka kódu pro vytvoření oprávnění

Následující ukázka kódu ukazuje, jak vytvořit prostředek oprávnění, číst token prostředku oprávnění a přidružit oprávnění k [uživateli](#users) vytvořenému výše.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Ukázka kódu ke čtení oprávnění pro uživatele

Následující fragment kódu ukazuje, jak načíst oprávnění přidružené k uživateli vytvořenévýše a vytvořit instanci nového CosmosClient jménem uživatele, s rozsahem na jeden klíč oddílu.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Přidání uživatelů a přiřazení rolí

Chcete-li přidat přístup čtečky účtů Azure Cosmos DB ke svému uživatelskému účtu, mají vlastník předplatného provést následující kroky na webu Azure Portal.

1. Otevřete portál Azure a vyberte svůj účet Azure Cosmos DB.
2. Klikněte na kartu **Řízení přístupu (IAM)** a potom klikněte na **+ Přidat přiřazení role**.
3. V podokně **Přidat přiřazení rolí** vyberte v poli **Role** **roli čtečky účtů Cosmos DB**.
4. V **poli Přiřadit přístup**vyberte **uživatele, skupinu nebo aplikaci Azure AD**.
5. V adresáři, ke kterému chcete udělit přístup, vyberte uživatele, skupinu nebo aplikaci.  Adresář můžete prohledávat podle zobrazované adresy, e-mailové adresy nebo identifikátorů objektů.
    Vybraný uživatel, skupina nebo aplikace se zobrazí v seznamu vybraných členů.
6. Klikněte na **Uložit**.

Entita teď může číst prostředky Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Odstranění nebo export uživatelských dat

Azure Cosmos DB umožňuje vyhledávat, vybírat, upravovat a odstraňovat všechna osobní data umístěná v databázi nebo kolekcích. Azure Cosmos DB poskytuje api pro vyhledání a odstranění osobních údajů, ale je vaší odpovědností používat api a definovat logiku potřebnou k vymazání osobních údajů. Každé vícemodelové rozhraní API (SQL, MongoDB, Gremlin, Cassandra, tabulka) poskytuje sady SDK různých jazyků, které obsahují metody vyhledávání a odstraňování osobních údajů. Můžete také povolit funkci [time to live (TTL)](time-to-live.md) k automatickému odstranění dat po zadaném období, aniž by vznikly další náklady.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o zabezpečení databáze Cosmos najdete v [tématu Zabezpečení databáze Cosmos DB](database-security.md).
- Informace o tom, jak vytvořit tokeny autorizace Azure Cosmos DB, najdete [v tématu Řízení přístupu na prostředky Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Ukázky správy uživatelů s uživateli a oprávněními, [.NET SDK v3 vzorky správy uživatelů](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
