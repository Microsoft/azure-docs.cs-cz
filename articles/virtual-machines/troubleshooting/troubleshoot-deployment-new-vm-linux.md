---
title: Řešení potíží s nasazení virtuálního počítače s Linuxem | Dokumentace Microsoftu
description: Řešení problémů s nasazením Resource Manageru při vytvoření nového virtuálního počítače Linux v Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 08009ca7f9faaa75e593670c22cf864c12236e8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413417"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Potíží s nasazením Resource Manageru s vytvářením nového virtuálního počítače Linux v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Nejčastější problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Jiné problémy při nasazení virtuálního počítače a dotazy, naleznete v tématu [řešení potíží s nasazením Linux virtuálního počítače v Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Protokoly aktivit shromažďování
Proces řešení potíží, shromažďování protokolů aktivit k identifikaci chyby související s problémem. Následující odkazy obsahují podrobné informace o procesu dodržovat.

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazení protokolů aktivit ke správě prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Pokud operační systém Linux zobecněn a je nahraný a zachytit pomocí nastavení zobecněný, pak nebudou všechny chyby. Podobně pokud je operační systém Linux specializovaná a je nahraný a zachytit pomocí speciální nastavení, pak nebudou všechny chyby.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Linux generalizovaný a nahrát ho jako specializovaná, obdržíte chybu časového limitu zřizování protože virtuální počítač zasekl ve fázi zřizování.

**N<sup>2</sup>:** Pokud je operační systém Linux specializované a nahrání generalizovaného, obdržíte chybu zřizování selhání, protože nový virtuální počítač běží s původní název počítače, uživatelské jméno a heslo.

**Řešení:**

Chcete-li oba tyto chyby vyřešit, nahrajte původní virtuální pevný disk, k dispozici v místním prostředí, stejné nastavení jako, který pro operační systém (zobecněný/specializované). Pokud chcete nahrát jako generalizovaná, nezapomeňte provést – zrušení zřízení nejprve.

**Zachycení chyb:**

**N<sup>3</sup>:** Pokud je operační systém Linux zobecněn, a jsou zachyceny jako specializovaná, obdržíte chybu časového limitu zřizování protože původní virtuální počítač se nedá použít jako ho označí jako zobecněný.

**N<sup>4</sup>:** Pokud je operační systém Linux specializované a jejím zaznamenání jako generalizovaná, obdržíte chybu zřizování selhání, protože nový virtuální počítač běží s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný protože je označená jako specializované.

**Řešení:**

Chcete-li oba tyto chyby vyřešit, odstraňte aktuální image na portálu, a [vytvoří další bitovou kopii z aktuální virtuální pevné disky](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) stejné nastavení jako, který pro operační systém (zobecněný/specializované).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: Vlastní / galerie a image z marketplace; došlo k chybě přidělení
Tato chyba nastane v situacích, když je připnutá novou žádost o virtuální počítač do clusteru, který nemůže zajišťovat podporu žádá velikost virtuálního počítače, nebo nemá žádné dostupné volné místo tak, aby vyhovovaly žádosti.

**1. příčina:** clusteru nepodporuje požadovanou velikost virtuálního počítače.

**Řešení 1:**

* Opakujte žádost, použijte menší velikost virtuálního počítače.
* Pokud velikost pro požadovaný virtuální počítač nejde změnit:
  * Zastavte všechny virtuální počítače ve skupině dostupnosti.
    Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaší skupiny dostupnosti*  >  **Virtuálních počítačů** > *váš virtuální počítač* > **Zastavit**.
  * Po zastavení všech virtuálních počítačů, vytvoření nového virtuálního počítače v požadovanou velikost.
  * Nejprve spusťte nový virtuální počítač a potom vyberte všechny zastavené virtuální počítače a klikněte na tlačítko **Start**.

**2. příčina:** clusteru nemá žádné volné prostředky.

**Řešení 2:**

* Opakujte požadavek později.
* Pokud nový virtuální počítač může být součástí do jiné skupiny dostupnosti
  * Vytvoření nového virtuálního počítače v různých dostupnosti (ve stejné oblasti).
  * Přidáte nový virtuální počítač do stejné virtuální síti.

## <a name="next-steps"></a>Další postup
Pokud narazíte na problémy při spuštění zastavený virtuální počítač s Linuxem nebo změně velikosti existujícího virtuálního počítače Linux v Azure, přečtěte si téma [potíže s restartováním nebo změnou velikosti existujícího virtuálního počítače s Linuxem v Azure Resource Manageru řešení potíží s nasazení](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

