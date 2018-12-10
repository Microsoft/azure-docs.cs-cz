---
title: Nelze vzdálené plochy na Azure Virtual Machines z důvodu statické IP adresy | Dokumentace Microsoftu
description: Zjistěte, jak potíží s RDP, jež je způsobena statická IP adresa v Microsoft Azure. | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141794"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Z důvodu statické IP adresy nelze ke vzdálené ploše na virtuálních počítačích Azure

Tento článek popisuje problém, který se po dokončení konfigurace statická IP adresa ve virtuálním počítači nejde vzdálené plochy Azure Windows Virtual Machines (VM).

> [!NOTE]
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptoms"></a>Příznaky

Když vytvoříte připojení RDP k virtuálnímu počítači v Azure, zobrazí se následující chybová zpráva:

**Vzdálené plochy nelze připojit ke vzdálenému počítači pro některou z těchto důvodů:**

1. **Není povolen vzdálený přístup k serveru**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není dostupný v síti**

**Ujistěte se, že vzdálený počítač je zapnutý a připojený k síti a zda je povolen vzdálený přístup.**

Když vrátíte se změnami na snímku obrazovky [Diagnostika spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, uvidíte, že virtuální počítač se spustí normálně a čeká se přihlašovací údaje na přihlašovací obrazovce.

## <a name="cause"></a>Příčina

Virtuální počítač má statickou IP adresu, která je definována v síťovém rozhraní v rámci Windows. Tato IP adresa se liší od adresu, která je definovaná na portálu Azure portal.

## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, povolte protokol DHCP pomocí sériového portu ovládací prvek nebo [resetování síťové rozhraní](reset-network-interface.md) pro virtuální počítač.

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se k [sériové konzoly a otevřené instance CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzole sériového portu není povolená na virtuálním počítači, přečtěte si téma [resetování síťové rozhraní](reset-network-interface.md).
2. Zaškrtněte, pokud služba DHCP je zakázána na síťovém rozhraní:

        netsh interface ip show config
3. Pokud je zakázané DHCP, vrácení konfiguraci síťového rozhraní pro používání protokolu DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Například pokud propojení 16b rozhraní názvy "Ethernet 2", spusťte následující příkaz:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Zadat dotaz na konfiguraci IP znovu a ujistěte se, že síťové rozhraní je nyní správně nastavené. Novou IP adresu by měl odpovídat účtu, které poskytuje Azure.

        netsh interface ip show config

    Restartujte virtuální počítač v tuto chvíli nemusíte. Virtuální počítač bude znovu dosažitelné.

Poté, pokud chcete nakonfigurovat statickou IP adresu pro virtuální počítač, přečtěte si téma [konfigurace statických IP adres pro virtuální počítač](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).