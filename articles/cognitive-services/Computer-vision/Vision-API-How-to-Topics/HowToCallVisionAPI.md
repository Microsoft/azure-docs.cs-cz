---
title: Volání rozhraní API pro analýzu obrázků
titleSuffix: Azure Cognitive Services
description: Naučte se volat rozhraní API pro analýzu obrázků a nakonfigurovat jeho chování.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012264"
---
# <a name="call-the-image-analysis-api"></a>Volání rozhraní API pro analýzu obrázků

Tento článek ukazuje, jak volat rozhraní API pro analýzu obrázků a vracet informace o vizuálních funkcích obrázku.

Tato příručka předpokládá, že jste už <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořili prostředek počítačové zpracování obrazu "  target="_blank"> vytvořit prostředek počítačové zpracování obrazu </a> a získali klíč předplatného a adresu URL koncového bodu. Pokud jste to ještě neudělali, začněte pomocí [rychlého](../quickstarts-sdk/image-analysis-client-library.md) startu.
  
## <a name="submit-data-to-the-service"></a>Odeslat data do služby

Do rozhraní API pro analýzu můžete odeslat buď místní bitovou kopii, nebo vzdálenou bitovou kopii. V části místní umístěte binární data obrázku do textu požadavku HTTP. Pro vzdálené úložiště zadáte adresu URL obrázku tak, že naformátujete text žádosti, třeba takto: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Určení způsobu zpracování dat

###  <a name="select-visual-features"></a>Vybrat funkce vizuálu

[Rozhraní API pro analýzu](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) umožňuje přístup ke všem funkcím pro analýzu imagí služby. Musíte určit, které funkce chcete použít, nastavením parametrů dotazu URL. Parametr může mít více hodnot oddělených čárkami. Každá zadaná funkce bude vyžadovat další dobu výpočtu, takže určete pouze to, co potřebujete.

|Parametr adresy URL | Hodnota | Popis|
|---|---|--|
|`visualFeatures`|`Adult` | zjišťuje, jestli je obrázek pornografickýý v podstatě (znázorňuje nahotu nebo sex), nebo je gorie (znázorňuje extrémní násilí nebo krev). Zjistil se také zřejmý sugestivní obsah (neboli obsah pikantní).|
||`Brands` | detekuje různé značky v rámci obrázku, včetně přibližného umístění. Argument značky je k dispozici pouze v angličtině.|
||`Categories` | kategorizuje obsah obrázků podle taxonomie definované v dokumentaci. Toto je výchozí hodnota `visualFeatures` .|
||`Color` | Určuje barvu zvýraznění, dominantní barvu a, zda je obrázek černý&bílá.|
||`Description` | popisuje obsah obrázku s úplnou větou v podporovaných jazycích.|
||`Faces` | zjišťuje, zda jsou k dispozici plošky. Pokud je k dispozici, vygenerujte souřadnice, pohlaví a stáří.|
||`ImageType` | detekuje, zda je obrázek klipartem nebo kreslením čáry.|
||`Objects` | detekuje různé objekty v rámci obrázku, včetně přibližného umístění. Argument Objects je k dispozici pouze v angličtině.|
||`Tags` | Taguje obrázek pomocí podrobného seznamu slov souvisejících s obsahem obrázku.|
|`details`| `Celebrities` | Identifikuje celebrit, pokud je v imagi zjištěný.|
||`Landmarks` |Identifikuje orientačních bodů, pokud jsou v imagi zjištěny.|

Vyplněná adresa URL může vypadat takto:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Zadat jazyky

Můžete také zadat jazyk vrácených dat. Následující parametr dotazu adresy URL určuje jazyk. Výchozí hodnota je `en`.

|Parametr adresy URL | Hodnota | Popis|
|---|---|--|
|`language`|`en` | Angličtina|
||`es` | Španělština|
||`ja` | Japonština|
||`pt` | Portugalština|
||`zh` | Zjednodušená čínština|

Vyplněná adresa URL může vypadat takto:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Vymezená volání rozhraní API**
>
> Některé funkce v analýze obrázků lze vyvolat přímo a také prostřednictvím volání funkce analyzovat rozhraní API. Můžete například provést vymezenou analýzu pouze značek obrázku vytvořením žádosti na `https://{endpoint}/vision/v3.2-preview.3/tag` . Další funkce, které je možné volat samostatně, najdete v [referenční dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) .

## <a name="get-results-from-the-service"></a>Získat výsledky ze služby

Služba vrátí `200` odpověď HTTP a tělo obsahuje vrácená data ve formě řetězce JSON. Následuje příklad odpovědi JSON.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

V následující tabulce najdete vysvětlení polí v tomto příkladu:

Pole | Typ | Content
------|------|------|
Značky  | `object` | Objekt nejvyšší úrovně pro pole značek.
tags[].Name | `string`    | Klíčové slovo z třídění značek.
tags[].Score    | `number`    | Hodnocení spolehlivosti mezi 0 a 1.
description     | `object`    | Objekt nejvyšší úrovně pro popis obrázku
description.tags[] |    `string`    | Seznam značek. Pokud není dostatečná důvěra v možnosti vytvoření titulku, mohou být značky jedinou informací, které jsou k dispozici volajícímu.
description.captions[].text    | `string`    | Výraz, který popisuje obrázek.
description.captions[].confidence    | `number`    | Skóre spolehlivosti fráze.

### <a name="error-codes"></a>Kódy chyb

Podívejte se na následující seznam možných chyb a jejich příčiny:

* 400
    * InvalidImageUrl-adresa URL obrázku je chybně naformátovaná nebo není přístupná.
    * InvalidImageFormat – vstupní data nejsou platným obrázkem.
    * InvalidImageSize-vstupní obrázek je příliš velký.
    * Typ funkce zadaný v NotSupportedVisualFeature není platný.
    * NotSupportedImage – Nepodporovaná image, například podřízená pornografie.
    * InvalidDetails – Nepodporovaná `detail` hodnota parametru
    * NotSupportedLanguage – požadovaná operace není v zadaném jazyce podporována.
    * BadArgument – chybová zpráva uvádí další podrobnosti.
* 415 – chyba typu média není podporována. Typ Content-Type není v povolených typech:
    * Pro adresu URL obrázku: typ obsahu by měl být Application/JSON.
    * Pro binární data obrázku: typ obsahu by měl být Application/oktet-Stream nebo multipart/form-data.
* 500
    * FailedToProcess
    * Časový limit – vypršel časový limit zpracování bitové kopie.
    * InternalServerError

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet REST API, přečtěte si [referenční informace k rozhraní API pro analýzu imagí](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
