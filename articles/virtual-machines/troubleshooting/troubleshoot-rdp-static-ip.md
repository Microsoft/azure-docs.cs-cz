---
title: Nelze vzdálené plochy na Azure Virtual Machines z důvodu statické IP adresy | Dokumentace Microsoftu
description: Zjistěte, jak potíží s RDP, jež je způsobena statická IP adresa v Microsoft Azure. | Dokumentace Microsoftu
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918185"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Z důvodu statické IP adresy nelze ke vzdálené ploše na virtuálních počítačích Azure

Tento článek popisuje problém, který se po dokončení konfigurace statická IP adresa ve virtuálním počítači nejde vzdálené plochy Azure Windows Virtual Machines (VM).


## <a name="symptoms"></a>Příznaky

Když vytvoříte připojení RDP k virtuálnímu počítači v Azure, zobrazí se následující chybová zpráva:

**Vzdálená plocha se nemůže připojit ke vzdálenému počítači z jednoho z těchto důvodů:**

1. **Vzdálený přístup k serveru není povolený.**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není v síti k dispozici.**

**Ujistěte se, že je vzdálený počítač zapnutý a připojený k síti a že je povolený vzdálený přístup.**

Po kontrole snímku obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) v Azure Portal vidíte, že se virtuální počítač spouští normálně a čeká na přihlašovací údaje na přihlašovací obrazovce.

## <a name="cause"></a>Příčina

Virtuální počítač má statickou IP adresu, která je definována v síťovém rozhraní v rámci Windows. Tato IP adresa se liší od adresu, která je definovaná na portálu Azure portal.

## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, pomocí sériového řízení povolte DHCP nebo [resetovat síťové rozhraní](reset-network-interface.md) pro virtuální počítač.

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](reset-network-interface.md).
2. Zaškrtněte, pokud služba DHCP je zakázána na síťovém rozhraní:

        netsh interface ip show config
3. Pokud je zakázané DHCP, vrácení konfiguraci síťového rozhraní pro používání protokolu DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Například pokud propojení 16b rozhraní názvy "Ethernet 2", spusťte následující příkaz:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Zadat dotaz na konfiguraci IP znovu a ujistěte se, že síťové rozhraní je nyní správně nastavené. Novou IP adresu by měl odpovídat účtu, které poskytuje Azure.

        netsh interface ip show config

    Restartujte virtuální počítač v tuto chvíli nemusíte. Virtuální počítač bude znovu dosažitelné.

Pokud chcete pro virtuální počítač nakonfigurovat statickou IP adresu, přečtěte si téma [Konfigurace statických IP adres pro virtuální počítač](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).