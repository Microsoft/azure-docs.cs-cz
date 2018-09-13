---
title: Virtuální počítač restartováním nebo změnou velikosti problémy | Dokumentace Microsoftu
description: Řešení potíží s nasazení classic s restartováním nebo změnou velikosti stávajícího virtuálního počítače Windows v Azure
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 06/15/2018
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 400b20e474257650a22e04c89ddde581bf0552f4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35913478"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Řešení potíží s nasazení classic s restartováním nebo změnou velikosti stávajícího virtuálního počítače Windows v Azure
> [!div class="op_single_selector"]
> * [Classic](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Při pokusu o spuštění virtuálního počítače pro zastavené Azure (VM), nebo změňte velikost existujícího virtuálního počítače Azure, je běžnou chybou, ke které dojde k selhání přidělení. Tato chyba výsledkem v případě, že cluster nebo oblasti buď nemá žádné prostředky k dispozici, nebo nepodporuje požadovanou velikost virtuálního počítače.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../../../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Protokoly auditu shromažďování
Proces řešení potíží, shromážděte protokoly auditu pro identifikaci chyby související s problémem.

Na webu Azure Portal, klikněte na tlačítko **Procházet** > **virtuálních počítačů** > *váš virtuální počítač Windows*  >   **Nastavení** > **protokoly auditu**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spuštění zastaveného virtuálního počítače
Zkuste spustit zastavený virtuální počítač ale dojde k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek na spuštění zastaveného virtuálního počítače, musí se pokusit v původním clusteru, který je hostitelem cloudovou službu. Cluster nemá volné místo dostupné ke splnění žádosti.

### <a name="resolution"></a>Řešení
* Vytvořit novou cloudovou službu a přidružte jej k buď oblast nebo virtuální sítě založené na oblasti, ale ne skupiny vztahů.
* Odstraňte zastavený virtuální počítač.
* Znovu vytvořte virtuální počítač v nové cloudové službě s použitím disků.
* Spusťte znovu vytvořit virtuální počítač.

Pokud dojde k chybě při pokusu vytvořit novou cloudovou službu, zkuste to znovu později nebo změnit oblast pro cloudovou službu.

> [!IMPORTANT]
> Novou cloudovou službu bude mít nový název a virtuální IP adresy, takže budete muset změnit tyto informace pro všechny závislosti, které používají tyto informace pro existující cloudovou službu.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti stávajícího virtuálního počítače
Zkuste změnit velikost existujícího virtuálního počítače, ale dojde k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek pro změnu velikosti virtuálního počítače má se pokusit v původním clusteru, který je hostitelem cloudovou službu. Cluster nepodporuje požadovanou velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
Zmenšit velikost virtuálního počítače a opakujte žádost o změnu velikosti.

* Klikněte na tlačítko **Procházet vše** > **virtuální počítače (classic)** > *váš virtuální počítač* > **nastavení**  >  **Velikost**. Podrobné pokyny najdete v článku [velikosti virtuálního počítače](https://msdn.microsoft.com/library/dn168976.aspx).

Pokud není možné zmenšit velikost virtuálního počítače, postupujte podle těchto kroků:

* Vytvořte novou cloudovou službu, zajištění není propojená se skupinu vztahů a nesouvisí s virtuální sítí, který je spojen do skupiny vztahů.
* Vytvoření nové, větší velikosti virtuálního počítače v ní.

Můžete sloučit všechny vaše virtuální počítače ve stejné cloudové službě. Pokud vaše existující cloudovou službu je přidružen virtuální sítě založené na oblasti, můžete připojit novou cloudovou službu do existující virtuální sítě.

Pokud stávající cloudové služby není přidružen virtuální sítě založené na oblasti, budete muset virtuální počítače v existující cloudovou službu odstraníte a znovu v novou cloudovou službu z jejich disků. Nicméně je dobré si uvědomit, že novou cloudovou službu bude mít nový název a virtuálních IP adres, takže budete muset aktualizovat pro všechny závislosti, které používají tyto informace pro existující cloudovou službu.

## <a name="next-steps"></a>Další postup
Pokud narazíte na problémy při vytváření virtuálního počítače s Windows v Azure, přečtěte si téma [řešení problémů s nasazením s vytvářením virtuálního počítače s Windows v Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

