---
title: 'Rychlý Start: detekce plošek v imagi pomocí REST API Azure a C #'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API Azure Face v jazyce C# k detekci ploch v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 1b8bcaf283e612b3ebe6d6b7bb5660e8b3179ad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858366"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Rychlý Start: detekce plošek v obrázku pomocí REST API obličeje a C #

V tomto rychlém startu použijete REST API Azure Face v jazyce C# k detekci lidských plošek v obrázku.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
- Libovolná edice sady [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V aplikaci Visual Studio vytvořte nový projekt **konzolové aplikace (.NET Framework)** a pojmenujte ho **FaceDetection**.
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.

## <a name="add-face-detection-code"></a>Přidat kód pro detekci obličeje

Otevřete soubor *program.cs* nového projektu. Zde přidáte kód potřebný k načtení obrázků a detekci ploch.

### <a name="include-namespaces"></a>Zahrnutí oborů názvů

Na začátek souboru *Program.cs* přidejte následující příkazy `using`.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Přidat důležitá pole

Přidejte třídu **program** obsahující následující pole. Tato data určují, jak se připojit ke službě obličeje a kde získat vstupní data. Budete muset aktualizovat `subscriptionKey` pole hodnotou klíče předplatného a možná budete muset změnit `uriBase` řetězec tak, aby obsahoval řetězec koncového bodu prostředku.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Příjem vstupu z obrázku

Do metody **Main** třídy **program** přidejte následující kód. Tento kód zapíše výzvu do konzoly s výzvou, aby uživatel zadal svou místní cestu k souboru bitové kopie. Pak volá jinou metodu, **MakeAnalysisRequest**a zpracovává image v tomto umístění.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Volání detekce obličeje REST API

Do třídy **Program** přidejte následující metodu. Vytvoří volání REST do Face API k detekci informací o obličejích ve vzdálené imagi ( `requestParameters` řetězec Určuje, které atributy obličeje mají být načteny). Pak zapíše výstupní data do řetězce JSON.

Pomocná metoda bude definována v následujících krocích.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>Zpracování dat vstupní bitové kopie

Do třídy **Program** přidejte následující metodu. Tato metoda převede obrázek v zadané cestě k souboru na bajtové pole.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Do třídy **Program** přidejte následující metodu. Tato metoda formátuje vstup JSON tak, aby byl snadněji čitelný. Vaše aplikace bude zapisovat tato řetězcová data do konzoly. Pak můžete zavřít třídu a obor názvů.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Spuštění aplikace

Úspěšná odpověď zobrazí tvářená data ve formátu JSON, který bude snadno čitelný. Příklad:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>Extrahovat atributy obličeje
 
Chcete-li extrahovat atributy obličeje, použijte model detekce 1 a přidejte `returnFaceAttributes` parametr dotazu.

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

Odpověď teď obsahuje atributy obličeje. Příklad:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci .NET, která používá volání REST se službou Azure Face k detekci plošek v imagi a vrácení jejich atributů. Dále si Projděte referenční dokumentaci Face API, kde najdete další informace o podporovaných scénářích.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
