---
title: Řešení chyb zjišťování VMware vCenter v Azure Site Recovery
description: Tento článek popisuje, jak řešit chyby zjišťování VMware vCenter v Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366648"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Řešení potíží se selháním zjišťování vCenter Server

Tento článek vám pomůže při řešení problémů, ke kterým dochází kvůli selháním zjišťování VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Jiné než číselné hodnoty ve vlastnosti maxSnapShots

Ve verzích starších než 9,20 se vCenter odpojí, když načte nečíselnou hodnotu pro  `snapshot.maxSnapShots` vlastnost Property na virtuálním počítači.

Tento problém je identifikovaný ID chyby 95126.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

Řešení tohoto problému:

- Identifikujte virtuální počítač a nastavte hodnotu na číselnou hodnotu (upravit nastavení virtuálního počítače v vCenter).

Nebo

- Upgradujte konfigurační server na verzi 9,20 nebo novější.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problémy s konfigurací proxy serveru pro připojení vCenter

Zjišťování vCenter dodržuje výchozí nastavení proxy serveru, které nakonfiguroval uživatel systému. Služba DRA během instalace konfiguračního serveru respektuje nastavení proxy poskytnuté uživatelem při instalaci konfiguračního serveru pomocí instalačního programu sjednocené instalace nebo šablony pro VAJÍČKy. 

Obecně platí, že se proxy server používá ke komunikaci s veřejnými sítěmi; například komunikace s Azure. Pokud je proxy server nakonfigurovaný a vCenter je v místním prostředí, nebude moct komunikovat s agentem DRA.

Při výskytu tohoto problému dojde k následujícím situacím:

- Server vCenter není \<vCenter> dosažitelný kvůli chybě: vzdálený server vrátil chybu: (503) Server není k dispozici.
- Server vCenter není \<vCenter> dosažitelný kvůli chybě: vzdálený server vrátil chybu: Nepodařilo se připojit ke vzdálenému serveru.
- Nepovedlo se připojit k serveru vCenter/ESXi.

Řešení tohoto problému:

Stáhněte si [Nástroj PsExec](/sysinternals/downloads/psexec). 

Použijte nástroj PsExec pro přístup k systémovému kontextu uživatele a určete, jestli je adresa proxy serveru nakonfigurovaná. Pak můžete přidat vCenter do seznamu pro obejití pomocí následujících postupů.

Pro konfiguraci zjišťování proxy serveru:

1. Pomocí nástroje PsExec otevřete IE v uživatelském kontextu systému.
    
    PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"

2. Upravte nastavení proxy serveru v Internet Exploreru, aby se nepoužívala IP adresa vCenter.
3. Restartujte službu tmanssvc.

Pro konfiguraci proxy serveru DRA:

1. Otevřete příkazový řádek a otevřete složku Microsoft Azure Site Recovery poskytovatele.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery poskytovatel**

3. Na příkazovém řádku spusťte následující příkaz.
   
   **DRCONFIGURATOR.EXE/Configure/AddBypassUrls [IP adresa/plně kvalifikovaný název domény vCenter Server poskytnutý v době přidání vCenter]**

4. Restartujte službu zprostředkovatele DRA.

## <a name="next-steps"></a>Další kroky

[Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)