---
title: Změnit velikost fondu kapacity nebo svazku pro Azure NetApp Files | Microsoft Docs
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
ms.topic: how-to
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7da604e8e49b0732680e5f641d1ff6e899ad474d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483478"
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

