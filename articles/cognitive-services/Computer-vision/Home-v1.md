---
title: Computer Vision API na služby Microsoft Cognitive Services | Dokumentace Microsoftu
description: Pomocí pokročilých algoritmů v rozhraní API pro počítačové zpracování obrazu umožňují zpracování obrázků a vrátí informace o Microsoft Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 84d931ad79bf32b39a4d771f6afd1c9a05ad2395
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714815"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co je počítač Vision API verze 1.0?

> [!IMPORTANT]
> Nová verze rozhraní API pro počítačové zpracování obrazu je nyní k dispozici, najdete v článku:
>- [Přehled](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Verze rozhraní API pro zpracování obrazu počítače 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Cloudové rozhraní API pro počítačové zpracování obrazu poskytuje vývojářům přístup k pokročilým algoritmům pro zpracování obrázků a vrácení informací. Na základě nahrání obrázku nebo zadání adresy URL obrázku mohou algoritmy Microsoftu pro počítačové zpracování obrazu různými způsoby analyzovat vizuální obsah, a to v závislosti na vstupech a volbách uživatelů. Pomocí rozhraní API pro počítačové zpracování obrazu můžete uživatelům díky analýze obrázků můžete:
* [Označit Image na základě obsahu.](#Tagging)
* [Kategorizace obrázků.](#Categorizing)
* [Určete typ a kvalitu imagí.](#Identifying)
* [Rozpoznávejte lidské tváře a vrátit jejich souřadnice. ](#Faces)
* [Rozpoznávání obsahu specifického pro doménu.](#Domain-Specific)
* [Generovat popisy obsahu.](#Descriptions)
* [Optické rozpoznávání znaků na slouží k identifikaci tištěný text nalezeno v obrázcích.](#OCR)
* [Rozpoznávání rukopisného textu.](#RecognizeText)
* [Rozlišení barevná schémata.](#Color)
* [Příznak obsahu pro dospělé.](#Adult)
* [Oříznutí fotografie má být použit jako miniatury.](#Thumbnails)

## <a name="requirements"></a>Požadavky
* Podporované metody zadávání znaků: Nezpracovaná binární ve formě application/octet-stream adresa URL obrázku nebo image.
* Podporované formáty image: JPEG, PNG, GIF, BMP.
* Velikost souboru obrázku: menší než 4 MB.
* Obrázek dimenze: větší než 50 × 50 pixelů.

## <a name="tagging-images"></a>Označování Imagí
Rozhraní API pro počítačové zpracování obrazu vrací značky na základě na více než 2 000 rozpoznatelných objektů, živých bytostí, krajin a akcí. Při značky jsou nejednoznačný nebo není, rozhraní API poskytne "pomocné parametry" pro objasnění významu značky v kontextu známé nastavení. Značky nejsou uspořádaná jako taxonomie a neexistuje žádná hierarchie dědičnosti. Kolekce obsahu značek tvoří základ pro bitovou kopii "popisu" zobrazí jako lidského čitelné jazyk, ve formátu v úplných větách. Všimněte si, že v tomto okamžiku angličtina je jediný podporovaný jazyk pro popis obrázku.

Po nahrání obrázku nebo zadáte adresu URL obrázku, výstupní API pro počítačové zpracování obrazu algoritmy značky na základě objektů, živých bytostí a akcí identifikované v bitové kopii. Označování není omezena pouze na hlavní předmět, jako je osoba v popředí, ale také zahrnuje nastavení (vnitřních nebo venkovní), o nábytek, nástroje, zařízení, zvířata, příslušenství, miniaplikace atd.

### <a name="example"></a>Příklad:
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Kategorizace obrázků
Kromě označování a popisy rozhraní API pro počítačové zpracování obrazu vrací na základě taxonomie kategorií definované v předchozích verzích. Tyto kategorie jsou uspořádané jako taxonomie s dědičného hierarchie nadřazený/podřízený. Všechny kategorie jsou v angličtině. Můžete použít samostatně nebo pomocí našich nových modelů.

### <a name="the-86-category-concept"></a>Koncept 86 kategorie
Na základě seznamu konceptů 86 vidět v následujícím diagramu, vizuální funkce nalezeném v obrázku lze označit od široké do konkrétní. Úplné taxonomie v textovém formátu, naleznete v tématu [taxonomie kategorií](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analýza kategorie](./Images/analyze_categories.png)

Image                                                  | Odpověď
------------------------------------------------------ | ----------------
![Žena stříška](./Images/woman_roof.png)                 | osoby
![Rodinných fotografií](./Images/family_photo.png)             | people_crowd
![Roztomilá pes](./Images/cute_dog.png)                     | animal_dog
![Venkovní Horská oblast](./Images/mountain_vista.png)       | outdoor_mountain
![Pro zpracování obrazu analyzovat Bread potravin](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identifikace typy obrázků
Kategorizace obrázků několika způsoby. Rozhraní API pro počítačové zpracování obrazu může nastavit logický příznak, který označuje, jestli obrázek je černobílý nebo barevný. Můžete také nastavit příznak označující, zda obrázek je nebo není Perokresba. To lze také určit, jestli obrázek je nebo není klipart a určit jeho kvality v důsledku na škále 0 – 3.

### <a name="clip-art-type"></a>Klipart – typ
Zjistí, jestli obrázek je klipart nebo ne.  

Hodnota | Význam
----- | --------------
0     | Bez klipart
1     | Nejednoznačný
2     | Normální klipart
3     | Dobré klipart

Image|Odpověď
----|----
![Pro zpracování obrazu analyzovat produkci minipivovarů ve Wisconsinu klipart](./Images/cheese_clipart.png)|3 dobré klipart
![Pro zpracování obrazu analyzovat House Yard](./Images/house_yard.png)|0 Non klipart

### <a name="line-drawing-type"></a>Typ čar
Zjistí, zda obrázek je nebo není Perokresba.

Image|Odpověď
----|----
![Pro zpracování obrazu analyzovat Lion kreslení](./Images/lion_drawing.png)|True
![Analýza květinu pro zpracování obrazu](./Images/flower.png)|False

### <a name="faces"></a>Tváře
Detekuje lidské tváře v obrázku a generuje souřadnic pro rozpoznávání tváře, rámeček pro rozpoznávání tváře, pohlaví a věk. Tyto funkce visual jsou podmnožinou metadata generovaná pro rozpoznávání tváře. Rozsáhlejší metadata generovaná pro tváří (Identifikace tváře, rozpoznávání pozice a další) použijte rozhraní API pro rozpoznávání tváře.  

Image|Odpověď
----|----
![Pro zpracování obrazu analyzovat žena stříška pro rozpoznávání tváře](./Images/woman_roof_face.png) | [{"age": 23, "pohlaví": "Ženský", "faceRectangle": {"left": 1379 "top": 320, "šířka": 310, "výška": 310}}]
![Pro zpracování obrazu analyzovat Mom dceřinou pro rozpoznávání tváře](./Images/mom_daughter_face.png) | [{"age": 28, "pohlaví": "Ženský", "faceRectangle": {"left": 447 "top": 195, "šířka": 162, "výška": 162}}, {"age": 10, "pohlaví": "Mužského", "faceRectangle": {"left": 355, "top": 87, "šířka": 143, "výška": 143}}]
![Pro zpracování obrazu analyzovat řady Phot pro rozpoznávání tváře](./Images/family_photo_face.png) | [{"age": 11 "pohlaví": "Mužského", "faceRectangle": {"left": 113 "top": 314, "šířka": 222, "výška": 222}}, {"age": 11 "pohlaví": "Ženský", "faceRectangle": {"left": 1200 "top": 632, "šířka": 215, "výška": 215}}, {"age": 41, "pohlaví": "Mužského", " faceRectangle": {"left": 514"top": 223"šířka": 205,"výška": 205}}, {"age": 37,"pohlaví":"Ženský","faceRectangle": {"left": 1008"top": 277,"šířka": 201,"výška": 201}}]


## <a name="domain-specific-content"></a>Obsahu specifického pro doménu

Kromě značek a nejvyšší úrovně kategorizaci, rozhraní API pro počítačové zpracování obrazu také podporuje speciální (nebo specifický pro doménu) informací. Specializované informace je možné implementovat jako samostatnou metodu nebo pomocí kategorizace vysoké úrovně. Funguje jako prostředky pro další upřesnění taxonomie kategorií 86 prostřednictvím přidání doménově specifické modely.

V současné době jsou jenom specializovaná informace, které jsou podporovány rozpoznávání celebrit a památek. Jsou specifické pro doménu upřesnění lidé a skupiny kategorií lidí a památek po celém světě.

Existují dvě možnosti použití doménově specifické modely:

### <a name="option-one---scoped-analysis"></a>Možnost 1 - s vymezeným oborem analýzy
Analyzujte pouze zvolené modelu vyvoláním volání rozhraní HTTP POST. Pro tuto možnost Pokud víte, které model, který chcete použít, zadejte název modelu a můžete získat informace jenom relevantní k tomuto modelu. Například můžete použít tuto možnost se díval jenom pro rozpoznávání celebrit. Odpověď obsahuje seznam potenciál odpovídající celebrit doplněny jejich skóre spolehlivosti.

### <a name="option-two---enhanced-analysis"></a>Možnost 2 – Rozšířená analýza
Analýza poskytnout další podrobnosti související s kategorií podle kategorie 86 taxonomie. Tato možnost je k dispozici pro použití v aplikacích, kde uživatelé chtějí získat analýzy obecný obrázek kromě podrobností z jednoho nebo více doménově specifické modely. Po vyvolání tato metoda je volána nejprve taxonomie kategorií 86 třídění. Pokud některou z kategorií odpovídat známé/porovnávání vzorů, následuje překontrolovat třídění volání. Například pokud "podrobnosti = all" nebo "details" zahrnout "celebrit", metoda se volá třídění celebrit po volání třídění 86 kategorie. Výsledek bude obsahovat značky začínající "people_".

## <a name="generating-descriptions"></a>Generování popisy 
API pro počítačové zpracování obrazu algoritmy analyzovat obsah v obrázku. Tato analýza tvoří základ pro "popisu" zobrazí jako čitelný jazyk v úplných větách. Popis shrnuje, co se nachází na obrázku. API pro počítačové zpracování obrazu algoritmy generovat různé popisy založené na objektech identifikované v bitové kopii. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší. Najdete příklad, který používá tuto technologii ke generování popisků obrazu robota [tady](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Příklad popisu generování
![B & vá budov](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Barevná schémata vnímání
Algoritmus počítačového zpracování obrazu extrahuje barvy z obrázku. Barvy se analyzují ve třech různých kontextech: popředí, pozadí a celek. Jsou seskupené do dvanácti 12 dominantních doplňkových barev. Tyto barvy zvýraznění jsou černá, modrá, brown, šedá, zelená, orange, růžová, nachová, červená, šedozelená, prázdné a žlutou barvou. V závislosti na barvy v obrázku mohou být vráceny jednoduché černobílý nebo barvy zvýraznění v šestnáctkové kódy.

Image                                                       | Popředí |Pozadí| Barvy
----------------------------------------------------------- | --------- | ------- | ------
![Venkovní Horská oblast](./Images/mountain_vista.png)            | Černá     | Černá   | Bílá
![Analýza květinu pro zpracování obrazu](./Images/flower.png)               | Černá     | Bílá   | Prázdné, Black, zelená
![Pro zpracování obrazu analyzovat trénování stanice](./Images/train_station.png) | Černá     | Černá   | Černá

### <a name="accent-color"></a>Barva motivu
Extrahovat z bitové kopie navržené tak, aby představují nejčastěji poutavých barva uživatelům prostřednictvím kombinace dominantní barvy a sytost barev.

Image                                                       | Odpověď
----------------------------------------------------------- | ----
![Venkovní Horská oblast](./Images/mountain_vista.png)            | #BC6F0F
![Analýza květinu pro zpracování obrazu](./Images/flower.png)               | #CAA501
![Pro zpracování obrazu analyzovat trénování stanice](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Černá a bílá
Příznak logické hodnoty, která určuje, jestli obrázek je černá a bílá nebo ne.

Image                                                      | Odpověď
---------------------------------------------------------- | ----
![Pro zpracování obrazu analýza sestavení](./Images/bw_buildings.png)      | True
![Pro zpracování obrazu analyzovat House Yard](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Nastavení příznaku obsah pro dospělé
V jednotlivých kategoriích visual je pro dospělé nebo pikantního skupiny, která umožňuje detekovat dospělé materiálů a omezuje zobrazení obrázků sexuální zneužívání obsahem. Filtr pro dospělé nebo pikantního obsahu zjišťování můžete nastavit pomocí posuvné stupnice tak, aby vyhovovaly preferencím uživatele.

## <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (OCR)
Technologie OCR detekuje textový obsah v obrázku a extrahuje zjištěný text do datového proudu Strojově čitelný znaků. Výsledek můžete použít k prohledávání a řadě dalších účelů, jako jsou zdravotnickými záznamy, zabezpečení a bankovnictví. Jazyk se detekuje automaticky. OCR šetří čas a zvyšuje pohodlí uživatelů tím, že jim pořídit fotografie textu místo zdlouhavého přepisování pořídili text.

OCR podporuje 25 jazycích. Tyto jazyky jsou: arabština, zjednodušené čínštiny, tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinka) Slovenština, španělština, švédština a turečtina.

V případě potřeby řeší OCR otočení rozpoznaný text ve stupních okolo osy vodorovný obrázek. OCR obsahuje souřadnice rámec jednotlivých slov, jak je znázorněno v následující ukázce.

![Přehled technologie OCR](./Images/vision-overview-ocr.png) požadavky pro optické rozpoznávání znaků:
- Velikost vstupního obrázku musí být až 40 x 40 3200 x 3200 pixelů.
- Na obrázku nesmí být větší než 10 megapixely k.

Vstupního obrázku můžete otočit o žádné nadbytečné 90 stupňů plus malé úhlu z až "40 stupňů.

Rozpoznávání textu přesnost závisí na kvalitu obrazu. Nesprávné čtení může být způsobeno v následujících případech:
- Použitím bitové kopie.
- Rukou psaný nebo kurzívových text.
- Styly umělecký písem.
- Malá velikost textu.
- Komplexní pozadí, stíny nebo oslnění přes text nebo Perspektiva narušení.
- Příliš velký nebo chybějící velká písmena na začátku slova
- Dolní index, horní index nebo přeškrtnutý text.

Omezení: Na fotografie, kde je dominantní textu, počet falešně pozitivních výsledků může pocházet částečně rozpoznaná slova. V některých fotografie, zejména fotografie žádný text můžete přesnost mnohem lišit v závislosti na typ bitové kopie.

## <a name="recognize-handwritten-text"></a>Rozpoznávání rukopisného textu
Tato technologie umožňuje detekovat a extrahovat rukou psaný text z poznámek, dopisů, esejí, bločky nebo Tabule, formuláře, atd. Pracuje s různými povrchy a pozadími, jako je bílý papír, žluté samolepicí bločky nebo tabule.

Rozpoznávání textu psaného rukou šetří čas i úsilí. Vzhledem k tomu, že stačí pořídit si obrázky textu a není potřeba ho přepisovat, může vám zajistit zvýšení produktivity. Umožňuje digitalizaci poznámek. Tato digitalizaci umožňuje implementovat rychlé a snadné vyhledávání. Už nebudete zavalení papíry.

Vstupní požadavky:
- Podporované formáty image: JPEG, PNG nebo BMP.
- Velikost souboru obrázku musí být menší než 4 MB.
- Rozměry obrázku musí být alespoň 40 x 40, maximálně 3200 x 3200.

Poznámka: Tato technologie je momentálně ve verzi Preview a je dostupná jenom pro anglický text.

## <a name="generating-thumbnails"></a>Generování miniatur
Miniatura je znázornění obrázek v plné velikosti. Pohyblivá podle zařízení, jako jsou telefony, tablety a počítače vytvořit potřebu rozložení rozhraní (UX) jiný uživatel a miniatur. Pomocí inteligentního ořezávání, toto rozhraní API pro počítačové zpracování obrazu funkce vám pomůže vyřešit problém.

Po nahrání obrázku, získá vygenerována vysoce kvalitní miniaturu a algoritmus rozhraní API pro počítačové zpracování obrazu analyzuje objekty v obrázku. To potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu (ROI). Výstup se zobrazí v rámci speciální, jak je znázorněno v následující ukázce. Vygenerované miniatury lze zobrazit pomocí aspekt deklaraci, která se liší od poměru stran u původní image tak, aby vyhovovaly potřebám uživatele.

Miniatura algoritmus funguje takto:

1. Odebere rušivé prvky z obrázku a rozpozná hlavní objekt oblasti zájmu (ROI).
2. Obrázek ořízne tak, podle zjištěných oblasti zájmu.
3. Změní poměr stran, aby odpovídala rozměrům cílového miniatur.

![Miniatury](./Images/thumbnail-demo.png)
