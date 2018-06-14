---
title: Řešení potíží s nasazení virtuálního počítače s Linuxem-RM | Microsoft Docs
description: Řešení problémů nasazení Resource Manager, když vytvoříte nový virtuální počítač Linux v Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: aea5db05843b0175b8ef8b713944e12262e33010
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27579259"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Řešení problémů nasazení Resource Manager pomocí vytvoření nového virtuálního počítače Linux v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Nejdůležitější problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Pro další problémy při nasazení virtuálního počítače a dotazy, najdete v části [potíží nasazení Linux virtuálního počítače v Azure](troubleshoot-deploy-vm.md).
## <a name="collect-activity-logs"></a>Shromážděte aktivity protokolů
Pokud chcete spustit Poradce při potížích, shromážděte protokoly aktivity k identifikaci chyby související s problém. Následující odkazy obsahují podrobné informace o na postupujte podle procesu.

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazit protokoly aktivity ke správě prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Pokud operačního systému Linux zobecněn a je nahrál nebo zachytit pomocí nastavení zobecněný, potom nebudou všechny chyby. Podobně pokud je operačním systémem Linux specializovaný a je nahrál nebo zachytit pomocí specializované nastavení a potom nebudou všechny chyby.

**Odešlete chyby:**

**N<sup>1</sup>:** Pokud operačního systému Linux zobecněn, a nahraje jako specializovaný, zobrazí se zřizování vypršení časového limitu vzhledem k tomu, že virtuální počítač zasekl ve fázi zřizování.

**N<sup>2</sup>:** Pokud operačního systému Linux specializuje a nahraje jako zobecněn, zřizování selhání chyba se zobrazí, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo.

**Řešení:**

Obě tyto chyby vyřešíte nahrajte původní virtuální pevný disk, k dispozici místní, stejné nastavení jako pro operační systém (zobecněn/specializuje). Pokud chcete nahrát jako zobecněn, nezapomeňte spustit - nejprve zrušit jejich zřízení.

**Zaznamenat chyby:**

**N<sup>3</sup>:** Pokud operačního systému Linux zobecněn, a se zaznamená jako specializovaný, zobrazí se zřizování vypršení časového limitu vzhledem k tomu, že původní virtuální počítač je nepoužitelný, jako je označena jako zobecněn.

**N<sup>4</sup>:** Pokud operačního systému Linux specializuje a z něj zachytí jako zobecněn, zřizování selhání chyba se zobrazí, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný, protože je označeno jako specializované.

**Řešení:**

Chcete-li obě tyto chyby, odstraňte aktuální image z portálu, a [kopii z aktuální virtuální pevné disky](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) stejné nastavení jako pro operační systém (zobecněn/specializuje).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: Vlastní nebo Galerie / marketplace obrázku; došlo k chybě přidělení
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
Pokud dojde k potížím při spuštění zastavený virtuální počítač s Linuxem nebo změnit jeho velikost existující virtuální počítač s Linuxem v Azure, najdete v části [problémy při nasazení Resource Manager řešení potíží s restartováním nebo změnou velikosti existující virtuální počítač Linux v Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

