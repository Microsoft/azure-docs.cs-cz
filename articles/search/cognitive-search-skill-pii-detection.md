---
title: Vnímání zjistitelného rozpoznávání PII (Preview)
titleSuffix: Azure Cognitive Search
description: Extrakce a maskování identifikovatelné osobní údaje z textu v kanálu rozšíření v Azure Kognitivní hledání. Tato dovednost je aktuálně ve verzi Public Preview.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: bec993c2b59aa03195b78a02668baf3f5fac6695
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080751"
---
#    <a name="pii-detection-cognitive-skill"></a>Vnímání zjistitelnosti PII

> [!IMPORTANT] 
> Tato dovednost je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Dovednost **detekce PII** získává osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. PIIDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do dovednosti rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena a jsou volitelná.

| Název parametru     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |    Kód jazyka vstupního textu V současné době `en` se podporuje jenom. |
| `minimumPrecision` | Hodnota mezi 0,0 a 1,0. Pokud je výsledek spolehlivosti (ve `piiEntities` výstupu) nižší než hodnota nastavená `minimumPrecision` , entita se nevrátí ani nemaskuje. Výchozí hodnota je 0,0. |
| `maskingMode` | Parametr, který poskytuje různé způsoby, jak maskovat zjištěné PII ve vstupním textu. Podporovány jsou následující možnosti: <ul><li>`none`(výchozí): to znamená, že se neprovede žádné maskování a `maskedText` Výstup nebude vrácen. </li><li> `redact`: Tato možnost odebere zjištěné entity ze vstupního textu a nenahradí je cokoli. Všimněte si, že v tomto případě bude posun ve `piiEntities` výstupu ve vztahu k původnímu textu, a ne maskovanému textu. </li><li> `replace`: Tato možnost nahradí zjištěné entity znakem uvedeným v `maskingCharacter` parametru.  Znak se zopakuje na délku zjištěné entity, takže posuny budou správně odpovídat vstupnímu textu i výstupu `maskedText` .</li></ul> |
| `maskingCharacter` | Znak, který bude použit k maskování textu v případě, že `maskingMode` je parametr nastaven na hodnotu `replace` . Podporovány jsou následující možnosti: `*` (výchozí), `#` , `X` . Tento parametr může být pouze v `null` případě, že není `maskingMode` nastaven na hodnotu `replace` . |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Nepovinný parametr. Výchozí je `en`.  |
| `text`          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu      | Description                   |
|---------------|-------------------------------|
| `piiEntities` | Pole komplexních typů, které obsahují následující pole: <ul><li>text (skutečný PII jako extrahovaný)</li> <li>typ</li><li>Podtyp</li><li>skóre (vyšší hodnota znamená, že je pravděpodobnější, že se jedná o skutečnou entitu)</li><li>posun (do vstupního textu)</li><li>length</li></ul> </br> [Možné typy a podtypy lze nalézt zde.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| `maskedText` | Pokud `maskingMode` je hodnota nastavena na jinou hodnotu než `none` , bude tento výstup výsledkem řetězce maskování provedeného na vstupním textu, jak je popsáno ve vybraném `maskingMode` .  Pokud `maskingMode` je nastaveno na `none` , nebude tento výstup k dispozici. |

##    <a name="sample-definition"></a>Definice vzorku

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Vzorový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Ukázkový výstup

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Všimněte si, že posuny vracené pro entity ve výstupu této dovednosti jsou přímo vráceny z [rozhraní API pro analýzu textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), což znamená, že pokud je používáte k indexování do původního řetězce, měli byste použít třídu [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) v rozhraní .NET, aby bylo možné extrahovat správný obsah.  [Další podrobnosti najdete tady.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Případy chyb a varování
Pokud kód jazyka pro dokument není podporován, je vrácena upozornění a extrahovány žádné entity.
Pokud je text prázdný, bude se vytvářet upozornění.
Pokud je text větší než 50 000 znaků, analyzují se pouze prvních 50 000 znaků a bude vydáno upozornění.

Pokud dovednost vrátí upozornění, výstup `maskedText` může být prázdný.  To znamená, že pokud očekáváte, že výstup bude existovat pro vstup do pozdějších dovedností, nebude fungovat podle očekávání. Mějte na paměti, že při psaní definice dovednosti.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
