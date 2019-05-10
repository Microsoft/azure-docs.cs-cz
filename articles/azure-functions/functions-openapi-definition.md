---
title: Vytvoření definice OpenAPI pro funkci s Azure API Management
description: Vytvořte definici OpenAPI, která umožní ostatním aplikacím a službám volat vaši funkci v Azure.
services: functions
keywords: OpenAPI, Swagger, cloud apps, cloud services,
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 255a7c9d0b9da15176fca90c6934a84fa0f863ed
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501860"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Vytvoření definice OpenAPI pro funkci s Azure API Management

Rozhraní REST API se často popisují pomocí definice OpenAPI. Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API.

V tomto kurzu vytvoříte funkci, která určí, jestli je nouzová oprava větrné turbíny nákladově efektivní. Pak vytvoříte definici OpenAPI pro funkci aplikace pomocí [Azure API Management](../api-management/api-management-key-concepts.md) tak, aby funkci lze volat z jiných aplikací a služeb.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření funkce v Azure
> * Vygeneruje definici rozhraní OpenAPI pomocí služby Azure API Management
> * Otestování definice zavoláním funkce

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace function app umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Vytvoření funkce

Tento kurz používá funkci aktivovanou protokolem HTTP, který přijímá dva parametry:

* Odhadovaný čas, aby turbíny opravit, v hodinách.
* Kapacitu turbíny v kilowattech. 

Funkce pak vypočítá náklady na opravu a jaký může být výnos turbíny za 24 hodin. Chcete-li vytvořit HTTP funkce aktivovaná [webu Azure portal](https://portal.azure.com).

1. Rozbalte aplikaci funkcí a vyberte tlačítko **+** vedle položky **Funkce**. Vyberte **na portálu** > **pokračovat**.

1. Vyberte **další šablony...** a pak vyberte **dokončit a zobrazení šablony**

1. Výběr triggeru HTTP, zadejte `TurbineRepair` pro funkci **název**, zvolte `Function` pro  **[úroveň ověřování](functions-bindings-http-webhook.md#http-auth)** a pak vyberte  **Vytvoření**.  

    ![Vytvoření funkce protokolu HTTP pro OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Nahraďte jeho obsah run.csx C# skriptu souboru následujícím kódem a pak zvolte **Uložit**:

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

1. Pokud chcete funkci otestovat, kliknutím na **Test** úplně vpravo rozbalte kartu Test. Jako **Text požadavku** zadejte následující hodnotu a klikněte na **Spustit**.

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

Nyní máte funkci, která určuje nákladovou efektivitu nouzových oprav. V dalším kroku vygenerujete definice OpenAPI pro aplikaci function app.

## <a name="generate-the-openapi-definition"></a>Generování definice OpenAPI

Nyní jste připraveni vygenerovat definici OpenAPI.

1. Pak vyberte aplikaci funkcí v **funkce platformy**, zvolte **API Management** a vyberte **vytvořit nový** pod **API Management**.

    ![Zvolte rozhraní API Management v funkce platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Pomocí API Management nastavení uvedená v tabulce pod obrázkem.

    ![Vytvořit novou službu API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název je generován a základě název vaší aplikace function App. |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém tento nový prostředek vytvoří. |  
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Stejného prostředku jako aplikace function app, který by měl získat nastavení za vás. |
    | **Umístění** | Západní USA | Zvolte umístění západní USA. |
    | **Název organizace** | Contoso | Název organizace používá na portálu pro vývojáře a e-mailových oznámení. |
    | **E-mail správce** | e-mailu | E-mailu, který přijal systémová oznámení ze služby API Management. |
    | **Cenová úroveň** | Využití (Preview) | Úroveň využití je ve verzi preview a není k dispozici ve všech oblastech. Úplné podrobnosti o cenách, najdete v článku [stránce s cenami API Management](https://azure.microsoft.com/pricing/details/api-management/) |

1. Zvolte **vytvořit** k vytvoření instance API Management, což může trvat několik minut.

1. Vyberte **povolení Application Insights** odeslat protokoly na stejném místě jako funkce aplikace, potvrďte zbývající výchozí hodnoty a vyberte **rozhraní API odkazu**.

1. **Import Azure Functions** otevře s **TurbineRepair** funkce zvýrazní. Zvolte **vyberte** pokračujte.

    ![Služba Azure Functions importovat do API managementu](media/functions-openapi-definition/import-function-openapi.png)

1. V **vytvořit z aplikace Function App** stránce, přijměte výchozí hodnoty a vyberte **Create**

    ![Vytvořit z aplikace Function App](media/functions-openapi-definition/create-function-openapi.png)

Rozhraní API je vytvořený pro funkci.

## <a name="test-the-openapi-definition"></a>Test definice OpenAPI

Než použijete definice rozhraní API, měli byste ověřit, že funguje.

1. Na **testovací** funkce, vyberte na kartě **příspěvek** operace

1. Zadejte hodnoty pro **hodin** a **kapacity**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klikněte na tlačítko **odeslat**, následně zobrazit odpověď HTTP.

    ![Test rozhraní API – funkce](media/functions-openapi-definition/test-function-api-openapi.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o službě API Management](../api-management/api-management-key-concepts.md)
