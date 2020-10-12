---
title: Optické rozpoznávání znaků (OCR) – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se optického rozpoznávání znaků (OCR) obrázků a dokumentů s tištěným a ručně psaným textem pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cbcfddcd02a3998b3b35b01d386816735c59ae7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526404"
---
# <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (OCR)

Azure rozhraní API pro počítačové zpracování obrazu zahrnuje funkce optického rozpoznávání znaků (OCR), které extrahují vytištěný nebo rukou psaný text z obrázků. Můžete extrahovat text z obrázků, například fotky licenčních desek nebo kontejnerů se sériovými čísly a také z dokumentů – faktury, Bill, finanční sestavy, články a další.

## <a name="read-api"></a>Rozhraní API pro čtení 

[Rozhraní API pro počítačové zpracování obrazu Ready](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) je nejnovější technologie OCR Azure ([naučíme se, co je nového](./whats-new.md)), která extrahuje vytištěný text (v několika jazycích), psaný text (jenom v angličtině), číslice a symboly měny z obrázků a vícestránkové dokumenty PDF. Je optimalizovaná pro extrakci textu z textu s velkým obrázkem a vícestránkové dokumenty PDF se smíšenými jazyky. Podporuje detekci tiskového i rukopisného textu ve stejném obrázku nebo dokumentu.

![Jak optické rozpoznávání znaků převede obrázky a dokumenty na strukturovaný výstup s extrahovaným textem](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Požadavky na vstup
Volání **pro čtení** bere jako vstup obrázky a dokumenty. Mají následující požadavky:

* Podporované formáty souborů: JPEG, PNG, BMP, PDF a TIFF
* Pro soubory PDF a TIFF se zpracují až 2000 stránek (jenom první dvě stránky pro bezplatnou úroveň).
* Velikost souboru musí být menší než 50 MB (4 MB pro úroveň Free) a rozměry aspoň 50 x 50 pixelů a maximálně 10000 x 10000 pixelů. 
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají právním nebo a3 velikosti papíru a menšímu.

### <a name="read-31-preview-allows-selecting-pages"></a>Čtení 3,1 Preview umožňuje vybrat stránky.
S [rozhraním API Read 3,1 Preview](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)pro velké vícestránkové dokumenty můžete zadat konkrétní čísla stránek nebo rozsahy stránek jako vstupní parametr pro extrakci textu pouze z těchto stránek. Toto je nový vstupní parametr kromě volitelného parametru jazyka.

> [!NOTE]
> **Vstup jazyka** 
>
> [Volání metody Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) má volitelný parametr žádosti pro jazyk. Toto je kód jazyka BCP-47 textu v dokumentu. Čtení podporuje automatické identifikaci jazyka a vícejazyčné dokumenty, takže zadejte jenom kód jazyka, pokud chcete vynutit zpracování dokumentu jako u tohoto konkrétního jazyka.

## <a name="the-read-call"></a>Volání metody Read

[Volání](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) ČTECÍHO rozhraní API pro čtení přebírá obrázek nebo dokument PDF jako vstup a extrahuje text asynchronně. Volání se vrátí s polem hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID operace, která se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Fakturace** 
>
> Stránka s [cenami počítačové zpracování obrazu](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) zahrnuje cenovou úroveň pro čtení. Každý analyzovaný obrázek nebo stránka je jedna transakce. Pokud zavoláte operaci s dokumentem PDF nebo TIFF obsahujícím stránky 100, operace čtení se bude počítat jako 100 transakcí a bude se vám účtovat transakce 100. Pokud jste provedli 50 volání operace a každé volání odeslalo dokument s 100 stránky, bude se vám účtovat 50 X 100 = 5000 transakcí.

## <a name="the-get-read-results-call"></a>Volání Get výsledky čtení

Druhým krokem je volání operace [získat výsledky čtení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Tato operace přijímá jako vstup ID operace, kterou vytvořila operace čtení. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Pokud nechcete, aby se překročila sazba požadavků za sekundu (RPS), použijte interval 1 až 2 sekund.

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace nebyla spuštěna. |
| |  | spuštěno: operace se zpracovává. |
| |  | Nepovedlo se: operace se nezdařila. |
| |  | úspěch: operace byla úspěšná. |

> [!NOTE]
> Úroveň Free omezuje rychlost požadavků na 20 volání za minutu. Placená úroveň umožňuje 10 požadavků za sekundu (RPS), které se dají na vyžádání zvýšit. Použijte kanál podpory Azure nebo tým vašeho účtu k vyžádání vyšší sazby žádosti za sekundu (RPS).

Pokud má pole **stav** hodnotu **úspěch** , odpověď JSON obsahuje extrahovaný textový obsah z obrázku nebo dokumentu. Odpověď JSON uchovává původní spojnici rozpoznaných slov. Obsahuje extrahované textové řádky a jejich Souřadnice ohraničovacího rámečku. Každý textový řádek obsahuje všechna extrahovaná slova s jejich souřadnicemi a výsledky spolehlivosti.

## <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```
### <a name="read-31-preview-adds-text-line-style-latin-languages-only"></a>Čtení 3,1 Preview přidá styl čáry textu (jenom jazyky latinky).
[Rozhraní API pro čtení 3,1 Preview](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) vypisuje objekt **vzhledu** , který klasifikuje, jestli je každý textový řádek ve stylu tisk nebo psaní rukou, a také hodnocení spolehlivosti. Tato funkce je podporovaná jenom pro jazyky v latince.

```json
  "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.836
            }
```
Začněte s [počítačové zpracování obrazu rychlým startem pro rozpoznávání OCR](./quickstarts-sdk/client-library.md) a [rychlým startem pro čtení REST API](./QuickStarts/CSharp-hand-text.md) začněte integraci možností optického rozpoznávání OCR do vašich aplikací.

## <a name="supported-languages-for-print-text"></a>Podporované jazyky pro tisk textu
[Rozhraní API pro čtení 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) podporuje extrakci vytištěného textu v angličtině, španělštině, němčině, francouzštině, italštině, portugalštině a nizozemských jazycích.

Úplný seznam jazyků podporovaných OCR najdete v [podporovaných jazycích](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="read-31-preview-adds-simplified-chinese-and-japanese"></a>Čtení 3,1 Preview přidává zjednodušenou čínštinu a japonštinu
[Verze Public Preview pro čtení 3,1 API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) přidává podporu pro zjednodušenou čínštinu a japonštinu. Pokud váš scénář vyžaduje podporu více jazyků, přečtěte si část [rozhraní API pro optické rozpoznávání znaků](#ocr-api) . 

## <a name="supported-languages-for-handwritten-text"></a>Podporované jazyky pro rukopisný text
Operace čtení aktuálně podporuje extrakci rukopisného textu výhradně v angličtině.

## <a name="use-the-rest-api-and-sdk"></a>Použití REST API a sady SDK
[REST API pro čtení 3. x](./QuickStarts/CSharp-hand-text.md) je upřednostňovanou možností pro většinu zákazníků kvůli snadné integraci a rychlé produktivitě mimo pole. Azure a služba Počítačové zpracování obrazu obsluhují požadavky na škálování, výkon, zabezpečení dat a dodržování předpisů a přitom se soustředit na splnění potřeb vašich zákazníků.

## <a name="deploy-on-premise-with-docker-containers"></a>Nasazení v místním počítači pomocí kontejnerů Docker
[Kontejner Read 2,0 Docker (Preview)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) umožňuje nasadit nové možnosti optického rozpoznávání OCR ve vlastním místním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat.

## <a name="example-outputs"></a>Ukázkové výstupy

### <a name="text-from-images"></a>Text z obrázků

Následující výstup čtecího rozhraní API zobrazuje extrahovaný text z obrázku s různými úhly textu, barvami a písmy.

![Obrázek několika slov v různých barvách a úhlech se zobrazeným extrahovaným textem](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text z dokumentů

Rozhraní API pro čtení může také přebírat dokumenty PDF jako vstup.

![Dokument faktury s uvedeným extrahovaným textem](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Psaný text

Operace čtení extrahuje rukou psaný text z obrázků (aktuálně pouze v angličtině).

![Obrázek ručně psané poznámky se zobrazeným extrahovaným textem](./Images/handwritten-example.png)

### <a name="printed-text"></a>Vytištěný text

Operace čtení může extrahovat vytištěný text v několika různých jazycích.

![Obrázek španělského textbooku se zobrazeným extrahovaným textem](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Smíšené jazyky dokumentů

Rozhraní API pro čtení podporuje obrázky a dokumenty, které obsahují několik různých jazyků, které se běžně označují jako smíšené jazykové dokumenty. To funguje tak, že před extrakcí jeho textového obsahu klasifikuje každý textový řádek v dokumentu do zjištěného jazyka.

![Obrázek frází v několika jazycích se zobrazeným extrahovaným textem](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ

[Rozhraní API pro rozpoznávání OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) používá starší model rozpoznávání, podporuje pouze image a provádí synchronní operace se zjištěným textem. Podívejte se na [jazyky podporované OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) a pak číst rozhraní API.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by mohli vývojáři, kteří používají služby čtení/OCR, znát zásady Microsoftu u zákaznických dat. Další informace najdete na stránce Cognitive Services v [Centru zabezpečení Microsoftu](https://www.microsoft.com/trust-center/product-overview) .

> [!NOTE]
> Počítač vison 2,0 operace RecognizeText se v procesu získávání a vyřazení nového rozhraní API pro čtení popsaných v tomto článku. Stávající zákazníci by měli [Přejít na použití operací čtení](upgrade-api-versions.md).

## <a name="next-steps"></a>Další kroky

- Začínáme s nástrojem [počítačové zpracování obrazu čtení 3,0 SDK pro rychlé](./quickstarts-sdk/client-library.md) zprovoznění v jazycích C#, Java, JavaScript nebo Python.
- K získání informací o tom, jak používat rozhraní REST API, použijte příručku [pro rychlý Start pro čtení 3,0 REST API](./QuickStarts/CSharp-hand-text.md) v jazycích C#, Java, JavaScript nebo Python.
- Přečtěte si o [REST API pro čtení 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Přečtěte si o [REST API verze Public Preview pro čtení 3,1](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) s přidanou podporou pro zjednodušenou čínštinu a japonštinu.
