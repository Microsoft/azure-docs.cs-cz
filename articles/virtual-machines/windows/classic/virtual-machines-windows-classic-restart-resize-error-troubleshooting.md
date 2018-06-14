---
title: Virtuální počítač restartováním nebo změnou velikosti problémy | Microsoft Docs
description: Řešení problémů nasazení classic s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure
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
ms.date: 11/03/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: bed5da25042d29983bad9a80cd44bdd7df261c2e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23989290"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Řešení problémů nasazení classic s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure
> [!div class="op_single_selector"]
> * [Classic](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Při pokusu o spuštění virtuálního počítače pro zastavený Azure (VM), nebo přizpůsobit existující virtuální počítač Azure je běžnou chybou, které zaznamenáte chybu přidělení. Tato chyba nastává clusteru nebo oblast buď nemá k dispozici prostředky nebo nemůže podporovat požadovaná velikost virtuálního počítače.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../../../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Protokoly auditu shromáždit
Pokud chcete spustit Poradce při potížích, shromážděte protokoly auditu k identifikaci chyby související s problém.

Na portálu Azure klikněte na tlačítko **Procházet** > **virtuální počítače** > *virtuálního počítače Windows* > **nastavení** > **protokoly auditu**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problém: Chyba při spouštění zastaveného virtuálního počítače
Pokoušíte se spustit zastaveného virtuálního počítače, ale získat došlo k chybě přidělení.

### <a name="cause"></a>Příčina
Požadavek na spuštění zastaveného virtuálního počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster nemá volné místo dostupné ke splnění tohoto požadavku.

### <a name="resolution"></a>Řešení
* Vytvořte novou cloudovou službu a přidružte ji k buď oblasti nebo na základě oblast virtuální sítě, ale není skupiny vztahů.
* Odstraňte zastaveného virtuálního počítače.
* Znovu vytvořte virtuální počítač v rámci nové cloudové služby s použitím disků.
* Spusťte znovu vytvořit virtuální počítač.

Pokud dojde k chybě při pokusu o vytvoření novou cloudovou službu, opakujte akci později nebo změnit oblast pro cloudové služby.

> [!IMPORTANT]
> Novou cloudovou službu bude mít nový název a VIP, takže budete muset změnit tyto informace pro všechny závislosti, které používají tyto informace pro stávající cloudovou službu.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problém: Chyba při změně velikosti stávajícího virtuálního počítače
Pokoušíte se změnit velikost existující virtuální počítač ale získat došlo k chybě přidělení.

### <a name="cause"></a>Příčina
Žádost o změně velikosti virtuálního počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Cluster však nepodporuje požadovaná velikost virtuálního počítače.

### <a name="resolution"></a>Řešení
Snižte požadovaná velikost virtuálního počítače a opakujte žádost změny velikosti.

* Klikněte na tlačítko **procházet všechny** > **virtuálních počítačů (klasické)** > *virtuálního počítače* > **nastavení**  >  **Velikost**. Podrobné pokyny najdete v tématu [změnit velikost virtuálního počítače](https://msdn.microsoft.com/library/dn168976.aspx).

Pokud není možné snížit velikost virtuálního počítače, postupujte takto:

* Vytvořte novou cloudovou službu, zajištění není propojená do skupiny vztahů a nejsou spojené s virtuální síť, která je propojená do skupiny vztahů.
* Vytvoření nové, větší velikost virtuálního počítače v ní.

Můžete sloučit všechny virtuální počítače v rámci stejné cloudové služby. Pokud je přidružený k virtuální síti na základě oblast stávající cloudovou službu, můžete připojit nové cloudové služby na existující virtuální síť.

Pokud existující cloudové služby není přidružený k virtuální síti založené na oblasti, budete muset odstranit virtuální počítače ve stávající cloudovou službu a vytvořte je znovu v rámci nové cloudové služby z jejich disky. Je důležité si pamatovat, že novou cloudovou službu bude mít nový název a VIP, tak budete muset aktualizovat tyto pro všechny závislosti, které používají tyto informace pro stávající cloudovou službu.

## <a name="next-steps"></a>Další kroky
Pokud dojde k potížím při vytvoření virtuálního počítače s Windows v Azure, najdete v části [řešení potíží s nasazením s vytvoření virtuálního počítače s Windows v Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

