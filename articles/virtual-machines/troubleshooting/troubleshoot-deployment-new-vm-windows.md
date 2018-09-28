---
title: Řešení potíží s nasazení virtuálního počítače Windows v Azure | Dokumentace Microsoftu
description: Řešení problémů s nasazením Resource Manageru při vytvoření nového virtuálního počítače Windows v Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24a12c9144535fecd23be432ee33402eb6528b28
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413270"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Řešení problémů s nasazením při vytváření nového virtuálního počítače Windows v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Nejčastější problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Jiné problémy při nasazení virtuálního počítače a dotazy, naleznete v tématu [řešení potíží s nasazením Windows virtuální počítač v Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Protokoly aktivit shromažďování
Proces řešení potíží, shromažďování protokolů aktivit k identifikaci chyby související s problémem. Následující odkazy obsahují podrobné informace o procesu dodržovat.

[Zobrazení operací nasazení](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Zobrazení protokolů aktivit ke správě prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Pokud operační systém Windows zobecněn a je nahraný a zachytit pomocí nastavení zobecněný, pak nebudou všechny chyby. Podobně pokud je operační systém Windows specializovaná a je nahraný a zachytit pomocí speciální nastavení, pak nebudou všechny chyby.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Windows generalizovaný a nahrát ho jako specializovaná, budete mít k vypršení časového limitu zřizování se zablokuje a na obrazovce OOBE virtuálního počítače.

**N<sup>2</sup>:** Pokud je operační systém Windows specializované a nahrání generalizovaného, zobrazí se zřizování chyba s virtuálním Počítačem, zaseknuto v OOBE obrazovky, protože nový virtuální počítač běží s původní název počítače uživatelské jméno a heslo.

**Řešení**

Chcete-li oba tyto chyby vyřešit, použijte [Add-AzureRmVhd původní virtuální pevný disk nahrát](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd), k dispozici místně, stejné nastavení jako, který pro operační systém (zobecněný/specializované). Pokud chcete nahrát jako generalizovaná, nezapomeňte nejdřív spusťte nástroj sysprep.

**Zachycení chyb:**

**N<sup>3</sup>:** Pokud je operační systém Windows zobecněn, a jsou zachyceny jako specializovaná, obdržíte chybu časového limitu zřizování protože původní virtuální počítač se nedá použít jako ho označí jako zobecněný.

**N<sup>4</sup>:** Pokud operační systém je Windows specializovaný, se zaznamenávají jako generalizovaná, obdržíte chybu zřizování selhání, protože nový virtuální počítač běží s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný protože je označená jako specializované.

**Řešení**

Chcete-li oba tyto chyby vyřešit, odstraňte aktuální image na portálu, a [vytvoří další bitovou kopii z aktuální virtuální pevné disky](../windows/create-vm-specialized.md) stejné nastavení jako, který pro operační systém (zobecněný/specializované).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problém: Vlastní/galerie/marketplace image; došlo k chybě přidělení
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
Pokud narazíte na problémy při spuštění zastaveného virtuálního počítače Windows nebo změně velikosti stávajícího virtuálního počítače Windows v Azure, přečtěte si téma [potíže s restartováním nebo změnou velikosti stávajícího virtuálního počítače Windows v Azure Resource Manageru řešení potíží s nasazení](restart-resize-error-troubleshooting.md).


