---
title: Brána firewall hostovaného operačního systému Azure VM je chybně nakonfigurovaná | Dokumenty společnosti Microsoft
description: Přečtěte si, jak diagnostikovat a opravit nesprávně nakonfigurovanou bránu firewall hostovaného operačního systému na vzdáleném virtuálním počítači Azure pomocí metody Serial Console nebo offline.
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422537"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Brána firewall hostovaného operačního systému Azure VM je chybně nakonfigurovaná

Tento článek uvádí, jak opravit chybně nakonfigurovanou bránu firewall hostovaného operačního systému na virtuálním počítači Azure.

## <a name="symptoms"></a>Příznaky

1.  Úvodní obrazovka virtuálního počítače (VM) ukazuje, že virtuální počítač je plně načten.

2.  V závislosti na konfiguraci hostovaného operačního systému může být některé nebo žádné síťové přenosy dosažení virtuálního počítače.

## <a name="cause"></a>Příčina

Nesprávnou konfigurací brány firewall hostovaného systému můžete blokovat některé nebo všechny druhy síťového provozu na virtuální ms.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku postiženého virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

K řešení tohoto problému použijte konzolu Serial Console nebo [opravte virtuální počítače offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) připojením systémového disku virtuálního počítače k virtuálnímu virtuálnímu počítače pro obnovení.

## <a name="online-mitigations"></a>Online skutečnosti snižující závažnost rizika

Připojte se k [konzoli Serial Console a otevřete instanci prostředí PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud konzola Serial Console není na virtuálním počítači povolená, přejděte na část Oprava virtuálního počítače offline v následujícím článku Azure:

 [Při pokusu o připojení k virtuálnímu počítači Azure přes Vzdálenou plochu dojde k vnitřní chybě](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Následující pravidla lze upravit tak, aby buď povolit přístup k virtuálnímu virtuálnímu zařízení (prostřednictvím RDP) nebo poskytnout snadnější řešení potíží:

*   Vzdálená plocha (TCP-In): Toto je standardní pravidlo, které poskytuje primární přístup k virtuálnímu počítači tím, že umožňuje RDP v Azure.

*   Vzdálená správa systému Windows (HTTP-In): Toto pravidlo umožňuje připojení k virtuálnímu počítači pomocí prostředí PowerShell., V Azure tento druh přístupu umožňuje použít aspekt skriptování vzdáleného skriptování a řešení potíží.

*   Sdílení souborů a tiskáren (SMB-In): Toto pravidlo umožňuje přístup ke sdílené síti jako možnost řešení potíží.

*   Sdílení souborů a tiskáren (požadavek na odezvu – ICMPv4-In): Toto pravidlo umožňuje příkaz ping na virtuální mk.

V instanci Služby sériové konzoly můžete zadat dotaz na aktuální stav pravidla brány firewall.

*   Dotaz pomocí zobrazovacího jména jako parametru:

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

*   Pokud zjistíte, že pravidlo je zakázáno, můžete ho povolit spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Při řešení potíží můžete vypnout profily brány firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Pokud tak učiníte, chcete-li správně nastavit bránu firewall, po dokončení řešení potíží znovu povolte bránu firewall.

    > [!Note]
    > Není nutné restartovat virtuální počítač použít tuto změnu.

*   Zkuste se znovu připojit k virtuálnímu virtuálnímu zařízení prostřednictvím rdp.

### <a name="offline-mitigations"></a>Skutečnosti snižující závažnost rizika v offline

1.  Chcete-li povolit nebo zakázat pravidla brány firewall, přečtěte si článek [Povolení nebo zakázání pravidla brány firewall v hostovaném osu Virtuálního počítače Azure](enable-disable-firewall-rule-guest-os.md).

2.  Zkontrolujte, zda se nenacházejí v [bráně firewall hostovaného operačního systému, který blokuje scénář příchozího provozu](guest-os-firewall-blocking-inbound-traffic.md).

3.  Pokud stále máte pochybnosti o tom, jestli brána firewall blokuje váš přístup, přečtěte [si informace o zakázání brány firewall hostovaného operačního systému ve virtuálním počítači Azure](disable-guest-os-firewall-windows.md)a potom znovu povolte bránu firewall pro systém hosta pomocí správných pravidel.

