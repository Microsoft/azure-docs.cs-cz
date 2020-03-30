---
title: Kognitivní dovednosti detekce PII (náhled)
titleSuffix: Azure Cognitive Search
description: Extrahujte a maskujte osobně identifikovatelné informace z textu v kanálu obohacení v Azure Cognitive Search. Tato dovednost je v současné době ve verzi Public Preview.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298883"
---
#    <a name="pii-detection-cognitive-skill"></a>Kognitivní dovednosti detekce pii

> [!IMPORTANT] 
> Tato dovednost je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.

Dovednost **detekce pii** extrahuje osobně identifikovatelné informace ze vstupního textu a poskytuje možnost maskovat je z tohoto textu různými způsoby. Tato dovednost používá modely strojového učení poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službách Cognitive Services.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete data před odesláním do dovednosti rozdělit, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

Parametry rozlišují malá a velká písmena a všechny jsou volitelné.

| Název parametru     | Popis |
|--------------------|-------------|
| defaultLanguageCode |    Kód jazyka vstupního textu. Prozatím je `en` podporovánpouze. |
| minimumPřesnost | Hodnota mezi 0,0 a 1,0. Pokud je skóre spolehlivosti (ve `piiEntities` výstupu) `minimumPrecision` nižší než nastavená hodnota, entita není vrácena nebo maskována. Výchozí hodnota je 0,0. |
| maskovací režim | Parametr, který poskytuje různé způsoby maskování zjištěných piI ve vstupním textu. Podporovány jsou následující možnosti: <ul><li>`none`(výchozí): To znamená, že nebude provedeno `maskedText` žádné maskování a výstup nebude vrácen. </li><li> `redact`: Tato možnost odstraní zjištěné entity ze vstupního textu a nic nenahradí. Všimněte si, že v `piiEntities` tomto případě posun ve výstupu bude ve vztahu k původní text a ne maskovaný text. </li><li> `replace`: Tato možnost nahradí zjištěné entity znakem `maskingCharacter` uvedeným v parametru.  Znak se bude opakovat na délku zjištěné entity tak, aby posuny správně odpovídaly `maskedText`jak vstupnímu textu, tak výstupu .</li></ul> |
| maskováníZnak | Znak, který bude použit k maskovanétext, `maskingMode` pokud `replace`je parametr nastaven na . Podporovány jsou následující `*` možnosti: (výchozí), `#`, `X`. Tento parametr může `null` `maskingMode` být pouze `replace`v případě, že není nastavena na . |


## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název      | Popis                   |
|---------------|-------------------------------|
| languageCode    | Nepovinný parametr. Výchozí je `en`.  |
| text          | Text k analýze.          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu      | Popis                   |
|---------------|-------------------------------|
| piiEntities | Pole komplexních typů, které obsahuje následující pole: <ul><li>text (skutečné PII jako extrahované)</li> <li>type</li><li>Podtypu</li><li>skóre (Vyšší hodnota znamená, že je pravděpodobnější, že bude skutečnou entitou)</li><li>posun (do vstupního textu)</li><li>length</li></ul> </br> [Možné typy a podtypy naleznete zde.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskovaný text | Pokud `maskingMode` je nastavena na `none`jinou hodnotu než , bude tento výstup výsledkem maskování `maskingMode`provedeného na vstupním textu, jak je popsáno vybraným .  Pokud `maskingMode` je `none`nastavena na , tento výstup nebude k dispozici. |

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
##    <a name="sample-input"></a>Vstup vzorku

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

Všimněte si, že posuny vrácené pro entity ve výstupu této dovednosti jsou přímo vráceny z [rozhraní API analýzy textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), což znamená, že pokud je používáte k indexování do původního řetězce, měli byste použít třídu [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) v rozhraní .NET, abyste extrahovali správný obsah.  [Více informací naleznete zde.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Chyby a varovné případy
Pokud kód jazyka pro dokument není podporován, je vráceno upozornění a nejsou extrahovány žádné entity.
Pokud je text prázdný, zobrazí se upozornění.
Pokud je text větší než 50 000 znaků, bude analyzováno pouze prvních 50 000 znaků a bude vydáno upozornění.

Pokud dovednost vrátí upozornění, `maskedText` výstup může být prázdný.  To znamená, že pokud očekáváte, že výstup existovat pro vstup do pozdějšídovednosti, nebude fungovat tak, jak bylo zamýšleno. Mějte to na paměti při psaní definice skillset.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
