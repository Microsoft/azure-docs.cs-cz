---
title: Poradce při potížích s nasazením virtuálních počítačů windows v Azure | Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením Správce prostředků při vytváření nového virtuálního počítače s Windows v Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981391"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Řešení problémů s nasazením při vytváření nového virtuálního počítače s Windows v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Další problémy s nasazením virtuálních počítačů a otázky [najdete v tématu Poradce při potížích s nasazením virtuálního počítače s Windows v Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Shromažďovat protokoly aktivit
Chcete-li spustit řešení potíží, shromažďovat protokoly aktivit k identifikaci chyby spojené s problémem. Následující odkazy obsahují podrobné informace o následujícím procesu.

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Pokud je operační systém Windows zobecněn a je nahrán a / nebo zachycen s generalizovaným nastavením, pak nebudou žádné chyby. Podobně, pokud je operační systém Windows specializovaný a je nahrán a / nebo zachycen s specializovaným nastavením, pak nebudou žádné chyby.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Windows generalizovaný a nahraje se jako specializovaný, získáte chybu časového času zřizování s virtuálním virtuálním serverem zablokovaným na obrazovce OOBE.

**N<sup>2</sup>:** Pokud je operační systém Windows specializovaný a nahraje se jako zobecněný, zobrazí se chyba selhání zřizování s virtuálním počítačem zablokovaným na obrazovce OOBE, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem.

**Rozlišení**

Chcete-li vyřešit obě tyto chyby, použijte [Add-AzVHD k nahrání původního virtuálního pevného disku](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), který je k dispozici místně, se stejným nastavením jako pro operační systém (generalizovaný/specializovaný). Chcete-li nahrát jako zobecněné, nezapomeňte nejprve spustit sysprep.

**Chyby zachycení:**

**N<sup>3</sup>:** Pokud je operační systém Windows generalizován a je zachycen jako specializované, zobrazí se chyba časového času zřizování, protože původní virtuální hod není použitelný, protože je označen jako generalizovaný.

**N<sup>4</sup>:** Pokud je operační systém Windows specializovaný a je zachycen jako generalizovaný, zobrazí se chyba selhání zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem. Původní virtuální virtuální byl také použitelný, protože je označen jako specializovaný.

**Rozlišení**

Chcete-li vyřešit obě tyto chyby, odstraňte aktuální obrázek z portálu a [znovu jej zachyťte z aktuálních virtuálních discích](../windows/create-vm-specialized.md) se stejným nastavením jako pro operační systém (generalizovaný/specializovaný).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problém: Vlastní/galerie/obrázek tržiště; selhání přidělení
K této chybě dochází v situacích, kdy je nový požadavek na virtuální počítače připnutý ke clusteru, který buď nepodporuje požadovanou velikost virtuálního počítače, nebo nemá volné místo pro uložení požadavku.

**Příčina 1:** Cluster nemůže podporovat požadovanou velikost virtuálního počítače.

**Řešení 1:**

* Opakujte požadavek pomocí menší velikosti virtuálního počítače.
* Pokud velikost požadovaného virtuálního počítače nelze změnit:
  * Zastavte všechny virtuální ho disponibilní služby v sadě dostupnosti.
    Klikněte na **skupiny** > prostředků*skupiny* > **prostředků Zdroje,** > *vaši dostupnost nastavit* > **virtuální počítače** > *virtuální počítač* > **zastavit**.
  * Po zastavení všech virtuálních počítače vytvořte nový virtuální počítače v požadované velikosti.
  * Nejprve spusťte nový virtuální virtuální ms, pak vyberte každý z zastavených virtuálních ms a klikněte na **Start**.

**Příčina 2:** Cluster nemá volné prostředky.

**Řešení 2:**

* Opakujte požadavek později.
* Pokud nový virtuální virtuální město může být součástí jiné sady dostupnosti
  * Vytvořte nový virtuální virtuální ms v jiné sadě dostupnosti (ve stejné oblasti).
  * Přidejte nový virtuální počítač do stejné virtuální sítě.

## <a name="next-steps"></a>Další kroky
Pokud narazíte na problémy při spuštění zastaveného virtuálního počítače s Windows nebo změně velikosti existujícího virtuálního počítače s Windows v Azure, [přečtěte si článek Řešení potíží s nasazením Správce prostředků s restartováním nebo opětovnou velikostí existujícího virtuálního počítače s Windows v Azure](restart-resize-error-troubleshooting.md).


