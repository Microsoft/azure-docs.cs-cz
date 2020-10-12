---
title: Řešení potíží s nasazením virtuálního počítače s Windows v Azure | Microsoft Docs
description: Řešení potíží s Správce prostředků nasazení při vytváření nového virtuálního počítače s Windows v Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: daberry
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b2f21cab4740013eb2de1d69f558c95461c493e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028392"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Řešení potíží s nasazením při vytváření nového virtuálního počítače s Windows v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Další problémy s nasazením virtuálních počítačů a dotazy najdete v tématu [řešení potíží s nasazením problémů s virtuálním počítačem s Windows v Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Shromažďování protokolů aktivit
Pokud chcete začít řešit potíže, Shromážděte protokoly aktivit a Identifikujte chybu spojenou s problémem. Následující odkazy obsahují podrobné informace o tom, jak postupovat.

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**A:** Pokud je operační systém Windows zobecněný a nahraje se nebo zachycuje s zobecněným nastavením, neobjeví se žádné chyby. Podobně platí, že pokud je operační systém Windows specializovaný a je nahrán a/nebo zachycen se specializovaným nastavením, pak nedojde k žádným chybám.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je OS Windows zobecněná a nahraje se jako specializované, zobrazí se na obrazovce OOBE Chyba časového limitu zřizování virtuálního počítače.

**N<sup>2</sup>:** Pokud je operační systém specializovaný na Windows a nahraje se jako zobecněný, zobrazí se chyba zřizování virtuálního počítače zablokované na obrazovce OOBE, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem.

**Řešení**

Chcete-li tyto chyby vyřešit, použijte [příkaz Add-AzVhd k nahrání původního virtuálního pevného disku](/powershell/module/az.compute/add-azvhd), který je k dispozici místně, se stejným nastavením jako v operačním systému (generalizovaná/specializovaná). Pokud se chcete nahrát jako zobecněné, nezapomeňte nejdřív spustit nástroj Sysprep.

**Zachytit chyby:**

**N<sup>3</sup>:** Pokud je OS Windows zobecněná a je zachycená jako specializovaná, zobrazí se chyba vypršení časového limitu zřizování, protože původní virtuální počítač nelze použít, protože je označen jako zobecněný.

**N<sup>4</sup>:** Pokud je operační systém specializovaný na Windows a zachycuje se jako zobecněný, zobrazí se chyba při zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem. Původní virtuální počítač také nelze použít, protože je označen jako specializovaný.

**Řešení**

Chcete-li tyto chyby vyřešit, odstraňte aktuální bitovou kopii z portálu a znovu [ji Zachyťte z aktuálních virtuálních pevných disků](../windows/create-vm-specialized.md) se stejným nastavením jako u operačního systému (generalizovaná/specializovaná).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problém: vlastní/Galerie/image Marketplace; selhání přidělení
K této chybě dochází v situacích, kdy je nový požadavek na virtuální počítač připnutý ke clusteru, který buď nepodporuje požadovanou velikost virtuálního počítače, nebo nemá k dispozici volné místo pro vyžádání žádosti.

**Příčina 1:** Cluster nepodporuje požadovanou velikost virtuálního počítače.

**Řešení 1:**

* Opakujte požadavek s menší velikostí virtuálního počítače.
* Pokud velikost požadovaného virtuálního počítače nejde změnit:
  * Zastavte všechny virtuální počítače ve skupině dostupnosti.
    Klikněte na **skupiny**prostředků  >  *vaše skupina prostředků*  >  **prostředky**  >  *vaše sada dostupnosti*  >  **Virtual Machines**  >  *your virtual machine*  >  **zastavíte**virtuální počítač.
  * Po zastavení všech virtuálních počítačů Vytvořte nový virtuální počítač v požadované velikosti.
  * Nejprve spusťte nový virtuální počítač a potom vyberte všechny zastavené virtuální počítače a klikněte na tlačítko **Spustit**.

**Příčina 2:** Cluster nemá volné prostředky.

**Řešení 2:**

* Opakujte požadavek později.
* Pokud může být nový virtuální počítač součástí jiné skupiny dostupnosti
  * Vytvořte nový virtuální počítač v jiné skupině dostupnosti (ve stejné oblasti).
  * Přidejte nový virtuální počítač do stejné virtuální sítě.

## <a name="next-steps"></a>Další kroky
Pokud narazíte na problémy při spuštění zastaveného virtuálního počítače s Windows nebo změníte velikost existujícího virtuálního počítače s Windows v Azure, přečtěte si téma [řešení potíží s nasazením správce prostředků potíží s restartováním nebo změnou velikosti stávajícího virtuálního počítače s Windows v Azure](restart-resize-error-troubleshooting.md).
