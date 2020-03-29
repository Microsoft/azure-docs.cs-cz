---
title: Vzdáleně se nelze připojit k virtuálním počítačům Azure, protože je neplatná | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit problém, ve kterém se nezdaří rdp, protože nic je zakázáno v azure virtuálním počítači| Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918236"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nelze vzdálenou plochu pro virtuální počítač, protože síťové rozhraní je zakázáno

Tento článek vysvětluje, jak vyřešit problém, ve kterém nelze vytvořit připojení ke vzdálené ploše k virtuálním počítačům Azure Windows (VM), pokud je síťové rozhraní zakázáno.


## <a name="symptoms"></a>Příznaky

Nelze vytvořit připojení RDP nebo jakýkoli jiný typ připojení k jiným portům k virtuálnímu počítači v Azure, protože síťové rozhraní ve virtuálním počítači je zakázáno.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li povolit rozhraní pro virtuální modul, použijte sériové řízení nebo [obnovení síťového rozhraní](#reset-network-interface) pro virtuální ho.

### <a name="use-serial-control"></a>Použití sériového řízení

1. Připojte se k [konzoli Serial Console a otevřete instanci CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzola Serial Console není na vašem virtuálním počítači povolená, přečtěte si článek [Obnovení síťového rozhraní](#reset-network-interface).
2. Zkontrolujte stav síťového rozhraní:

        netsh interface show interface

    Poznamenejte si název zakázaného síťového rozhraní.

3. Povolte síťové rozhraní:

        netsh interface set interface name="interface Name" admin=enabled

    Pokud má například mezipracovní rozhraní název "Ethernet 2", spusťte následující příkaz:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Zkontrolujte stav síťového rozhraní znovu a ujistěte se, že je povoleno síťové rozhraní.

        netsh interface show interface

    V tomto okamžiku není nutné restartovat virtuální počítač. Virtuální měkce bude zase dosažitelný.

5.  Připojte se k virtuálnímu virtuálnímu zařízení a zjistěte, zda je problém vyřešen.

## <a name="reset-network-interface"></a>Obnovit síťové rozhraní

Chcete-li obnovit síťové rozhraní, změňte adresu IP na jinou adresu IP, která je k dispozici v podsíti. K tomu použijte Azure Portal nebo Azure PowerShell. Další informace naleznete v tématu [Reset síťového rozhraní](reset-network-interface.md).
