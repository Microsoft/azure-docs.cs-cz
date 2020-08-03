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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7d1c54d05430600e9fffa2659dbaaf6db0b086b5
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512869"
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

