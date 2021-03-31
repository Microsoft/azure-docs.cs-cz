---
title: Nákladový model pro Azure NetApp Files | Microsoft Docs
description: Popisuje nákladový model pro Azure NetApp Files pro správu nákladů ze služby.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325602"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model nákladů pro Azure NetApp Files 

Princip nákladového modelu pro Azure NetApp Files vám pomůže se správou vašich výdajů ze služby. 

Nákladový model specifický pro replikaci mezi oblastmi najdete v tématu [cost model pro replikaci mezi jednotlivými oblastmi](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Výpočet spotřeby kapacity

Azure NetApp Files se účtuje podle zřízené kapacity úložiště.  Zřízená kapacita se přiděluje vytvořením fondů kapacit.  Fondy kapacity se účtují na základě $/provisioned-GiB/month v hodinových přírůstcích. Minimální velikost pro jeden fond kapacity je 4 TiB a fondy kapacity je možné následně rozbalí v 1-TiB přírůstcích. Svazky se vytvářejí v rámci fondů kapacity.  Každému svazku je přiřazena kvóta, která se snižuje z kapacity zřízené fondy. Kvóta, kterou je možné přiřadit k rozsahům svazků z minimálně 100 GiB, až do 100 TiB.  

V případě aktivního svazku je spotřeba kapacity proti kvótě založená na logické (efektivní) kapacitě.

Pokud skutečnou spotřebou kapacity svazku překročí kvótu úložiště, může se svazek dál zvětšovat. Zápisy budou i nadále povoleny, dokud je skutečná velikost svazku menší než omezení systému (100 TiB).  

Celková využitá kapacita fondu kapacity na základě jeho zřízené částky je součtem většího z přidělené kvóty nebo skutečné spotřeby všech svazků v rámci fondu: 

   ![Výpočet využité kapacity celkem](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Následující diagram znázorňuje tyto koncepty.  
* Máme fond kapacit s 4 TiBy zřízené kapacity.  Fond obsahuje tři svazky.  
    * Ke svazku 1 je přiřazena kvóta 2 TiB a má 800 GiB spotřeby.  
    * Ke svazku 2 je přiřazena kvóta 1 TiB a má 100 GiB spotřeby.  
    * Ke svazku 3 je přiřazena kvóta 500 GiB, ale má 800 GiB spotřeby (nadlimitní využití).  
* Fond kapacit se měří na 4 TiB kapacity (zřízené množství).  
    3,8 TiB kapacity (2 TiB a 1 TiB kvóta ze svazků 1 a 2 a 800 GiB ze skutečné spotřeby pro svazek 3). A zbývá 200 GiB kapacity.

   ![Fond kapacity se třemi svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Nadlimitní využití kapacity  

Pokud celková využitá kapacita fondu překročí zřízenou kapacitu, budou zápisy dat stále povoleny.  Po uplynutí období odkladu (jedna hodina) i v případě, že využitá kapacita fondu stále přesáhne zřízenou kapacitu, bude velikost fondu automaticky zvýšena v přírůstcích po 1 TiB, dokud není zřízená kapacita větší než celková využitá kapacita.  Například na ilustraci výše se pokud jednotka 3 stále rozrůstá a skutečná spotřeba dosáhne 1,2 TiB, pak po uplynutí období odkladu se automaticky změní velikost fondu na 5 TiB.  Výsledkem je, že kapacita zřízeného fondu (5 TiB) překračuje využitou kapacitu (4,2 TiB).  

I když velikost fondu kapacity se automaticky zvětšuje tak, aby splňovala požadavky svazku, při snížení velikosti svazku se automaticky nesníží. Pokud chcete fond kapacit zmenšit po zmenšení velikosti svazku (například po vyčištění dat svazku), musíte velikost fondu kapacity _ručně_ snížit.

## <a name="manual-changes-of-the-pool-size"></a>Ruční změny velikosti fondu  

Velikost fondu můžete zvětšit nebo zmenšit ručně. Platí ale následující omezení:
* Minimální a maximální limity služby  
    Přečtěte si článek o [omezeních prostředků](azure-netapp-files-resource-limits.md).
* TiB přírůstek po počátečním 4 TiB minimálním nákupu
* Minimální přírůstek fakturace za hodinu
* Velikost zřízeného fondu se nemusí snížit na míň než celková využitá kapacita fondu.
* U fondů kapacit s ruční technologií QoS se dá velikost fondu snížit jenom v případě, že velikost a úroveň služby poskytují větší propustnost než skutečná přiřazená propustnost všech svazků.

## <a name="behavior-of-maximum-size-pool-overage"></a>Chování maximální velikosti nadlimitního fondu   

Maximální velikost fondu kapacit, který můžete vytvořit nebo změnit jeho velikost, je 500 TiB.  Pokud celková využitá kapacita ve fondu kapacit překračuje 500 TiB, dojde k následujícím situacím:
* Zápisy dat budou pořád povoleny (Pokud je svazek pod maximem systému 100 TiB).
* Po uplynutí hodinové odkladové lhůty se fond automaticky změní na TiB zvýšení velikosti, dokud zajištěná kapacita fondu nepřekročí celkovou využitou kapacitu.
* Další zřízené a fakturované kapacity fondu vyšší než 500 TiB se nedají použít k přiřazení kvóty svazku. Nedají se také použít k rozšíření omezení technologie QoS výkonu.  
    Podívejte se na [úrovně služeb](azure-netapp-files-service-levels.md) o omezeních výkonu a o velikosti QoS.

Následující diagram znázorňuje tyto koncepty:
* Máme fond kapacit s úrovní Premium Storage a kapacitou 500-TiB. Fond obsahuje devět svazků.
    * K svazkům 1 až 8 je přiřazena kvóta 60 TiB.  Celková využitá kapacita je 480 TiB.  
        Každý svazek má omezení QoS 3,75 GiB/s propustnost (60 TiB * 64 MiB/s).  
    * Na jednotce 9 je přiřazena kvóta 20 TiB.  
        Svazek 9 má omezení QoS 1,25 GiB/s propustnost (20 TiB * 64 MiB/s).
* Svazek 9 je nadlimitní scénář. Má 25 TiB se skutečnou spotřebou.  
    * Po hodinové odkladné době se velikost fondu kapacity změní na 505 TiB.  
        To znamená, že celková využitá kapacita = 8 × 60-TiB kvóta pro svazky 1 až 8 a 25 TiB skutečné spotřeby pro svazek 9.
    * Fakturovaná kapacita je 505 TiB.
    * Kvótu svazku pro svazek 9 nelze zvýšit (protože celková přidělená kvóta pro fond nesmí překročit 500 TiB).
    * Další propustnost technologie QoS možná nebude přiřazena (protože celková technologie QoS pro fond je stále založená na 500 TiB).

   ![Fond kapacit s devíti svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Spotřeba kapacity snímků 

Spotřeba kapacity snímků v Azure NetApp Files se účtuje na základě kvóty nadřazeného svazku.  V důsledku toho sdílí stejnou fakturační sazbu jako fond kapacit, do kterého patří svazek.  Na rozdíl od aktivního svazku se ale spotřeba snímků měří na základě přírůstkové spotřebované kapacity.  Azure NetApp Files snímky mají rozdíl v podstatě. V závislosti na rychlosti změny dat mají snímky často větší kapacitu, než je logická kapacita aktivního svazku. Předpokládejme například, že máte snímek 500ho svazku, který obsahuje pouze 10 GiB rozdílových dat. Kapacita, která se účtuje na základě kvóty svazku pro tento snímek, bude 10 GiB, ne 500 GiB. 

## <a name="next-steps"></a>Další kroky

* [Stránka s cenami Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Nákladový model pro replikaci mezi oblastmi](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
