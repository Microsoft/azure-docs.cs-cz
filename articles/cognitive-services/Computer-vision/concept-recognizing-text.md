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
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979523"
---
# <a name="optical-character-recognition-ocr"></a>optické rozpoznávání znaků (OCR),

Azure rozhraní API pro počítačové zpracování obrazu zahrnuje funkce optického rozpoznávání znaků (OCR), které extrahují vytištěný nebo rukou psaný text z obrázků. Můžete extrahovat text z obrázků, například fotky licenčních desek nebo kontejnerů se sériovými čísly a také z dokumentů – faktury, Bill, finanční sestavy, články a další.

## <a name="read-api"></a>Rozhraní API pro čtení 

[Rozhraní API pro počítačové zpracování obrazu Ready](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) je nejnovější technologie OCR Azure ([naučíme se, co je nového](./whats-new.md)), která extrahuje vytištěný text (v několika jazycích), psaný text (jenom v angličtině), číslice a symboly měny z obrázků a vícestránkové dokumenty PDF. Je optimalizovaná pro extrakci textu z textu s velkým obrázkem a vícestránkové dokumenty PDF se smíšenými jazyky. Podporuje detekci tiskového i rukopisného textu ve stejném obrázku nebo dokumentu.

![Jak optické rozpoznávání znaků převede obrázky a dokumenty na strukturovaný výstup s extrahovaným textem](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Požadavky na vstup
Volání **pro čtení** bere jako vstup obrázky a dokumenty. Mají následující požadavky:

* Podporované formáty souborů: JPEG, PNG, BMP, PDF a TIFF
* Pro soubory PDF a TIFF se zpracují až 2000 stránek (jenom první dvě stránky pro bezplatnou úroveň).
* Velikost souboru musí být menší než 50 MB (4 MB pro úroveň Free) a rozměry aspoň 50 x 50 pixelů a maximálně 10000 x 10000 pixelů. 
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají právním nebo a3 velikosti papíru a menšímu.

> [!NOTE]
> **Vstup jazyka** 
>
> [Volání metody Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) má volitelný parametr žádosti pro jazyk. Čtení podporuje automatické identifikaci jazyka a vícejazyčné dokumenty, takže zadejte jenom kód jazyka, pokud chcete vynutit zpracování dokumentu jako u tohoto konkrétního jazyka.

## <a name="ocr-demo-examples"></a>Ukázka optického rozpoznávání znaků (příklady)

![Ukázky OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Krok 1: operace čtení

[Volání](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ČTECÍHO rozhraní API pro čtení přebírá obrázek nebo dokument PDF jako vstup a extrahuje text asynchronně. Volání se vrátí s polem hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID operace, která se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Fakturace** 
>
> Stránka s [cenami počítačové zpracování obrazu](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) zahrnuje cenovou úroveň pro čtení. Každý analyzovaný obrázek nebo stránka je jedna transakce. Pokud zavoláte operaci s dokumentem PDF nebo TIFF obsahujícím stránky 100, operace čtení se bude počítat jako 100 transakcí a bude se vám účtovat transakce 100. Pokud jste provedli 50 volání operace a každé volání odeslalo dokument s 100 stránky, bude se vám účtovat 50 X 100 = 5000 transakcí.

## <a name="step-2-the-get-read-results-operation"></a>Krok 2: operace získání výsledků čtení

Druhým krokem je volání operace [získat výsledky čtení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Tato operace přijímá jako vstup ID operace, kterou vytvořila operace čtení. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Pokud nechcete, aby se překročila sazba požadavků za sekundu (RPS), použijte interval 1 až 2 sekund.

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace nebyla spuštěna. |
| |  | spuštěno: operace se zpracovává. |
| |  | Nepovedlo se: operace se nezdařila. |
| |  | úspěch: operace byla úspěšná. |

> [!NOTE]
> Úroveň Free omezuje rychlost požadavků na 20 volání za minutu. Placená úroveň umožňuje 10 požadavků za sekundu (RPS), které se dají na vyžádání zvýšit. Použijte kanál podpory Azure nebo tým vašeho účtu k vyžádání vyšší sazby žádosti za sekundu (RPS).

Pokud má pole **stav** hodnotu **úspěch** , odpověď JSON obsahuje extrahovaný textový obsah z obrázku nebo dokumentu. Odpověď JSON uchovává původní spojnici rozpoznaných slov. Obsahuje extrahované textové řádky a jejich Souřadnice ohraničovacího rámečku. Každý textový řádek obsahuje všechna extrahovaná slova s jejich souřadnicemi a výsledky spolehlivosti.

> [!NOTE]
> Data odeslaná do `Read` operace jsou dočasně zašifrovaná a uložená po krátké době a pak se odstraní. To umožňuje aplikacím získat extrahovaný text jako součást odpovědi služby.

## <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
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

## <a name="natural-reading-order-output"></a>Přirozený výstup pořadí čtení
Pomocí [rozhraní API pro čtení 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)určete pořadí, ve kterém jsou textové řádky ve výstupu s `readingOrder` parametrem dotazu. Použijte `natural` pro více uživatelsky přívětivého výstupu pořadí čtení, jak je znázorněno v následujícím příkladu.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Příklad pořadí čtení v OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Ručně psaná klasifikace pro textové řádky (jenom latinku)
Odpověď [rozhraní API pro čtení 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) zahrnuje klasifikaci, jestli každý textový řádek má styl rukopisu, nebo ne, spolu s hodnocením spolehlivosti. Tato funkce je podporovaná jenom pro jazyky v latince. Následující příklad ukazuje ručně vytvořenou klasifikaci textu v obrázku.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Příklad klasifikace rukopisu OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Vybrat stránky nebo rozsahy stránek pro extrakci textu
S [rozhraním API pro čtení 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)pro velké vícestránkové dokumenty použijte `pages` parametr dotazu, který určuje čísla stránek nebo rozsahy stránek k extrakci textu pouze z těchto stránek. Následující příklad ukazuje dokument s 10 stránkami a text extrahovaný pro oba případy – všechny stránky (1-10) a vybrané stránky (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Výstup vybraných stránek":::

## <a name="supported-languages"></a>Podporované jazyky
Rozhraní API pro čtení podporují pro text stylu tisku celkem 73 jazyků. Podívejte se na úplný seznam [jazyků podporovaných rozpoznáváním OCR](./language-support.md#optical-character-recognition-ocr). Rukopisné optické rozpoznávání znaků je podporováno výhradně pro angličtinu.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Použití cloudového rozhraní API nebo nasazení v místním systému
Cloudová rozhraní API pro čtení 3. x jsou upřednostňovanou možností pro většinu zákazníků kvůli snadné integraci a rychlé produktivitě mimo pole. Azure a služba Počítačové zpracování obrazu obsluhují požadavky na škálování, výkon, zabezpečení dat a dodržování předpisů a přitom se soustředit na splnění potřeb vašich zákazníků.

Pro místní nasazení vám [kontejner pro čtení Docker (Preview)](./computer-vision-how-to-install-containers.md) umožňuje nasadit nové možnosti optického rozpoznávání OCR ve vlastním místním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat.

## <a name="ocr-api"></a>ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ

[Rozhraní API pro rozpoznávání OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) používá starší model rozpoznávání, podporuje pouze image a provádí synchronní operace se zjištěným textem. Podívejte se na [jazyky podporované OCR](./language-support.md#optical-character-recognition-ocr) a pak číst rozhraní API.

> [!NOTE]
> Počítač vison 2,0 operace RecognizeText se v procesu získávání a vyřazení nového rozhraní API pro čtení popsaných v tomto článku. Stávající zákazníci by měli [Přejít na použití operací čtení](upgrade-api-versions.md).

## <a name="next-steps"></a>Další kroky

- Začněte s [Počítačové zpracování obrazu REST API nebo se šablonou rychlý Start knihovny klienta](./quickstarts-sdk/client-library.md).
- Přečtěte si o [REST API pro čtení 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Přečtěte si o [REST API verze Public Preview pro čtení 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) s podporou celkem 73 jazyků.
