---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: b1cf3dc24482da70372f775d0ba63a203df1b285
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571990"
---
Některé odpovědi Bingu zahrnuje adresy URL miniatur obsluhuje Bingu. Můžete změnit velikost a oříznutí obrázků miniatur. 

> [!NOTE]
> Zkontrolujte, velikost a oříznutí miniatury, která poskytují scénáři vyhledávání a respektují práva třetí strany, podle potřeby pomocí rozhraní API Bingu pro vyhledávání a zobrazit požadavky.


Změna velikosti obrázku, zahrnují w (šířka) a (výška) parametrů v adrese URL na miniaturu dotazu. Zadejte šířku a výšku v pixelech. Příklad:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Pokud změníte velikost obrázku, je zachováno jeho poměru stran. Zachovat poměr stran bílé odsazení se můžou přidat na okraj obrázku. Například pokud změníte velikost bitové kopie 480 x 359 200 x 200 bez oříznutí, celou šířku obsahuje bitovou kopii ale výška obsahuje 25 prázdné odsazení v horní a dolní části obrázku v pixelech. Stejné by mít hodnotu true, pokud image byla 359 × 480 měřiče s výjimkou vlevo a pravého ohraničení by obsahovat bílé odsazení. Pokud jste Oříznout okraje obrázku, není přidaná bílé odsazení.  

 
Následující obrázek znázorňuje původní velikost obrázek miniatury (480 x 300).  
  
![Původní bitové kopie na šířku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Následující obrázek znázorňuje obrázek se změněnou velikostí na 200 x 200. Se zachová poměr stran a horní a dolní ohraničení se vyplní prázdné (černý okraj je součástí zobrazíte odsazení).  
  
![Obrázek změněnou velikostí na šířku](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Pokud zadáte dimenze, které jsou větší než původní šířka a výška image, image je, aby bylo vytvořeno bílé na levého a horního ohraničení.  
  
K oříznutí obrázku, zahrnout c (oříznutí) parametr dotazu. Následující jsou možné hodnoty, které můžete zadat.  
  
- 4&mdash;nevidomí poměr  
- 7&mdash;inteligentní poměr  
  
Pokud jste požádali o poměr inteligentní oříznutí (c = 7), na obrázku je oříznutý v centru na obrázku oblasti zájmu pasivního při zachování poměru stran obrázku. Oblasti zájmu je oblast obrázku, který určuje Bingu obsahuje většinu části import. Následující znázorňuje na příkladu oblast zájmu.  
  
![Oblasti zájmu](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Změna velikosti obrázku a požádat o poměr inteligentní oříznutí obrázku sníží na požadovanou velikost při zachování poměru stran. Na obrázku je pak oříznutý podle změněnou dimenze. Například pokud se změněnou velikostí šířka je menší než nebo rovna hodnotě výšky, image je oříznutý vlevo a vpravo od středu oblasti zájmu. V opačném případě je obrázek ořízne na horní a dolní část center oblasti zájmu.  
  
 
Následující obrázek znázorňuje obrázek redukovány na 200 x 200 pomocí inteligentní poměr oříznutí.  
  
![Na šířku obrázku ořízne na 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 100 pomocí inteligentní poměr oříznutí.  
   
![Na šířku obrázku ořízne na 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Následující obrázek znázorňuje obrázek redukovány na 100 x 200 pomocí inteligentní poměr oříznutí.  
  
![Na šířku obrázku ořízne na 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Pokud Bing nelze určit, na obrázku oblasti zájmu, Bing používá nevidomí poměr oříznutí.  
  
Pokud jste požádali o oříznutí nevidomí poměr (c = 4), Bing používá následující pravidla Oříznout okraje obrázku.  
  
- Pokud (původní šířka obrázku / původní výška obrázku) < (požadovaná šířka obrázku / požadovaná výška obrázku), obrázek se měří z horní části v levém horním a oříznou kolem v dolní části.  
- Pokud (původní šířka obrázku / původní výška obrázku) > (požadovaná šířka obrázku / požadovaná výška obrázku), měřeno v Centru pro a ořízne na levou a pravou obrazu.  



Následující příklad zobrazuje na výšku obrázku, který je 225 x 300.  
  
![Původní slunečnicová obrázek](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 200 pomocí nevidomí poměr oříznutí. Na obrázku se měří z levého horního rohu výsledná v dolní části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Následující obrázek znázorňuje obrázek redukovány na 200 x 100 pomocí nevidomí poměr oříznutí. Na obrázku se měří z levého horního rohu výsledná v dolní části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Následující obrázek znázorňuje obrázek redukovány na 100 x 200 pomocí nevidomí poměr oříznutí. Na obrázku se měří v centru, což vede k levé a pravé části obrázku se vyhnete.  
  
![Slunečnicová obrázek ořízne na 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

