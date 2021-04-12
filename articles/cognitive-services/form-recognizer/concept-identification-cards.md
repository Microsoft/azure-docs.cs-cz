---
title: ID – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s extrakcí dat z dokumentů identity pomocí předem připravených rozhraní API pro rozpoznávání formulářů.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed8516f9a898131338fb5b4d75e25cd774c5ab43
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285349"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Model předem sestaveného identifikační karty (ID) pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů Azure může analyzovat a extrahovat informace z identifikačních karet pro státní správu (IDs) pomocí předpřipraveného modelu ID. Kombinuje naše výkonné funkce [optického rozpoznávání znaků (OCR)](../computer-vision/overview-ocr.md) s možnostmi rozpoznávání ID k extrakci klíčových informací z celosvětových cestovních pasů a licencí na ovladače USA (všechny 50 státy a D.C.). Rozhraní API ID extrahují klíčové informace z těchto dokumentů identity, jako je například křestní jméno, příjmení, datum narození, číslo dokumentu a další. Toto rozhraní API je k dispozici v nástroji pro rozpoznávání formulářů v 2.1 Preview jako na cloudovou službu a jako místní kontejner.

## <a name="what-does-the-id-service-do"></a>Co dělá služba ID? 

Služba představěných ID extrahuje klíčové hodnoty z celosvětových cestovních pasů a licencí pro ovladače USA a vrátí je do organizované strukturované odpovědi JSON. 

![Licence ukázkového ovladače](./media/id-example-drivers-license.JPG)

![Ukázka služby Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Extrahovaná pole

|Název| Typ | Description | Hodnota | 
|:-----|:----|:----|:----|
|  Země | country | Směrové číslo země kompatibilní se standardem ISO 3166 | UŽIVATELEM | 
|  DateOfBirth | date | DOB ve formátu RRRR-MM-DD | "1980-01-01" | 
|  DateOfExpiration | date | Datum vypršení platnosti ve formátu RRRR-MM-DD | "2019-05-05" |  
|  DocumentNumber | řetězec | Příslušné číslo služby Passport, číslo řidičského průkazu atd. | "340020013" |  
|  FirstName | řetězec | Extrahováný křestní jméno a iniciála, pokud je k dispozici | "JENNIFER" | 
|  LastName | řetězec | Extrahované příjmení | "BROOKS" |   
|  Provozovatel | country | Směrové číslo země kompatibilní se standardem ISO 3166 | UŽIVATELEM |
|  Sex | gender (pohlaví) | Možné extrahované hodnoty zahrnují "M", "F" a "X" | "F" | 
|  MachineReadableZone | object | Extrahování služby Passport MRZ, včetně dvou řádků 44 znaků | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | řetězec | Typ dokumentu, například pas, licence k ovladači | užívat |  
|  Adresa | řetězec | Extrahovaná adresa (jenom licence řidiče) | "123 ULICE ADRESA MĚSTO WA 99999-1234"|
|  Oblast | řetězec | Extrahovaná oblast, stát, provincie atd. (jenom licence řidiče) | Praze | 

### <a name="additional-features"></a>Další funkce

Rozhraní API pro identifikátory vrátí také následující informace:

* Úroveň spolehlivosti pole (každé pole vrátí přidruženou hodnotu spolehlivosti)
* Nezpracovaný text OCR (OCR – textový výstup extrahovaný pro celou příjem)
* Ohraničující pole všech extrahovaných polí v licencích ovladače USA
* Hraniční rámeček pro zónu čitelnou pro počítače (MRZ) ve službě Passports

  > [!NOTE]
  > Předem připravené identifikátory nezjišťují pravost ID.
  >
  > Předem vytvořená ID pro rozpoznávání formulářů extrahují klíčová data z dat ID. Nedetekuje ale platnost ani pravost původního dokumentu identity. 

## <a name="try-it-out"></a>Vyzkoušet

Chcete-li vyzkoušet službu ID rozpoznávání formulářů, použijte nástroj uživatelské rozhraní online Sample:

> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Podporované typy ID  

* **Předem sestavená ID v 2.1 – Preview. 3** Extrahuje klíčové hodnoty z celosvětových cestovních pasů a licencí pro americké ovladače. 

  > [!NOTE]
  > Podpora typu ID 
  >
  > Aktuálně podporované typy ID zahrnují celosvětovou licenci pro Passport a americké ovladače. Aktivně se snažíme rozšířit podporu ID na jiné dokumenty identity po celém světě.

## <a name="post-analyze-id-document"></a>Dokument po analýze ID

Operace [analyzovat ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) vezme obrázek nebo PDF s ID jako vstup a získá hodnoty zájmu. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Výsledek získání dokumentu analýzy ID

<!---
Need to update this with updated APIM links when available
-->

Druhým krokem je volání operace [**Get analyzovat IdDocument Result**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat ID. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace analýzy nebyla spuštěna. |
| |  | běží: probíhá operace analýzy. |
| |  | Nepovedlo se: operace analýzy se nezdařila. |
| |  | úspěch: operace analýzy byla úspěšná. |

Pokud má pole **stav** hodnotu **úspěch** , bude odpověď JSON zahrnovat porozumění příjmu a výsledky rozpoznávání textu. Výsledek ID je uspořádán jako slovník hodnot pojmenovaných polí, kde každá hodnota obsahuje extrahovaný text, normalizovanou hodnotu, ohraničovací rámeček, jistotu a odpovídající prvky slova. Výsledek rozpoznávání textu je uspořádán jako hierarchie řádků a slov s textem, ohraničujícím polem a informacemi o spolehlivosti.

![Ukázka výsledků pro příjem](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON: `readResults` uzel obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `documentResults`Uzel obsahuje hodnoty ID, které model zjistil. Tento uzel je také tam, kde najdete užitečné páry klíč/hodnota jako křestní jméno, příjmení, číslo dokumentu a další.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si vlastní ID a ukázky v [ukázkovém uživatelském rozhraní pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/).
- Přihlaste se k [rychlému startu pro rozpoznávání formulářů](quickstarts/client-library.md) a začněte psát aplikaci zpracovávající ID pomocí nástroje pro rozpoznávání formulářů ve vývojovém jazyce podle vašeho výběru.

## <a name="see-also"></a>Viz také

* [**Co je služba Rozpoznávání formulářů?**](./overview.md)
* [**Referenční dokumentace REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
