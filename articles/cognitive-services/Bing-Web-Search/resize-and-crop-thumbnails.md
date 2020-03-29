---
title: Změna velikosti a oříznutí miniatur obrázků – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Některé odpovědi z rozhraní API pro vyhledávání Bing udávají adresy URL miniatur y obsluhované službou Bing, jejichž velikost a oříznutí můžete změnit a mohou obsahovat parametry dotazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110635"
---
# <a name="resize-and-crop-thumbnail-images"></a>Změna velikosti a oříznutí miniatur obrázků

Některé odpovědi z rozhraní API pro vyhledávání Bing udávají adresy URL miniatur y obsluhované službou Bing, jejichž velikost a oříznutí můžete změnit a mohou obsahovat parametry dotazu. Například:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Pokud zobrazíte podmnožinu těchto miniatur, poskytněte možnost zobrazení zbývajících obrazů.

> [!NOTE]
> Ujistěte se, že oříznutí a změna velikosti miniatur budou poskytovat scénář vyhledávání, který respektuje práva třetích stran, jak to vyžadují [požadavky na použití a zobrazení](use-display-requirements.md)rozhraní API pro vyhledávání Bing .

## <a name="resize-a-thumbnail"></a>Změna velikosti miniatury 

Chcete-li změnit velikost miniatury, bing doporučuje `w` zadat pouze `h` jeden parametry (šířka) nebo (výška) dotazu v adrese URL miniatury. Určení pouze výšky nebo šířky umožní Bingzachovat původní aspekt obrazu. Určete šířku a výšku v obrazových bodech. 

Pokud je například původní miniatura 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

A chcete zmenšit jeho `w` velikost, nastavit parametr `336`na novou `h` hodnotu (například ) a odebrat parametr:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Pokud zadáte pouze výšku nebo šířku miniatury, zůstane zachován původní poměr stran obrázku. Pokud zadáte oba parametry a poměr stran se neudrží, Bing přidá bílé odsazení k okraji obrazu.

Pokud například změníte velikost obrázku 480 × 359 na 200 × 200 bez oříznutí, bude celá šířka obsahovat obrázek, ale výška bude obsahovat 25 pixelů bílého odsazení v horní a dolní části obrázku. Pokud by byl obrázek 359 × 480, levé a pravé ohraničení by obsahovalo bílé odsazení. Pokud obraz oříznete, bílé odsazení se nepřidá.  

Následující obrázek znázorňuje původní velikost miniatury (480x300).  
  
![Původní obrázek na šířku](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Následující obrázek znázorňuje velikost obrázku na 200x200. Poměr stran je zachován a horní a dolní hranice jsou doplněny bílou barvou (zde je zahrnutčerný okraj pro zobrazení odsazení).  
  
![Velikost obrázku na šířku](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Pokud zadáte rozměry, které jsou větší než původní šířka a výška obrazu, bing přidá bílé odsazení k levému a hornímu okraji.  

## <a name="request-different-thumbnail-sizes"></a>Žádost o různé velikosti miniatur

Chcete-li požádat o jinou velikost miniatury, odeberte `id` všechny `pid` parametry dotazu z adresy URL miniatury s výjimkou parametrů a. Potom přidejte `&w` parametr dotazu (šířka) nebo `&h` (výška) s požadovanou velikostí obrázku v obrazových bodech, ale ne obojí. Bing zachová původní poměr stran obrázku. 

Chcete-li zvětšit šířku obrázku určeného výše uvedenou adresou URL na 165 pixelů, použijte následující adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Pokud požadujete obrázek, který je větší než původní velikost obrázku, Bing přidá bílé odsazení kolem obrázku podle potřeby. Pokud je například původní velikost obrázku 474 x 316 a je nastavena `&w` na 500, Bing vrátí obrázek 500x333. Tento obrázek bude mít 8,5 pixelů bílého odsazení podél horního a dolního okraje a 13 pixelů odsazení na levém a pravém okraji.

Chcete-li zabránit bingu v přidání bílého odsazení, pokud `&p` je požadovaná velikost větší než původní velikost obrázku, nastavte parametr dotazu na 0. Pokud například zahrnete `&p=0` parametr do výše uvedené adresy URL, bing vrátí obrázek 474 × 316 namísto obrázku 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Pokud zadáte `&w` parametry `&h` dotazu, Bing zachová poměr stran obrázku a podle potřeby přidá bílé odsazení. Pokud je například původní velikost obrázku 474 x 316 a nastavíte parametry šířky`&w=200&h=200`a výšky na 200 × 200 ( ), vrátí Bing obrázek, který obsahuje 33 pixelů bílého odsazení v horní a dolní části. Pokud zahrnete parametr dotazu, `&p` Bing vrátí obrázek 200x134.

## <a name="crop-a-thumbnail"></a>Oříznutí miniatury 

Chcete-li obrázek `c` oříznout, zahrňte parametr (oříznutého) dotazu. Můžete použít následující hodnoty:
  
- `4`&mdash; Blind poměr  
- `7`&mdash; Inteligentní poměr  

### <a name="smart-ratio-cropping"></a>Inteligentní poměr oříznutí

Pokud požadujete oříznutí inteligentního `c` poměru `7`(nastavením parametru na ), Bing ořízne obraz ze středu jeho oblasti zájmu směrem ven při zachování poměru stran obrázku. Oblast zájmu je oblast obrazu, která Bing určuje obsahuje nejvíce importčástí. Následující ukazuje příklad oblasti zájmu.  
  
![Region zájmu](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Pokud změníte velikost obrázku a požádáte o oříznutí inteligentního poměru, Bing zmenší obraz na požadovanou velikost při zachování poměru stran. Bing pak ořízne obraz na základě rozměrů velikosti. Pokud je například velikost šířky menší nebo rovna výšce, bing ořízne obraz vlevo a vpravo od středu oblasti zájmu. V opačném případě bing ořízne ji na začátek a dolů středu oblasti zájmu.  
  
 
Následující obrázek zredukovaný na 200x200 pomocí oříznutí inteligentního poměru. Protože Bing měří obraz z levého horního rohu, je spodní část obrazu oříznuta. 
  
![Obrázek na šířku oříznutý na 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Následující obrázek zredukovaný na 200x100 pomocí oříznutí inteligentního poměru. Protože Bing měří obraz z levého horního rohu, je spodní část obrazu oříznuta. 
   
![Obrázek na šířku oříznutý na 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Následující obrázek zredukovaný na 100x200 pomocí oříznutí inteligentního poměru. Protože Bing měří obraz ze středu, oříznou se levá a pravá část obrazu.
  
![Obrázek na šířku oříznutý na 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Pokud Bing nemůže určit oblast zájmu obrázku, služba použije oříznutí Blind Ratio.  

### <a name="blind-ratio-cropping"></a>Oříznout poměr oříznutí

Pokud požadujete oříznutí Blind Ratio `c` (nastavením parametru `4`na ), bing použije k oříznutí obrazu následující pravidla.  
  
- Pokud `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`se obraz změří z levého horního rohu a ořízne se dole.  
- Pokud `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`se obraz změří od středu a ořízne doleva a doprava.  

Následující obrázek na výšku je 225x300.  
  
![Původní slunečnicový obraz](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Následující obrázek zredukovaný na 200x200 pomocí oříznutí Blind Ratio. Obraz se měří od levého horního rohu, což vede k oříznutí spodní části obrazu.  
  
![Slunečnicový obrázek oříznutý na 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Následující obrázek zredukovaný na 200x100 pomocí oříznutí Blind Ratio. Obraz se měří od levého horního rohu, což vede k oříznutí spodní části obrazu.  
  
![Slunečnicový obrázek oříznutý na 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Následující obrázek zredukovaný na 100x200 pomocí oříznutí Blind Ratio. Obraz se měří od středu, což vede k oříznutí levé a pravé části obrazu.  
  
![Slunečnicový obrázek oříznutý na 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Další kroky

* [Co jsou vyhledávací api Bingu?](bing-api-comparison.md)
* [Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití](use-display-requirements.md)
