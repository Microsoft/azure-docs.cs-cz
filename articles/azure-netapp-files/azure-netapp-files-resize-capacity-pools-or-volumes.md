---
title: Změnit velikost fondu kapacity nebo svazku pro Azure NetApp Files | Microsoft Docs
description: Přečtěte si, jak změnit velikost fondu kapacity nebo svazku. Změna velikosti fondu kapacity změní zakoupenou kapacitu Azure NetApp Files.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325500"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Změna velikosti fondu kapacity nebo svazku
Velikost fondu kapacity nebo svazku můžete podle potřeby změnit. 

## <a name="resize-the-capacity-pool"></a>Změna velikosti fondu kapacity 

Velikost fondu kapacity můžete změnit v TiB přírůstcích nebo snížení. Velikost fondu kapacity ale nemůže být menší než 4 TiB. Změna velikosti fondu kapacity změní zakoupenou kapacitu Azure NetApp Files.

1. V okně Spravovat účet NetApp klikněte na fond kapacit, u kterého chcete změnit velikost. 
2. Klikněte pravým tlačítkem myši na název fondu kapacity nebo klikněte na "..." ikona na konci řádku fondu kapacit pro zobrazení kontextové nabídky. 
3. Pro změnu velikosti nebo odstranění fondu kapacity použijte možnosti místní nabídky.

## <a name="resize-a-volume"></a>Změna velikosti svazku

Velikost svazku můžete podle potřeby změnit. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.

1. V okně Spravovat účet NetApp klikněte na **svazky**. 
2. Klikněte pravým tlačítkem na název svazku, u kterého chcete změnit velikost, nebo klikněte na "..." ikona na konci řádku svazku, ve kterém se zobrazí místní nabídka.
3. Pro změnu velikosti nebo odstranění svazku použijte možnosti místní nabídky.

## <a name="next-steps"></a>Další kroky

- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
- [Dynamická změna úrovně služeb svazku](dynamic-change-volume-service-level.md) 