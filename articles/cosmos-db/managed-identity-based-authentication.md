---
title: Jak používat systémem přiřazenou spravovanou identitu pro přístup k datům Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat spravovanou identitu přiřazenou k Azure Active Directory (Azure AD) (identitu spravované služby) pro přístup ke klíčům z Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641249"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Použití spravovaných identit přiřazených systémem pro přístup k datům Azure Cosmos DB

V tomto článku nastavíte *robustní, střídání klíčů agnostik* řešení pro přístup ke klíčům Azure Cosmos DB pomocí [spravovaných identit](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Příklad v tomto článku používá Funkce Azure, ale můžete použít libovolnou službu, která podporuje spravované identity. 

Dozvíte se, jak vytvořit aplikaci funkcí, která má přístup k datům Azure Cosmos DB bez nutnosti zkopírovat klíče Azure Cosmos DB. Aplikace funkce se probudí každou minutu a zaznamená aktuální teplotu akvária akvária. Informace o tom, jak nastavit aplikaci spouštěných časovačem, najdete v tématu Vytvoření funkce v Azure, která se aktivuje v článku [časovače.](../azure-functions/functions-create-scheduled-function.md)

Pro zjednodušení scénáře je nastavení [Time To Live](./time-to-live.md) již nakonfigurováno tak, aby vyčistilo starší teplotní dokumenty. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Přiřazení spravované identity přiřazené systému k aplikaci funkce

V tomto kroku přiřadíte spravovanou identitu přiřazenou systémem vaší aplikaci funkcí.

1. Na [webu Azure Portal](https://portal.azure.com/)otevřete podokno **Funkce Azure** a přejděte do aplikace funkce. 

1. Otevřete kartu Identita **funkcí** > **platformy:** 

   ![Snímek obrazovky s funkcemi platformy a možnostmi identity pro aplikaci funkcí](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na kartě **Identita** **zapněte** **stav** systémové identity a vyberte **Uložit**. Podokno **Identita** by mělo vypadat takto:  

   ![Snímek obrazovky se stavem systémové identity nastavený na Zapnuto](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Udělení přístupu k účtu Azure Cosmos

V tomto kroku přiřadíte roli systémové spravované identitě aplikace funkce. Azure Cosmos DB má několik předdefinovaných rolí, které můžete přiřadit ke spravované identitě. Pro toto řešení použijete následující dvě role:

|Vestavěná role  |Popis  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Můžete spravovat účty Azure Cosmos DB. Umožňuje načítání klíčů pro čtení a zápis. |
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Umí číst data účtu Azure Cosmos DB. Umožňuje načítání čtecích klíčů. |

> [!IMPORTANT]
> Podpora řízení přístupu na základě rolí v Azure Cosmos DB platí pouze pro operace roviny řízení. Operace roviny dat jsou zabezpečeny prostřednictvím hlavních klíčů nebo tokenů prostředků. Další informace najdete v článku [Zabezpečený přístup k datům.](secure-access-to-data.md)

> [!TIP] 
> Při přiřazování rolí přiřaďte pouze potřebný přístup. Pokud vaše služba vyžaduje pouze čtení dat, přiřaďte roli **Čtečky účtů Cosmos DB** spravované identitě. Další informace o důležitosti přístupu k nejnižším oprávněním naleznete v článku [Nižší expozice privilegovaných účtů.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

V tomto scénáři aplikace funkce bude číst teplotu akvária a pak zapíše zpět tato data do kontejneru v Azure Cosmos DB. Vzhledem k tomu, že aplikace funkce musí zapisovat data, budete muset přiřadit roli **přispěvatele účtu DocumentDB.** 

1. Přihlaste se k portálu Azure a přejděte na svůj účet Azure Cosmos DB. Otevřete podokno **Řízení přístupu (IAM)** a potom kartu **Přiřazení rolí:**

   ![Snímek obrazovky s ovládacím podoknem Access a kartou Přiřazení rolí](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Vyberte **+ Přidat** > **přiřazení role**.

1. Panel **Přidat přiřazení role** se otevře vpravo:

   ![Snímek obrazovky s podoknem Přidat přiřazení role](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Role**: Vybrat **přispěvatele účtu DocumentDB**
   * **Přiřazení přístupu**k : V podčásti **Vybrat systémově přiřazenou spravovanou identitu** vyberte **možnost Aplikace funkce**.
   * **Vyberte:** Podokno bude naplněno všemi aplikacemi funkcí ve vašem předplatném, které mají **identitu spravovaného systému**. V takovém případě vyberte aplikaci funkce **FishTankTemperatureService:** 

      ![Snímek obrazovky s podoknem Přidat přiřazení role vyplněným příklady](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Po výběru aplikace funkce vyberte **Uložit**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programově přístup ke klíčům Azure Cosmos DB

Teď máme aplikaci funkcí, která má systémem přiřazenou spravovanou identitu s rolí **přispěvatele účtu DocumentDB** v oprávněních Azure Cosmos DB. Následující kód aplikace funkce získá klíče Azure Cosmos DB, vytvoří objekt CosmosClient, získá teplotu akvária a pak ji uloží do Azure Cosmos DB.

Tato ukázka používá [rozhraní API seznam klíčů](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) pro přístup ke klíčům účtu Azure Cosmos DB.

> [!IMPORTANT] 
> Pokud chcete přiřadit roli [Čtečka účtů Cosmos DB,](#grant-access-to-your-azure-cosmos-account) budete muset použít [rozhraní API pro čtení v seznamu](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys). Tím se naplní pouze klíče jen pro čtení.

Rozhraní API klíčů `DatabaseAccountListKeysResult` seznamu vrátí objekt. Tento typ není definován v knihovnách Jazyka C#. Následující kód ukazuje implementaci této třídy:  

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

Příklad také používá jednoduchý dokument s názvem "TemperatureRecord", který je definován takto:

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

Ke získání systémově přiřazeného tokenu spravované identity použijete knihovnu [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Další způsoby získání tokenu a další informace `Microsoft.Azure.Service.AppAuthentication` o knihovně naleznete v článku [ověřování mezi službami.](../key-vault/general/service-to-service-authentication.md)


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

Nyní jste připraveni [nasadit aplikaci funkce](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Další kroky

* [Ověřování založené na certifikátech pomocí Azure Cosmos DB a Azure Active Directory](certificate-based-authentication.md)
* [Zabezpečení klíčů Azure Cosmos DB pomocí azure key vaultu](access-secrets-from-keyvault.md)
* [Základní plán zabezpečení pro Azure Cosmos DB](security-baseline.md)
