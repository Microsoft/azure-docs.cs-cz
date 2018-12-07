---
title: Vytvoření definice OpenAPI pro funkci | Dokumentace Microsoftu
description: Vytvořte definici OpenAPI, která umožní ostatním aplikacím a službám volat vaši funkci v Azure.
services: functions
keywords: OpenAPI, Swagger, cloud apps, cloud services,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 6daa29b4e8f09a4f8a40c3b92d2e2e86a5dea6aa
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993181"
---
# <a name="create-an-openapi-definition-for-a-function"></a>Vytvoření definice OpenAPI pro funkci

Rozhraní REST API se často popisují pomocí definice OpenAPI (dříve označované jako soubor [Swagger](https://swagger.io/)). Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API.

V tomto kurzu vytvoříte funkci, která určí, jestli je nouzová oprava větrné turbíny nákladově efektivní. Pak vytvoříte definici OpenAPI pro aplikaci funkcí, aby bylo možné funkci volat i z jiných aplikací a služeb.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření funkce v Azure
> * Vygenerování definice OpenAPI pomocí nástrojů OpenAPI
> * Úprava definice, aby poskytovala další metadata
> * Otestování definice zavoláním funkce

> [!IMPORTANT]
> OpenAPI funkce je aktuálně ve verzi preview a je dostupná jenom pro verzi 1.x modulu runtime Azure Functions.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="set-the-functions-runtime-version"></a>Nastavení verze modulu runtime Functions

Ve výchozím nastavení používá aplikace function app vytvoříte verze 2.x modulu runtime. Verze modulu runtime je nutné nastavit zpět do 1.x před vytvořením funkce.

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

## <a name="create-the-function"></a>Vytvoření funkce

Tento kurz používá funkci aktivovanou protokolem HTTP, která přijímá dva parametry: odhadovaný čas opravy turbíny (v hodinách) a kapacitu turbíny (v kilowattech). Funkce pak vypočítá náklady na opravu a jaký může být výnos turbíny za 24 hodin.

1. Rozbalte aplikaci funkcí a vyberte tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí. 

    ![Stručný úvod do služby Functions na webu Azure Portal](media/functions-openapi-definition/add-first-function.png)

1. Do vyhledávacího pole zadejte `http` a zvolte pro šablonu triggeru HTTP **jazyk C#**. 

    ![Volba triggeru HTTP](./media/functions-openapi-definition/select-http-trigger-portal.png)

1. Jako **Název** funkce zadejte `TurbineRepair`, jako **[Úroveň ověřování](functions-bindings-http-webhook.md#http-auth)** zvolte `Function` a pak vyberte **Vytvořit**.  

    ![Vytvoření funkce aktivované protokolem HTTP](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Nahraďte obsah souboru run.csx následujícím kódem a klikněte na **Uložit**:

    ```csharp
    using System.Net;

    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
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

Nyní máte funkci, která určuje nákladovou efektivitu nouzových oprav. Dále pro aplikaci funkcí vygenerujete a upravíte definici OpenAPI.

## <a name="generate-the-openapi-definition"></a>Generování definice OpenAPI

Nyní jste připraveni vygenerovat definici OpenAPI. Tuto definici můžou používat jiné technologie Microsoftu, jako jsou API Apps, [PowerApps](functions-powerapps-scenario.md) a [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), i vývojářské nástroje třetích stran, jako je [Postman](https://www.getpostman.com/docs/importing_swagger) a [řada dalších balíčků](https://swagger.io/tools/).

1. Vyberte pouze *operace*, které vaše rozhraní API podporuje (v tomto případě POST). Díky tomu bude vygenerovaná definice rozhraní API přehlednější.

    1. Na kartě **Integrace** vaší nové funkce triggeru HTTP změňte **Povolené metody HTTP** na **Vybrané metody**.

    1. V části **Vybrané metody HTTP** zrušte všechny možnosti kromě **POST** a pak klikněte na **Uložit**.

        ![Vybrané metody HTTP](media/functions-openapi-definition/selected-http-methods.png)

1. Klikněte na název vaší aplikace funkcí (například **function-demo-energy**) > **Funkce platformy** > **Definice rozhraní API**.

    ![Definice rozhraní API](media/functions-openapi-definition/api-definition.png)

1. Na kartě **Definice rozhraní API** klikněte na **Funkce**.

    ![Zdroj definice rozhraní API](media/functions-openapi-definition/api-definition-source.png)

    Tento krok pro vaši aplikaci funkcí povolí sadu možností OpenAPI, včetně koncového bodu pro hostování souboru OpenAPI z domény vaší aplikace funkcí, vložené kopie [editoru OpenAPI](https://editor.swagger.io) a generátoru šablon definic rozhraní API.

1. Klikněte na **Vygenerovat šablonu definic rozhraní API** > **Uložit**.

    ![Vygenerovat šablonu definic rozhraní API](media/functions-openapi-definition/generate-template.png)

    Azure ve vaší aplikaci funkcí zkontroluje funkce triggeru HTTP a s použitím informací v souboru functions.json vygeneruje definici rozhraní OpenAPI. Tady je vygenerovaná definice:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #https://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Tato definice je popsaná jako _šablona_, protože k tomu, aby byla úplnou definicí OpenAPI, vyžaduje více metadat. Definici upravíte v dalším kroku.

## <a name="modify-the-openapi-definition"></a>Úprava definice OpenAPI

Teď, když máte definici šablony, upravíte ji, aby poskytovala další metadata týkající se operací rozhraní API a datových struktur. V **definici rozhraní API** odstraňte vygenerovanou definici od `post` až do konce definice, vložte níže uvedený obsah a klikněte na **Uložit**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

V tomto případě můžete prostě vložit aktualizovaná metadata, ale je důležité porozumět typům úprav, které jsme s výchozí šablonou provedli:

* Určili jsme, že rozhraní API generuje a spotřebovává data ve formátu JSON.

* Určili jsme požadované parametry a jejich názvy a datové typy.

* Určili jsme návratové hodnoty úspěšné odpovědi a jejich názvy a datové typy.

* Zadali jsme popisné souhrny a popisy rozhraní API, jeho operací a parametrů. To je důležité pro lidi, kteří budou tuto funkci používat.

* Přidali jsme x-ms-summary a x-ms-visibility, které se používají v uživatelském rozhraní pro Microsoft Flow a Logic Apps. Další informace najdete v tématu [Rozšíření OpenAPI pro vlastní rozhraní API v Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> V definici zabezpečení jsme ponechali výchozí metodu ověřování – klíč rozhraní API. Kdybyste použili jiný typ ověřování, tuto část definice byste změnili.

Další informace o definování operací rozhraní API najdete ve [specifikaci OpenAPI](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Test definice OpenAPI

Než definici rozhraní API použijete, je vhodné ji otestovat v uživatelském rozhraní služby Azure Functions.

1. Na kartě **Správa** pro vaši funkci v části **Klíče hostitele** zkopírujte **výchozí** klíč.

    ![Zkopírování klíče rozhraní API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Tento klíč použijete při testování a také při volání rozhraní API z aplikace nebo služby.

1. Vraťte se do definice rozhraní API: **function-demo-energy** > **Funkce platformy** > **Definice rozhraní API**.

1. V pravém podokně klikněte na **Ověřit**, zadejte klíč rozhraní API, který jste zkopírovali, a klikněte na **Ověřit**.

    ![Ověření pomocí klíče rozhraní API](media/functions-openapi-definition/authenticate-api-key.png)

1. Posuňte se dolů a klikněte na **Vyzkoušet tuto operaci**.

    ![Vyzkoušet tuto operaci](media/functions-openapi-definition/try-operation.png)

1. Zadejte hodnoty pro **hours** (hodiny) a **capacity** (kapacita).

    ![Zadání parametrů](media/functions-openapi-definition/parameters.png)

    Všimněte si, že uživatelské rozhraní používá popisy z definice rozhraní API.

1. Klikněte na **Odeslat požadavek** a pak kliknutím na kartu **Přehledný** zobrazte výstup.

    ![Odeslání požadavku](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření funkce v Azure
> * Vygenerování definice OpenAPI pomocí nástrojů OpenAPI
> * Úprava definice, aby poskytovala další metadata
> * Otestování definice zavoláním funkce

Přejděte k dalšímu tématu, kde se naučíte vytvořit aplikaci PowerApps používající definici OpenAPI, kterou jste vytvořili.

> [!div class="nextstepaction"]
> [Volání funkce z PowerApps](functions-powerapps-scenario.md)
