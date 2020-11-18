---
title: Odstranit replikace pro replikaci Azure NetApp Files mezi oblastmi | Microsoft Docs
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695703"
---
# <a name="delete-replications"></a>Odstranění replikací

Odstraněním replikace svazku můžete ukončit připojení replikace mezi zdrojovým a cílovým svazkem. Operaci odstranění můžete provést buď ze zdrojového, nebo cílového svazku. Operace odstranění odebírá jenom autorizaci pro replikaci. neodebere zdrojový nebo cílový svazek. 

## <a name="steps"></a>Postup

1. Před odstraněním replikace svazku se ujistěte, že partnerský vztah replikace byl přerušen.    
    Podívejte se [na téma zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md) a [přerušení partnerského vztahu replikace](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. Replikaci svazku odstraníte tak, že vyberete **replikaci** ze zdrojového nebo cílového svazku.  

2. Klikněte na **Odstranit**.    

3. Potvrďte odstranění zadáním **Ano** a kliknutím na **Odstranit**.   

    ![Odstranit replikaci](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Řešení potíží při replikaci mezi oblastmi](troubleshoot-cross-region-replication.md)

