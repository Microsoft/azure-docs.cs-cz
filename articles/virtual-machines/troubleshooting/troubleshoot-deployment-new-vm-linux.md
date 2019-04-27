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
ms.openlocfilehash: 9fea914fdf9b025fd5d38219a6bfc81b4a9cc584
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125612"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Potíží s nasazením Resource Manageru s vytvářením nového virtuálního počítače Linux v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Nejčastější problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Další dotazy a potíže s nasazením virtuálního počítače najdete v tématu [Řešení potíží s nasazením linuxového virtuálního počítače v Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Protokoly aktivit shromažďování
Proces řešení potíží, shromažďování protokolů aktivit k identifikaci chyby související s problémem. Následující odkazy obsahují podrobné informace o procesu dodržovat.

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazení protokolů aktivit ke správě prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**ACOVNÍ ADRESÁŘ:** Pokud je operační systém Linux zobecněn, a je nahraný a zachytit pomocí nastavení zobecněný, pak nebudou všechny chyby. Podobně pokud je operační systém Linux specializovaná a je nahraný a zachytit pomocí speciální nastavení, pak nebudou všechny chyby.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Linux generalizovaný a nahrát ho jako specializovaná, obdržíte chybu časového limitu zřizování protože virtuální počítač zasekl ve fázi zřizování.

**N<sup>2</sup>:** Pokud je operační systém Linux specializované a nahrání generalizovaného, obdržíte chybu zřizování selhání, protože nový virtuální počítač běží s původní název počítače, uživatelské jméno a heslo.

**Řešení:**

Chcete-li oba tyto chyby vyřešit, nahrajte původní virtuální pevný disk, k dispozici v místním prostředí stejné nastavení jako, který pro operační systém (zobecněný/specializované). Pokud chcete nahrát jako generalizovaná, nezapomeňte provést – zrušení zřízení nejprve.

**Zachycení chyb:**

**N<sup>3</sup>:** Pokud je operační systém Linux zobecněn, a jsou zachyceny jako specializovaná, obdržíte chybu časového limitu zřizování protože původní virtuální počítač se nedá použít jako ho označí jako zobecněný.

**N<sup>4</sup>:** Pokud je operační systém Linux specializované a jejím zaznamenání jako generalizovaná, obdržíte chybu zřizování selhání, protože nový virtuální počítač běží s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný protože je označená jako specializované.

**Řešení:**

Chcete-li oba tyto chyby vyřešit, odstraňte aktuální image na portálu, a [vytvoří další bitovou kopii z aktuální virtuální pevné disky](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) stejné nastavení jako, který pro operační systém (zobecněný/specializované).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: Vlastní / galerie a image z marketplace; došlo k chybě přidělení
Tato chyba nastane v situacích, když je připnutá novou žádost o virtuální počítač do clusteru, který nemůže zajišťovat podporu žádá velikost virtuálního počítače, nebo nemá žádné dostupné volné místo tak, aby vyhovovaly žádosti.

**1. příčina:** Cluster nepodporuje požadovanou velikost virtuálního počítače.

**Řešení 1:**

* Opakujte žádost, použijte menší velikost virtuálního počítače.
* Pokud velikost pro požadovaný virtuální počítač nejde změnit:
  * Zastavte všechny virtuální počítače ve skupině dostupnosti.
    Klikněte na tlačítko **skupiny prostředků** > *vaší skupiny prostředků* > **prostředky** > *vaší skupiny dostupnosti*  >  **Virtuálních počítačů** > *váš virtuální počítač* > **Zastavit**.
  * Po zastavení všech virtuálních počítačů, vytvoření nového virtuálního počítače v požadovanou velikost.
  * Nejprve spusťte nový virtuální počítač a potom vyberte všechny zastavené virtuální počítače a klikněte na tlačítko **Start**.

**2. příčina:** Cluster nemá žádné volné prostředky.

**Řešení 2:**

* Opakujte požadavek později.
* Pokud nový virtuální počítač může být součástí do jiné skupiny dostupnosti
  * Vytvoření nového virtuálního počítače v různých dostupnosti (ve stejné oblasti).
  * Přidáte nový virtuální počítač do stejné virtuální síti.

## <a name="next-steps"></a>Další postup
Pokud narazíte na problémy při spuštění zastavený virtuální počítač s Linuxem nebo změně velikosti existujícího virtuálního počítače Linux v Azure, přečtěte si téma [potíže s restartováním nebo změnou velikosti existujícího virtuálního počítače s Linuxem v Azure Resource Manageru řešení potíží s nasazení](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

