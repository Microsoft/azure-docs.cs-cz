---
title: Model nákladů pro soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje model nákladů pro soubory Azure NetApp pro správu výdajů ze služby.
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
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524217"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model nákladů pro Azure NetApp Files 

Principy modelu nákladů pro soubory NetApp Azure vám pomůže spravovat vaše náklady ze služby.

## <a name="calculation-of-capacity-consumption"></a>Výpočet spotřebu kapacity

Služba soubory Azure NetApp se účtuje na kapacitě zřízeného úložiště.  Zřízená kapacita bude přidělena tak, že vytvoříte fondy kapacity.  Kapacity fondů se účtují podle $/ zřízené GB/měsíc hodinový přírůstky. Minimální velikost pro jednu kapacitu fondu je 4 TB a fondy kapacity se následně rozšířit v přírůstcích po 1 TB. Svazky jsou vytvořeny v rámci kapacity fondů.  Každý svazek je přiřazená kvóta této sníží z fondy zřízené kapacity. Kvóty, jemuž lze přiřadit ke svazkům rozsahů v rozmezí od 100 GiB maximálně 92 TiB.  

Pro svazek aktivní spotřebu kapacity proti kvóty je na základě logické (skutečná) kapacity.

Pokud využívání aktuální kapacita svazku překročí svou kvótu úložiště, svazek můžete nadále rozšiřovat. Zápisy se ještě povolené jako skutečný objem velikost je menší než omezení systému (100 TB).  

Celková kapacita používané v rámci kapacity fondu před jeho zřízená velikost je součtem delší než přidělenou kvótu nebo skutečné spotřebě všechny svazky ve fondu: 

   ![Výpočet celkového použitá kapacita](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Následující diagram znázorňuje tyto koncepty.  
* Máme kapacity fondu s 4 TB zřízená kapacita.  Tento fond obsahuje tři svazky.  
    * Svazek 1 je přiřazená kvóta 2 TB a 800 GB spotřeby.  
    * Svazek 2 je přiřazená kvóta 1 TB a 100 GB spotřeby.  
    * Svazek 3 je přiřazená kvóta 500 GB, ale má 800 GB spotřeby (Nadlimitní využití).  
* Pro 4 TB kapacity (zřízená velikost) se měří kapacita fondu.  
    3.8 spotřebované TiB kapacity (2 TB a 1 TiB kvóty ze svazků 1 a 2 a 800 GB skutečné spotřeby pro svazek 3). A zbývajících 200 GB kapacity.

   ![Kapacita fondu se tři svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Nadlimitní události úrovně v spotřebu kapacity  

Při použití celkové kapacity fondu překračuje jeho zřízená kapacita, zápisy dat je ještě povolené.  Po skončení období odkladu (jedna hodina) Pokud použitá kapacita fondu pořád překračuje zřízené kapacity, pak velikost fondu se automaticky zvýší v krocích po 1 TiB dokud zřízená kapacita je větší než celkový počet použitá kapacita.  Například na obrázku výše, pokud svazek 3 se stále rozrůstá a skutečné spotřebě dosáhne 1,2 TB, pak po skončení období odkladu velikost fondu se automaticky se nastaví na 5 TB.  Výsledkem je, že kapacita fondu zřízené (5 TiB) překračuje použitá kapacita (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Ruční změny velikosti fondu  

Můžete ručně zvýšit nebo snížit velikost fondu. Nicméně platí následující omezení:
* Minimální a maximální limity služby  
    Přečtěte si článek [omezení prostředků](azure-netapp-files-resource-limits.md).
* Krok 1 TiB po počáteční 4-TiB minimální částka nákupu
* Součástí hodinové fakturace přírůstek minimální
* Velikost zřízeného fondu není možné snížit na hodnotu menší než celková kapacita využitá ve fondu.

## <a name="behavior-of-maximum-size-pool-overage"></a>Chování překročení maximální velikosti fondu   

Maximální velikost kapacity fondu, který můžete vytvořit nebo změnit velikost na je 500 TB.  Při použití celkové kapacity v kapacitě fondu překročí 500 TB, dojde v následujících případech:
* Za zápisy dat se ještě povolené (Pokud je svazek pod systém maximálně 100 TB).
* Po skončení poskytnuté lhůty hodinová fondu bude automaticky nastavena velikost v přírůstcích po 1 TB, dokud kapacita fondu zřízené překročí celkový počet použitá kapacita.
* Další zřízený a kapacita fondu, vyšší než 500 TB se nedá použít k přiřazení kvóty se neúčtují. Je také nelze použít rozšíření QoS omezení výkonu.  
    Zobrazit [úrovně služby](azure-netapp-files-service-levels.md) o limity pro výkon a změna velikosti technologie QoS.

Následující diagram znázorňuje tyto koncepty:
* Máme kapacitu fondu úložiště úrovně Premium a 500 TB kapacity. Tento fond obsahuje devět svazky.
    * Svazky 1 až 8 jsou přiřazeny kvóta 60 TB.  Celkový počet použitá kapacita je 480 TiB.  
        Každý svazek má limit QoS 3,75 GiB/s propustností (60 TiB * 64 MiB/s).  
    * Svazek 9 se přiřadí se kvóta 20 TB.  
        Technologie QoS limit 1,25 GiB/s propustnosti je svazek 9 (60 TiB * 64 MiB/s).
* Při překročení limitu scénář je svazek 9. Má 25 TiB skutečné spotřebě.  
    * Po skončení období odkladu hodinová bude se velikost kapacity fondu nastaví na 505 TiB.  
        To znamená, celková kapacita využitá 8 * 60 =-TiB kvótu pro svazky 1 až 8 až 25 TiB skutečné spotřeby pro svazek 9.
    * Billed kapacita je 505 TiB.
    * Kvóty pro svazek 9 nelze zvýšit, (protože celkový počet přidělenou kvótu pro fond nesmí přesáhnout 500 TB).
    * Větší propustnost QoS nesmí přiřazovat (protože celkový počet technologie QoS pro fond je stále založená na 500 TB).

   ![Kapacita fondu se devět svazky](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Využití kapacity snímků 

Využití kapacity snímků ve službě soubory Azure NetApp je strhávat kvóty nadřazené svazku.  V důsledku toho, která sdílí stejné fakturační sazba jako kapacita fondu, ke kterému patří svazku.  Na rozdíl od active svazku se měří na základě využité přírůstkové kapacity spotřeby snímku.  Azure souborů NetApp snímky jsou rozdílové ze své podstaty. V závislosti na frekvenci změn dat snímky využívají často mnohem nižší kapacitu než kapacita logické aktivního svazku. Například předpokládejme, že máte snímku svazku 500 GB, který obsahuje jenom 10 GB rozdílová data. Kapacita, který je poplatek účtovat podle kvóty pro tento snímek bude 10 GB, ne 500 GB. 

## <a name="next-steps"></a>Další postup

* [Soubory Azure NetApp stránce s cenami](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Úrovně služeb pro soubory Azure NetApp](azure-netapp-files-service-levels.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
