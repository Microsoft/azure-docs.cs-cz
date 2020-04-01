---
title: Vystavení funkcí pomocí rozhraní OpenAPI pomocí azure api managementu
description: Vytvořte definici OpenAPI, která umožní ostatním aplikacím a službám volat vaši funkci v Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210204"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Vytvoření definice OpenAPI pro rozhraní API bez serveru pomocí Azure API Management

REST API jsou často popsány pomocí definice OpenAPI. Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API.

V tomto kurzu vytvoříte funkci, která určí, jestli je nouzová oprava větrné turbíny nákladově efektivní. Potom vytvoříte definici OpenAPI pro aplikaci funkcí pomocí [Azure API Management](../api-management/api-management-key-concepts.md) tak, aby funkce může být volána z jiných aplikací a služeb.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření funkce v Azure
> * Generování definice OpenAPI pomocí Azure API Management
> * Otestování definice zavoláním funkce
> * Stažení definice OpenAPI

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace funkce umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Vytvoření funkce

Tento kurz používá funkci aktivovanou protokolem HTTP, která má dva parametry:

* Odhadovaný čas na opravu turbíny, v hodinách.
* Kapacita turbíny v kilowattech. 

Funkce pak vypočítá náklady na opravu a jaký může být výnos turbíny za 24 hodin. Vytvoření funkce aktivované protokolem HTTP na [webu Azure Portal:](https://portal.azure.com)

1. Rozbalte aplikaci funkcí **+** a vyberte tlačítko vedle **položky Funkce**. Vyberte **Možnost Pokračovat v portálu** > **Continue**.

1. Vyberte **Další šablony...**, pak vyberte **Dokončit a zobrazit šablony.**

1. Vyberte aktivační `TurbineRepair` událost HTTP, zadejte příkaz **název**funkce , zvolte `Function` **[úroveň ověřování](functions-bindings-http-webhook-trigger.md#http-auth)** a pak vyberte **Vytvořit**.  

    ![Vytvořit funkci HTTP pro OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Nahraďte obsah souboru skriptu run.csx C# následujícím kódem a pak zvolte **Uložit**:

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

1. Chcete-li funkci otestovat, klepněte na tlačítko **Testovat** zcela vpravo rozbalte kartu Test. Zadejte následující hodnotu pro **tělo požadavku**a klepněte na tlačítko **Spustit**.

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

Nyní máte funkci, která určuje nákladovou efektivitu nouzových oprav. Dále vygenerujete definici OpenAPI pro aplikaci funkce.

## <a name="generate-the-openapi-definition"></a>Generování definice OpenAPI

Nyní jste připraveni vygenerovat definici OpenAPI.

1. Vyberte aplikaci funkcí a pak v **části Funkce platformy**zvolte **Správa rozhraní API** a v části Správa rozhraní **API**vyberte **Vytvořit nové** .

    ![Volba správy rozhraní API v funkcích platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Použijte nastavení správy rozhraní API, jak je uvedeno v tabulce pod obrázkem.

    ![Vytvořit novou službu správy rozhraní API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název se vygeneruje na základě názvu aplikace funkce. |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je vytvořen tento nový prostředek. |  
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Stejný prostředek jako vaše aplikace funkce, která by měla být nastavena pro vás. |
    | **Umístění** | USA – západ | Zvolte umístění v USA – západ. |
    | **Název organizace** | Contoso | Název organizace používané na portálu pro vývojáře a pro e-mailová oznámení. |
    | **E-mail správce** | váš e-mail | E-mail, který obdržel systémová oznámení od api managementu. |
    | **Cenová úroveň** | Spotřeba (náhled) | Úroveň spotřeby je ve verzi Preview a není dostupná ve všech oblastech. Úplné podrobnosti o cenách najdete na [stránce s cenami pro správu rozhraní API.](https://azure.microsoft.com/pricing/details/api-management/) |

1. Volbou možnosti **Vytvořit** vytvořte instanci služby API Management, což může několik minut trvat.

1. Vyberte **Povolit přehledy aplikací,** chcete-li odesílat protokoly na stejné místo jako aplikace funkce, pak přijměte zbývající výchozí hodnoty a vyberte **rozhraní API pro propojení**.

1. **Import funkcí Azure** se otevře se zvýrazněnou funkcí **TurbineRepair.** Pokračujte volbou možnosti **Vybrat**.

    ![Import funkcí Azure do správy rozhraní API](media/functions-openapi-definition/import-function-openapi.png)

1. Na stránce **Vytvořit z aplikace funkce** přijměte výchozí hodnoty a vyberte **Vytvořit**

    ![Vytvořit z aplikace funkce](media/functions-openapi-definition/create-function-openapi.png)

Rozhraní API je nyní vytvořen pro funkci.

## <a name="test-the-api"></a>Testování rozhraní API

Před použitím definice OpenAPI byste měli ověřit, že rozhraní API funguje.

1. Na kartě **Test** vaší funkce vyberte operaci **POST.**

1. Zadejte hodnoty pro **hodiny** a **kapacitu**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klepněte na tlačítko **Odeslat**a zobrazte odpověď PROTOKOLU HTTP.

    ![Rozhraní API testovací funkce](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Stažení definice OpenAPI

Pokud vaše rozhraní API funguje podle očekávání, můžete si stáhnout definici OpenAPI.

1. V horní části stránky vyberte **Stáhnout definici OpenAPI.**
   
   ![Stažení definice OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Otevřete stažený soubor JSON a zkontrolujte definici.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

K vytvoření definice OpenAPI vašich funkcí jste použili integraci správy rozhraní API. Nyní můžete upravit definici ve správě rozhraní API na portálu. Další [informace o správě rozhraní API](../api-management/api-management-key-concepts.md)naleznete také v

> [!div class="nextstepaction"]
> [Úprava definice OpenAPI ve správě rozhraní API](../api-management/edit-api.md)
