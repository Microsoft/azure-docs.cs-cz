---
title: Odstranění replikace svazků nebo svazků pro replikaci Azure NetApp Files mezi oblastmi | Microsoft Docs
description: V této části najdete popis postupu odstranění připojení replikace, které už mezi zdrojovým a cílovým svazkem není potřeba.
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
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249885"
---
# <a name="delete-volume-replications-or-volumes"></a>Odstranit replikace svazků nebo svazky

Tento článek popisuje, jak odstranit replikaci svazků. Také popisuje, jak odstranit zdrojový nebo cílový svazek.

## <a name="delete-volume-replications"></a>Odstranit replikace svazků

Odstraněním replikace svazku můžete ukončit připojení replikace mezi zdrojovým a cílovým svazkem. Je nutné odstranit replikaci z cílového svazku. Operace odstranění odebírá jenom autorizaci pro replikaci. neodebere zdrojový nebo cílový svazek. 

1. Před odstraněním replikace svazku se ujistěte, že partnerský vztah replikace byl přerušen. Přerušení partnerského vztahu replikace: 

    1. Vyberte *cílový* svazek. Klikněte na **replikace** v části služba úložiště.  

    2.  Než budete pokračovat, ověřte následující pole:  
        * Ujistěte se, že stav zrcadlení znázorňuje ***zrcadlené** _.   
            Nepokoušejte se přerušit partnerský vztah replikace, pokud stav zrcadlení ukazuje _Uninitialized *.
        * Ujistěte se, že stav vztahu zobrazuje ***nečinné** _.   
            Neprovádějte pokus o přerušení replikace partnerských vztahů, pokud stav relace zobrazuje _Transferring *.   

        Podívejte se [na téma zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md). 

    3.  Klikněte na **přerušit partnerský vztah**.  

    4.  Po zobrazení výzvy zadejte **Ano** a klikněte na tlačítko **přerušit**. 

        ![Přerušit partnerský vztah replikace](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Replikaci svazku odstraníte tak, že vyberete **replikaci** ze zdrojového nebo cílového svazku.  

2. Klikněte na **Odstranit**.    

3. Potvrďte odstranění zadáním **Ano** a kliknutím na **Odstranit**.   

    ![Odstranit replikaci](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Odstranit zdrojové nebo cílové svazky

Pokud chcete odstranit zdrojový nebo cílový svazek, musíte v uvedeném pořadí provést následující kroky. V opačném případě `Volume with replication cannot be deleted` dojde k chybě.  

1. Z cílového svazku [odstraňte replikaci svazku](#delete-volume-replications).   

2. Odstraňte cílový nebo zdrojový svazek podle potřeby tak, že kliknete pravým tlačítkem na název svazku a vyberete **Odstranit**.   

    ![Snímek obrazovky, který zobrazuje nabídku svazku po kliknutí pravým tlačítkem myši.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Řešení potíží při replikaci mezi oblastmi](troubleshoot-cross-region-replication.md)

