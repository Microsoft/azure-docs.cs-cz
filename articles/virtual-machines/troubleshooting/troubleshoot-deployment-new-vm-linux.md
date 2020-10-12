---
title: Řešení potíží s nasazením virtuálního počítače se systémem Linux | Microsoft Docs
description: Řešení potíží s Správce prostředků nasazení při vytváření nového virtuálního počítače se systémem Linux v Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: daberry
ms.openlocfilehash: f48963a4d18e80cb67bfbbdc532d34f89b8b5d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028443"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Řešení potíží s nasazením Správce prostředků při vytváření nového virtuálního počítače se systémem Linux v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Další dotazy a potíže s nasazením virtuálního počítače najdete v tématu [Řešení potíží s nasazením linuxového virtuálního počítače v Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Shromažďování protokolů aktivit
Pokud chcete začít řešit potíže, Shromážděte protokoly aktivit a Identifikujte chybu spojenou s problémem. Následující odkazy obsahují podrobné informace o tom, jak postupovat.

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**A:** Pokud je operační systém systému Linux zobecněn a je nahrán a/nebo zachycen s zobecněným nastavením, pak nedojde k žádným chybám. Podobně platí, že pokud je operační systém Linux specializovaný a je nahrán a/nebo zachycen se specializovaným nastavením, pak nedojde k žádným chybám.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Linux zobecněný a nahraje se jako specializované, zobrazí se chyba časového limitu zřizování, protože virtuální počítač se zablokuje ve fázi zřizování.

**N<sup>2</sup>:** Pokud je operační systém Linux specializovaný a nahraje se jako zobecněný, zobrazí se chyba zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem.

**Rozhodnutí**

Chcete-li tyto chyby vyřešit, nahrajte původní virtuální pevný disk, který je k dispozici místně, se stejným nastavením jako v operačním systému (generalizovaná/specializovaná). Pokud se chcete nahrát jako zobecněné, nezapomeňte nejdřív spustit – zrušit zřízení.

**Zachytit chyby:**

**N<sup>3</sup>:** Pokud je operační systém Linux zobecněný a je zachycen jako specializovaný, zobrazí se chyba časového limitu zřizování, protože původní virtuální počítač nelze použít, protože je označen jako zobecněný.

**N<sup>4</sup>:** Pokud je operační systém Linux specializovaný a zachycuje se jako zobecněný, zobrazí se chyba při zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem. Původní virtuální počítač také nelze použít, protože je označen jako specializovaný.

**Rozhodnutí**

Chcete-li tyto chyby vyřešit, odstraňte aktuální bitovou kopii z portálu a znovu [ji Zachyťte z aktuálních virtuálních pevných disků](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se stejným nastavením jako u operačního systému (generalizovaná/specializovaná).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: vlastní/Galerie/image Marketplace; selhání přidělení
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
Pokud narazíte na problémy při spuštění zastaveného virtuálního počítače se systémem Linux nebo změníte velikost stávajícího virtuálního počítače se systémem Linux v Azure, přečtěte si téma [řešení potíží s nasazením správce prostředků při restartování nebo změně velikosti stávajícího virtuálního počítače se systémem Linux v Azure](./troubleshoot-deploy-vm-linux.md?toc=/azure/virtual-machines/linux/toc.json)
