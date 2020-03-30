---
title: Nákladový model pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje nákladový model pro soubory Azure NetApp pro správu výdajů ze služby.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995103"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model nákladů pro Azure NetApp Files 

Principy nákladového modelu pro soubory Azure NetApp vám pomůže spravovat výdaje ze služby.

## <a name="calculation-of-capacity-consumption"></a>Výpočet spotřeby kapacity

Soubory Azure NetApp se účtují z zřízeného úložiště kapacity.  Zřízená kapacita se přiděluje vytvořením fondů kapacity.  Fondy kapacit se účtují na základě $/zřízené-GiB/měsíc v hodinových přírůstcích. Minimální velikost pro jeden fond kapacity je 4 TiB a kapacity fondy mohou být následně rozšířeny v krocích po 1-TiB. Svazky jsou vytvářeny v rámci fondů kapacity.  Každému svazku je přiřazena kvóta, která se snižuje z kapacity zřízené fondy. Kvóta, která může být přiřazena svazkům, se pohybuje od minimálně 100 GiB do maximálně 100 TiB.  

Pro aktivní objem je spotřeba kapacity oproti kvótě založena na logické (efektivní) kapacitě.

Pokud skutečná spotřeba kapacity svazku překročí kvótu úložiště, může svazek nadále růst. Zápisy budou stále povoleny, dokud je skutečná velikost svazku menší než limit systému (100 TiB).  

Celková využitá kapacita v kapacitním fondu oproti její zřízené částce je součtem větší z přidělené kvóty nebo skutečné spotřeby všech objemů v rámci seskupení: 

   ![Výpočet celkové použité kapacity](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Následující diagram znázorňuje tyto pojmy.  
* Máme kapacitní bazén se 4 TiB zřízené kapacity.  Fond obsahuje tři svazky.  
    * 1. dílu je přidělena kvóta 2 TiB a má 800 GIB spotřeby.  
    * Díl2 je přiřazena kvóta 1 TiB a má 100 GIB spotřeby.  
    * Objem 3 je přiřazena kvóta 500 GIB, ale má 800 GIB spotřeby (nadbytek).  
* Kapacitní fond se měří pro 4 TIB kapacity (zřízené částky).  
    Spotřebováno je 3,8 TiB kapacity (2 TiB a 1 TiB kvóty ze svazků 1 a 2 a 800 GB skutečné spotřeby pro svazek 3). A zbývá 200 Gb kapacity.

   ![Kapacitní fond se třemi svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Překročení kapacity  

Pokud celková využitá kapacita fondu překročí jeho zřízenou kapacitu, zápisy dat jsou stále povoleny.  Po uplynutí doby odkladu (jedna hodina), pokud využitá kapacita fondu stále překročí jeho zřízenou kapacitu, bude velikost fondu automaticky zvýšena v přírůstcích po 1 TiB, dokud nebude zřízená kapacita větší než celková využitá kapacita.  Například na obrázku výše, pokud svazek 3 nadále roste a skutečná spotřeba dosáhne 1,2 TiB, pak po uplynutí doby odkladu, fondu se automaticky změní velikost na 5 TiB.  Výsledkem je, že kapacita zřízeného fondu (5 TiB) přesahuje využitou kapacitu (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Ruční změny velikosti fondu  

Velikost fondu můžete zvětšit nebo zmenšit ručně. Platí však následující omezení:
* Minimální a maximální limity služeb  
    Podívejte se na článek o [omezení prostředků](azure-netapp-files-resource-limits.md).
* Přírůstek o 1 TiB po počátečním minimálním nákupu 4-TiB
* Minimální přírůstek minimální fakturace za hodinu
* Velikost zřízeného fondu nesmí být snížena na méně než celková využitá kapacita ve fondu.

## <a name="behavior-of-maximum-size-pool-overage"></a>Chování nadlimitního fondu maximální velikosti   

Maximální velikost fondu kapacity, který můžete vytvořit nebo změnit velikost je 500 TiB.  Pokud celková využitá kapacita v kapacitním fondu přesáhne 500 TiB, nastanou následující situace:
* Zápisy dat budou stále povoleny (pokud je svazek pod maximální min. 100 TiB).
* Po uplynutí hodinové doby odkladu se velikost fondu automaticky změní v přírůstcích po 1 TiB, dokud kapacita zřízeného fondem nepřekročí celkovou využitou kapacitu.
* Dodatečnou kapacitu zřízeného a fakturovaného fondu přesahující 500 TiB nelze použít k přiřazení hromadné kvóty. Nelze jej také použít k rozšíření omezení výkonu QoS.  
    Podívejte se na [úrovně služeb](azure-netapp-files-service-levels.md) o omezení výkonu a velikosti QoS.

Následující diagram znázorňuje tyto pojmy:
* Máme kapacitní fond s úrovní úložiště Premium a kapacitou 500 TiB. Bazén obsahuje devět svazků.
    * Svazkům 1 až 8 je přiřazena kvóta 60 TiB.  Celková využitá kapacita je 480 TiB.  
        Každý svazek má qoS limit 3,75 GiB/s propustnost (60 TiB * 64 MiB/s).  
    * Svazku 9 je přidělena kvóta 20 TiB.  
        Svazek 9 má qoS limit 1,25 GiB/s propustnost (20 TiB * 64 MiB/s).
* Svazek 9 je scénář nadbytku. Má 25 TiB skutečné spotřeby.  
    * Po uplynutí jedné hodiny odkladu se velikost kapacity fondu změní na 505 TiB.  
        To znamená celkovou použitou kapacitu = 8 * 60-TiB kvóta pro svazky 1 až 8 a 25 TiB skutečné spotřeby pro svazek 9.
    * Fakturovaná kapacita je 505 TiB.
    * Kvótu svazku pro svazek 9 nelze zvýšit (protože celková přiřazená kvóta pro fond nesmí překročit 500 TiB).
    * Další propustnost QoS nemusí být přiřazena (protože celkový QoS pro fond je stále založen na 500 TiB).

   ![Kapacitní bazén s devíti svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Spotřeba kapacity snímků 

Využití kapacity snímků v azure netapp soubory se účtuje podle kvóty nadřazeného svazku.  V důsledku toho sdílí stejnou fakturační sazbu jako fond kapacity, do kterého svazek patří.  Na rozdíl od aktivního svazku se však spotřeba snímku měří na základě spotřebované přírůstkové kapacity.  Snímky souborů Azure NetApp jsou rozdílné povahy. V závislosti na rychlosti změny dat snímky často spotřebovávají mnohem méně kapacity než logická kapacita aktivního svazku. Předpokládejme například, že máte snímek svazku 500 GiB, který obsahuje pouze 10 GiB rozdílových dat. Kapacita, která je účtována proti kvótě svazku pro tento snímek by 10 GiB, nikoli 500 GiB. 

## <a name="next-steps"></a>Další kroky

* [Stránka o cenách souborů Azure NetApp](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
