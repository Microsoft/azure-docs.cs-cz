---
title: Naučte se zabezpečit přístup k datům v Azure Cosmos DB
description: Přečtěte si o konceptech řízení přístupu v Azure Cosmos DB, včetně primárních klíčů, klíčů jen pro čtení, uživatelů a oprávnění.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 574592d4434b9d8c49086b82bab0b8775fb67e03
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371728"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Zabezpečený přístup k datům ve službě Azure Cosmos DB

Tento článek poskytuje přehled zabezpečení přístupu k datům uloženým v [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB používá dva typy klíčů k ověřování uživatelů a poskytování přístupu k jeho datům a prostředkům. 

|Typ klíče|Zdroje a prostředky|
|---|---|
|[Primární klíče](#primary-keys) |Používá se pro prostředky pro správu: databázové účty, databáze, uživatele a oprávnění.|
|[Tokeny prostředků](#resource-tokens)|Používá se pro prostředky aplikace: kontejnery, dokumenty, přílohy, uložené procedury, triggery a UDF.|

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Primární klíče

Primární klíče poskytují přístup ke všem prostředkům správy pro účet databáze. Každý účet se skládá ze dvou primárních klíčů: primárního a sekundárního klíče. Účelem duálních klíčů je, abyste mohli znovu vygenerovat nebo obnovit klíče a zajistit tak nepřetržitý přístup k vašemu účtu a datům. Další informace o primárních klíčích najdete v článku o [zabezpečení databáze](database-security.md#primary-keys) .

### <a name="key-rotation"></a>Střídání klíčů<a id="key-rotation"></a>

Proces otáčení primárního klíče je jednoduchý. 

1. Pokud chcete získat sekundární klíč, přejděte na Azure Portal.
2. Nahraďte primární klíč svým sekundárním klíčem ve vaší aplikaci. Ujistěte se, že všichni klienti Cosmos DB napříč všemi nasazeními se okamžitě restartují a začnou používat aktualizovaný klíč.
3. Otočení primárního klíče v Azure Portal.
4. Ověří, jestli má nový primární klíč fungovat u všech prostředků. Proces střídání klíčů může trvat kratší dobu než minutu a hodiny v závislosti na velikosti Cosmos DB účtu.
5. Nahraďte sekundární klíč novým primárním klíčem.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Základní střídání klíčů v Azure Portal – demonstrace zabezpečení databáze NoSQL" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Ukázka kódu pro použití primárního klíče

Následující ukázka kódu ukazuje, jak pomocí koncového bodu a primárního klíče účtu Cosmos DB vytvořit instanci objektu DocumentClient a vytvořit databázi:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

Následující ukázka kódu ukazuje, jak pomocí koncového bodu a primárního klíče účtu Azure Cosmos DB vytvořit instanci `CosmosClient` objektu:

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>Tokeny prostředků <a id="resource-tokens"></a>

Tokeny prostředků poskytují přístup k prostředkům aplikace v rámci databáze. Tokeny prostředků:

- Poskytněte přístup ke konkrétním kontejnerům, klíčům oddílu, dokumentům, přílohám, uloženým procedurám, aktivačním událostem a UDF.
- Se vytvoří, když se [uživateli](#users) udělí [oprávnění](#permissions) ke konkrétnímu prostředku.
- Se znovu vytvoří, když se na základě metody POST, GET nebo PUT vrátí prostředek s oprávněním.
- Použijte token prostředku hash specificky vytvořený pro uživatele, prostředek a oprávnění.
- Je časová vazba s přizpůsobitelné období platnosti. Výchozí platný časový rozsah je jedna hodina. Životnost tokenu je však možné výslovně zadat, maximálně po dobu pěti hodin.
- Zajištění bezpečného alternativního řešení pro poskytování primárního klíče.
- Umožněte klientům číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB v závislosti na oprávněních, která byly uděleny.

Můžete použít token prostředku (vytvořením Cosmos DB uživatelů a oprávnění), pokud chcete poskytnout přístup k prostředkům v účtu Cosmos DB ke klientovi, který nemůže být důvěryhodný k primárnímu klíči.  

Cosmos DB tokeny prostředků poskytují bezpečnou alternativu, která klientům umožňuje číst, zapisovat a odstraňovat prostředky v účtu Cosmos DB v závislosti na oprávněních, která jste udělili, a aniž byste museli klíč jenom primární nebo jen pro čtení.

Tady je typický vzor návrhu, který umožňuje vyžádat, vygenerovat a doručit tokeny prostředků klientům:

1. Služba střední vrstvy je nastavená na poskytování mobilní aplikace pro sdílení fotografií uživatele.
2. Služba střední vrstvy má primární klíč účtu Cosmos DB.
3. Aplikace Photo je nainstalovaná na mobilních zařízeních koncových uživatelů.
4. Při přihlášení aplikace Photo vytvoří identitu uživatele se službou střední vrstvy. Tento mechanismus vybudování identity je čistě až do aplikace.
5. Po navázání identity služba střední vrstvy požaduje oprávnění na základě identity.
6. Služba střední vrstvy pošle token prostředku zpátky do aplikace pro telefon.
7. Aplikace pro telefon může i nadále používat token prostředku k přímému přístupu k prostředkům Cosmos DB s oprávněními definovanými tokenem prostředku a v intervalu povoleném tokenem prostředku.
8. Po vypršení platnosti tokenu prostředku obdrží další požadavky 401 neoprávněnou výjimku.  V tuto chvíli aplikace pro telefon znovu naváže identitu a požádá o nový token prostředku.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Základní střídání klíčů v Azure Portal – demonstrace zabezpečení databáze NoSQL" border="false":::

Generování a Správa tokenů prostředků jsou zpracovávány nativními klientskými knihovnami Cosmos DB; Pokud však použijete REST, je nutné vytvořit hlavičky žádosti nebo ověřování. Další informace o vytváření ověřovacích hlaviček pro REST najdete v tématu [Access Control v Cosmos DBch prostředcích](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) nebo ve zdrojovém kódu pro naši sadu [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) nebo [ sadu SDKNode.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Příklad služby střední vrstvy, která se používá ke generování tokenů prostředků nebo jejich zprostředkovateli, najdete v [aplikaci ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Uživatelé<a id="users"></a>

Azure Cosmos DB uživatelé jsou přidruženi k databázi Cosmos.  Každá databáze může obsahovat nula nebo více Cosmos DB uživatelů. Následující ukázka kódu ukazuje, jak vytvořit uživatele Cosmos DB pomocí sady [Azure Cosmos DB .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Každý Cosmos DB uživatel má metodu ReadAsync (), která se dá použít k načtení seznamu [oprávnění](#permissions) přidružených k uživateli.

## <a name="permissions"></a>Oprávnění<a id="permissions"></a>

Prostředek oprávnění je přidružen k uživateli a přiřazený v kontejneru a také na úrovni klíče oddílu. Každý uživatel může obsahovat nula nebo více oprávnění. Prostředek oprávnění poskytuje přístup k tokenu zabezpečení, který uživatel potřebuje při pokusu o přístup ke konkrétnímu kontejneru nebo datům v určitém klíči oddílu. Existují dvě dostupné úrovně přístupu, které mohou být poskytnuty prostředkem oprávnění:

- Vše: uživatel má úplné oprávnění k prostředku.
- Přečtěte si: uživatel může číst jenom obsah prostředku, ale nemůže provádět operace zápisu, aktualizace nebo odstranění na prostředku.

> [!NOTE]
> Aby bylo možné spustit uložené procedury, musí mít uživatel oprávnění všechna oprávnění na kontejneru, ve kterém bude uložená procedura spuštěna.

### <a name="code-sample-to-create-permission"></a>Ukázka kódu pro vytvoření oprávnění

Následující ukázka kódu ukazuje, jak vytvořit prostředek oprávnění, přečtěte si token prostředku prostředku oprávnění a přidružte oprávnění k výše vytvořenému [uživateli](#users) .

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

### <a name="code-sample-to-read-permission-for-user"></a>Ukázka kódu pro oprávnění ke čtení pro uživatele

Následující fragment kódu ukazuje, jak načíst oprávnění přidružené k výše vytvořenému uživateli a vytvořit instanci nového CosmosClient jménem uživatele, vymezenou na jeden klíč oddílu.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Přidat uživatele a přiřadit role

Chcete-li přidat ke svému uživatelskému účtu přístup ke čtečce účtu Azure Cosmos DB, požádejte vlastníka předplatného o provedení následujících kroků v Azure Portal.

1. Otevřete Azure Portal a vyberte svůj účet Azure Cosmos DB.
2. Klikněte na kartu **řízení přístupu (IAM)** a pak klikněte na  **+ Přidat přiřazení role**.
3. V podokně **Přidat přiřazení role** v poli **role** vyberte **Cosmos DB role čtecího modulu účtu**.
4. V **poli přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
5. V adresáři vyberte uživatele, skupinu nebo aplikaci, ke kterým chcete udělit přístup.  Můžete hledat v adresáři podle zobrazovaného jména, e-mailové adresy nebo identifikátorů objektů.
    Vybraný uživatel, skupina nebo aplikace se zobrazí v seznamu vybrané členy.
6. Klikněte na **Uložit**.

Entita může nyní číst prostředky Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Odstranit nebo exportovat uživatelská data

Azure Cosmos DB umožňuje vyhledávat, vybírat, upravovat a odstraňovat osobní údaje nacházející se v databázi nebo kolekcích. Azure Cosmos DB poskytuje rozhraní API k hledání a odstraňování osobních údajů, ale vaše zodpovědnost za použití rozhraní API a definování logiky vyžadované k mazání osobních údajů. Každé rozhraní API pro více modelů (SQL, MongoDB, Gremlin, Cassandra, Table) poskytuje různé jazykové sady SDK, které obsahují metody pro hledání a odstraňování osobních údajů. Funkci [TTL (Time to Live)](time-to-live.md) můžete také povolit, aby se data po uplynutí určité doby automaticky odstranila, aniž by se musely účtovat žádné další náklady.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o zabezpečení databáze Cosmos najdete v tématu [zabezpečení databáze Cosmos DB](database-security.md).
- Informace o tom, jak vytvořit Azure Cosmos DB autorizační tokeny, najdete v tématu [Access Control na Azure Cosmos DBch prostředcích](/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Ukázky správy uživatelů pomocí uživatelů a oprávnění, [ukázky správy uživatelů sady .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
