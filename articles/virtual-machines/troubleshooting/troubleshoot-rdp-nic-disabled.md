---
title: Nejde se vzdáleně připojit k Azure Virtual Machines, protože síťová karta je zakázaná | Microsoft Docs
description: Naučte se řešit potíže s tím, že se protokol RDP nepovede, protože síťová karta je zakázaná ve virtuálním počítači Azure | Microsoft Docs
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
ms.openlocfilehash: 0e79efc9de43fc0a3044e9ae1e3959f63bb6e69f
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090246"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nejde o vzdálenou plochu k virtuálnímu počítači, protože síťové rozhraní je zakázané.

Tento článek vysvětluje, jak vyřešit problém, ve kterém nemůžete vytvořit připojení ke vzdálené ploše do Azure Windows Virtual Machines (virtuální počítače), pokud je síťové rozhraní zakázané.


## <a name="symptoms"></a>Příznaky

Nemůžete vytvořit připojení RDP ani jiný typ připojení k virtuálnímu počítači v Azure, protože síťové rozhraní je ve virtuálním počítači zakázané.

![Snímek obrazovky zobrazující virtuální počítač, ve kterém je síťové rozhraní odpojeno.](./media/troubleshoot-rdp-nic-disabled/disconnected.png)

![Snímek obrazovky zobrazující virtuální počítač, ve kterém je síťové rozhraní zakázané.](./media/troubleshoot-rdp-nic-disabled/disabled.png)


## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete povolit rozhraní pro virtuální počítač, použijte pro virtuální počítač sériové řízení nebo [resetování síťového rozhraní](#reset-network-interface) .

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](#reset-network-interface).
2. Ověřte stav síťového rozhraní:

    ```console
    netsh interface show interface
    ```

    Poznamenejte si název zakázaného síťového rozhraní.

3. Povolit síťové rozhraní:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Pokud je třeba rozhraní pro spolupráci s názvem "Ethernet 2", spusťte následující příkaz:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Zkontrolujte stav síťového rozhraní znovu a ujistěte se, že je síťové rozhraní povolené.

    ```console
    netsh interface show interface
    ```

    V tuto chvíli nemusíte restartovat virtuální počítač. Virtuální počítač bude dostupný zpátky.

5.  Připojte se k virtuálnímu počítači a podívejte se, jestli se problém vyřeší.

## <a name="reset-network-interface"></a>Resetovat síťové rozhraní

Pokud chcete resetovat síťové rozhraní, změňte IP adresu na jinou IP adresu, která je k dispozici v podsíti. K tomu použijte Azure Portal nebo Azure PowerShell. Další informace najdete v tématu [resetování síťového rozhraní](reset-network-interface.md).
