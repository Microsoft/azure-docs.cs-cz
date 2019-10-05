---
title: Řešení potíží s navrácením služeb po havárii v místním prostředí během zotavení virtuálního počítače VMware do Azure pomocí Azure Site Recovery | Microsoft Docs
description: V tomto článku se dozvíte, jak řešit problémy navrácení služeb po obnovení a zotavení po havárii virtuálního počítače VMware do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: c27e72333618f73b67eec9b5c0c3a70239a1c0b3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970857"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Řešení potíží se selháním zjišťování vCenter

Tento článek vám pomůže při řešení problémů, ke kterým dochází kvůli selháním zjišťování VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Jiné než číselné hodnoty ve vlastnosti maxSnapShots

Ve verzích starších než 9,20 se vCenter odpojí, když načte nečíselnou hodnotu vlastnosti @no__t vlastnost-0 na virtuálním počítači.

Tento problém je identifikovaný ID chyby 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Problém vyřešíte takto:

- Identifikujte virtuální počítač a nastavte hodnotu na číselnou hodnotu (upravit nastavení virtuálního počítače v vCenter).

nebo

- Upgradujte konfigurační server na verzi 9,20 nebo novější.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problémy s konfigurací proxy serveru pro připojení vCenter

Zjišťování vCenter dodržuje výchozí nastavení proxy serveru, které nakonfiguroval uživatel systému. Služba DRA během instalace konfiguračního serveru respektuje nastavení proxy poskytnuté uživatelem při instalaci konfiguračního serveru pomocí instalačního programu sjednocené instalace nebo šablony pro VAJÍČKy. 

Obecně platí, že se proxy server používá ke komunikaci s veřejnými sítěmi; například komunikace s Azure. Pokud je proxy server nakonfigurovaný a vCenter je v místním prostředí, nebude moct komunikovat s agentem DRA.

Při výskytu tohoto problému dojde k následujícím situacím:

- > VCenter serveru \<vCenter není dosažitelný kvůli chybě: vzdálený server vrátil chybu: (503) Server není k dispozici.
- > VCenter serveru \<vCenter není dosažitelný kvůli chybě: vzdálený server vrátil chybu: Nepodařilo se připojit ke vzdálenému serveru.
- Nepovedlo se připojit k serveru vCenter/ESXi.

Problém vyřešíte takto:

Stáhněte si [Nástroj PsExec](https://aka.ms/PsExec). 

Použijte nástroj PsExec pro přístup k systémovému kontextu uživatele a určete, jestli je adresa proxy serveru nakonfigurovaná. Pak můžete přidat vCenter do seznamu pro obejití pomocí následujících postupů.

Pro konfiguraci zjišťování proxy serveru:

1. Pomocí nástroje PsExec otevřete IE v uživatelském kontextu systému.
    
    PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"

2. Upravte nastavení proxy serveru v Internet Exploreru, aby se nepoužívala IP adresa vCenter.
3. Restartujte službu tmanssvc.

Pro konfiguraci proxy serveru DRA:

1. Otevřete příkazový řádek a otevřete složku Microsoft Azure Site Recovery poskytovatele.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery poskytovatel**

3. Z příkazového řádku spusťte následující příkaz.
   
   **DRCONFIGURATOR. EXE/Configure/AddBypassUrls [IP adresa/plně kvalifikovaný název domény vCenter Server poskytované v době přidání vCenter]**

4. Restartujte službu zprostředkovatele DRA.

## <a name="next-steps"></a>Další kroky

[Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
