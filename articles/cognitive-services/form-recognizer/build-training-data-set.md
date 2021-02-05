---
title: Postup sestavení sady školicích dat pro vlastní model – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Naučte se, jak zajistit, aby byla sada dat pro školení optimalizovaná pro školení modelu pro rozpoznávání formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ee57ccb82e771ee8ab93b09e476a94df32278069
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585105"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Sestavení sady školicích dat pro vlastní model

Když použijete vlastní model pro rozpoznávání formulářů, poskytnete vlastní školicí data do operace [vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) , aby model mohl vytvořit výukové formuláře pro konkrétní odvětví. V této příručce se dozvíte, jak shromažďovat a připravovat data pro efektivní výuku modelu.

Potřebujete alespoň pět vyplněných tvarů stejného typu.

Pokud chcete použít manuálně popsání školicích dat, musíte začínat aspoň s 5 vyplněnými formuláři stejného typu. Kromě požadované datové sady můžete stále používat neoznačené formuláře.

## <a name="custom-model-input-requirements"></a>Požadavky na vstup vlastního modelu

Nejprve se ujistěte, že se vaše školicí data nastavují podle požadavků na vstup pro nástroj pro rozpoznávání formulářů.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Tipy k datům školení

Pomocí těchto dalších tipů můžete dále optimalizovat datovou sadu pro školení.

* Pokud je to možné, používejte textové dokumenty PDF namísto dokumentů na základě obrázků. Naskenované soubory PDF jsou zpracovávány jako obrázky.
* Pro vyplněné formuláře použijte příklady, které mají všechna jejich pole vyplněna.
* Používejte formuláře s různými hodnotami v každém poli.
* Pokud jsou obrázky z formuláře nižší kvality, použijte větší sadu dat (například obrázek 10-15).

## <a name="upload-your-training-data"></a>Nahrajte školicí data.

Když se spojíte se sadou dokumentů formuláře, které budete používat pro školení, je nutné ji nahrát do kontejneru úložiště objektů BLOB v Azure. Pokud si nejste jisti, jak vytvořit účet služby Azure Storage pomocí kontejneru, postupujte podle pokynů [pro rychlý start Azure Storage pro Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Použijte standardní úroveň výkonu.

Pokud chcete použít ručně označené údaje, budete také muset nahrát *.labels.js* a *.ocr.js* soubory, které odpovídají vašim školicím dokumentům. K vygenerování těchto souborů můžete použít [Nástroj pro označování ukázkových popisků](./quickstarts/label-tool.md) (nebo vlastní uživatelské rozhraní).

### <a name="organize-your-data-in-subfolders-optional"></a>Uspořádání dat v podsložkách (volitelné)

Ve výchozím nastavení budou rozhraní API pro [vlastní model výuky](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) používat jenom dokumenty formuláře, které jsou umístěné v kořenovém adresáři kontejneru úložiště. Můžete však s daty v podsložkách vlaky, pokud ji zadáte v volání rozhraní API. Obvykle tělo volání [vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) má následující formát, kde `<SAS URL>` je adresa URL sdíleného přístupového podpisu vašeho kontejneru:

```json
{
  "source":"<SAS URL>"
}
```

Pokud do textu žádosti přidáte následující obsah, rozhraní API bude zaškolit dokumenty umístěné v podsložkách. `"prefix"`Pole je volitelné a omezí data školicí sady na soubory, jejichž cesty začínají daným řetězcem. Hodnota, například, `"Test"` způsobí, že rozhraní API bude zobrazovat pouze soubory nebo složky, které začínají slovem "test".

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

Teď, když jste se naučili, jak vytvořit školicí sadu dat, postupujte podle rychlého startu a Naučte se vlastní model pro rozpoznávání formulářů a začněte ho používat na formulářích.

* [Výuka modelu a extrakce dat formuláře pomocí klientské knihovny nebo REST API](./quickstarts/client-library.md)
* [Výuka pomocí popisků pomocí ukázkového nástroje pro označování](./quickstarts/label-tool.md)

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)