---
title: Změna velikosti fondu kapacit nebo svazku pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak změnit velikost fondu kapacity nebo svazku.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794615"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Změna velikosti fondu kapacity nebo svazku
Podle potřeby můžete změnit velikost fondu kapacity nebo svazku. 

## <a name="resize-the-capacity-pool"></a>Změna velikosti fondu kapacity 

Velikost fondu kapacity můžete změnit v přírůstcích nebo sníženích o 1 TiB. Velikost fondu kapacity však nemůže být menší než 4 TiB. Změna velikosti fondu kapacit změní zakoupenou kapacitu souborů Azure NetApp.

1. V okně Spravovat účet NetApp klikněte na fond kapacity, který chcete změnit. 
2. Klikněte pravým tlačítkem myši na název fondu kapacity nebo klikněte na tlačítko "..." na konci řádku fondu kapacity pro zobrazení kontextové nabídky. 
3. Pomocí možností místní nabídky změňte velikost nebo odstraňte fond kapacit.

## <a name="resize-a-volume"></a>Změna velikosti svazku

Velikost svazku můžete podle potřeby změnit. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.

1. V okně Spravovat účet NetApp klepněte na **položku Svazky**. 
2. Klikněte pravým tlačítkem myši na název svazku, jehož velikost chcete změnit, nebo klikněte na tlačítko "...". na konci řádku svazku, aby se zobrazila místní nabídka.
3. Pomocí možností místní nabídky změňte velikost nebo odstraňte svazek.

