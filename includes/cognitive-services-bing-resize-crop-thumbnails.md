---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "67868980"
---
Mezi odpovědi Bingu patří adresy URL pro miniatury, které obsluhuje Bing. Můžete změnit velikost obrázků miniatur a jejich oříznutí. 

> [!NOTE]
> Zajistěte, aby velikost a oříznutí miniatury poskytovaly scénář hledání a dodržovala práva třetích stran, jak vyžadují Vyhledávání Bingu používání rozhraní API a požadavky na zobrazení.


Chcete-li změnit velikost obrázku, zahrňte parametr dotazu w (Width), parametr dotazu (Height), nebo obojí v adrese URL miniatury. Zadejte šířku a výšku v pixelech. Příklad:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Pokud zadáte pouze šířku nebo pouze parametr dotazu Height, zachová Bing poměr stran obrázku. Pokud zadáte šířku i výšku a nezachováte původní poměr stran obrázku, Bing přidá bílé odsazení k okraji obrázku. Pokud například změníte velikost 480x359 obrázku na 200x200 bez ořezávání, plná šířka obsahuje obrázek, ale výška obsahuje 25 pixelů bílého odsazení v horní a dolní části obrázku. Totéž by platilo, pokud se obrázek 359x480 s výjimkou levého a pravého ohraničení, které obsahuje bílé odsazení. Pokud oříznete obrázek, nepřidá se bílé odsazení.  

 
Následující obrázek ukazuje původní velikost obrázku miniatury (480x300).  
  
![Obrázek původní na šířku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Následující obrázek ukazuje, že se velikost obrázku změní na 200x200. Poměr stran se zachová a horní a dolní ohraničení jsou doplněna bílou (k zobrazení odsazení je vloženo černé ohraničení).  
  
![Obrázek se změněnou velikostí na šířku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Pokud zadáte rozměry, které jsou větší než původní šířka a výška obrázku, obrázek je doplněn bílým vlevo a horním okrajem.  
  
Chcete-li oříznout obrázek, zahrňte parametr dotazu c (ořízne). Níže jsou uvedené možné hodnoty, které můžete zadat.  
  
- 4&mdash;rovný poměr  
- 7&mdash;inteligentní poměr  
  
Pokud si vyžádáte oříznutí inteligentního poměru (c = 7), obrázek se ořízne od středu oblasti obrázku, která je zajímavá směrem ven a zároveň se zachováním poměru stran obrázku. Oblast zájmu je oblast obrázku, kterou Bing určí, obsahuje nejvíce importovaných částí. Níže vidíte ukázkovou oblast zájmu.  
  
![Oblast zájmu](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Pokud změníte velikost obrázku a vyžádáte oříznutí inteligentního poměru, obrázek se při zachování poměru stran zmenší na požadovanou velikost. Obrázek se pak ořízne na základě dimenzí se změněnou velikostí. Pokud je například šířka se změněnou velikostí menší nebo rovna výšce, obrázek se ořízne vlevo a vpravo od středu oblasti zájmu. V opačném případě se obrázek ořízne v horní a dolní části středu oblasti zájmu.  
  
 
Následující obrázek ukazuje obraz zmenšený na 200x200 pomocí ořezávání inteligentního poměru.  
  
![Obrázek na šířku oříznutý na 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Následující obrázek ukazuje obraz zmenšený na 200x100 pomocí ořezávání inteligentního poměru.  
   
![Obrázek na šířku oříznutý na 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Následující obrázek ukazuje obraz zmenšený na 100x200 pomocí ořezávání inteligentního poměru.  
  
![Obrázek na šířku oříznutý na 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Pokud Bing nemůže určit oblast obrázku, která je zajímavá, Bing použije oříznutí nedokončeného poměru.  
  
Pokud si vyžádáte oříznutí poměru sleposti (c = 4), Bing pomocí následujících pravidel ořízne bitovou kopii.  
  
- Pokud (původní šířka obrázku/původní výška obrázku) < (požadovaná šířka obrázku/požadovaná výška obrázku), obrázek se měří z levého horního rohu a ořízne se v dolní části.  
- Pokud (původní šířka obrázku/původní výška obrázku) > (požadovaná šířka obrázku/požadovaná výška obrázku), obrázek se měří od středu a ořízne vlevo a vpravo.  



Níže vidíte obrázek na výšku, který je 225x300.  
  
![Původní slunečnicová image](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Následující obrázek ukazuje obraz zmenšený na 200x200 pomocí oříznutí poměru po sleposti. Obrázek se měří od levého horního rohu, které má za následek oříznutí dolní části obrázku.  
  
![Slunečnicová image oříznutá na 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Následující obrázek ukazuje obraz zmenšený na 200x100 pomocí oříznutí poměru po sleposti. Obrázek se měří od levého horního rohu, které má za následek oříznutí dolní části obrázku.  
  
![Slunečnicová image oříznutá na 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Následující obrázek ukazuje obraz zmenšený na 100x200 pomocí oříznutí poměru po sleposti. Obrázek se měří od středu, což znamená, že se oříznou levou a pravou část obrázku.  
  
![Slunečnicová image oříznutá na 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

