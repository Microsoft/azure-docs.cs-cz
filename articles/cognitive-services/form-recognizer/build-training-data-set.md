---
title: Jak vytvořit trénovací datovou sadu pro vlastní model - Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak zajistit, aby byla vaše trénovací sada dat optimalizovaná pro trénování modelu rozpoznávání formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380622"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Vytvoření trénovací datové sady pro vlastní model

Při použití vlastního modelu nástroje pro rozpoznávání formulářů poskytnete vlastní trénovací data, aby mohl model trénovat do formulářů specifických pro dané odvětví. Můžete trénovat model s pěti vyplněnými formuláři nebo prázdným formulářem (v názvu souboru musíte uvést slovo "prázdný" plus dva vyplněné formuláře. I v případě, že máte dostatek vyplněných formulářů pro trénování, přidání prázdného formuláře do sady dat školení může zlepšit přesnost modelu.

Pokud chcete použít ručně označená trénovací data, měli byste začít s alespoň pěti formami stejného typu. Ve stejné sadě dat můžete stále používat formuláře bez popisků a prázdný formulář.

## <a name="training-data-tips"></a>Tipy pro trénování dat

Je důležité použít sadu dat, která je optimalizovaná pro školení. Pomocí následujících tipů zajistíte, že z operace [Vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) dosáhnete nejlepších výsledků:

* Pokud je to možné, použijte místo dokumentů založených na obrazech textové dokumenty PDF. Naskenované pdf se zpracovávají jako obrazy.
* Pro vyplněné formuláře použijte příklady, ve kterých jsou vyplněna všechna pole.
* Používejte formuláře s různými hodnotami v každém poli.
* Pokud jsou obrázky formuláře nižší kvality, použijte větší sadu dat (například 10–15 obrázků).
* Celková velikost trénovací datové sady může být až 500 stránek.

## <a name="general-input-requirements"></a>Obecné vstupní požadavky

Ujistěte se, že vaše trénovací sada dat také splňuje vstupní požadavky pro veškerý obsah nástroje pro rozpoznávání formulářů. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Nahrání tréninkových dat

Když dáte dohromady sadu dokumentů formuláře, které budete používat pro školení, musíte ji nahrát do kontejneru úložiště objektů blob Azure. Pokud nevíte, jak vytvořit účet úložiště Azure s kontejnerem, postupujte podle [rychlého startu služby Azure Storage pro portál Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Uspořádání dat do podsložek (volitelné)

Ve výchozím nastavení bude rozhraní [API vlastního modelu trénovat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) pouze dokumenty formuláře, které jsou umístěny v kořenovém adresáři kontejneru úložiště. Můžete však trénovat s daty v podsložkách, pokud je zadáte ve volání rozhraní API. Za normálních okolností tělo [volání vlastního modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) `<SAS URL>` vlaku má následující formulář, kde je adresa URL podpisu sdílený přístup vašeho kontejneru:

```json
{
  "source":"<SAS URL>"
}
```

Pokud přidáte následující obsah do těla požadavku, rozhraní API bude trénovat s dokumenty umístěnými v podsložkách. Pole `"prefix"` je volitelné a omezí sadu trénovacích dat na soubory, jejichž cesty začínají daným řetězcem. Takže hodnota `"Test"`, například způsobí, že API se podívat pouze na soubory nebo složky, které začínají slovem "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit trénovací sadu dat, postupujte podle rychlého startu trénovat vlastní model nástroje pro rozpoznávání formulářů a začněte jej používat ve formulářích.

* [Úvodní příručka: Trénování modelu a extrahování dat formuláře pomocí cURL](./quickstarts/curl-train-extract.md)
* [Úvodní příručka: Trénování modelu a extrahování dat formuláře pomocí rozhraní REST API s Pythonem](./quickstarts/python-train-extract.md)
* [Trénování s popisky s využitím REST API a Pythonu](./quickstarts/python-labeled-data.md)