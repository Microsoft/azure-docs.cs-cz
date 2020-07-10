---
title: Optické rozpoznávání znaků (OCR) – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se optického rozpoznávání znaků (OCR) z obrázků a dokumentů s tištěným a ručně psaným textem pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 6bc118145bec30085c2d9fbf726c40a20b312430
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207036"
---
# <a name="optical-character-recognition-ocr"></a>optické rozpoznávání znaků (OCR),

Rozhraní API pro počítačové zpracování obrazu Microsoftu zahrnuje funkce optického rozpoznávání znaků (OCR), které extrahují vytištěný nebo rukou psaný text z obrázků a dokumentů PDF. Rozhraní API pro optické rozpoznávání písma extrahují text z obou obdobných dokumentů (obrázky, naskenované dokumenty) a dokumentů s více číslicemi. Můžete extrahovat text z obrázků v nevolném formátu, například fotografie z licenčních desek nebo kontejnerů se sériovými čísly a také z dokumentů – faktury, Bill, finanční sestavy, články a další. Nové rozhraní API pro čtení s ROZPOZNÁVÁNÍm je dostupné jako součást spravované služby v cloudu nebo místně (kontejnerech). Také podporuje virtuální sítě a soukromé koncové body, aby splňovaly požadavky na dodržování předpisů a ochranu osobních údajů vaší organizace.

## <a name="read-api"></a>Rozhraní API pro čtení 

[Rozhraní API pro čtení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) počítačové zpracování obrazu je nejnovější technologie OCR od Microsoftu, která extrahuje vytištěný text (sedm jazyků), psaný text (jenom v angličtině), číslice a symboly měny z obrázků a VÍCESTRÁNKOVÉ dokumenty PDF. Je optimalizovaná pro extrakci textu z textu ve velkém a vícestránkovém dokumentu PDF se smíšenými jazyky. Podporuje detekci vytištěného a rukopisného textu (jenom v angličtině) ve stejném obrázku nebo dokumentu. Podívejte se na stránku úplný seznam [podporovaných jazyků s podporou OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="how-ocr-works"></a>Jak funguje optické rozpoznávání znaků

[Rozhraní API pro čtení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) podporuje textově náročné dokumenty až na 2000 stránek, a proto se provádí asynchronně. Prvním krokem je zavolat operaci čtení. Operace čtení bere jako vstup obrázek nebo dokument PDF a vrátí ID operace. 

Druhým krokem je volání operace [Get Results](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Tato operace přebírá ID operace, kterou vytvořila operace čtení. Pak vrátí extrahovaný textový obsah z obrázku nebo dokumentu ve formátu JSON. Odpověď JSON uchovává původní spojnici rozpoznaných slov. Obsahuje extrahované textové řádky a jejich Souřadnice ohraničovacího rámečku. Každý textový řádek obsahuje všechna extrahovaná slova s jejich souřadnicemi a výsledky spolehlivosti.

V případě potřeby přečte aplikace správné otočení rozpoznané stránky tím, že vrátí posun otáčení ve stupních o vodorovné ose obrázků, jak je vidět na následujícím obrázku.

![Jak optické rozpoznávání znaků převede obrázky a dokumenty na strukturovaný výstup s extrahovaným textem](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Ukázka výstupu OCR

Ve formátu JSON se vrátí úspěšná odpověď, jak je znázorněno v následujícím příkladu:

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

Postupujte podle pokynů pro rychlý Start [vytisknutého a ručně psaného textu a](./QuickStarts/CSharp-hand-text.md) implementujte optické rozpoznávání znaků pomocí jazyka C# a REST API.

### <a name="input-requirements"></a>Požadavky na vstup

Rozhraní API pro čtení má následující vstupy:
* Podporované formáty souborů: JPEG, PNG, BMP, PDF a TIFF
* Pro PDF a TIFF se zpracují až 2000 stránek. Pro předplatitele bezplatné úrovně se zpracují jenom první dvě stránky.
* Velikost souboru musí být menší než 50 MB a rozměry aspoň 50 x 50 pixelů a maximálně 10000 x 10000 pixelů.
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají právním nebo a3 velikosti papíru a menšímu.

### <a name="text-from-images"></a>Text z obrázků

Následující výstup čtecího rozhraní API zobrazuje extrahované textové řádky a slova z obrázku s textem v různých úhlech, barvách a písmech.

![Otočení obrázku a jeho přečtení a vymezený text](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text z dokumentů

Kromě obrázků přebírá rozhraní API pro čtení jako vstup dokument PDF.

![Otočení obrázku a jeho přečtení a vymezený text](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Psaný text v angličtině

Nyní operace čtení podporuje extrakci rukopisného textu výhradně v angličtině.

![Otočení obrázku a jeho přečtení a vymezený text](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Vytištěný text v podporovaných jazycích

Rozhraní API pro čtení podporuje extrakci vytištěného textu v angličtině, španělštině, němčině, francouzštině, italštině, portugalštině a dánských jazycích. Pokud váš scénář vyžaduje podporu více jazyků, přečtěte si téma Přehled rozhraní OCR v tomto dokumentu. Podívejte se na seznam všech [jazyků podporovaných OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

![Otočení obrázku a jeho přečtení a vymezený text](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Podpora smíšených jazyků

Rozhraní API pro čtení podporuje obrázky a dokumenty s více jazyky, které se běžně označují jako smíšené jazykové dokumenty. Rozbalí každý textový řádek v dokumentu do zjištěného jazyka před extrahováním obsahu textu.

![Otočení obrázku a jeho přečtení a vymezený text](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu čtení, informace o zásadách Microsoftu pro zákaznická data. Další informace najdete na stránce Cognitive Services v [Centru zabezpečení Microsoftu](https://www.microsoft.com/en-us/trust-center/product-overview) .

### <a name="deploy-on-premises"></a>Nasazení na místě

Čtení je také k dispozici jako kontejner Docker (Preview), který vám umožní nasadit nové funkce OCR ve vlastním prostředí. Kontejnery jsou skvělé pro specifické požadavky zabezpečení a zásad správného řízení dat. Viz [Jak nainstalovat a spustit čtení kontejnerů.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ

[Rozhraní API pro rozpoznávání OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) používá starší model rozpoznávání, podporuje pouze image a provádí synchronní operace se zjištěným textem. Podívejte se na [jazyky podporované OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) , než je rozhraní API pro čtení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [REST API pro čtení 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Postupujte podle pokynů v rychlém startu pro [extrakci textu](./QuickStarts/CSharp-hand-text.md) a IMPLEMENTUJTE rozpoznávání OCR pomocí jazyků C#, Java, JavaScript nebo Python spolu s REST API.
