---
title: Vystavte své funkce pomocí OpenAPI s využitím Azure API Management
description: Vytvořte definici OpenAPI, která umožní ostatním aplikacím a službám volat vaši funkci v Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212904"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Vytvoření definice OpenAPI pro rozhraní API bez serveru s využitím Azure API Management

Rozhraní REST API se často popisují pomocí definice OpenAPI. Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API.

V tomto kurzu vytvoříte funkci, která určí, jestli je nouzová oprava větrné turbíny nákladově efektivní. Pak vytvoříte definici OpenAPI pro aplikaci Function App pomocí [Azure API Management](../api-management/api-management-key-concepts.md) tak, aby se funkce mohla volat z jiných aplikací a služeb.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření funkce v Azure
> * Generování definice OpenAPI pomocí Azure API Management
> * Otestování definice zavoláním funkce
> * Stažení definice OpenAPI

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Vytvoření funkce

V tomto kurzu se používá funkce aktivované protokolem HTTP, která přijímá dva parametry:

* Odhadovaná doba k provedení opravy turbíny (v hodinách)
* Kapacita turbíny v kilowatthodinách. 

Funkce pak vypočítá, kolik se bude opravit, a kolik výnosů turbíny by mohlo vznášet za dobu 24 hodin. Postup vytvoření funkce aktivované protokolem HTTP v [Azure Portal](https://portal.azure.com):

1. V levé nabídce aplikace Functions vyberte **funkce** a potom v horní nabídce vyberte **Přidat** .

1. V okně **Nová funkce** vyberte **Trigger http**.

1. V případě **nové funkce** zadejte `TurbineRepair` . 

1. V rozevíracím seznamu **[úroveň autorizace](functions-bindings-http-webhook-trigger.md#http-auth)** zvolte **funkce** a pak vyberte **vytvořit funkci**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Vytvoření funkce HTTP pro OpenAPI":::

1. Vyberte **kód + test** a potom v rozevíracím seznamu vyberte **Spustit. csx** . Nahraďte obsah souboru skriptu jazyka C# s příponou run. csx následujícím kódem a potom zvolte **Uložit**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Tento kód funkce vrátí zprávu `Yes` nebo `No` , která označuje, jestli je Nouzová oprava nákladově efektivní. Vrátí také příležitost výnosů, kterou turbína představuje, a náklady na řešení turbíny.

1. Chcete-li otestovat funkci, vyberte možnost **test**, vyberte kartu **vstup** , zadejte následující **text pro tělo** a potom vyberte možnost **Spustit**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Test funkce na webu Azure Portal":::

    Na kartě **výstup** se vrátí následující výstup:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Nyní máte funkci, která určuje nákladovou efektivitu nouzových oprav. Dále vygenerujete definici OpenAPI pro aplikaci Function App.

## <a name="generate-the-openapi-definition"></a>Generování definice OpenAPI

Generování definice OpenAPI:

1. Vyberte aplikaci Function App, v levé nabídce vyberte **API Management** a potom v části **API Management** vyberte **vytvořit novou** .

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Zvolit API Management":::


1. Použijte nastavení API Management, jak je uvedeno v následující tabulce:

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název se vygeneruje na základě názvu vaší aplikace Function App. |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém je tento nový prostředek vytvořen. |  
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Stejný prostředek jako aplikace Function App, který by měl být nastaven za vás. |
    | **Umístění** | USA – západ | Vyberte umístění Západní USA. |
    | **Název organizace** | Contoso | Název organizace, který se používá na portálu pro vývojáře, a pro e-mailová oznámení. |
    | **E-mail správce** | Váš e-mail | E-mail, který přijal systémová oznámení z API Management. |
    | **Cenová úroveň** | Využití | Úroveň spotřeby není dostupná ve všech oblastech. Úplné podrobnosti o cenách najdete na [stránce s cenami API Management](https://azure.microsoft.com/pricing/details/api-management/) . |

    ![Vytvořit novou službu API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Volbou možnosti **Vytvořit** vytvořte instanci služby API Management, což může několik minut trvat.

1. Když Azure vytvoří instanci, povolí možnost **povolit Application Insights** na stránce. Vyberte ho, aby se protokoly odesílaly na stejné místo jako aplikace funkce, a pak vyberte **propojit rozhraní API**.

1. Otevře se **Azure Functions importu** se zvýrazněnou funkcí **TurbineRepair** . Pokračujte volbou možnosti **Vybrat**.

    ![Importovat Azure Functions do API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Na stránce **vytvořit z Function App** přijměte výchozí hodnoty a pak vyberte **vytvořit**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Vytvořit z Function App":::

    Azure vytvoří rozhraní API pro funkci.

## <a name="test-the-api"></a>Testování rozhraní API

Před použitím definice OpenAPI byste měli ověřit, že rozhraní API funguje.

1. Na stránce Function App vyberte **API Management**, vyberte kartu **test** a pak vyberte **post TurbineRepair**. 

1. Do **textu žádosti** zadejte následující kód:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Vyberte **Odeslat** a pak zobrazte **odpověď HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Rozhraní API pro testování funkcí":::

## <a name="download-the-openapi-definition"></a>Stažení definice OpenAPI

Pokud vaše rozhraní API funguje podle očekávání, můžete si stáhnout definici OpenAPI.

1. V horní části stránky vyberte **Stáhnout definici openapi** .
   
   ![Stažení definice OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Uložte stažený soubor JSON a otevřete ho. Zkontrolujte definici.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

Použili jste integraci API Management k vygenerování definice OpenAPI vašich funkcí. Nyní můžete upravit definici v API Management na portálu. Můžete si taky [přečíst další informace o API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Upravte definici OpenAPI v API Management](../api-management/edit-api.md)
