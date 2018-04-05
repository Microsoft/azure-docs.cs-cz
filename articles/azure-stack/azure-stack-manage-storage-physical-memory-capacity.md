---
title: Spravovat kapacita fyzické paměti pro zásobník Azure | Microsoft Docs
description: Monitorovat a spravovat adresní prostor úložiště k dispozici pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/03/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: 34b03cf1fda29706936968ab93cb35a6a2510c65
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Spravovat kapacita fyzické paměti pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Pokud chcete zvýšit kapacitu celkově dostupné paměti zásobník Azure, můžete přidat další paměť. V zásobníku Azure fyzický server se také označuje jako *uzlu jednotky škálování*. Všechny uzly jednotek škálování, které jsou členy jednotky škálování jednoho musí mít stejné množství paměti.

> [!note]  
> Než budete pokračovat, vyhledejte v dokumentaci výrobce hardwaru pro případ, upgraduje paměti, že výrobce podporuje fyzické paměti upgradu. Smlouva OEM dodavatele hardwaru podpory může vyžadovat, že dodavatele provádět umístění rack fyzického serveru a aktualizaci firmwaru zařízení.

Následující vývojový diagram znázorňuje obecný postup pro přidání paměti do každého uzlu jednotky škálování.

![Přidejte do každého uzlu jednotky škálování paměti](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Přidejte do stávající uzel paměti
V následujících krocích najdete přehled procesu přidání paměti. 

> [!Warning]  
Nepostupujte podle těchto kroků bez odkazující na dokumentaci poskytnutou výrobcem OEM.

> [!Warning]  
Jednotka škálování celý musí být vypnuté postupného upgradu paměti nepodporuje.

1. Zastavit pomocí kroků popsaných v zásobníku Azure [zahájení a ukončení zásobník Azure](azure-stack-start-and-stop.md) článku.
2. Upgradujte paměť na každý fyzický počítač pomocí dokumentaci od výrobce hardwaru.
3. Spustit pomocí kroků v zásobníku Azure [zahájení a ukončení zásobník Azure](azure-stack-start-and-stop.md) článku.

## <a name="next-steps"></a>Další postup

 - Informace o tom, které chcete spravovat účty úložiště v Azure zásobníku najít, obnovit a opětovné využití kapacity úložiště na základě obchodních potřeb, najdete v části [spravovat účty úložiště v Azure zásobníku](azure-stack-manage-storage-accounts.md).
 - Další operátor cloudu Azure zásobníku monitoruje a spravuje úložnou kapacitu jejich nasazení zásobník Azure najdete v tématu [spravovat kapacita úložiště pro Azure zásobníku](azure-stack-manage-storage-shares.md). 