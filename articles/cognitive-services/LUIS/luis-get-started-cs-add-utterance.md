---
title: Změna, trénovat na základě modelu aplikaceC#
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu pro C# přidáte příklady promluv do aplikace domácí automatizace a budete aplikaci trénovat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 23692bd6a0c708b2747a3cc211b8238d30dfe5db
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161742"
---
# <a name="quickstart-change-model-using-c"></a>Rychlý start: Změnit model použitíC#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Nainstalovaný programovací jazyk C#
* Balíčky NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) a [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in LUIS-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Vytvoření kódu rychlého startu 

V sadě Visual Studio vytvořte novou **konzolovou aplikaci pro klasickou plochu Windows** využívající .NET Framework. 

![Typ projektu v sadě Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Přidání závislosti System.Web

Projekt sady Visual Studio potřebuje **System.Web**. V Průzkumníku řešení klikněte pravým tlačítkem na **Odkazy** a vyberte **Přidat odkaz**.

![Přidání odkazu na System.Web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Přidání dalších závislostí

Projekt sady Visual Studio potřebuje **JsonFormatterPlus** a **CommandLineParser**. V Průzkumníku řešení klikněte pravým tlačítkem na **Odkazy** a vyberte **Spravovat balíčky NuGet**. Vyhledejte každý z těchto dvou balíčků a přidejte je. 

![Přidání závislostí třetích stran](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Psaní kódu jazyka C#
Soubor **Program.cs** by měl vypadat takto:

```C#
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

Přidejte závislosti.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Do třídy **Program** přidejte řetězce a ID služby LUIS.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Přidejte třídu pro správu parametrů příkazového řádku do třídy **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Do třídy **Program** přidejte metodu žádosti GET.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Do třídy **Program** přidejte metodu žádosti POST. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Do třídy **Program** přidejte ukázkové promluvy z metody souboru.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Jakmile se změny na model aplikují, trénujte model. Do třídy **Program** přidejte metodu.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Trénink se nemusí dokončit okamžitě, zkontrolujte stav a ověřte, že se trénování dokončilo. Do třídy **Program** přidejte metodu.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Pokud chcete spravovat argumenty příkazového řádku, přidejte hlavní kód. Do třídy **Program** přidejte metodu.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Zkopírování souboru utterances.json do výstupního adresáře

V Průzkumníku řešení klikněte pravým tlačítkem na soubor `utterances.json` a vyberte **Vlastnosti**. V okně Vlastnosti označte položku **Akce sestavení** jako `Content` a položku **Kopírovat do výstupního adresáře** jako `Copy Always`.  

![Označení souboru JSON jako obsahu](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Vytvoření kódu

Kód vytvořte v sadě Visual Studio. 

## <a name="run-code"></a>Spuštění kódu

V adresáři /bin/Debug projektu spusťte aplikaci v příkazovém řádku. 

```console
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

Tento příkazový řádek ukazuje výsledky volání rozhraní API pro přidávání promluv. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md) 