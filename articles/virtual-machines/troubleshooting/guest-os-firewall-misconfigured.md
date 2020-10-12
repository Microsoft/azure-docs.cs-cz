---
title: Brána firewall hostovaného operačního systému virtuálního počítače Azure je chybně nakonfigurovaná | Microsoft Docs
description: Naučte se pomocí sériové konzoly nebo offline metody diagnostikovat a opravit nesprávně nakonfigurovanou bránu firewall hostovaného operačního systému na vzdáleném virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422537"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Brána firewall hostovaného operačního systému virtuálního počítače Azure je chybně nakonfigurovaná.

Tento článek zavádí, jak opravit nesprávně nakonfigurovanou bránu firewall hostovaného operačního systému na virtuálním počítači Azure.

## <a name="symptoms"></a>Příznaky

1.  Úvodní obrazovka virtuálního počítače ukazuje, že je virtuální počítač plně načtený.

2.  V závislosti na tom, jak je hostovaný operační systém nakonfigurovaný, může dojít k nějakému nebo žádnému síťovému provozu, který se dostane k virtuálnímu počítači.

## <a name="cause"></a>Příčina

Chybná konfigurace brány firewall systému hosta může blokovat některé nebo všechny druhy síťových přenosů do virtuálního počítače.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, použijte konzolu sériového portu nebo [opravte virtuální počítač offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) připojením systémového disku virtuálního počítače k virtuálnímu počítači pro obnovení.

## <a name="online-mitigations"></a>Online zmírnění

Připojte se ke [konzole sériového portu a pak otevřete instanci prostředí PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud na virtuálním počítači není povolená konzola sériového prostředí, v následujícím článku Azure v části "opravit virtuální počítač offline":

 [Při pokusu o připojení k virtuálnímu počítači Azure přes Vzdálenou plochu dojde k vnitřní chybě](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Následující pravidla je možné upravit tak, aby povolovala přístup k virtuálnímu počítači (prostřednictvím protokolu RDP), nebo aby bylo možné zajistit snazší řešení potíží:

*   Vzdálená plocha (TCP-in): Toto je standardní pravidlo, které poskytuje primární přístup k virtuálnímu počítači tím, že povoluje protokol RDP v Azure.

*   Vzdálená správa systému Windows (HTTP-in): Toto pravidlo vám umožní připojit se k virtuálnímu počítači pomocí PowerShellu. v Azure vám tento druh přístupu umožní používat skriptování ke vzdálenému skriptování a odstraňování potíží.

*   Sdílení souborů a tiskáren (SMB-in): Toto pravidlo umožňuje přístup ke sdílené síťové složce jako možnost řešení potíží.

*   Sdílení souborů a tiskáren (požadavek na odezvu – ICMPv4-In): Toto pravidlo umožňuje testovat virtuální počítač z příkazového testu.

V instanci přístupu ke konzole sériového portu můžete zadat dotaz na aktuální stav pravidla brány firewall.

*   Dotaz pomocí zobrazovaného názvu jako parametru:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Dotaz pomocí místního portu, který aplikace používá:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Dotaz pomocí místní IP adresy, kterou aplikace používá:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Pokud vidíte, že je pravidlo zakázané, můžete ho povolit spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Pro řešení potíží můžete profily brány firewall vypnout:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Pokud to provedete tak, že bránu firewall nastavíte správně, po dokončení řešení potíží znovu povolte bránu firewall.

    > [!Note]
    > Pro použití této změny nemusíte restartovat virtuální počítač.

*   Zkuste se znovu připojit k virtuálnímu počítači přes RDP.

### <a name="offline-mitigations"></a>Zmírnění offline

1.  Pokud chcete povolit nebo zakázat pravidla brány firewall, přečtěte si téma [Povolení nebo zakázání pravidla brány firewall v hostovaném operačním systému virtuálního počítače Azure](enable-disable-firewall-rule-guest-os.md).

2.  Ověřte, jestli jste ve [scénáři příchozího provozu blokujícím bránu firewall hostovaného operačního systému](guest-os-firewall-blocking-inbound-traffic.md).

3.  Pokud stále nejste jistí, jestli brána firewall blokuje váš přístup, přečtěte si téma [Zakázání brány firewall hostovaného operačního systému ve virtuálním počítači Azure](disable-guest-os-firewall-windows.md)a pak znovu povolte bránu firewall systému hosta pomocí správných pravidel.

