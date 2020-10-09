---
title: 'Rychlý Start: detekce ploch v imagi pomocí REST API Azure a Java'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API Azure Face a Java k detekci ploch v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fbe62cf00422710e18a6b112adc08f19ea03177b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858349"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí rozhraní REST API a Javy

V tomto rychlém startu použijete REST API Azure Face a Java k detekci lidských plošek v obraze.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Jakékoli Java IDE podle vašeho výběru.

## <a name="create-the-java-project"></a>Vytvoření projektu Java

1. Vytvořte novou aplikaci příkazového řádku Java v integrovaném vývojovém prostředí a přidejte do ní **Hlavní** třídu s metodou **Main** .
1. Následující knihovny naimportujte do projektu Java. Pokud používáte Maven, jsou k dispozici souřadnice Maven pro každou knihovnu.
   - [Klient Apache HTTP](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.6)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [Knihovna JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Protokolování pro Apache](https://commons.apache.org/proper/commons-logging/download_logging.cgi) -nahlašování (pro čtení z více protokolů): přečtení z protokolu: 1.1.2)

## <a name="add-face-detection-code"></a>Přidat kód pro detekci obličeje

Otevřete hlavní třídu projektu. Zde přidáte kód potřebný k načtení obrázků a detekci ploch.

### <a name="import-packages"></a>Import balíčků

`import`Na začátek souboru přidejte následující příkazy.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Přidat důležitá pole

Nahraďte **Hlavní** třídu následujícím kódem. Tato data určují, jak se připojit ke službě obličeje a kde získat vstupní data. Budete muset aktualizovat `subscriptionKey` pole hodnotou klíče předplatného a změnit `uriBase` řetězec tak, aby obsahoval správný řetězec koncového bodu. Můžete také chtít nastavit `imageWithFaces` hodnotu na cestu, která odkazuje na jiný soubor obrázku.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

`faceAttributes`Pole je jednoduše seznamem určitých typů atributů. Určí, které informace se mají načíst o zjištěných plochách.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Volání detekce obličeje REST API

Přidejte metodu **Main** s následujícím kódem. Vytvoří volání REST do Face API k detekci informací o obličejích ve vzdálené imagi ( `faceAttributes` řetězec Určuje, které atributy obličeje mají být načteny). Pak zapíše výstupní data do řetězce JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Přímo pod předchozím kódem přidejte následující blok, který převede vracená data JSON do čitelnějšího formátu před jeho tiskem do konzoly. Nakonec zavřete blok try-catch, metodu **Main** a **hlavní** třídu.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Spuštění aplikace

Zkompilujte kód a spusťte jej. Úspěšná odpověď zobrazí tvářená data v snadno čitelném formátu JSON v okně konzoly. Příklad:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Extrahovat atributy obličeje
 
Chcete-li extrahovat atributy obličeje, použijte model detekce 1 a přidejte `returnFaceAttributes` parametr dotazu.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

Odpověď teď obsahuje atributy obličeje. Příklad:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci v jazyce Java, která používá volání REST k Azure Face API k detekci ploch v obrázku a vrácení jejich atributů. Dále si Projděte referenční dokumentaci Face API, kde najdete další informace o podporovaných scénářích.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
