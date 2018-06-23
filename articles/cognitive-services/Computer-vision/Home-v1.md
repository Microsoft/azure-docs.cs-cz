---
title: Počítače vize rozhraní API pro službu Microsoft kognitivní | Microsoft Docs
description: Použijte pokročilé algoritmy v rozhraní API vize počítače při zpracování obrázků a vrátí informace ve kognitivní služby společnosti Microsoft.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343485"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co je počítač vize rozhraní API verze 1.0?

> [!IMPORTANT]
> Nová verze rozhraní API vize počítači je nyní k dispozici, najdete v tématu:
>- [Přehled](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Počítač vize rozhraní API verze 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Rozhraní API vize počítače založené na cloudu poskytuje vývojářům přístup k pokročilé algoritmy pro zpracování obrázků a vrátí informace. Nahrávání obrázku nebo zadat adresu URL obrázku, můžete Microsoft počítače vize algoritmy analyzovat visual obsah různými způsoby podle vstupy a možnosti výběru. S rozhraním API vize počítače uživatelů můžete analyzovat bitových kopií do:
* [Značka Image na základě obsahu.](#Tagging)
* [Kategorizace bitové kopie.](#Categorizing)
* [Určete typ a kvalitu bitové kopie.](#Identifying)
* [Zjištění lidského řezy a vrátit jejich souřadnice. ](#Faces)
* [Rozpoznat specifické pro doménu obsah.](#Domain-Specific)
* [Generovat popisy obsahu.](#Descriptions)
* [Slouží k identifikaci tištěné text najít v obrázcích optické rozpoznávání znaků.](#OCR)
* [Rozpoznat ručně psaný text.](#RecognizeText)
* [Rozlišení barevná schémata.](#Color)
* [Příznak obsah pro dospělé.](#Adult)
* [Oříznutí fotografie má být použit jako miniatury.](#Thumbnails)

## <a name="requirements"></a>Požadavky
* Podporované metody zadávání znaků: Nezpracovaná binární ve formě application/octet-stream adresa URL obrázku nebo image.
* Podporované formáty bitové kopie: JPEG, PNG, GIF, BMP.
* Velikost souboru obrázku: méně než 4 MB volného místa.
* Obrázek dimenze: větší než 50 × 50 pixelů.

## <a name="tagging-images"></a>Označování bitové kopie
Rozhraní API vize počítači vrátí značky na základě více než 2000 rozpoznatelném objekty, životností lidé, kulisy a akce. Když jsou nejednoznačné značky nebo není běžné znalostní báze, odpověď rozhraní API Nápověda je poskytována prostřednictvím '' upřesnit význam značky v kontextu známé nastavení. Značky nejsou uspořádána jako taxonomii a neexistuje žádná hierarchie dědičnosti. Kolekce obsahu značky tvoří základ pro bitovou kopii popis zobrazí jako lidského čitelný jazyk, ve formátu v dokončení věty. Všimněte si, že v tomto okamžiku jazykem je angličtina pouze podporované pro popis bitové kopie.

Po odeslání bitovou kopii nebo zadáte adresu URL obrázku, výstupní algoritmy API vize počítače na základě objektů, životností lidé a akce, které jsou určené v obrázku značky. Označování není omezeno na hlavní subjektu, jako je například osoby v popředí, ale také zahrnuje nábytek (vnitřní nebo venku), nastavení, nástroje, rostlin, zvířat, příslušenství, miniaplikacemi atd.

### <a name="example"></a>Příklad:
![House_Yard](./Images/house_yard.jpg) '

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
## <a name="categorizing-images"></a>Kategorizaci bitové kopie
Kromě označování a popisy vrátí počítače vize API kategorií na základě taxonomii definované v předchozích verzích. Tyto kategorie jsou uspořádána jako taxonomii s hierarchií dědičného nadřazený podřízený. Všechny kategorie jsou v angličtině. Můžete použít samostatně nebo s naší nové modely.

### <a name="the-86-category-concept"></a>Koncept 86 kategorie
Na základě seznamu 86 konceptů vidět v následujícím diagramu, visual funkce, které se nacházejí v bitové kopii lze rozdělit od široké až konkrétní. Úplné taxonomii v textovém formátu, najdete v části [kategorie taxonomii](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analýza kategorií](./Images/analyze_categories.jpg)

Image                                                  | Odpověď
------------------------------------------------------ | ----------------
![Žena střešní](./Images/woman_roof.jpg)                 | osoby
![Rodiny fotografii](./Images/family_photo.jpg)             | people_crowd
![Cute PSA](./Images/cute_dog.jpg)                     | animal_dog
![Venku Horská](./Images/mountain_vista.jpg)       | outdoor_mountain
![Vize analyzovat jídlo chléb](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>Identifikace typy obrázků
Existuje několik způsobů zařadit do kategorií bitové kopie. Rozhraní API vize počítač můžete nastavit logický příznak indikující, zda bitová kopie je černobílý nebo barev. Můžete ho také nastavit příznak označující, zda je obrázek kreslení čáry nebo ne. Můžete také určit, zda obrázek není klip obrázky, nebo není a znamenat jeho kvalitu jako takový na škále 0-3.

### <a name="clip-art-type"></a>Typ klip obrázky
Zjistí, zda bitová kopie je klip obrázky, nebo ne.  

Hodnota | Význam
----- | --------------
0     | Non klip – obrázky
1     | nejednoznačný
2     | Normální – klip – obrázky
3     | dobré klip – obrázky

Image|Odpověď
----|----
![Vize analyzovat sýr klip obrázky](./Images/cheese_clipart.jpg)|3 dobré klip – obrázky
![Vize analyzovat úklidové metr](./Images/house_yard.jpg)|0 Non klip – obrázky

### <a name="line-drawing-type"></a>Typ čar
Zjistí, zda je obrázek kreslení čáry nebo ne.

Image|Odpověď
----|----
![Vize analyzovat Lion kreslení](./Images/lion_drawing.jpg)|True
![Vize analyzovat květina](./Images/flower.jpg)|False

### <a name="faces"></a>Tváře
Zjistí lidského tyto řezy v obrázku a vygeneruje souřadnice řez, rámeček pro vzhled, pohlaví a stáří. Tyto funkce visual, jsou podmnožinou metadata vygenerované řez. Rozsáhlejší metadata vygenerované řezy (obličeje identifikace, pozice zjišťování a další) pomocí rozhraní API řez.  

Image|Odpověď
----|----
![Vize analyzovat žena střešní vzhled](./Images/woman_roof_face.png) | [{"Stáří": 23, "pohlaví": "Ženského", "faceRectangle": {"left": 1379 "top": 320, "šířka": 310, "výška": 310}}]
![Vize analyzovat Mom dceřiné vzhled](./Images/mom_daughter_face.png) | [{"Stáří": 28, "pohlaví": "Ženského", "faceRectangle": {"left": 447 "top": 195, "šířka": 162, "výška": 162}}, {"Stáří": 10, "pohlaví": "Mužského", "faceRectangle": {"left": 355 "top": 87, "šířka": 143, "výška": 143}}]
![Vize analyzovat rodiny Phot vzhled](./Images/family_photo_face.png) | [{"Stáří": 11, "pohlaví": "Mužského", "faceRectangle": {"left": 113 "top": 314, "šířka": 222, "výška": 222}}, {"Stáří": 11, "pohlaví": "Ženského", "faceRectangle": {"left": 1200 "top": 632, "šířka": 215, "výška": 215}}, {"Stáří": 41, "pohlaví": "Mužského", " faceRectangle": {"left": 514"top": 223"šířka": 205,"výška": 205}}, {"Stáří": 37,"pohlaví":"Ženského","faceRectangle": {"left": 1008"top": 277,"šířka": 201,"výška": 201}}]


## <a name="domain-specific-content"></a>Obsah specifické pro doménu

Kromě označování a nejvyšší úrovně kategorizaci, rozhraní API vize počítač podporuje také specializované (nebo specifické pro doménu) informací. Specializované informace se dá implementovat jako samostatnou metodu nebo s vysoké úrovně kategorizaci. Funguje jako prostředek pro další upřesnění taxonomii 86 kategorie prostřednictvím přidání modelů specifické pro doménu.

V současné době jsou pouze specializované informace podporovaných celebrit rozpoznávání a významné. Jsou specifické pro doménu obecnější pro osoby a osoby skupiny kategorií a zajímavá po celém světě.

Existují dvě možnosti pro použití modelů specifické pro doménu:

### <a name="option-one---scoped-analysis"></a>Možnost 1 - vymezená analýzy
Analýza pouze zvolený model vyvoláním volání na HTTP POST. Pro tuto možnost Pokud víte, které model, který chcete použít, zadejte název modelu a můžete získat informace jenom relevantní pro tento model. Například můžete použít tuto možnost jenom hledání celebrit rozpoznávání. Odpověď obsahuje seznam potenciální odpovídající celebrit, doplněny jejich spolehlivosti skóre.

### <a name="option-two---enhanced-analysis"></a>Možnost 2 - rozšířené analýzy
Analýza poskytnout další podrobnosti související s kategorií ze taxonomii 86 kategorie. Tato možnost je k dispozici pro použití v aplikacích, kde uživatelé chtějí získat obecné image analysis kromě podrobností z jednoho nebo více modelů specifické pro doménu. Když tato metoda je volána, třídění 86 kategorie taxonomii jako první. Pokud žádné ze skupin odpovídat známé nebo odpovídající modely, následuje druhého průchodu třídění volání. Například pokud se podrobnosti = all' nebo 'celebrit, zahrnout "Podrobnosti", metoda volá třídění celebrit po volání třídění 86 kategorie. Výsledek obsahuje značky začínající 'people_'.

## <a name="generating-descriptions"></a>Generování popisy 
Algoritmy API vize počítače analyzovat obsah bitovou kopii. Tato analýza tvoří základ pro "Popis" zobrazí jako čitelný jazyk v dokončení věty. Popis shrnuje, co se nachází v bitovou kopii. Algoritmy API vize počítače generují různé popisy podle objekty identifikovat v bitové kopii. Popisy jsou každý vyhodnotit a vygeneruje spolehlivosti skóre. Seznam je poté vrácen seřazené z nejvyšší skóre spolehlivosti nejnižší. Naleznete příklad robota, které používá tuto technologii ke generování popisků obrazu [zde](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Příklad popisu generování
![B & volit budovy](./Images/bw_buildings.jpg) '
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

## <a name="perceiving-color-schemes"></a>Vnímání barevná schémata
Algoritmus počítače vize extrahuje barvy z obrázku. Barvy se analyzují ve třech různých kontextech: popředí, pozadí a celek. Jsou seskupené do barvy dvanáct 12 dominantní zvýraznění. Tyto barvy zvýraznění jsou černé, blue, menších, šedá, zelená, oranžová, růžová, fialová, červená, šedozelená, prázdné a žlutě. V závislosti na barvy v obraze mohou být vráceny jednoduché černobílý nebo barvy zvýraznění v hexadecimální kódy.

Image                                                       | Popředí |Pozadí| Barvy
----------------------------------------------------------- | --------- | ------- | ------
![Venku Horská](./Images/mountain_vista.jpg)            | Černá     | Černá   | Bílá
![Vize analyzovat květina](./Images/flower.jpg)               | Černá     | Bílá   | Prázdné, černé, zelená
![Vize analyzovat Train stanice](./Images/train_station.jpg) | Černá     | Černá   | Černá

### <a name="accent-color"></a>Barva zvýraznění
Barva extrahovat z image představují barvu nejvíce poutavých uživatelům prostřednictvím směs dominantní barvy a sytost.

Image                                                       | Odpověď
----------------------------------------------------------- | ----
![Venku Horská](./Images/mountain_vista.jpg)            | #BC6F0F
![Vize analyzovat květina](./Images/flower.jpg)               | #CAA501
![Vize analyzovat Train stanice](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>Černobílý
Logický příznak, který určuje, zda je obrázek černé & bílé nebo ne.

Image                                                      | Odpověď
---------------------------------------------------------- | ----
![Vize analyzovat sestavení](./Images/bw_buildings.jpg)      | True
![Vize analyzovat úklidové metr](./Images/house_yard.jpg)      | False

## <a name="flagging-adult-content"></a>Označování obsah pro dospělé
V jednotlivých kategoriích visual je pro dospělé a zájem skupinu, která umožňuje zjišťování pro dospělé materiály a omezuje zobrazení bitové kopie obsahující sexuální obsah. Filtr pro zjišťování obsahu pro dospělé a zájem můžete nastavit na posuvné stupnice zohlednit preferencím uživatele.

## <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (rozpoznávání znaků)
Technologie rozpoznávání znaků zjistí textového obsahu v obrázku a extrahuje identifikovaných text do proudu strojově čitelným znak. Výsledek můžete použít k hledání a mnoha dalším účelům jako lékařské záznamy, zabezpečení a bankovnictví. Jazyk se detekuje automaticky. Rozpoznávání znaků šetří čas a poskytuje užitečný pro uživatele tím, že aby se změny fotografie textu místo přepisování text.

Rozpoznávání znaků podporuje 25 jazyků. Tyto jazyky jsou jazyky: arabština, zjednodušené čínštině, tradiční čínština, čeština, dánština, holandština, angličtina, finština, francouzština, němčina, řečtina, maďarština, italština, japonština, korejština, norština, polština, portugalština, rumunština, ruština, srbština (cyrilice a latinka) Slovenština, španělština, švédština a turečtina.

V případě potřeby rozpoznávání znaků opraví oběh rozpoznaný text ve stupních, okolo osy vodorovné bitové kopie. Rozpoznávání znaků poskytuje souřadnice rámce jednotlivých slov, jak je vidět níže obrázku.

![Přehled rozpoznávání znaků](./Images/vision-overview-ocr.png) požadavky pro rozpoznávání znaků:
- Velikost vstupní bitové kopie musí být mezi 40 x 40 a 3200 x 3200 pixelů.
- Obrázek nesmějí být větší než 10 megapixely.

Vstupní image lze otáčet o žádné více 90 stupňů plus malé úhel z se 40 stupňů.

Přesnost rozpoznávání textu závisí na kvalitu bitovou kopii. Nesprávné čtení může být způsobeno těchto situacích:
- Rozmazaně bitové kopie.
- Ručně nebo kurzívových text.
- Styly uměleckého písem.
- Malé textové velikost.
- Komplexní pozadí, stínů nebo oslnění přes text nebo Perspektiva narušení.
- Chybí nebo je příliš velký velkých písmen na začátku slova
- Dolní, horní index nebo přeškrtnutí text.

Omezení: Na fotografie, kde je dominantní text, mohou falešně pozitivních pocházet z částečně rozpoznaný slova. Na některých fotografie, zejména fotografie bez jakýkoli text, přesnost závisí na mnoho typ obrázku.

## <a name="recognize-handwritten-text"></a>Rozpoznat ručně psaný Text
Tato technologie umožňuje zjistit a extrahovat ručně psaný text z poznámky, písmena, essays, Tabule, formuláře atd. Pracuje s různými povrchy a pozadími, jako je bílý papír, žluté samolepicí bločky nebo tabule.

Rozpoznávání textu psaného rukou šetří čas i úsilí. Vzhledem k tomu, že stačí pořídit si obrázky textu a není potřeba ho přepisovat, může vám zajistit zvýšení produktivity. Ji umožňuje digitalizace poznámky. Tato digitization umožňuje implementovat rychlý a snadný vyhledávání. Už nebudete zavalení papíry.

Vstupní požadavky:
- Podporované formáty bitové kopie: JPEG, PNG nebo BMP.
- Velikost souboru obrázku musí být menší než 4 MB volného místa.
- Obrázek dimenze musí být alespoň 40 x 40, maximálně 3200 x 3200.

Poznámka: Tato technologie je momentálně ve verzi Preview a je dostupná jenom pro anglický text.

## <a name="generating-thumbnails"></a>Vytváření miniatur
Miniatura je malá reprezentace obraz v plné velikosti. Různým zařízení, jako jsou telefony, tablety a počítače vytvořit potřebu velikost miniatur a rozložení prostředí (UX) jiný uživatel. Použití inteligentního oříznutí, tato funkce rozhraní API vize počítače vám pomůže vyřešit problém.

Po nahrání bitovou kopii, získá vygeneruje Miniatura vysoce kvalitní a algoritmus počítače vize API analyzuje objektů v rámci bitovou kopii. Ji pak ořízne obrázek podle požadavků oblasti zájmu (ROI). Výstup získá zobrazit v rámci speciální rozhraní, jak je vidět níže obrázku. Vygenerovaný miniaturu lze zobrazit pomocí aspekt dávce, které se liší od poměr stran původní bitové kopie pro přizpůsobení potřebám uživatele.

Algoritmus miniatur funguje takto:

1. Odebere rušivě elementy z bitové kopie a rozpozná objekt hlavní oblast zájmu (ROI).
2. Ořízne obrázek na základě identifikovaných oblasti zájmu.
3. Změní poměr stran podle cílové dimenze miniatur.

![Miniatury](./Images/thumbnail-demo.png)
