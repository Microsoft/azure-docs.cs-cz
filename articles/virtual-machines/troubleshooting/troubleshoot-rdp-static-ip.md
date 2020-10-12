---
title: Z důvodu statické IP adresy nejde Vzdálená plocha do Azure Virtual Machines. Microsoft Docs
description: Naučte se řešit potíže s protokolem RDP, které jsou způsobené statickou IP adresou v Microsoft Azure. | Microsoft Docs
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
ms.openlocfilehash: 49f3f44c7de8c700d0093c5eb6f166a1dffb34a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087244"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Z důvodu statické IP adresy nejde Vzdálená plocha do Azure Virtual Machines.

Tento článek popisuje problém, ve kterém po nakonfigurování statické IP adresy na virtuálním počítači nemůžete do Azure Windows Virtual Machines (virtuální počítače) vzdálené plochy.


## <a name="symptoms"></a>Příznaky

Když provedete připojení RDP k virtuálnímu počítači v Azure, zobrazí se tato chybová zpráva:

**Vzdálená plocha se nemůže připojit ke vzdálenému počítači z jednoho z těchto důvodů:**

1. **Vzdálený přístup k serveru není povolený.**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není v síti k dispozici.**

**Ujistěte se, že je vzdálený počítač zapnutý a připojený k síti a že je povolený vzdálený přístup.**

Po kontrole snímku obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) v Azure Portal vidíte, že se virtuální počítač spouští normálně a čeká na přihlašovací údaje na přihlašovací obrazovce.

## <a name="cause"></a>Příčina

Virtuální počítač má statickou IP adresu, která je definovaná v síťovém rozhraní v systému Windows. Tato IP adresa se liší od adresy, která je definována v Azure Portal.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, pomocí sériového řízení povolte DHCP nebo [resetovat síťové rozhraní](reset-network-interface.md) pro virtuální počítač.

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](reset-network-interface.md).
2. Ověřte, jestli je na síťovém rozhraní zakázaný protokol DHCP:

    ```console
    netsh interface ip show config
    ```

3. Pokud je protokol DHCP zakázaný, obnovte konfiguraci síťového rozhraní tak, aby používala protokol DHCP:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhc
    ```

    Pokud například názvy rozhraní pro spolupráci "Ethernet 2", spusťte následující příkaz:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhc
    ```

4. Znovu spusťte dotaz na konfiguraci protokolu IP, abyste se ujistili, že je síťové rozhraní teď správně nastavené. Nová IP adresa by měla odpovídat adrese, kterou poskytuje Azure.

    ```console
    netsh interface ip show config
    ```

    V tuto chvíli nemusíte restartovat virtuální počítač. Virtuální počítač bude dostupný zpátky.

Pokud chcete pro virtuální počítač nakonfigurovat statickou IP adresu, přečtěte si téma [Konfigurace statických IP adres pro virtuální počítač](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).