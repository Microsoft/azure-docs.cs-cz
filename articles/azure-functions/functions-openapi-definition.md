---
title: Vystavte své funkce pomocí OpenAPI s využitím Azure API Management
description: Vytvořte definici OpenAPI, která umožní ostatním aplikacím a službám volat vaši funkci v Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 56e2182b408b4e8b1f89eee7458a27c5dd54bb55
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562009"
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

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Vytvoření funkce

V tomto kurzu se používá funkce aktivované protokolem HTTP, která přijímá dva parametry:

* Odhadovaná doba k provedení opravy turbíny (v hodinách)
* Kapacita turbíny v kilowatthodinách. 

Funkce pak vypočítá náklady na opravu a jaký může být výnos turbíny za 24 hodin. Postup vytvoření funkce aktivované protokolem HTTP v [Azure Portal](https://portal.azure.com):

1. Rozbalte aplikaci funkcí a vyberte tlačítko **+** vedle položky **Funkce**. Vyberte > **pokračovat** **v portálu** .

1. Vyberte **Další šablony...** a pak vyberte **Dokončit a zobrazit šablony** .

1. Vyberte možnost aktivační událost HTTP, jako **název**funkce zadejte `TurbineRepair`, pro **[úroveň ověřování](functions-bindings-http-webhook.md#http-auth)** zvolte `Function` a pak vyberte **vytvořit**.  

    ![Vytvoření funkce HTTP pro OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Nahraďte obsah souboru skriptu run. csx C# následujícím kódem a potom zvolte **Uložit**:

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

    Tento kód funkce vrátí zprávu `Yes` nebo `No`, která značí, jestli je nouzová oprava nákladově efektivní, a obsahuje také možné výnosy turbíny a náklady na opravu turbíny.

1. Pokud chcete funkci otestovat, kliknutím na **test** úplně vpravo rozbalte kartu test. pro **tělo žádosti**zadejte následující hodnotu a klikněte na **Spustit**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Test funkce na webu Azure Portal](media/functions-openapi-definition/test-function.png)

    V textu odpovědi se vrátí následující hodnota.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Nyní máte funkci, která určuje nákladovou efektivitu nouzových oprav. Dále vygenerujete definici OpenAPI pro aplikaci Function App.

## <a name="generate-the-openapi-definition"></a>Generování definice OpenAPI

Nyní jste připraveni vygenerovat definici OpenAPI.

1. Vyberte aplikaci Function App, potom v části **funkce platformy**zvolte možnost **API Management** a v části **API Management**vyberte **vytvořit novou** .

    ![Zvolit API Management ve funkcích platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Použijte nastavení API Management, jak je uvedeno v tabulce pod obrázkem.

    ![Vytvořit novou službu API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název se vygeneruje na základě názvu vaší aplikace Function App. |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém je tento nový prostředek vytvořen. |  
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Stejný prostředek jako aplikace Function App, který by měl být nastaven za vás. |
    | **Umístění** | USA – západ | Vyberte umístění Západní USA. |
    | **Název organizace** | Contoso | Název organizace, který se používá na portálu pro vývojáře, a pro e-mailová oznámení. |
    | **E-mail správce** | Váš e-mail | E-mail, který přijal systémová oznámení z API Management. |
    | **Cenová úroveň** | Spotřeba (Preview) | Úroveň spotřeby je ve verzi Preview a není dostupná ve všech oblastech. Úplné podrobnosti o cenách najdete na [stránce s cenami API Management](https://azure.microsoft.com/pricing/details/api-management/) . |

1. Volbou možnosti **Vytvořit** vytvořte instanci služby API Management, což může několik minut trvat.

1. Vyberte **povolit Application Insights** pro posílání protokolů na stejné místo jako aplikace Functions, potom přijměte zbývající výchozí hodnoty a vyberte **propojit rozhraní API**.

1. Otevře se **Azure Functions importu** se zvýrazněnou funkcí **TurbineRepair** . Pokračujte volbou možnosti **Vybrat**.

    ![Importovat Azure Functions do API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Na stránce **vytvořit z Function App** přijměte výchozí hodnoty a vyberte **vytvořit** .

    ![Vytvořit z aplikace funkcí](media/functions-openapi-definition/create-function-openapi.png)

Rozhraní API se teď pro funkci vytvořilo.

## <a name="test-the-api"></a>Testovat rozhraní API

Před použitím definice OpenAPI byste měli ověřit, že rozhraní API funguje.

1. Na kartě **test** ve vaší funkci vyberte operace **post** .

1. Zadejte hodnoty pro **hodiny** a **kapacitu** .

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klikněte na **Odeslat**a pak ZOBRAZTE odpověď HTTP.

    ![Rozhraní API pro testování funkcí](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Stažení definice OpenAPI

Pokud vaše rozhraní API funguje podle očekávání, můžete si stáhnout definici OpenAPI.

1. V horní části stránky vyberte **Stáhnout definici openapi** .
   
   ![Stažení definice OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Otevřete stažený soubor JSON a zkontrolujte definici.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

Použili jste integraci API Management k vygenerování definice OpenAPI vašich funkcí. Nyní můžete upravit definici v API Management na portálu. Můžete si taky [přečíst další informace o API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Upravte definici OpenAPI v API Management](../api-management/edit-api.md)
