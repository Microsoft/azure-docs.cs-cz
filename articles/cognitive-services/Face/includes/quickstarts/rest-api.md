---
title: Rychlý Start REST API obličeje
description: Použijte REST API obličeje s kudrlinkou k detekci plošek, najděte podobné (vyhledávání obličejem na obrázku), identifikujte plošky (hledání rozpoznávání obličeje) a migrujte data na obličej.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 1d5a596efca2b1818a6d4ed4995904707a03dba0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193828"
---
Začněte s rozpoznáváním obličeje pomocí REST API obličeje. Služba Faceer poskytuje přístup k pokročilým algoritmům pro zjišťování a rozpoznávání lidských plošek na obrázcích.

REST API obličej použijte k těmto akcím:

* [Rozpoznávání tváří na obrázku](#detect-faces-in-an-image)
* [Hledání podobných plošek](#find-similar-faces)

> [!NOTE]
> V tomto rychlém startu se pomocí oblé příkazy zavolá REST API. REST API můžete volat také pomocí programovacího jazyka. Příklady najdete v ukázkách v [jazycích C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)a [Přejít](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest)na ukázky GitHubu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání tváře budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* [PowerShell verze 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)nebo podobná aplikace příkazového řádku.


## <a name="detect-faces-in-an-image"></a>Rozpoznávání tváří na obrázku

Použijete příkaz podobný následujícímu pro volání rozhraní API pro rozpoznávání tváře a získání dat atributu Face z obrázku. Nejprve zkopírujte kód do textového editoru &mdash; . před spuštěním příkazu je třeba provést změny určitých částí příkazu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Proveďte následující změny:
1. Přiřaďte `Ocp-Apim-Subscription-Key` svůj platný klíč předplatného obličeje.
1. Změňte první část adresy URL dotazu tak, aby odpovídala koncovému bodu, který odpovídá vašemu klíči předplatného.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Volitelně můžete změnit adresu URL v těle požadavku tak, aby odkazovala na jiný obrázek.

Po provedení změn otevřete příkazový řádek a zadejte nový příkaz. 

### <a name="examine-the-results"></a>Prozkoumání výsledků

V okně konzoly byste měli vidět informace o tváři jako data JSON. Například:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Získat atributy obličeje
 
Chcete-li extrahovat atributy obličeje, zavolejte rozhraní API detekce znovu, ale nastavte `detectionModel` na `detection_01` . Přidejte `returnFaceAttributes` také parametr dotazu. Příkaz by měl nyní vypadat takto. Stejně jako dřív vložte klíč předplatného a koncový bod pro svůj obličej.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Prozkoumání výsledků

Vracené informace o tváři teď obsahují atributy obličeje. Například:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
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
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
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
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Vyhledání podobných tváří

Tato operace bere jednu zjištěnou plošku (zdroj) a hledá v sadě dalších tváře (cíle) k vyhledání shody (vyhledávání na ploše podle obrázku). Pokud najde shodu, vytiskne ID spárovaného obličeje konzole.

### <a name="detect-faces-for-comparison"></a>Detekovat plošky pro porovnání

Nejdřív je potřeba detekovat plošky na obrázcích, abyste je mohli porovnat. Spusťte tento příkaz jako v části [detekovat plošky](#detect-faces-in-an-image) . Tato metoda detekce je optimalizována pro operace porovnání. Neextrahuje podrobné atributy obličeje jako v části výše a používá jiný model detekce.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Vyhledejte `"faceId"` hodnotu v odpovědi JSON a uložte ji do dočasného umístění. Pak znovu zavolejte výše uvedený příkaz pro tyto ostatní adresy URL obrázků a uložte také jejich ID obličeje. Tato ID použijete jako cílovou skupinu ploch, ze kterých se má najít podobná plocha.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Nakonec detekuje jeden zdrojový obličej, který budete používat pro porovnání, a uložte jeho ID. Nechte toto ID oddělené od ostatních.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Najít shody

Zkopírujte následující příkaz do textového editoru.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Pak proveďte následující změny:
1. Přiřaďte `Ocp-Apim-Subscription-Key` svůj platný klíč předplatného obličeje.
1. Změňte první část adresy URL dotazu tak, aby odpovídala koncovému bodu, který odpovídá vašemu klíči předplatného.

Pro tuto hodnotu použijte následující obsah JSON `body` :

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Použít zdrojové ID obličeje pro `"faceId"` .
1. Vložte další ID tváře jako podmínku v `"faceIds"` poli.

### <a name="examine-the-results"></a>Prozkoumání výsledků

Dostanete odpověď JSON, která obsahuje seznam ID obličeje, které odpovídají vaší obličeji dotazu.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak použít REST API obličeje k provádění základních úloh rozpoznávání obličeje. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Co je služba Rozpoznávání tváře?](../../overview.md)