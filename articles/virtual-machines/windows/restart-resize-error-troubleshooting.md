---
title: Virtuální počítač restartováním nebo změnou velikosti problémy v Azure | Microsoft Docs
description: Řešení problémů nasazení Resource Manager s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure
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
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 482a194535cba8715b70ecff4679691515b206a9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27581392"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Řešení potíží s nasazením s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure
Při pokusu o spuštění virtuálního počítače pro zastavený Azure (VM), nebo přizpůsobit existující virtuální počítač Azure je běžnou chybou, které zaznamenáte chybu přidělení. Tato chyba nastává clusteru nebo oblast buď nemá k dispozici prostředky nebo nemůže podporovat požadovaná velikost virtuálního počítače.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Shromážděte aktivity protokolů
Pokud chcete spustit Poradce při potížích, shromážděte protokoly aktivity k identifikaci chyby související s problém. Následující odkazy obsahují podrobné informace o procesu:

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazit protokoly aktivity ke správě prostředků Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spouštění zastaveného virtuálního počítače
Pokoušíte se spustit zastaveného virtuálního počítače, ale získat došlo k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek na spuštění zastaveného virtuálního počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster nemá volné místo dostupné ke splnění tohoto požadavku.

### <a name="resolution"></a>Řešení
* Zastavte všechny virtuální počítače v sadě dostupnosti a znovu spusťte každý virtuální počítač.
  
  1. Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaše skupina dostupnosti* > **virtuální počítače** > *virtuálního počítače* > **Zastavit**.
  2. Po zastavení všech virtuálních počítačích, vyberte jednotlivé zastaven virtuálních počítačů a klikněte na příkaz spustit.
* Opakujte žádost restartovat později.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti stávajícího virtuálního počítače
Pokoušíte se změnit velikost existující virtuální počítač ale získat došlo k chybě přidělení.

### <a name="cause"></a>Příčina
Žádost o změně velikosti virtuálního počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster však nepodporuje požadovaná velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
* Opakujte tuto žádost pomocí menší velikost virtuálního počítače.
* Pokud velikost požadovaný virtuální počítač nelze změnit:
  
  1. Zastavte všechny virtuální počítače v sadě dostupnosti.
     
     * Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaše skupina dostupnosti* > **virtuální počítače** > *virtuálního počítače* > **Zastavit**.
  2. Po zastavení všech virtuálních počítačů, změňte velikost požadovaný virtuální počítač na větší velikost.
  3. Vyberte změněnou velikostí virtuálního počítače a klikněte na **spustit**, a následné spuštění všech virtuálních počítačích zastaven.

## <a name="next-steps"></a>Další postup
Pokud dojde k potížím při vytvoření nového virtuálního počítače s Windows v Azure, najdete v části [řešení potíží s nasazením s vytvoření nového virtuálního počítače Windows v Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

