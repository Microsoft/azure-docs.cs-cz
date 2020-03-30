---
title: Poradce při potížích s chybami zjišťování vcentru VMware v azure site recovery
description: Tento článek popisuje, jak řešit selhání zjišťování vMware vCenter v Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091239"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Poradce při potížích s zjišťováním serveru vcenter

Tento článek vám pomůže vyřešit problémy, ke kterým dochází z důvodu selhání zjišťování vCenter VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Nečíselné hodnoty ve vlastnosti maxSnapShots

Ve verzích před 9.20 se vCenter odpojí, když načte `snapshot.maxSnapShots` nečíselnou hodnotu vlastnosti vlastnosti na virtuálním počítači.

Tento problém je identifikován chybou ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Chcete-li tento problém vyřešit:

- Identifikujte virtuální ho a nastavte hodnotu na číselnou hodnotu (nastavení úprav virtuálních zařízení v centru).

Nebo

- Upgradujte konfigurační server na verzi 9.20 nebo novější.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problémy s konfigurací proxy serveru pro připojení vCenter

vCenter Discovery respektuje výchozí nastavení serveru proxy systému nakonfigurované uživatelem systému. Služba DRA respektuje nastavení proxy serveru poskytovaná uživatelem během instalace konfiguračního serveru pomocí jednotného instalačního programu nebo šablony OVA. 

Obecně platí, že proxy se používá ke komunikaci s veřejnými sítěmi; jako je například komunikace s Azure. Pokud je server proxy nakonfigurován a vCenter je v místním prostředí, nebude moci komunikovat s DRA.

Při výskytu tohoto problému nastanou následující situace:

- VCenter \<vCenter vCenter> není dostupný z důvodu chyby: Vzdálený server vrátil chybu: (503) Server není k dispozici
- VCenter \<vCenter vCenter> není dostupný z důvodu chyby: Vzdálený server vrátil chybu: Nelze se připojit ke vzdálenému serveru.
- Nelze se připojit k serveru vCenter/ESXi.

Chcete-li tento problém vyřešit:

Stáhněte si [nástroj PsExec](https://aka.ms/PsExec). 

Pomocí nástroje PsExec přetekovat do kontextu uživatele systému a určit, zda je nakonfigurována adresa proxy. Potom můžete přidat vCenter do seznamu bypass pomocí následujících postupů.

Pro konfiguraci proxy serveru Discovery:

1. Otevřete aplikaci IE v kontextu systému uživatele pomocí nástroje PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Upravte nastavení serveru proxy v aplikaci Internet Explorer tak, aby obejila adresu IP centra.
3. Restartujte službu tmanssvc.

Pro konfiguraci proxy serveru DRA:

1. Otevřete příkazový řádek a otevřete složku Zprostředkovatel eloka webu Microsoft Azure.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Na příkazovém řádku spusťte následující příkaz.
   
   **DRKONFIGURÁTOR. EXE /configure /AddBypassUrls [IP Adresa/FQDN serveru vCenter, který je k dispozici v době přidání aplikace vCenter]**

4. Restartujte službu zprostředkovatele DRA.

## <a name="next-steps"></a>Další kroky

[Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
