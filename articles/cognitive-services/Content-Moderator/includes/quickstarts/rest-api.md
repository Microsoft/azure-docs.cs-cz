---
title: Rychlý Start Content Moderator REST API
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s Azure Content Moderator REST API. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: ebef33072b802ffc35b8c011d974dbcd203fa6e1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561473"
---
Začněte s REST API Azure Content Moderator. 

Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Použijte službu pro moderování obsahu s AI ke skenování textu, obrázku a videí a automatické použití příznaků obsahu. Potom Integrujte svoji aplikaci pomocí nástroje pro kontrolu online, což je prostředí moderátora pro tým pro lidské kontrolory. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.

REST API Content Moderator použijte k těmto akcím:

* Střední text
* Střední obrázky

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" vytvořte prostředek Content moderator vytvoření prostředku "  target="_blank"> Content Moderator <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Content Moderator budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.


## <a name="moderate-text"></a>Střední text

Použijete příkaz podobný následujícímu pro volání rozhraní API Content Moderator k analýze textu a k vytištění výsledků do konzoly.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Zkopírujte příkaz do textového editoru a proveďte následující změny:

1. Přiřaďte `Ocp-Apim-Subscription-Key` svůj platný klíč předplatného obličeje.
1. Změňte první část adresy URL dotazu tak, aby odpovídala koncovému bodu, který odpovídá vašemu klíči předplatného.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Volitelně můžete změnit text požadavku na libovolný řetězec textu, který chcete analyzovat.

Po provedení změn otevřete příkazový řádek a zadejte nový příkaz. 

### <a name="examine-the-results"></a>Prozkoumání výsledků

V okně konzoly by se měly zobrazit výsledky moderování textu jako data JSON. Například:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Další informace o atributech textu, které Content Moderator obrazovky pro, najdete v příručce [Koncepty pro moderování textu](../../text-moderation-api.md) .

## <a name="moderate-images"></a>Střední obrázky

Použijete příkaz podobný následujícímu pro volání rozhraní Content Moderator API pro střední vzdálenou bitovou kopii a k vytištění výsledků do konzoly.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Zkopírujte příkaz do textového editoru a proveďte následující změny:

1. Přiřaďte `Ocp-Apim-Subscription-Key` svůj platný klíč předplatného obličeje.
1. Změňte první část adresy URL dotazu tak, aby odpovídala koncovému bodu, který odpovídá vašemu klíči předplatného.
1. Volitelně můžete změnit `"Value"` adresu URL v textu požadavku na libovolnou vzdálenou image, kterou chcete střední.

> [!TIP]
> Střední místní image můžete také předat do textu žádosti. V [referenční dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) se dozvíte, jak to udělat.

Po provedení změn otevřete příkazový řádek a zadejte nový příkaz. 

### <a name="examine-the-results"></a>Prozkoumání výsledků

V okně konzoly byste měli vidět výsledky moderování obrázků, které se zobrazují jako data JSON. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Další informace o atributech obrázků, které Content Moderator obrazovky pro, najdete v příručce [Koncepty pro moderování imagí](../../image-moderation-api.md) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat REST API Content Moderator k provádění úloh moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

* [Koncepty moderování obrázků](../../image-moderation-api.md)
* [Koncepty moderování textu](../../text-moderation-api.md)
* [Co je Azure Content Moderator?](../../overview.md)