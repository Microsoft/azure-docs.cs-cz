---
title: Správa kapacity fyzické paměti pro službu Azure Stack | Dokumentace Microsoftu
description: Monitorovat a spravovat adresní prostor úložiště k dispozici pro službu Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 928e4c7b6f7711dcad8190b6c368bd7f1df2b66c
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305889"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Správa kapacity fyzické paměti pro službu Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Ke zvýšení kapacity celkově dostupné paměti pro službu Azure Stack, můžete přidat další paměť. Ve službě Azure Stack fyzický server se také označuje jako *uzel jednotek škálování*. Všechny uzly škálovací jednotky, které jsou členy jedné škálovací jednotky musí mít stejné množství paměti.

> [!note]  
> Než budete pokračovat, v dokumentaci od výrobce hardwaru a zjistěte, jestli výrobce vašeho podporuje upgrade fyzické paměti. Vaše smlouva podporu dodavatele hardwaru OEM může vyžadovat, že se na dodavatele provádět umístění fyzického serveru do racku a aktualizaci firmwaru zařízení.

Následující vývojový diagram ukazuje obecné postup přidávání paměti na každý uzel jednotek škálování.

![Přidejte do každého uzlu jednotky škálování paměti](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Přidejte do existující uzel paměť
Následující kroky obsahují podrobný přehled procesu přidávání paměti. 

> [!Warning]  
Nepostupujte podle těchto kroků bez ohledu na dokumentaci k poskytnutou výrobcem OEM.

> [!Warning]  
Celá jednotka škálování musí být vypnut, protože paměti se zajištěním provozu není podporováno.

1. Zastavit pomocí kroků popsaných v Azure Stack [spouštění a zastavování služby Azure Stack](azure-stack-start-and-stop.md) článku.
2. Upgrade paměti na každý fyzický počítač s použitím dokumentace od výrobce hardwaru.
3. Spuštění pomocí postupu ve službě Azure Stack [spouštění a zastavování služby Azure Stack](azure-stack-start-and-stop.md) článku.

## <a name="next-steps"></a>Další postup

 - Zjistěte, jak spravovat účty úložiště ve službě Azure Stack najít, obnovit a získat zpět na základě obchodních potřeb kapacity úložiště, najdete v článku [spravovat účty úložiště ve službě Azure Stack](azure-stack-manage-storage-accounts.md).
 - Další operátor cloudu Azure Stack, monitoruje a spravuje je kapacita jejich nasazení Azure Stack, najdete v článku [spravovat kapacitu úložiště pro službu Azure Stack](azure-stack-manage-storage-shares.md). 
