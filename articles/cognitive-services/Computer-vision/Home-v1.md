---
title: Co je rozhraní API pro počítačové zpracování obrazu?
titlesuffix: Azure Cognitive Services
description: Rozhraní API pro počítačové zpracování obrazu nabízí vývojářům přístup k pokročilým algoritmům, které zpracovávají obrázky a vrací informace.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 09a7f02df1102e42bc45b4615a2af4235372bcdc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957023"
---
# <a name="what-is-computer-vision-api-version-10"></a>Co je rozhraní API pro počítačové zpracování obrazu verze 1.0?

> [!IMPORTANT]
> K dispozici je nová verze rozhraní API pro počítačové zpracování obrazu. Další informace:
>- [Přehled](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Rozhraní API pro počítačové zpracování obrazu verze 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Cloudové rozhraní API pro počítačové zpracování obrazu poskytuje vývojářům přístup k pokročilým algoritmům pro zpracování obrázků a vrácení informací. Na základě nahrání obrázku nebo zadání adresy URL obrázku mohou algoritmy Microsoftu pro počítačové zpracování obrazu různými způsoby analyzovat vizuální obsah, a to v závislosti na vstupech a volbách uživatelů. Jaké možnosti mají uživatelé rozhraní API pro počítačové zpracování obrazu při analýze obrázků:
* [Můžou označovat obrázky podle obsahu.](#Tagging)
* [Můžou zařazovat obrázky do kategorií.](#Categorizing)
* [Můžou určovat typ a kvalitu obrázků.](#Identifying)
* [Můžou zjišťovat lidské tváře a získávat jejich souřadnice.](#Faces)
* [Můžou rozpoznávat obsah určité domény.](#Domain-Specific)
* [Můžou generovat popisy obsahu.](#Descriptions)
* [Můžou používat optické rozpoznávání písma k identifikaci vytištěného textu na obrázcích.](#OCR)
* [Můžou rozpoznávat rukopis.](#RecognizeText)
* [Můžou rozlišovat barevná schémata.](#Color)
* [Můžou označovat obsah pro dospělé.](#Adult)
* [Můžou ořezávat fotky používané jako miniatury.](#Thumbnails)

## <a name="requirements"></a>Požadavky
* Podporované vstupní metody: Nezpracovaný binární obrázek ve tvaru streamu typu application/octet nebo adresa URL obrázku.
* Podporované formáty obrázku: JPEG, PNG, GIF, BMP.
* Velikost souboru obrázku: méně než 4 MB.
* Rozměry obrázku: větší než 50 × 50 pixelů.

## <a name="tagging-images"></a>Označování obrázků
Rozhraní API pro počítačové zpracování obrazu vrací značky na základě tisíce rozpoznatelných objektů, živých bytostí, krajin a akcí. Pokud jsou značky nejasné nebo nejsou obecně známé, nabídne API v odpovědi nápovědu, která objasní význam značky v kontextu známého prostředí. Značky nejsou uspořádané podle taxonomie a neexistují hierarchie dědění. Shromážděné značky obsahu tvoří základ „popisu“ obrázku, který se zobrazí v čitelném jazyce formátovaném do celých vět. V tuto chvíli je jediným podporovaným jazykem používaným k popisu obrázku angličtina.

Po nahrání obrázku nebo zadání URL obrázku vygenerují algoritmy rozhraní API pro počítačového zpracování obrazu značky, které vycházejí z předmětů, živých bytostí a akcí rozpoznaných na obrázku. Značky se neomezují na hlavní předmět, například postavu v popředí, ale zahrnují také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.

### <a name="example"></a>Příklad:
![Dvůr s domem](./Images/house_yard.png) '

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
## <a name="categorizing-images"></a>Zařazení obrázků do kategorií
Kromě označení a popisu vrátí rozhraní API také taxonomické kategorie definované v předchozích verzích. Tyto kategorie jsou taxonomicky uspořádané jako dědičné hierarchie nadřízených a podřízených prvků. Všechny kategorie jsou v angličtině. Můžete je používat samostatně nebo s našimi novými modely.

### <a name="the-86-category-concept"></a>Systém 86 kategorií
Na základě seznamu 86 položek, které si můžete prohlédnout na následujícím diagramu, je možné zařadit obrazové prvky nalezené na obrázku do obecných a specifických kategorií. Celou taxonomii v textovém formátu najdete v článku o [taxonomických kategoriích](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analýza kategorií](./Images/analyze_categories.png)

Image                                                  | Odpověď
------------------------------------------------------ | ----------------
![Žena na střeše](./Images/woman_roof.png)                 | people
![Rodinná fotka](./Images/family_photo.png)             | people_crowd
![Pejsek](./Images/cute_dog.png)                     | animal_dog
![Na horách](./Images/mountain_vista.png)       | outdoor_mountain
![Vizuální analýza pečiva](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Určování typů obrázků
Existují různé způsoby zařazování obrázku do kategorií. Rozhraní API pro počítačové zpracování obrazu může nastavit logický příznak, který určuje, jestli je obrázek černobílý nebo barevný. Může také nastavit příznak, který určuje, jestli se jedná o kresbu. Dokáže také určit, jestli jde o klipart a jak je obrázek kvalitní na stupnici 0–3.

### <a name="clip-art-type"></a>Typ klipart
Zjistí, jestli je obrázek klipart.  

Hodnota | Význam
----- | --------------
0     | Není klipart
1     | Nejednoznačné
2     | Normální klipart
3     | Dobrý klipart

Image|Odpověď
----|----
![Vizuální analýza klipartu sýru](./Images/cheese_clipart.png)|3 good-clip-art
![Vizuální analýza domu s dvorem](./Images/house_yard.png)|0 Non-clip-art

### <a name="line-drawing-type"></a>Typ kresba
Zjistí, jestli je na obrázku kresba.

Image|Odpověď
----|----
![Vizuální analýza kresby lva](./Images/lion_drawing.png)|True
![Vizuální analýza květiny](./Images/flower.png)|False

### <a name="faces"></a>Tváře
Zjistí lidské tváře na obrázku a vygeneruje jejich souřadnice, obdélník tváře, pohlaví a věk. Tyto obrazové vlastnosti tvoří podmnožinu metadat generovaných pro tvář. Pokud potřebujete pro tváře generovat podrobnější metadata (identifikace tváře, zjišťování postoje atd.), použijte rozhraní API pro rozpoznávání tváře.  

Image|Odpověď
----|----
![Vizuální analýza ženy na střeše](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Vizuální analýza tváří matky s dcerou](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Vizuální analýza tváří na fotce rodiny](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Obsah určité domény

Kromě označení a zařazení do kategorií nejvyšší úrovně podporuje rozhraní API pro počítačové zpracování obrazu také zvláštní informace, které se týkají určité domény. Tyto zvláštní informace je možné implementovat jako samostatnou metodu nebo společně s kategorií nejvyšší úrovně. Funguje jako prostředek, který dále zpřesňuje 86 taxonomických kategorií o modely určitých domén.

V současnosti jsou podporované pouze zvláštní informace o rozpoznaných celebritách a památkách. Jedná se o doménové zpřesnění kategorií lidé, skupiny lidí a památky po celém světě.

Existují dvě možnosti, jak používat modely určité domény:

### <a name="option-one---scoped-analysis"></a>První možnost – zúžená analýza
Analyzuje jenom zvolený model voláním operace HTTP POST. Pokud u této možnosti víte, jaký model chcete použít, zadejte jeho název. Získáte informace, které se týkají jen daného modelu. Tuto možnost můžete použít, když třeba hledáte jenom rozpoznávání celebrit. V odpovědi bude seznam možných shodných celebrit doplněný o skóre spolehlivosti.

### <a name="option-two---enhanced-analysis"></a>Druhá možnost – rozšířená analýza
Při této analýze můžete zadat další podrobnosti týkající se kategorií z 86 taxonomických kategorií. Tuto možnost můžete použít v aplikacích, ve kterých uživatelé kromě podrobností z jednoho nebo několika doménových modelů chtějí získat i obecnou analýzu obrázku. Při volání této metody se nejprve volá klasifikátor taxonomie, která obsahuje 86 kategorií. Pokud některé kategorie odpovídají známým/shodným modelům, následuje druhé volání klasifikátoru. Pokud třeba „details=all“ nebo „details“ obsahuje „celebrities“, potom metoda po volání klasifikátoru 86 kategorií volá klasifikátor celebrit. Výsledkem budou značky, které začínají na people_ (lidé_).

## <a name="generating-descriptions"></a>Generování popisů 
Algoritmy rozhraní API pro počítačové zpracování obrazu analyzují obsah obrazu. Tato analýza tvoří základ popisu zobrazeného v čitelném jazyce a v celých větách. Popis shrnuje zjištěný obsah obrázku. Algoritmy rozhraní API pro počítačové zpracování obrazu generují různé popisy, které vycházejí z objektů identifikovaných na obrázku. Jednotlivé popisy se vyhodnotí a vygeneruje se pro ně skóre spolehlivosti. Pak se vrátí seznam seřazený od nejvyššího skóre spolehlivosti po nejnižší. Příklad robota, který tuto technologii používá ke generování popisů obrázků, najdete [zde](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Příklad generování popisu
![Černobílé budovy](./Images/bw_buildings.png) '
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

## <a name="perceiving-color-schemes"></a>Vnímání barevných schémat
Algoritmus počítačového zpracování obrazu extrahuje z obrázku barvy. Barvy se analyzují ve třech různých kontextech: popředí, pozadí a celek. Jsou seskupené do dvanácti dominantních doplňkových barev. Jsou to tyto doplňkové barvy: černá, modrá, hnědá, šedá, zelená, oranžová, růžová, fialová, červená, šedozelená, bílá a žlutá. Podle toho, jaké barvy jsou na obrázku, může být vrácena jednoduše černá a bílá nebo hexadecimální kódy doplňkových barev.

Image                                                       | Popředí |Pozadí| Barvy
----------------------------------------------------------- | --------- | ------- | ------
![Na horách](./Images/mountain_vista.png)            | Black     | Black   | White
![Vizuální analýza květiny](./Images/flower.png)               | Black     | White   | White, Black, Green
![Vizuální analýza stanice metra](./Images/train_station.png) | Black     | Black   | Black

### <a name="accent-color"></a>Doplňková barva
Jde o barvu extrahovanou z obrázku, která je na něm pro uživatele nejvýraznější. Představuje mix barev a sytosti.

Image                                                       | Odpověď
----------------------------------------------------------- | ----
![Na horách](./Images/mountain_vista.png)            | #BC6F0F
![Vizuální analýza květiny](./Images/flower.png)               | #CAA501
![Vizuální analýza stanice metra](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Černá a bílá
Logický příznak, který určuje, jestli je obrázek černobílý.

Image                                                      | Odpověď
---------------------------------------------------------- | ----
![Vizuální analýza budovy](./Images/bw_buildings.png)      | True
![Vizuální analýza domu s dvorem](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Označení obsahu pro dospělé
Mezi různými obrazovými kategoriemi je i skupina nevhodného obsahu a obsahu pro dospělé. Tato skupina umožňuje zjišťovat materiály pro dospělé a omezuje zobrazování obrázků se sexuálním obsahem. Filtr pro rozpoznání nevhodného obsahu a obsahu pro dospělé je možné podle potřeby nastavit na posuvné stupnici podle preferencí uživatele.

## <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (OCR)
Technologie OCR rozpozná na obrázku textový obsah a extrahuje zjištěný text do strojově čitelného znakového streamu. Výsledek můžete použít k vyhledávání nebo k různým jiným účelům, třeba jako lékařské záznamy, pro zabezpečení nebo bankovnictví. Jazyk se rozpoznává automaticky. Technologie OCR šetří čas a zvyšuje pohodlí uživatelů, protože umožňuje text místo přepisování vyfotografovat.

OCR podporuje 25 jazyků. Jsou to tyto jazyky: angličtina, arabština, čeština, čínština (tradiční), čínština (zjednodušená), dánština, finština, francouzština, holandština, italština, japonština, korejština, maďarština, němčina, norština, polština, portugalština, rumunština, ruština, řečtina, slovenština, srbština (cyrilice a latinka), španělština, švédština a turečtina.

V případě potřeby technologie OCR opraví otočení rozpoznaného textu ve stupních kolem vodorovné osy obrázku. OCR nabízí souřadnice rámečku každého slova. Je to vidět na následujícím obrázku.

![Přehled technologie OCR](./Images/vision-overview-ocr.png) Požadavky na OCR:
- Vstupní obrázek musí mít rozměry 40 × 40 až 3200 × 3200 pixelů.
- Obrázek nesmí být větší než 10 megapixelů.

Vstupní obrázek může být otočený libovolně o 90 stupňů plus malý úhel do 40 stupňů.

Přesnost rozpoznaného textu závisí na kvalitě obrázku. Nepřesnosti při čtení mohou způsobovat následující situace:
- rozostřené obrázky;
- rukopis nebo text psaný kurzívou;
- umělecké styly písma;
- malé písmo;
- složité pozadí, stíny nebo odlesky vržené na text nebo narušená perspektiva;
- příliš velká nebo chybějící velká písmena na začátku slov;
- dolní index, horní index nebo přeškrtnutý text.

Omezení: Na fotkách, na kterých převažuje text, můžou vznikat falešné pozitivní výsledky v podobě částečně rozpoznaných slov. Na některých fotkách, zejména na fotkách bez textu, se může přesnost výrazně lišit podle typu obrázku.

## <a name="recognize-handwritten-text"></a>Rozpoznání rukopisu
Tato technologie umožňuje detekovat a extrahovat rukopis z poznámek, dopisů, esejů, tabulí, formulářů apod. Pracuje s různými povrchy a pozadími, jako je bílý papír, žluté samolepicí bločky nebo tabule.

Rozpoznávání rukopisného textu šetří čas i námahu. Můžete být produktivnější, protože nemusíte text přepisovat, ale stačí ho vyfotit. Poznámky můžete digitalizovat. Digitalizace umožňuje snadno a rychle implementovat vyhledávání. Také už nebudete tak zavaleni papíry.

Požadavky na vstupy:
- Podporované formáty obrázku: JPEG, PNG a BMP.
- Soubor s obrázkem musí být menší než 4 MB.
- Rozměry obrázku musí být alespoň 40 × 40 ale maximálně 3200 × 3200 pixelů.

Poznámka: Tato technologie je v současnosti ve verzi Preview a je dostupná jenom pro text v angličtině.

## <a name="generating-thumbnails"></a>Generování miniatur
Miniatura je zmenšenina velkého obrázku. Různá zařízení, jako jsou telefony, tablety nebo počítače, potřebují různá rozložení uživatelského prostředí a různě velké miniatury. Tento problém řeší funkce rozhraní API pro počítačové zpracování obrazu s inteligentním ořezáváním.

Po nahrání obrázku se vygeneruje co nejkvalitnější miniatura a algoritmus rozhraní API pro počítačové zpracování obrazu analyzuje objekty na obrázku. To potom obrázek ořízne tak, aby odpovídal požadavkům oblasti zájmu. Výstup se zobrazí ve zvláštním formátu. Je to vidět na následujícím obrázku. Vygenerovaná miniatura může mít jiný poměr stran než původní obrázek, aby bylo možné vyhovět potřebám uživatelů.

Algoritmus miniatury funguje následovně:

1. Odebere rušivé prvky z obrázku a rozpozná hlavním objektem, oblast zájmu.
2. Obrázek ořízne tak, podle zjištěných oblast zájmu.
3. Změní poměr stran, aby odpovídal rozměrům cílové miniatury.

![Miniatury](./Images/thumbnail-demo.png)
