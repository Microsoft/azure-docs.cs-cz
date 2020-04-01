---
title: Jak používat systémem přiřazenou spravovanou identitu pro přístup k datům Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat spravovanou identitu přiřazenou systému Azure AD pro přístup ke klíčům z Azure Cosmos DB. msi, identita spravované služby, ad, azure active directory, identita
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417230"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Jak používat systémem přiřazenou spravovanou identitu pro přístup k datům Azure Cosmos DB

V tomto článku nastavíte **robustní, agnostik střídání klíčů klíče,** řešení pro přístup ke klíčům Azure Cosmos DB využitím [spravovaných identit](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Příklad v tomto článku používá funkci Azure. Tohoto řešení však můžete dosáhnout pomocí libovolné služby, která podporuje spravované identity. 

Dozvíte se, jak vytvořit funkci Azure, která má přístup k Azure Cosmos DB bez nutnosti zkopírovat klíče Azure Cosmos DB. Funkce se probudí každou minutu a zaznamená aktuální teplotu akvária akvária. Chcete-li se dozvědět, jak nastavit časovač aktivovaný funkce Azure viz Vytvořit funkci v Azure, která se aktivuje v článku [časovače.](../azure-functions/functions-create-scheduled-function.md)

Pro zjednodušení scénáře je vyčištění starších teplotních dokumentů zpracováno již nakonfigurovaným nastavením [Time To Live.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Přiřazení spravované identity přiřazené systému k funkci Azure

V tomto kroku přiřadíte spravované identity přiřazené systému k funkci Azure.

1. Na [webu Azure Portal](https://portal.azure.com/)otevřete podokno **Funkce Azure** a přejděte do aplikace funkce. 

1. Otevřete kartu Identita **funkcí** > **platformy:** 

   ![Karta Identita](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na kartě **Identita** **zapněte** stav **Identita systému.** Nezapomeňte vybrat **uložit**a potvrďte, že chcete zapnout identitu systému. Na konci panel **u systémové identity** by měl vypadat takto:  

   ![Zapnutá identita systému](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Udělení přístupu spravované identity k vašemu účtu Azure Cosmos

V tomto kroku přiřadíte roli spravované identitě přiřazené k funkci Azure Function. Azure Cosmos DB má několik předdefinovaných rolí, které můžete přiřadit ke spravované identitě. Pro toto řešení použijete následující dvě role:

|Vestavěná role  |Popis  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Můžete spravovat účty Azure Cosmos DB. Umožňuje načítání klíčů pro čtení a zápis. |
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Umí číst data účtu Azure Cosmos DB. Umožňuje načítání čtecích klíčů. |

> [!IMPORTANT]
> Podpora RBAC v Azure Cosmos DB se vztahuje pouze na řízení operací roviny. Operace roviny dat jsou zabezpečeny pomocí hlavních klíčů nebo tokenů prostředků. Další informace najdete v článku [Zabezpečený přístup k datům.](secure-access-to-data.md)

> [!TIP] 
> Při přiřazování rolí přiřaďte pouze potřebný přístup. Pokud vaše služba vyžaduje pouze čtení dat, přiřaďte spravovanou identitu roli **Čtečka účtů Cosmos DB.** Další informace o důležitosti přístupu k nejnižším oprávněním naleznete v [článku o nižší expozici privilegovaných účtů.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Pro váš scénář budete číst teplotu a pak zapíšete zpět tato data do kontejneru v Azure Cosmos DB. Vzhledem k tomu, že je třeba zapisovat data, použijete roli **přispěvatele účtu DocumentDB.** 

1. Přihlaste se k portálu Azure a přejděte na svůj účet Azure Cosmos DB. Otevřete **podokno Správa přístupu (IAM)** a potom na kartě **Přiřazení rolí:**

   ![Podokno IAM](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Vyberte tlačítko **+ Přidat** a **pak přidejte přiřazení role**.

1. Vpravo se otevře panel **Přidat přiřazení role:**

   ![Přidat roli](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Role** – vybrat **přispěvatele účtu DocumentDB**
   * **Přiřadit přístup** – V podčásti Vybrat **systém přiřazenou spravovanou identitu** vyberte **možnost Aplikace funkce**.
   * **Vyberte** – podokno bude naplněno všemi aplikacemi funkcí ve vašem předplatném, které mají **identitu spravovaného systému**. V našem případě vyberu aplikaci funkce **SummaryService:** 

      ![Vybrat přiřazení](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Po výběru identity aplikace funkce klikněte na **Uložit**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Programově přístup ke klíčům Azure Cosmos DB z funkce Azure

Teď máme aplikaci funkcí, která má spravanou identitu přiřazenou systémem. Tato identita je dána role **přispěvatele účtu DocumentDB** v oprávněních Azure Cosmos DB. Následující kód aplikace funkce získá klíče Azure Cosmos DB, vytvoří objekt CosmosClient, získá teplotu a uloží to do Cosmos DB.

Tato ukázka používá [rozhraní API seznam klíčů](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) pro přístup ke klíčům účtu Azure Cosmos DB.

> [!IMPORTANT] 
> Pokud chcete přiřadit roli [ **Čtečka účtů Cosmos DB,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) budete muset použít [rozhraní API klíče jen](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)pro čtení . Tím se naplní pouze klíče jen pro čtení.

Rozhraní API klíčů `DatabaseAccountListKeysResult` seznamu vrátí objekt. Tento typ není definován v knihovnách Jazyka C#. Následující kód ukazuje implementaci této třídy:  

```csharp 
namespace SummarizationService 
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

Knihovnu [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) použijete k získání systémově přiřazeného tokenu spravované identity. Další způsoby získání tokenu a další `Microsoft.Azure.Service.AppAuthentication` informace o knihovně najdete v článku [Ověřování služby.](../key-vault/service-to-service-authentication.md)

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Nyní jste připraveni k [nasazení funkce Azure](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Další kroky

* [Ověřování založené na certifikátech pomocí Azure Cosmos DB a Active Directory](certificate-based-authentication.md)
* [Zabezpečení klíčů Azure Cosmos s využitím služby Azure Key Vault](access-secrets-from-keyvault.md)
* [Základní plán zabezpečení pro Azure Cosmos DB](security-baseline.md)
