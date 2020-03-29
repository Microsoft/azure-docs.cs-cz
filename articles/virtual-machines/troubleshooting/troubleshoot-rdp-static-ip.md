---
title: Nelze vzdálenou plochu virtuálních počítačů Azure z důvodu statické IP| Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problém protokolu RDP, který je způsoben statickou IP adresou v Microsoft Azure.| Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918185"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Nelze vzdálenou plochu na virtuální počítače Azure z důvodu statické IP

Tento článek popisuje problém, ve kterém nelze vzdálenou plochu na Virtuální počítače Azure Windows (VMs) po statické IP je nakonfigurován ve virtuálním počítači.


## <a name="symptoms"></a>Příznaky

Když vytvoříte připojení RDP k virtuálnímu počítači v Azure, zobrazí se následující chybová zpráva:

**Vzdálená plocha se nemůže připojit ke vzdálenému počítači z jednoho z těchto důvodů:**

1. **Vzdálený přístup k serveru není povolen.**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není v síti k dispozici.**

**Zkontrolujte, zda je vzdálený počítač zapnutý a připojený k síti a zda je povolen vzdálený přístup.**

Když zkontrolujete snímek obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, uvidíte spuštění virtuálního počítače normálně a čeká na přihlašovací údaje na přihlašovací obrazovce.

## <a name="cause"></a>Příčina

Virtuální počítač má statickou IP adresu, která je definována v síťovém rozhraní v systému Windows. Tato IP adresa se liší od adresy, která je definována na webu Azure Portal.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte sériový ovládací prvek k povolení DHCP nebo [obnovení síťového rozhraní](reset-network-interface.md) pro virtuální hod.

### <a name="use-serial-control"></a>Použití sériového řízení

1. Připojte se k [konzoli Serial Console a otevřete instanci CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzola Sériové konzole není na vašem virtuálním počítači povolená, přečtěte si článek [Obnovení síťového rozhraní](reset-network-interface.md).
2. Zkontrolujte, zda je v síťovém rozhraní zakázánserver DHCP:

        netsh interface ip show config
3. Pokud je služba DHCP zakázána, vraťte konfiguraci síťového rozhraní a použijte takto:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Pokud například názvy mezipracovního rozhraní "Ethernet 2", spusťte následující příkaz:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Znovu se dotazovat na konfiguraci protokolu IP a ujistěte se, že je nyní správně nastaveno síťové rozhraní. Nová IP adresa by měla odpovídat té, kterou poskytuje Azure.

        netsh interface ip show config

    V tomto okamžiku není nutné restartovat virtuální počítač. Virtuální měkce bude zase dosažitelný.

Poté, pokud chcete nakonfigurovat statickou IP adresu pro virtuální počítače, najdete v [tématu Konfigurace statických IP adres pro virtuální počítače](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).