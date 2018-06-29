---
title: Kurz přidávání promluv do aplikace LUIS pomocí jazyka C# | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí jazyka C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264239"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>Kurz: Přidávání promluv do aplikace LUIS pomocí jazyka C# 
V tomto kurzu napíšete program, který do záměru přidá promluvu pomocí rozhraní API pro vytváření v jazyce C#.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření projektu konzoly v sadě Visual Studio 
> * Přidání metody pro volání rozhraní API služby LUIS za účelem přidání promluvy a trénování aplikace
> * Přidání souboru JSON s ukázkovými promluvami pro záměr BookFlight (Rezervovat let)
> * Spuštění konzoly a zobrazení stavu trénování pro promluvy

Další informace najdete v technické dokumentaci k rozhraním API pro [přidání ukázkové promluvy do záměru](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a [stav trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/). 
* **[Klíč pro vytváření](luis-concept-keys.md#authoring-key)** pro službu LUIS. Tento klíč najdete v části Account Settings (Nastavení účtu) na webu [LUIS](luis-reference-regions.md).
* [**ID stávající aplikace**](./luis-get-started-create-app.md) LUIS. ID aplikace je uvedené na řídicím panelu aplikace. Před spuštěním tohoto kódu musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytváří záměry ani entity. Přidává promluvy pro existující záměry a entity. 
* **ID verze** aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* Ve VSCode vytvořte nový soubor `add-utterances.cs`.

> [!NOTE] 
> Kompletní řešení v jazyce C#, včetně ukázkového souboru `utterances.json`, je dostupné v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/).

## <a name="create-the-project-in-visual-studio"></a>Vytvoření projektu v sadě Visual Studio

V sadě Visual Studio vytvořte novou **konzolovou aplikaci pro klasickou plochu Windows** využívající .NET Framework. 

![Typ projektu v sadě Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>Přidání závislosti System.Web

V projektu sady Visual Studio je potřeba **System.Web**. V Průzkumníku řešení klikněte pravým tlačítkem na **Odkazy** a vyberte **Přidat odkaz**.

![Přidání odkazu na System.Web](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>Psaní kódu jazyka C#
Soubor **Program.cs** by měl vypadat takto:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Přidejte závislosti System.IO a System.Net.Http.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


Do třídy **Program** přidejte řetězce a ID služby LUIS.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

Přidejte metodu JsonPrettyPrint.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

Přidejte požadavek GET sloužící k vytváření promluv nebo zahájení trénování. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


Přidejte požadavek POST sloužící k vytváření promluv nebo zahájení trénování. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

Přidejte funkci `AddUtterances`.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


Přidejte funkci `Train`. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

Přidejte funkci `Status`.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

Pokud chcete spravovat argumenty, přidejte hlavní kód.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>Určení promluv, které se mají přidat
Vytvořte soubor `utterances.json` a upravte ho tím, že určíte **pole promluv**, které chcete přidat do aplikace LUIS. Záměr a entity již **musí** být v aplikaci LUIS.

> [!NOTE]
> Před spuštěním kódu v souboru `add-utterances.cs` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytváří záměry ani entity. Pouze přidává promluvy pro existující záměry a entity.

Pole `text` obsahuje text promluvy. Pole `intentName` musí odpovídat názvu záměru v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete označovat žádné entity, zadejte prázdný seznam, jak je znázorněno v následujícím příkladu:

Pokud seznam entityLabels není prázdný, hodnoty `startCharIndex` a `endCharIndex` musí označovat entitu, na kterou odkazuje pole `entityName`. Oba indexy se počítají od nuly, což znamená, že číslo 6 v horním příkladu označuje písmeno S ve slově Seattle, a ne mezeru před velkým S.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="mark-the-json-file-as-content"></a>Označení souboru JSON jako obsahu
V Průzkumníku řešení klikněte pravým tlačítkem na soubor `utterances.json` a vyberte **Vlastnosti**. V okně Vlastnosti označte položku **Akce sestavení** jako `Content` a položku **Kopírovat do výstupního adresáře** jako `Copy Always`.  

![Označení souboru JSON jako obsahu](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>Přidání promluvy z příkazového řádku

Sestavte a spusťte aplikaci z příkazového řádku s jazykem C# z adresáře /bin/Debug. Ujistěte se, že se soubor utterances.json nachází i v tomto adresáři.

Zavoláním souboru add-utterances.cs pouze s argumentem utterance.json se přidají nové promluvy, ale nepoužijí se k trénování služby LUIS.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

Tento příkazový řádek ukazuje výsledky volání rozhraní API pro přidávání promluv. Pole `response` obsahuje promluvy, které se přidaly, v následujícím formátu. Hodnota `hasError` je false, což značí, že se promluva přidala.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Přidání promluvy a trénování z příkazového řádku
Zavoláním add-utterance s argumentem `-train` odešlete požadavek na trénování. 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> Duplicitní promluvy se nepřidají znovu, ale ani nezpůsobí chybu. Pole `response` bude obsahovat ID původní promluvy.

Následující JSON ukazuje výsledek úspěšného požadavku na trénování:

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

Po zařazení požadavku na trénování do fronty může dokončení trénování chvíli trvat.

## <a name="get-training-status-from-the-command-line"></a>Získání stavu trénování z příkazového řádku
Zavoláním aplikace s argumentem `-status` zkontrolujte stav trénování a zobrazte podrobnosti o stavu.

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s kurzem hotovi, odeberte sadu Visual Studio a konzolovou aplikaci, pokud už je nepotřebujete. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
