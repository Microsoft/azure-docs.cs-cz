---
title: Řešení potíží s nasazení virtuálního počítače s Windows v Azure | Microsoft Docs
description: Řešení problémů nasazení Resource Manager, když vytvoříte nový virtuální počítač Windows v Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fff29f6cfed4989386ca5bbd12184dce525add76
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27580367"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Řešení problémů nasazení, při vytváření nového virtuálního počítače Windows v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Nejdůležitější problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Pro další problémy při nasazení virtuálního počítače a dotazy, najdete v části [potíží nasazení systému Windows virtuálního počítače v Azure](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Shromážděte aktivity protokolů
Pokud chcete spustit Poradce při potížích, shromážděte protokoly aktivity k identifikaci chyby související s problém. Následující odkazy obsahují podrobné informace o na postupujte podle procesu.

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazit protokoly aktivity ke správě prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Pokud operační systém je Windows zobecněn a je nahrál nebo zachytit pomocí nastavení zobecněný, pak nebudou všechny chyby. Podobně pokud je operačním systémem Windows specializovaný a je nahrál nebo zachytit pomocí specializované nastavení a potom nebudou všechny chyby.

**Odešlete chyby:**

**N<sup>1</sup>:** Pokud operačního systému Windows zobecněn, a nahraje jako specializovaný, budete mít zřizování vypršení časového limitu se zablokuje na obrazovce při prvním zapnutí virtuálního počítače.

**N<sup>2</sup>:** Pokud operačního systému Windows specializuje a nahraje jako zobecněn, budete mít zřizování chyba selhání se virtuální počítač na obrazovce při prvním zapnutí zablokuje, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo.

**Řešení**

Chcete-li obě tyto chyby vyřešit, použijte [přidat AzureRmVhd nahrát původní virtuální pevný disk](https://msdn.microsoft.com/library/mt603554.aspx), k dispozici na místních počítačích stejné nastavení jako pro operační systém (zobecněn/specializuje). Pokud chcete nahrát jako zobecněn, nezapomeňte nejprve spustit nástroj sysprep.

**Zaznamenat chyby:**

**N<sup>3</sup>:** Pokud operačního systému Windows zobecněn, a se zaznamená jako specializovaný, zobrazí se zřizování vypršení časového limitu vzhledem k tomu, že původní virtuální počítač je nepoužitelný, jako je označena jako zobecněn.

**N<sup>4</sup>:** Pokud operačního systému Windows specializuje a z něj zachytí jako zobecněn, zřizování selhání chyba se zobrazí, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný, protože je označeno jako specializované.

**Řešení**

Chcete-li obě tyto chyby, odstraňte aktuální image z portálu, a [kopii z aktuální virtuální pevné disky](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) stejné nastavení jako pro operační systém (zobecněn/specializuje).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problém: Vlastní nebo Galerie/marketplace obrázku; došlo k chybě přidělení
Tato chyba nastane v situacích, když novou žádost o virtuální počítač umístěn v clusteru, který nepodporuje požadovanou velikost virtuálního počítače, nebo nemá dostupné volné místo pro uložení žádosti.

**Příčina 1:** clusteru nepodporuje požadovaná velikost virtuálního počítače.

**Řešení 1:**

* Opakujte tuto žádost pomocí menší velikost virtuálního počítače.
* Pokud velikost požadovaný virtuální počítač nelze změnit:
  * Zastavte všechny virtuální počítače v sadě dostupnosti.
    Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaše skupina dostupnosti* > **virtuální počítače** > *virtuálního počítače* > **Zastavit**.
  * Po zastavení všech virtuálních počítačů, vytvořte nový virtuální počítač v požadovaná velikost.
  * Nejprve spusťte nový virtuální počítač a potom vyberte jednotlivé zastaven virtuálních počítačů a klikněte na **spustit**.

**Příčina 2:** clusteru nemá uvolnění prostředků.

**Řešení 2:**

* Opakujte požadavek později.
* Pokud nový virtuální počítač může být součástí skupiny s jinou dostupnosti
  * Vytvoření nového virtuálního počítače v různých dostupnosti nastavit (ve stejné oblasti).
  * Přidáte nový virtuální počítač do stejné virtuální síti.

## <a name="next-steps"></a>Další postup
Pokud dojde k potížím při spuštění zastaveného virtuálního počítače Windows nebo přizpůsobit existující virtuální počítač Windows v Azure, najdete v části [problémy při nasazení Resource Manager řešení potíží s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

