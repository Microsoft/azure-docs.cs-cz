---
title: Omezení prostředků pro soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje omezení pro souborů NetApp Azure prostředky, včetně omezení pro účty NetApp, fondy kapacitu, svazky, snímky a delegované podsítě.
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668154"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro soubory Azure NetApp umožňuje správu svazků.

- Každé předplatné Azure může mít maximálně 10 účtů NetApp.
- Každý účet NetApp může mít maximálně 25 kapacity fondů.
- Každá kapacita fondu může patřit do pouze jeden účet NetApp.  
- Minimální velikost pro jednu kapacitu fondu je 4 TB a maximální velikost je 500 TB. 
- Každá kapacita fondu může mít maximálně 500 svazky.
- Minimální velikost pro samostatný svazek je 100 GB a maximální velikost je 92 TiB.
- Každý svazek může mít maximálně 255 snímky.
- Každá virtuální síť Azure (Vnet) může mít jenom jednu podsíť delegovat do služby soubory Azure NetApp.

**Další kroky**

[Vysvětlení hierarchii úložiště souborů Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
