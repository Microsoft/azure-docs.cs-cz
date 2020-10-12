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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708660"
---
# <a name="delete-replications"></a>Odstranění replikací

Odstraněním replikace svazku můžete ukončit připojení replikace mezi zdrojovým a cílovým svazkem. Operaci odstranění můžete provést buď ze zdrojového, nebo cílového svazku. Operace odstranění odebírá jenom autorizaci pro replikaci. neodebere zdrojový nebo cílový svazek. 

## <a name="steps"></a>Postup

1. Replikaci svazku odstraníte tak, že vyberete **replikaci** ze zdrojového nebo cílového svazku.  

2. Klikněte na **Odstranit**.    

3. Potvrďte odstranění zadáním **Ano** a kliknutím na **Odstranit**.   

    ![Odstranit replikaci](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Řešení potíží při replikaci mezi oblastmi](troubleshoot-cross-region-replication.md)

