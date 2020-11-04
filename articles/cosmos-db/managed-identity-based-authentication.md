---
title: Použití spravované identity přiřazené systémem pro přístup k datům služby Azure Cosmos DB
description: Přečtěte si, jak nakonfigurovat spravovanou identitu přiřazenou systémem Azure Active Directory (Azure AD) pro přístup k klíčům z Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: b3bd6a71898576ac23cdd10c1eb52e1ef3a39b95
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336584"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Použití spravovaných identit přiřazených systémem pro přístup k Azure Cosmos DB datům
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V tomto článku nastavíte robustní řešení nezávislá pro *střídání klíčů* pro přístup k Azure Cosmos DB klíčům pomocí [spravovaných identit](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Příklad v tomto článku používá Azure Functions, ale můžete použít libovolnou službu, která podporuje spravované identity. 

Naučíte se, jak vytvořit aplikaci Function App, která bude mít přístup k Azure Cosmos DB dat, aniž byste museli kopírovat žádné Azure Cosmos DB klíče. Aplikace Function App se probudí každou minutu a zaznamená aktuální teplotu aquariumy rybí nádrže. Informace o tom, jak nastavit aplikaci funkcí aktivované časovačem, najdete v tématu [Vytvoření funkce v Azure, která se aktivuje článkem časovače](../azure-functions/functions-create-scheduled-function.md) .

Aby se tento scénář zjednodušil, je nastavení [Time to Live](./time-to-live.md) už nakonfigurované pro vyčištění starších dokumentů s teplotou. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Přiřazení spravované identity přiřazené systémem do aplikace Function App

V tomto kroku přiřadíte aplikaci Function App spravovanou identitu přiřazenou systémem.

1. V [Azure Portal](https://portal.azure.com/)otevřete podokno **funkce Azure** a přejdete do aplikace Function App. 

1. Otevřete kartu **Identita funkcí platformy**  >  **Identity** : 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Snímek obrazovky znázorňující funkce platformy a možnosti identity pro aplikaci Function App":::

1. Na kartě **Identita** **zapněte** **stav** identity systému a vyberte **Uložit**. Podokno **Identita** by mělo vypadat takto:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Snímek obrazovky zobrazující stav identity systému nastaven na zapnuto":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Udělení přístupu k účtu Azure Cosmos

V tomto kroku přiřadíte roli spravované identitě přiřazené k systému aplikace Function App. Azure Cosmos DB má několik předdefinovaných rolí, které můžete přiřadit ke spravované identitě. Pro toto řešení použijete tyto dvě role:

|Předdefinovaná role  |Popis  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Může spravovat účty Azure Cosmos DB. Umožňuje načtení klíčů pro čtení i zápis. |
|[Role čtečky účtu Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Může číst data Azure Cosmos DB účtu. Umožňuje načtení klíčů pro čtení. |

> [!IMPORTANT]
> Podpora řízení přístupu založeného na rolích v Azure Cosmos DB platí jenom pro řízení operací roviny. Operace roviny dat jsou zabezpečeny prostřednictvím primárních klíčů nebo tokenů prostředků. Další informace najdete v článku [zabezpečený přístup k datům](secure-access-to-data.md) .

> [!TIP] 
> Přiřadíte-li role, přiřaďte pouze potřebný přístup. Pokud vaše služba vyžaduje jenom čtení dat, přiřaďte k spravované identitě roli **Čtenář účtu Cosmos DB** . Další informace o významu minimálního přístupu k oprávnění najdete v článku o [nižší expozici privilegovaných účtů](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) .

V tomto scénáři načte aplikace Functions teplotu Aquarium a pak tato data zapíše zpátky do kontejneru v Azure Cosmos DB. Vzhledem k tomu, že aplikace Function App musí data zapisovat, je nutné přiřadit roli **Přispěvatel účtu DocumentDB** . 

### <a name="assign-the-role-using-azure-portal"></a>Přiřaďte roli pomocí Azure Portal

1. Přihlaste se k Azure Portal a přejít na účet Azure Cosmos DB. Otevřete podokno **řízení přístupu (IAM)** a pak kartu **přiřazení rolí** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Snímek obrazovky znázorňující podokno řízení přístupu a kartu přiřazení rolí":::

1. Vyberte **+ Přidat** > **Přidat přiřazení role**.

1. Otevře se panel **přiřazení role přidat** napravo:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Snímek obrazovky s podoknem přiřazení role přidání":::

   * **Role** : vyberte **Přispěvatel účtu DocumentDB** .
   * **Přiřadit přístup k** : v podčásti **Vybrat spravovanou identitu přiřazenou systémem** vyberte **Function App**.
   * **Vyberte** : v předplatném se naplní všechny aplikace Function App, které mají **identitu spravovaného systému**. V takovém případě vyberte aplikaci funkcí **FishTankTemperatureService** : 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Snímek obrazovky s podoknem přiřazení role přidání, které se naplní příklady":::

1. Po výběru aplikace Function App vyberte **Save (Uložit** ).

### <a name="assign-the-role-using-azure-cli"></a>Přiřazení role pomocí Azure CLI

Pokud chcete přiřadit roli pomocí rozhraní příkazového řádku Azure, otevřete Azure Cloud Shell a spusťte následující příkazy:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programový přístup k klíčům Azure Cosmos DB

Teď máme aplikaci Function App, která má spravovanou identitu přiřazenou systémem pomocí role **Přispěvatel účtu DocumentDB** v oprávněních Azure Cosmos DB. Následující kód aplikace Function App získá Azure Cosmos DB klíčů, vytvoří objekt CosmosClient, získá teplotu Aquarium a pak ho uložte do Azure Cosmos DB.

V této ukázce se k přístupu k klíčům účtu Azure Cosmos DB používá [rozhraní API pro klíče seznamu](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) .

> [!IMPORTANT] 
> Pokud chcete přiřadit roli [čtenáře účtu Cosmos DB](#grant-access-to-your-azure-cosmos-account) , budete muset použít [seznam rozhraní API klíče jen pro čtení](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Tím se naplní jenom klíče jen pro čtení.

Rozhraní API seznam klíčů vrátí `DatabaseAccountListKeysResult` objekt. Tento typ není definován v knihovnách jazyka C#. Následující kód ukazuje implementaci této třídy:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

Příklad také používá jednoduchý dokument nazvaný "TemperatureRecord", který je definován následujícím způsobem:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Pomocí knihovny [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) získáte spravovaný token identity přiřazený systémem. Další informace o tom, jak získat token a zjistit další informace o `Microsoft.Azure.Service.AppAuthentication` knihovně, najdete v článku o [ověřování služby pro službu](../key-vault/general/service-to-service-authentication.md) .


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Teď jste připraveni [nasadit aplikaci Function App](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Další kroky

* [Ověřování na základě certifikátů pomocí Azure Cosmos DB a Azure Active Directory](certificate-based-authentication.md)
* [Zabezpečené Azure Cosmos DB klíčů pomocí Azure Key Vault](access-secrets-from-keyvault.md)
* [Základní hodnoty zabezpečení pro Azure Cosmos DB](security-baseline.md)
