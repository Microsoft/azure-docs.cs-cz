---
title: Konfigurace řízení přístupu na základě role pro účet Azure Cosmos DB pomocí Azure AD
description: Zjistěte, jak nakonfigurovat řízení přístupu na základě role pomocí Azure Active Directory pro účet Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: thweiss
ms.openlocfilehash: acb82a66c8ef4adcfeef0fcb527c44512ad4feaf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040868"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Konfigurace řízení přístupu na základě role pomocí Azure Active Directory pro účet Azure Cosmos DB (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB řízení přístupu na základě role je teď ve verzi Preview. Tato verze Preview se poskytuje bez smlouva SLA a nedoporučuje se pro produkční úlohy. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

> [!NOTE]
> Tento článek se týká řízení přístupu na základě role pro operace roviny dat v Azure Cosmos DB. Pokud používáte operace roviny správy, přečtěte si článek [řízení přístupu na základě rolí](role-based-access-control.md) , které se používá v článku věnovaném provozním plochám správy.

Azure Cosmos DB zpřístupňuje integrovaný systém řízení přístupu na základě role (RBAC), který vám umožní:

- Ověřuje vaše požadavky na data pomocí Azure Active Directory (Azure AD) identity.
- Autorizovat žádosti o data pomocí jemně odstupňovaného modelu oprávnění založeného na rolích.

## <a name="concepts"></a>Koncepty

NeAzure Cosmos DB rovina dat je založena na konceptech, které se běžně nacházejí v jiných systémech RBAC, jako je [Azure RBAC](../role-based-access-control/overview.md):

- [Model oprávnění](#permission-model) se skládá ze sady **akcí**; Každá z těchto akcí se mapuje na jednu nebo více databázových operací. Mezi příklady akcí patří čtení položky, zápis položky nebo spuštění dotazu.
- Azure Cosmos DB uživatelé vytvářejí **[Definice rolí](#role-definitions)** obsahující seznam povolených akcí.
- Definice rolí se přiřadí konkrétním identitám Azure AD prostřednictvím **[přiřazení rolí](#role-assignments)**. Přiřazení role také definuje obor, na který se definice role vztahuje; v současné době jsou v současné době tři obory:
    - Účet Azure Cosmos DB,
    - Databáze Azure Cosmos DB,
    - Kontejner Azure Cosmos DB.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Koncepty RBAC":::

> [!NOTE]
> Azure Cosmos DB RBAC aktuálně nezveřejňuje žádné předdefinované definice rolí.

## <a name="permission-model"></a><a id="permission-model"></a> Model oprávnění

V následující tabulce jsou uvedeny všechny akce vystavené modelem oprávnění.

| Název | Odpovídající operace s databází |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Čtení metadat účtu. Podrobnosti najdete v tématu [žádosti o metadata](#metadata-requests) . |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Vytvořte novou položku. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Číst jednotlivou položku podle jejího ID a klíče oddílu (bod-čtení). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Nahradí existující položku. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" položka, která znamená, že ji vytvoří, pokud neexistuje, nebo ji nahraďte, pokud existuje. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Odstraní položku. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Spustí [dotaz SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Čtení z [kanálu změn](read-change-feed.md)kontejneru. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Provede [uloženou proceduru](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Spravujte [konflikty](conflict-resolution-policies.md) pro účty oblastí s vícenásobným zápisem (tj. seznam a odstranit položky z informačního kanálu konfliktů). |

Zástupné znaky jsou podporovány na úrovni *kontejnerů* a *položek* :

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Tento model oprávnění pokrývá pouze databázové operace, které umožňují čtení a zápis dat. Nepokrývá **žádný** druh operací správy, jako je vytváření kontejnerů nebo změna propustnosti. K ověřování operací správy pomocí identity AAD použijte místo toho [Azure RBAC](role-based-access-control.md) .

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Žádosti o metadata

Při použití sady Azure Cosmos DB SDK vystaví tyto sady SDK žádosti o metadata jen pro čtení během inicializace a poskytování specifických požadavků na data. Tyto požadavky na metadata načítají různé podrobnosti konfigurace, například: 

- Globální konfigurace vašeho účtu, která zahrnuje oblasti Azure, ve kterých je účet k dispozici.
- Klíč oddílu vašich kontejnerů nebo jejich zásad indexování.
- Seznam fyzických oddílů, které vytvářejí kontejner a jejich adresy.

Nečtou  žádná data, která jste uložili do svého účtu.

Pro zajištění nejlepší transparentnosti našeho modelu oprávnění jsou tyto žádosti o metadata výslovně pokryté `Microsoft.DocumentDB/databaseAccounts/readMetadata` akcí. Tato akce by měla být povolená v každé situaci, kdy se k účtu Azure Cosmos DB přistupoval prostřednictvím jedné ze sady Azure Cosmos DB SDK. Dá se přiřadit (prostřednictvím přiřazení role) na libovolné úrovni v hierarchii Azure Cosmos DB (tj. účet, databáze nebo kontejner).

Skutečné požadavky na metadata povolené `Microsoft.DocumentDB/databaseAccounts/readMetadata` akcí závisí na rozsahu, ke kterému je akce přiřazena:

| Obor | Žádosti povolené akcí |
|---|---|
| Účet | – Výpis databází pod účtem<br>– Pro každou databázi pod účtem jsou povolené akce v oboru databáze. |
| databáze | -Čtení metadat databáze<br>– Výpis kontejnerů v rámci databáze<br>– Pro každý kontejner v rámci databáze, povolené akce v oboru kontejneru |
| Kontejner | -Čtení metadat kontejneru<br>-Výpis fyzických oddílů v kontejneru<br>– Řešení adresy každého fyzického oddílu |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Vytvořit definice rolí

Při vytváření definice role je potřeba zadat:

- Název vašeho účtu Azure Cosmos DB.
- Skupina prostředků, která obsahuje váš účet.
- Typ definice role; v tuto `CustomRole` chvíli se podporuje jenom.
- Název definice role
- Seznam [akcí](#permission-model) , které má role umožňovat.
- Jeden nebo více oborů, na které může být přiřazena definice role; podporované rozsahy:
    - `/` (na úrovni účtu),
    - `/dbs/<database-name>` (na úrovni databáze),
    - `/dbs/<database-name>/colls/<container-name>` (na úrovni kontejneru).

> [!NOTE]
> Níže popsané operace jsou v tuto chvíli dostupné v:
> - Azure PowerShell: [AZ. CosmosDB verze 2.0.1-Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [přípona cosmosdb-Preview verze 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Vytvořte roli s názvem *MyReadOnlyRole* , která obsahuje pouze akce čtení:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Vytvořte roli s názvem *MyReadWriteRole* , která obsahuje všechny akce:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Uveďte definice rolí, které jste vytvořili pro načtení jejich ID:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Vytvořte roli s názvem *MyReadOnlyRole* , která obsahuje pouze akce čtení:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Vytvořte roli s názvem *MyReadWriteRole* , která obsahuje všechny akce:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Uveďte definice rolí, které jste vytvořili pro načtení jejich ID:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Vytvoření přiřazení rolí

Jakmile vytvoříte definice rolí, můžete je přidružit k identitám AAD. Při vytváření přiřazení role je potřeba zadat:

- Název vašeho účtu Azure Cosmos DB.
- Skupina prostředků, která obsahuje váš účet.
- ID definice role, která se má přiřadit
- ID objektu zabezpečení identity, ke které by měla být přiřazena definice role
- Rozsah přiřazení role; podporované rozsahy:
    - `/` (na úrovni účtu)
    - `/dbs/<database-name>` (na úrovni databáze)
    - `/dbs/<database-name>/colls/<container-name>` (na úrovni kontejneru)

  Obor se musí shodovat nebo být dílčím oborem jednoho z oborů přiřazení v rámci definice role.

> [!NOTE]
> Pokud chcete vytvořit přiřazení role pro instanční objekt, ujistěte se, že používáte jeho **ID objektu** , jak je uvedeno v části **podnikové aplikace** okna **Azure Active Directoryového** portálu.

> [!NOTE]
> Níže popsané operace jsou v tuto chvíli dostupné v:
> - Azure PowerShell: [AZ. CosmosDB verze 2.0.1-Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [přípona cosmosdb-Preview verze 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Přiřazení role k identitě:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Přiřazení role k identitě:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principalId $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inicializace sady SDK pomocí Azure AD

Chcete-li ve své aplikaci použít Azure Cosmos DB RBAC, je nutné aktualizovat způsob, jakým sadu Azure Cosmos DB SDK inicializujete. Místo předání primárního klíče účtu musíte předat instanci `TokenCredential` třídy. Tato instance poskytuje sadu Azure Cosmos DB SDK s kontextem požadovaným pro načtení tokenu AAD jménem identity, kterou chcete použít.

Způsob vytvoření `TokenCredential` instance je mimo rámec tohoto článku. Existuje mnoho způsobů, jak vytvořit takovou instanci v závislosti na typu identity AAD, kterou chcete použít (hlavní uživatelské rozhraní, instanční objekt, skupina atd.). Nejdůležitější je, že se vaše `TokenCredential` instance musí přeložit na identitu (ID objektu zabezpečení), ke které jste přiřadili role. Můžete najít příklady vytvoření `TokenCredential` třídy:

- [v .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [v jazyce Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)

Níže uvedené příklady používají instanční objekt s `ClientSecretCredential` instancí.

### <a name="in-net"></a>V .NET

> [!NOTE]
> `preview`Pro přístup k této funkci je nutné použít verzi sady Azure Cosmos DB .NET SDK.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>V Javě

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

## <a name="auditing-data-requests"></a>Požadavky na auditování dat

Při použití Azure Cosmos DB RBAC se [diagnostické protokoly](cosmosdb-monitor-resource-logs.md) rozšiřují s využitím informací o identitě a autorizaci pro každou datovou operaci. To vám umožní provést podrobné auditování a načíst identitu AAD použitou pro všechny požadavky na data odeslané na váš Azure Cosmos DB účet.

Tyto další informace se toků v kategorii protokolu **DataPlaneRequests** a skládají ze dvou dalších sloupců:

- `aadPrincipalId_g` zobrazuje hlavní ID identity AAD, která se použila k ověření žádosti.
- `aadAppliedRoleAssignmentId_g` zobrazuje [přiřazení role](#role-assignments) , které bylo dodrženo při autorizaci žádosti.

## <a name="limits"></a>Omezení

- Můžete vytvořit až 100 definic rolí a přiřazení rolí 2 000 na účet Azure Cosmos DB.
- Řešení skupiny Azure AD se v současné době nepodporuje u identit, které patří do více než 200 skupin.
- Token Azure AD se v současnosti předává jako hlavička s každou jednotlivou žádostí odeslanou službě Azure Cosmos DB, čímž se zvyšuje celková velikost datové části.
- Přístup k datům pomocí Azure AD prostřednictvím [Azure Cosmos DB Exploreru](data-explorer.md) se ještě nepodporuje. Použití Průzkumníka Azure Cosmos DB dál vyžaduje, aby měl uživatel k tomuto primárnímu klíči přístup.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Která Azure Cosmos DB rozhraní API podporuje RBAC?

V tuto chvíli se podporuje jenom rozhraní SQL API.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Je možné spravovat definice rolí a přiřazení rolí z Azure Portal?

Azure Portal Podpora správy rolí ještě není k dispozici.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Které sady SDK v Azure Cosmos DB SQL API podporují RBAC?

Sady SDK pro [.NET V3](sql-api-sdk-dotnet-standard.md) a [Java v4](sql-api-sdk-java-v4.md) jsou aktuálně podporované.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Automaticky aktualizuje Azure Cosmos DB sady SDK, když vyprší platnost tokenu služby Azure AD?

Ano.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Je možné zakázat použití primárního klíče účtu při použití možnosti RBAC?

Zakázání primárního klíče účtu není momentálně možné.

## <a name="next-steps"></a>Další kroky

- Získejte přehled o [zabezpečení přístupu k datům v Cosmos DB](secure-access-to-data.md).
- Přečtěte si další informace o [RBAC pro správu Azure Cosmos DB](role-based-access-control.md).
