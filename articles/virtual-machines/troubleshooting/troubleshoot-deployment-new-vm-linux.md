---
title: Poradce při potížích s nasazením virtuálního počítače s Linuxem| Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením Správce prostředků při vytváření nového virtuálního počítače s Linuxem v Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981408"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Poradce při potížích s nasazením Správce prostředků při vytváření nového virtuálního počítače s Linuxem v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Další dotazy a potíže s nasazením virtuálního počítače najdete v tématu [Řešení potíží s nasazením linuxového virtuálního počítače v Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Shromažďovat protokoly aktivit
Chcete-li spustit řešení potíží, shromažďovat protokoly aktivit k identifikaci chyby spojené s problémem. Následující odkazy obsahují podrobné informace o následujícím procesu.

[Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md)

[Zobrazení protokolů aktivit pro správu prostředků Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Pokud je operační systém Linux zobecněn a je nahrán a / nebo zachycen s generalizovaným nastavením, pak nebudou žádné chyby. Podobně, pokud je operační systém Linux specializovaný a je nahrán a / nebo zachycen se specializovaným nastavením, pak nebudou žádné chyby.

**Chyby nahrávání:**

**N<sup>1</sup>:** Pokud je operační systém Linux generalizované a je nahrán jako specializované, dostanete chybu časového času zřizování, protože virtuální počítač je přilepená ve fázi zřizování.

**N<sup>2</sup>:** Pokud je operační systém Linux specializovaný a je odeslán jako generalizovaný, zobrazí se chyba selhání zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem.

**Rozlišení:**

Chcete-li vyřešit obě tyto chyby, nahrajte původní virtuální pevný disk, který je k dispozici místně, se stejným nastavením jako pro operační systém (generalizovaný/specializovaný). Chcete-li nahrát jako generalizované, nezapomeňte nejprve spustit -deprovision.

**Chyby zachycení:**

**N<sup>3</sup>:** Pokud je operační systém Linux generalizován a je zachycen jako specializované, dostanete chybu časového času zřizování, protože původní virtuální počítač není použitelný, protože je označen jako generalizovaný.

**N<sup>4</sup>:** Pokud je operační systém Linux specializovaný a je zachycen jako generalizovaný, zobrazí se chyba selhání zřizování, protože nový virtuální počítač běží s původním názvem počítače, uživatelským jménem a heslem. Původní virtuální virtuální byl také použitelný, protože je označen jako specializovaný.

**Rozlišení:**

Chcete-li vyřešit obě tyto chyby, odstraňte aktuální obrázek z portálu a [znovu jej zachyťte z aktuálních virtuálních discích](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se stejným nastavením jako pro operační systém (generalizovaný/specializovaný).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: Vlastní / galerie / tržiště image; selhání přidělení
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
Pokud narazíte na problémy při spuštění zastaveného virtuálního počítače s Linuxem nebo při změně velikosti existujícího virtuálního počítače s Linuxem v Azure, [přečtěte si článek Řešení potíží s nasazením Správce prostředků s restartováním nebo opětovnou velikostí existujícího virtuálního počítače SIP v Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

