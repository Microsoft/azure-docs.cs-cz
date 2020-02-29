---
title: Nelze vzdáleně připojit k Azure Virtual Machines vzhledem k tomu, že síťová karta je zakázaná. | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže, ve kterém RDP selže, protože síťové rozhraní je zakázaný ve virtuálním počítači Azure | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918236"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nelze provést vzdálené plochy k virtuálnímu počítači, protože síťové rozhraní je zakázaná.

Tento článek vysvětluje, jak vyřešit problém, ve kterém nemůžete provádět připojení ke vzdálené ploše do Azure Windows Virtual Machines (VM) je zakázána. síťové rozhraní.


## <a name="symptoms"></a>Příznaky

Připojení ke vzdálené ploše nebo jakýkoli jiný typ připojení k dalším portům nelze provést na virtuálním počítači v Azure, protože síťové rozhraní ve virtuálním počítači zakázaná.

## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete povolit rozhraní pro virtuální počítač, použijte pro virtuální počítač sériové řízení nebo [resetování síťového rozhraní](#reset-network-interface) .

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](#reset-network-interface).
2. Kontrola stavu síťového rozhraní:

        netsh interface show interface

    Poznamenejte si název zakázané síťové rozhraní.

3. Povolení síťové rozhraní:

        netsh interface set interface name="interface Name" admin=enabled

    Například pokud propojení 16b rozhraní s názvem "Ethernet 2", spusťte následující příkaz:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Zkontrolujte stav rozhraní sítě znovu a ujistit se, zda je povoleno rozhraní sítě.

        netsh interface show interface

    Restartujte virtuální počítač v tuto chvíli nemusíte. Virtuální počítač bude znovu dosažitelné.

5.  Připojit k virtuálnímu počítači a podívat se, zda byl problém vyřešen.

## <a name="reset-network-interface"></a>Resetování síťové rozhraní

Resetování síťové rozhraní, změňte IP adresu na jinou IP adresu, která je dostupná v podsíti. To provedete pomocí webu Azure portal nebo Azure Powershellu. Další informace najdete v tématu [resetování síťového rozhraní](reset-network-interface.md).
