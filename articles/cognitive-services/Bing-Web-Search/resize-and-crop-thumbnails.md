---
title: Změnit velikost a ořízne obrázek miniatury – rozhraní API webové vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak změnit velikost a oříznout miniatury poskytované rozhraní API pro vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867895"
---
# <a name="resize-and-crop-thumbnail-images"></a>Změna velikosti a oříznutí obrázků miniatur

Některé odpovědi z rozhraní API pro vyhledávání Bingu zahrnují adresy URL obsluhují Bingu, které můžete změnit velikost a oříznutí obrázků miniatur a může obsahovat parametry dotazu. Příklad:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Pokud můžete zobrazit pouze podmnožinu těchto miniatury, zadejte možnost zobrazit zbývající bitové kopie.

> [!NOTE]
> Ujistěte se, že oříznutí a změna velikosti miniatur poskytne hledání scénář, který dodržuje práva třetí strany, podle požadavků rozhraní API Bingu pro vyhledávání [použít a zobrazit požadavky](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Velikost miniatury 

Ke změně velikosti miniatury, doporučuje Bing, zadejte pouze jeden `w` (šířka) nebo `h` (výška) parametry dotazu v adrese URL na miniaturu. Určení pouze výšku nebo šířku umožňuje Bingu udržovat aspekt původní image. Zadejte šířku a výšku v pixelech. 

Například, pokud je původní miniaturu 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

A chcete zmenšit jeho velikost, nastavte `w` parametr na novou hodnotu (například `336`) a odeberte `h` parametr:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Pokud zadáte pouze výšku nebo width miniatury, budou zachované původní poměr stran obrázku. Pokud zadáte oba parametry a poměr stran se neuchovávají, Bingu přidá odsazení bílé ohraničení obrázku.

Například pokud změníte velikost bitové kopie 480 x 359 200 x 200 bez oříznutí, celou šířku bude obsahovat obrázek ale výška bude obsahovat 25 prázdné odsazení v horní a dolní části obrázku v pixelech. Pokud image byla 359 × 480 měřiče, levé a pravé ohraničení by obsahovat bílé odsazení. Pokud jste Oříznout okraje obrázku, není přidaná bílé odsazení.  

Následující obrázek znázorňuje původní velikost obrázek miniatury (480 x 300).  
  
![Původní bitové kopie na šířku](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Následující obrázek znázorňuje obrázek se změněnou velikostí na 200 x 200. Se zachová poměr stran a horní a dolní ohraničení se vyplní prázdné (černý okraj je součástí zobrazíte odsazení).  
  
![Obrázek změněnou velikostí na šířku](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Pokud chcete zadat dimenzí, které jsou větší než původní šířka a výška obrázku, Bingu bílé odsazení přidá do levého a horního ohraničení.  

## <a name="request-different-thumbnail-sizes"></a>Požádat o různých velikostech miniatur

Chcete-li jiný obrázek miniatury velikost požadavku, odeberte všechny parametry dotazu z adresy URL na miniaturu, s výjimkou `id` a `pid` parametry. Pak přidejte buď `&w` (šířka) nebo `&h` parametr dotazu (výška) s požadovanou image velikost v pixelech, ale ne obojí. Bing zachová původní poměr stran obrázku. 

Chcete-li zvětšit šířku obrázku určeném výše zobrazenou adresu URL na 165 pixelů, použijte následující adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Pokud jste požádali o bitovou kopii, která je větší než původní velikost obrázku, přidá Bingu bílé výplně kolem obrázku podle potřeby. Například pokud je na obrázku původní velikost je 474 x 316 a nastavíte `&w` na 500, Bing vrátí 500 × 333 image. Tato image bude mít 8,5 pixelů podél horní a dolní okraj a odsazení v levé a pravé hrany v pixelech 13 bílé odsazení.

Chcete-li zabránit přidání bílého odsazení Pokud požadovaná velikost je větší než původní velikost obrázku Bing, nastavte `&p` dotazování parametru na hodnotu 0. Například pokud zahrnete `&p=0` parametr výše zobrazenou adresu URL, Bing vrátí 474 x 316 image místo 500 × 333 image:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Pokud zadáte obě `&w` a `&h` parametry dotazu, Bingu se zachovat poměr stran obrázku a přidá bílé odsazení podle potřeby. Například pokud je na obrázku původní velikost je 474 x 316 a nastavte parametry šířku a výšku na 200 x 200 (`&w=200&h=200`), Bing vrátí bitovou kopii, která obsahuje 33 pixelů bílé odsazení nahoře a dole. Pokud zahrnete `&p` parametr dotazu Bing vrátí 200 x 134 image.

## <a name="crop-a-thumbnail"></a>Oříznout Miniatura 

Oříznutí obrázku, zahrňte `c` parametr dotazu (oříznutí). Můžete použít následující hodnoty:
  
- `4` &mdash; Skrytá poměr  
- `7` &mdash; Inteligentní poměr  

### <a name="smart-ratio-cropping"></a>Inteligentní oříznutí poměr

Pokud jste požádali o poměr inteligentní oříznutí (nastavením `c` parametr `7`), Bingu se oříznutí obrázku v centru jeho oblasti zájmu směrem ven, při zachování poměru stran obrázku. Oblasti zájmu je oblast obrázku, který určuje Bingu obsahuje většinu části import. Následující znázorňuje na příkladu oblast zájmu.  
  
![Oblasti zájmu](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Pokud změnu velikosti obrázku a požádat o poměr inteligentní oříznutí, snižuje Bingu bitovou kopii na požadovanou velikost při zachování poměru stran. Bing potom obrázek ořízne tak, na základě změněnou rozměrů. Například pokud se změněnou velikostí šířka je menší než nebo rovna hodnotě výšky, Bingu se Oříznout okraje obrázku, vlevo a vpravo od středu oblasti zájmu. V opačném případě Bingu se oříznout ji na horní a dolní část center oblasti zájmu.  
  
 
Následující obrázek znázorňuje obrázek redukovány na 200 x 200 pomocí inteligentní poměr oříznutí. Protože Bingu měří image z levého horního rohu, dolní část obrázku je oříznutý. 
  
![Na šířku obrázku ořízne na 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 100 pomocí inteligentní poměr oříznutí. Protože Bingu měří image z levého horního rohu, dolní část obrázku je oříznutý. 
   
![Na šířku obrázku ořízne na 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Následující obrázek znázorňuje obrázek redukovány na 100 x 200 pomocí inteligentní poměr oříznutí. Protože Bingu měří image z centra, levé a pravé části obrázku jsou oříznuté.
  
![Na šířku obrázku ořízne na 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Pokud Bing nelze určit, na obrázku oblasti zájmu, bude služba používat nevidomí poměr oříznutí.  

### <a name="blind-ratio-cropping"></a>Skrytá poměr oříznutí

Pokud jste požádali o oříznutí nevidomí poměr (nastavením `c` parametr `4`), Bing používá následující pravidla Oříznout okraje obrázku.  
  
- Pokud `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, bitové kopie je měřená od levého horního rohu a oříznou kolem v dolní části.  
- Pokud `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, bitové kopie se měří v Centru pro a ořízne na levou a pravou.  

Následující příklad zobrazuje na výšku obrázku, který je 225 x 300.  
  
![Původní slunečnicová obrázek](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 200 pomocí nevidomí poměr oříznutí. Na obrázku se měří z levého horního rohu, což vede k dolní části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 100 pomocí nevidomí poměr oříznutí. Na obrázku se měří z levého horního rohu, což vede k dolní části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Následující obrázek znázorňuje obrázek redukovány na 100 x 200 pomocí nevidomí poměr oříznutí. Na obrázku se měří v centru, což vede k levé a pravé části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Další postup

* [Co jsou rozhraní API pro vyhledávání Bingu?](bing-api-comparison.md)
* [Použití rozhraní API Bingu pro vyhledávání a zobrazit požadavky](use-display-requirements.md)
