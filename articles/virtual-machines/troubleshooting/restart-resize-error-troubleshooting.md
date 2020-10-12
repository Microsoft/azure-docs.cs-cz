---
title: Restartování virtuálního počítače nebo změna velikosti problémů v Azure | Microsoft Docs
description: Řešení potíží s nasazením Správce prostředků problémy s restartováním nebo změnou velikosti stávajícího virtuálního počítače v Azure
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
ms.openlocfilehash: 85acd8e26ca10730638332047a37d281358d205f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526549"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Řešení potíží s nasazením při restartování nebo změně velikosti stávajícího virtuálního počítače s Windows v Azure
Při pokusu o spuštění zastaveného virtuálního počítače Azure nebo změně velikosti stávajícího virtuálního počítače Azure se běžně narazí na chybu přidělení. K této chybě dojde, když v clusteru nebo oblasti nejsou k dispozici prostředky nebo pokud není podporována požadovaná velikost virtuálního počítače.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Shromažďování protokolů aktivit
Pokud chcete začít řešit potíže, Shromážděte protokoly aktivit a Identifikujte chybu spojenou s problémem. Následující odkazy obsahují podrobné informace o procesu:

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spuštění zastaveného virtuálního počítače
Pokusíte se spustit zastavený virtuální počítač, ale získáte selhání přidělení.

### <a name="cause"></a>Příčina
Žádost o spuštění zastaveného virtuálního počítače se musí provést v původním clusteru, který hostuje cloudovou službu. Cluster ale nemá k dispozici volné místo ke splnění požadavku.

### <a name="resolution"></a>Řešení
* Zastavte všechny virtuální počítače ve skupině dostupnosti a pak restartujte všechny virtuální počítače.
  
  1. Klikněte na **skupiny**prostředků  >  *vaše skupina prostředků*  >  **prostředky**  >  *vaše sada dostupnosti*  >  **Virtual Machines**  >  *your virtual machine*  >  **zastavíte**virtuální počítač.
  2. Po zastavení všech virtuálních počítačů vyberte všechny zastavené virtuální počítače a klikněte na spustit.
* Opakujte požadavek na restartování později.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti existujícího virtuálního počítače
Pokusíte se změnit velikost existujícího virtuálního počítače, ale získáte selhání přidělení.

### <a name="cause"></a>Příčina
Žádost o změnu velikosti virtuálního počítače se musí provést v původním clusteru, který hostuje cloudovou službu. Cluster ale nepodporuje požadovanou velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
* Opakujte požadavek s menší velikostí virtuálního počítače.
* Pokud velikost požadovaného virtuálního počítače nejde změnit:
  
  1. Zastavte všechny virtuální počítače ve skupině dostupnosti.
     
     * Klikněte na **skupiny**prostředků  >  *vaše skupina prostředků*  >  **prostředky**  >  *vaše sada dostupnosti*  >  **Virtual Machines**  >  *your virtual machine*  >  **zastavíte**virtuální počítač.
  2. Až se všechny virtuální počítače zastaví, změňte velikost požadovaného virtuálního počítače na větší velikost.
  3. Vyberte virtuální počítač se změněnou velikostí a klikněte na **Spustit**a potom spusťte všechny zastavené virtuální počítače.

## <a name="next-steps"></a>Další kroky
Pokud narazíte na problémy při vytváření nového virtuálního počítače s Windows v Azure, přečtěte si téma řešení potíží s [nasazením při vytváření nového virtuálního počítače s Windows v Azure](./troubleshoot-deployment-new-vm-windows.md).
