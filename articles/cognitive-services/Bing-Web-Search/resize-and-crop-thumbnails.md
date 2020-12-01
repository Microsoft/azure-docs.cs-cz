---
title: Změnit velikost a oříznout miniatury obrázků – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Mezi odpovědi z rozhraní API pro vyhledávání Bingu patří adresy URL k obrázkům miniatur poskytovaných bingem, které můžete měnit velikosti a oříznutí a můžou obsahovat parametry dotazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350479"
---
# <a name="resize-and-crop-thumbnail-images"></a>Změna velikosti a oříznutí obrázků miniatur

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mezi odpovědi z rozhraní API pro vyhledávání Bingu patří adresy URL k obrázkům miniatur poskytovaných bingem, které můžete měnit velikosti a oříznutí a můžou obsahovat parametry dotazu. Příklad:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Pokud zobrazíte podmnožinu těchto miniatur, zadejte možnost zobrazení zbývajících obrázků.

> [!NOTE]
> Ujistěte se, že oříznutí a změna velikosti miniatur budou sloužit jako scénář hledání, který respektují práva třetích stran, jak vyžadují Vyhledávání Bingu rozhraní API [použití a zobrazení požadavků](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Změna velikosti miniatury 

Pokud chcete změnit velikost miniatury, Bing doporučuje, abyste zadali jenom jeden `w` parametr dotazu (Width) nebo `h` (Height) na adrese URL miniatury. Když zadáte jenom výšku nebo šířku, umožníte službě Bing zachovat původní aspekt obrázku. Zadejte šířku a výšku v pixelech. 

Například pokud je původní Miniatura 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

A chcete zmenšit jeho velikost, nastavit `w` parametr na novou hodnotu (například `336` ) a odebrat `h`  parametr:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Pokud zadáte pouze výšku nebo šířku miniatury, bude zachován původní poměr stran obrázku. Pokud zadáte oba parametry a poměr stran se nezachová, Bing přidá bílé odsazení k okraji obrázku.

Pokud například změníte velikost 480x359 obrázku na 200x200 bez ořezávání, plná šířka bude mít obrázek, ale výška bude obsahovat 25 pixelů bílého odsazení v horní a dolní části obrázku. Pokud se obrázek 359x480, levý a pravý okraj by obsahoval bílé odsazení. Pokud oříznete obrázek, nepřidá se bílé odsazení.  

Následující obrázek ukazuje původní velikost obrázku miniatury (480x300).  
  
![Obrázek původní na šířku](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Následující obrázek ukazuje, že se velikost obrázku změní na 200x200. Poměr stran se zachová a horní a dolní ohraničení jsou doplněna bílou (k zobrazení odsazení je uvedené černé ohraničení).  
  
![Obrázek se změněnou velikostí na šířku](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Pokud zadáte rozměry, které jsou větší než původní šířka a výška obrázku, Bing přidá bílé odsazení k levému a hornímu okraji.  

## <a name="request-different-thumbnail-sizes"></a>Žádosti o jiné velikosti miniatur

Chcete-li požádat o jinou velikost obrázku miniatur, odeberte všechny parametry dotazu z adresy URL miniatury s výjimkou `id` `pid` parametrů a. Pak přidejte `&w` parametr dotazu (Width) nebo `&h` (Height) s požadovanou velikostí obrázku v pixelech, ale ne obojí. Bing bude udržovat původní poměr stran obrázku. 

Chcete-li zvětšit šířku obrázku určeného výše uvedenou adresou URL na 165 pixelů, použijte následující adresu URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Pokud si vyžádáte obrázek, který je větší než původní velikost obrázku, Bing podle potřeby přidá bílé odsazení kolem obrázku. Pokud je například původní velikost obrázku 474x316 a nastavíte `&w` 500, Bing vrátí image 500x333. Tento obrázek bude mít 8,5 pixelů bílého odsazení podél horního a dolního okraje a 13 pixelů odsazení na levém a pravém okraji.

Pokud chcete zabránit tomu, aby se v Bingu přidalo bílé odsazení, pokud je požadovaná velikost větší než původní velikost obrázku, nastavte `&p` parametr dotazu na 0. Například pokud zahrnete `&p=0` parametr do výše uvedené adresy URL, Bing vrátí obrázek 474x316 místo image 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Pokud zadáte `&w` `&h` parametry dotazu i a, Bing bude udržovat poměr stran obrázku a podle potřeby přidá bílé odsazení. Pokud je například původní velikost obrázku 474x316 a nastavíte parametry Width a Height na 200x200 ( `&w=200&h=200` ), Bing vrátí obrázek, který obsahuje 33 pixelů bílého odsazení v horní a dolní části. Pokud zahrnete `&p` parametr dotazu, Bing vrátí 200x134 image.

## <a name="crop-a-thumbnail"></a>Oříznutí miniatury 

Chcete-li oříznout obrázek, zahrňte `c` parametr dotazu (ořízne). Můžete použít následující hodnoty:
  
- `4`&mdash;Neslepý poměr  
- `7`&mdash;Inteligentní poměr  

### <a name="smart-ratio-cropping"></a>Oříznutí inteligentního poměru

Pokud si vyžádáte oříznutí inteligentního poměru (nastavením `c` parametru na `7` ), Bing ořízne obrázek od středu své oblasti zájmu směrem ven a přitom zachovává poměr stran obrázku. Oblast zájmu je oblast obrázku, kterou Bing určí, obsahuje nejvíce importovaných částí. Níže vidíte ukázkovou oblast zájmu.  
  
![Oblast zájmu](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Pokud změníte velikost obrázku a vyžádáte oříznutí inteligentního poměru, Bing při zachování poměru stran zmenší obrázek na požadovanou velikost. Bing pak ořízne obrázek na základě dimenzí se změněnou velikostí. Pokud je například šířka se změněnou velikostí menší nebo rovna výšce, Bing ořízne obrázek vlevo a vpravo od středu oblasti zájmu. V opačném případě bude Bing v oblasti zájmu oříznout na horní a dolní část centra.  
  
 
Následující obrázek ukazuje obraz zmenšený na 200x200 pomocí ořezávání inteligentního poměru. Vzhledem k tomu, že Bing měří obrázek z levého horního rohu, je dolní část obrázku oříznuta. 
  
![Obrázek na šířku oříznutý na 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Následující obrázek ukazuje obraz zmenšený na 200x100 pomocí ořezávání inteligentního poměru. Vzhledem k tomu, že Bing měří obrázek z levého horního rohu, je dolní část obrázku oříznuta. 
   
![Obrázek na šířku oříznutý na 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Následující obrázek ukazuje obraz zmenšený na 100x200 pomocí ořezávání inteligentního poměru. Vzhledem k tomu, že Bing měří obrázek z centra, jsou oříznuté levé a pravé části obrázku.
  
![Obrázek na šířku oříznutý na 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Pokud Bing nemůže určit oblast obrázku, která je zajímavá, bude tato služba používat oříznutí poměru.  

### <a name="blind-ratio-cropping"></a>Oříznutí poměru rolety

Pokud si vyžádáte oříznutí poměru sleposti (nastavením `c` parametru na hodnotu `4` ), Bing pomocí následujících pravidel ořízne obrázek.  
  
- Pokud se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` Obrázek měří z levého horního rohu a v dolní části se ořízne.  
- Pokud `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` je obrázek měřen od středu a oříznutý vlevo a vpravo.  

Níže vidíte obrázek na výšku, který je 225x300.  
  
![Původní slunečnicová image](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Následující obrázek ukazuje obraz zmenšený na 200x200 pomocí oříznutí poměru po sleposti. Obrázek se měří od levého horního rohu, které má za následek oříznutí dolní části obrázku.  
  
![Slunečnicová image oříznutá na 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Následující obrázek ukazuje obraz zmenšený na 200x100 pomocí oříznutí poměru po sleposti. Obrázek se měří od levého horního rohu, které má za následek oříznutí dolní části obrázku.  
  
![Slunečnicová image oříznutá na 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Následující obrázek ukazuje obraz zmenšený na 100x200 pomocí oříznutí poměru po sleposti. Obrázek se měří od středu, což znamená, že se oříznou levou a pravou část obrázku.  
  
![Slunečnicová image oříznutá na 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro vyhledávání Bingu?](bing-api-comparison.md)
* [Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití](use-display-requirements.md)