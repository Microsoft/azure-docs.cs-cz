---
title: 'Rychlý start: Rozpoznávání tváří v obrázku pomocí .NET SDK služby Azure pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete Azure SDK pro rozpoznávání tváře s C# k rozpoznávání tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: eaaeda1460ecffc2b1a53bb46901aedc2e9563c4
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313309"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Rychlý start: Rozpoznávání tváří v obrázku pomocí sady .NET SDK pro rozpoznávání tváře

V tomto rychlém startu použijete typ písma služby SDK s C# k detekci lidských tváří v obrázku. Funkční příklad kódu v rámci tohoto rychlého startu, najdete v článku do projektu pro rozpoznávání tváře [Cognitive Services pro zpracování obrazu csharp šablon rychlý Start](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) úložišti na Githubu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový **Konzolová aplikace (.NET Framework)** projektu a pojmenujte ho **FaceDetection**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky NuGet**. Klikněte na tlačítko **Procházet** kartě a vyberte **zahrnout předběžné verze**; najít a nainstalovat balíček následující:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>Přidejte kód pro rozpoznávání tváře detekce

Otevřete nový projekt *Program.cs* souboru. Zde přidejte kód potřebný k načtení bitové kopie a rozpoznávání tváří.

### <a name="include-namespaces"></a>Zahrnutí oborů názvů

Na začátek souboru *Program.cs* přidejte následující příkazy `using`.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Přidat nezbytné pole

Do třídy **Program** přidejte následující pole. Tato data Určuje, jak se připojit ke službě pro rozpoznávání tváře a kde se stáhnout vstupní data. Budete muset aktualizovat `subscriptionKey` pole s hodnotou váš klíč předplatného a může být nutné změnit `faceEndpoint` řetězec tak, aby obsahoval identifikátor správné oblasti. Také budete muset nastavit `localImagePath` a/nebo `remoteImageUrl` hodnoty cest, které odkazují na skutečné soubory obrázků.

`faceAttributes` Pole je jednoduše pole určitých typů atributů. Určí které informace se mají načíst informace o zjištěných tváří.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>Vytváření a používání klienta pro rozpoznávání tváře

Dále přidejte následující kód, který **hlavní** metodu **Program** třídy. Tím se nastaví klientských rozhraní API pro rozpoznávání tváře.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

Také v **hlavní** metodu, přidejte následující kód pro rozpoznávání tváře klienta nově vytvořené služby používat k rozpoznávání tváří v obrázku vzdálené a místní. Metody zjišťování bude dále definici. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Do třídy **Program** přidejte následující metodu. Klient služby pro rozpoznávání tváře využívá k rozpoznávání tváří na vzdálené kopie, odkazovaná adresou URL. Všimněte si, že se používá `faceAttributes` pole&mdash; **DetectedFace** objekty přidané do `faceList` bude mít zadané atributy (v tomto případě stáří a pohlaví).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Podobně, přidejte **DetectLocalAsync** metody. Rozpoznávání tváří na místní image, odkazuje cestu k souboru pomocí klienta služby pro rozpoznávání tváře.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Načíst a zobrazit obličejových atributů

Dále definujte **GetFaceAttributes** metody. Vrátí řetězec s informacemi o příslušné atribut.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Nakonec definujte **DisplayAttributes** metody zapsat data. atribut pro rozpoznávání tváře do výstupu konzoly.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>Spuštění aplikace

Úspěšná odpověď zobrazí pohlaví a stáří pro každou tvář na obrázku. Příklad:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci .NET, můžete použít službu rozhraní API pro rozpoznávání tváře k rozpoznávání tváří na obrázcích místních i vzdálených. V dalším kroku kurzu podrobnější naleznete v tématu jak prezentovat informace pro rozpoznávání tváře uživateli v intuitivní.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace WPF ke zjišťování a analyzujte tváře v obrázku](../Tutorials/FaceAPIinCSharpTutorial.md)
