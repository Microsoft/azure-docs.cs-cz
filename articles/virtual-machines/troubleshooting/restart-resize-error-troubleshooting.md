---
title: Problémy s restartováním nebo opětovnou sazí virtuálního počítače v Azure | Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením Správce prostředků při restartování nebo opětovném nastavení velikosti existujícího virtuálního počítače v Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965612"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Řešení potíží s nasazením při restartování nebo změně velikosti stávajícího virtuálního počítače s Windows v Azure
Při pokusu o spuštění zastaveného virtuálního počítače (VM) nebo změnit velikost existujícího virtuálního počítače Azure, běžné chyby, se kterými se setkáte, je selhání přidělení. Tato chyba se stane, když cluster nebo oblast nemá k dispozici prostředky nebo nemůže podporovat požadovanou velikost virtuálního počítače.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Shromažďovat protokoly aktivit
Chcete-li spustit řešení potíží, shromažďovat protokoly aktivit k identifikaci chyby spojené s problémem. Následující odkazy obsahují podrobné informace o procesu:

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spuštění zastaveného virtuálního počítače
Pokusíte se spustit zastavený virtuální ms, ale získat selhání přidělení.

### <a name="cause"></a>Příčina
Požadavek na spuštění zastaveného virtuálního aplikace musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster však nemá volné místo k dispozici pro splnění požadavku.

### <a name="resolution"></a>Řešení
* Zastavte všechny virtuální počítače v sadě dostupnosti a restartujte každý virtuální počítač.
  
  1. Klikněte na **skupiny** > prostředků*skupiny* > **prostředků Zdroje,** > *vaši dostupnost nastavit* > **virtuální počítače** > *virtuální počítač* > **zastavit**.
  2. Po zastavení všech virtuálních disek vyberte každý z zastavených virtuálních ms a klikněte na Start.
* Opakujte požadavek na restartování později.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti existujícího virtuálního počítače
Pokusíte se změnit velikost existujícího virtuálního počítače, ale získat selhání přidělení.

### <a name="cause"></a>Příčina
Požadavek na změny velikosti virtuálního počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster však nepodporuje požadovanou velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
* Opakujte požadavek pomocí menší velikosti virtuálního počítače.
* Pokud velikost požadovaného virtuálního počítače nelze změnit:
  
  1. Zastavte všechny virtuální ho disponibilní služby v sadě dostupnosti.
     
     * Klikněte na **skupiny** > prostředků*skupiny* > **prostředků Zdroje,** > *vaši dostupnost nastavit* > **virtuální počítače** > *virtuální počítač* > **zastavit**.
  2. Po zastavení všech virtuálních počítače změňte velikost požadovaného virtuálního počítače na větší velikost.
  3. Vyberte velikost virtuálního virtuálního virtuálního ms a klikněte na **Start**a spusťte každý zastavený virtuální ms.

## <a name="next-steps"></a>Další kroky
Pokud při vytváření nového virtuálního počítače s Windows v Azure narazíte na problémy, [přečtěte si článek Řešení problémů s nasazením při vytváření nového virtuálního počítače s Windows v Azure](../windows/troubleshoot-deployment-new-vm.md).

