---
title: Problémy s restartováním nebo změnou velikosti VM v Azure | Dokumentace Microsoftu
description: Řešení potíží s potíže s restartováním nebo změnou velikosti stávajícího virtuálního počítače Windows v Azure modelu nasazení Resource Manageru
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4e0c77c03856b4851ee5fe49bd6ae54d47f6c31
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899162"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Nasazení řešení potíží s restartováním nebo změnou velikosti stávajícího virtuálního počítače Windows v Azure
Při pokusu o spuštění virtuálního počítače pro zastavené Azure (VM), nebo změňte velikost existujícího virtuálního počítače Azure, je běžnou chybou, ke které dojde k selhání přidělení. Tato chyba výsledkem v případě, že cluster nebo oblasti buď nemá žádné prostředky k dispozici, nebo nepodporuje požadovanou velikost virtuálního počítače.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Protokoly aktivit shromažďování
Proces řešení potíží, shromažďování protokolů aktivit k identifikaci chyby související s problémem. Následující odkazy obsahují podrobné informace o procesu:

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazení protokolů aktivit ke správě prostředků Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spuštění zastaveného virtuálního počítače
Zkuste spustit zastavený virtuální počítač ale dojde k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek na spuštění zastaveného virtuálního počítače, musí se pokusit v původním clusteru, který je hostitelem cloudovou službu. Cluster nemá volné místo dostupné ke splnění žádosti.

### <a name="resolution"></a>Řešení
* Zastavit všechny virtuální počítače ve skupině dostupnosti a poté restartujte všechny virtuální počítače.
  
  1. Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaší skupiny dostupnosti*  >  **Virtuálních počítačů** > *váš virtuální počítač* > **Zastavit**.
  2. Po zastavení všech virtuálních počítačů, vyberte jednotlivé zastavené virtuální počítače a klikněte na spustit.
* Žádost o restartování opakujte později.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti stávajícího virtuálního počítače
Zkuste změnit velikost existujícího virtuálního počítače, ale dojde k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek pro změnu velikosti virtuálního počítače má se pokusit v původním clusteru, který je hostitelem cloudovou službu. Cluster nepodporuje požadovanou velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
* Opakujte žádost, použijte menší velikost virtuálního počítače.
* Pokud velikost pro požadovaný virtuální počítač nejde změnit:
  
  1. Zastavte všechny virtuální počítače ve skupině dostupnosti.
     
     * Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaší skupiny dostupnosti*  >  **Virtuálních počítačů** > *váš virtuální počítač* > **Zastavit**.
  2. Po zastavení všech virtuálních počítačů, změňte velikost požadovaný virtuální počítač na větší velikost.
  3. Vyberte virtuální počítač, jehož velikost byla změněna a klikněte na tlačítko **Start**, a pak spusťte všechny zastavené virtuální počítače.

## <a name="next-steps"></a>Další postup
Pokud narazíte na problémy při vytváření nového virtuálního počítače Windows v Azure, přečtěte si téma [řešení problémů s nasazením s vytvářením nového virtuálního počítače Windows v Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

