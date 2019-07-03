---
title: Vytvoření trénovací datové sady pro vlastní model - Rozlišovač formuláře
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak zajistit vaše trénovací datové sady je optimalizovaná pro trénování modelu Rozlišovač formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ad9bba53390e3c4262f999ebcc57ab354f1e3d69
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537633"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Vytvoření trénovací datové sady pro vlastní model

Při použití vlastního modelu Rozlišovač formuláře zadejte cvičných dat tak můžete do formulářů specifických pro dané odvětví trénování modelu. Navíc dva formuláře vyplněné můžete vyškolíme model pomocí pěti formuláře vyplněné nebo prázdný formulář (musí obsahovat slovo "prázdný" v názvu souboru). I v případě, že máte dostatek formuláře vyplněné trénování s, přidání prázdný formulář do vaší trénovací datové sady můžete zlepšit přesnost modelu.

## <a name="training-data-tips"></a>Školení datových tipech

Je důležité používat datové sady, která je optimalizovaná pro vzdělávání. Použijte následující tipy k zajistěte, aby co nejlépe z [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operace:

* Pokud je to možné používejte textové dokumenty PDF místo dokumentů na základě bitové kopie. Prohledaná PDF jsou zpracovávány jako bitové kopie.
* Pokud je k dispozici máte použijte jeden prázdný formulář a dva formuláře vyplněné.
* Pro formuláře vyplněné použijte příklady, které mají na všech jejich pole vyplněna.
* Použití s různými hodnotami v každé pole formulářů.
* Pokud váš obrázky formuláře jsou nižší kvality, použijte větší datové sady (například obrázky 10 až 15).

## <a name="general-input-requirements"></a>Obecné požadavky na vstupu

Zajistěte, aby že vaše trénovací datové sady také řídí vstupní požadavky pro veškerý obsah formuláře Rozlišovače. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Nahrát trénovacích dat

Pokud jste sestavili sadu dokumentů formuláře, které budete používat pro školení, budete muset nahrát do kontejneru úložiště objektů blob v Azure. Pokud si nejste jisti, jak vytvořit účet služby Azure storage s kontejnerem, následující [rychlý start Azure Storage k webu Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Uspořádání dat v podsložkách (volitelné)

Ve výchozím nastavení [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) rozhraní API se použije pouze formulář dokumenty, které jsou umístěné v kořenovém adresáři vašeho kontejneru úložiště. Však můžete tréninku s daty v podsložkách-li zadat ve volání rozhraní API. Za normálních okolností text [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) volání má následující formát, ve kterém `<SAS URL>` je adresa URL pro podpis sdíleného přístupu vašeho kontejneru:

```json
{
  "source":"<SAS URL>"
}
```

Pokud přidáte následující obsah do textu žádosti rozhraní API bude trénování s dokumenty, které jsou umístěny v podsložkách. `"prefix"` Pole je volitelné a omezí trénovací datové sady do souborů, jejichž cesty začínají daným řetězcem. Takže hodnota `"Test"`, například způsobí, že rozhraní API se podívat na soubory nebo složky, které začínají znakem slova "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak vytvářet trénovací datové sady, postupujte podle rychlého startu trénování modelu vlastní formulář rozpoznávání a začít používat ve formulářích.

* [Rychlé zprovoznění: Trénování modelu a extrahovat data formuláře pomocí cURL](./quickstarts/curl-train-extract.md)
* [Rychlé zprovoznění: Trénování modelu a extrahování dat formuláře pomocí rozhraní REST API s využitím Pythonu](./quickstarts/python-train-extract.md)

