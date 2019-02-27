---
title: Řešení potíží s navrácení služeb po obnovení do místní během zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje způsoby řešení potíží navrácení služeb po obnovení a opětovného nastavování ochrany při zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: a0707ad356e51a9e4d4f73c54085fe9736bb0752
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885295"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Řešení potíží se selháním zjišťování serveru vCenter

Tento článek pomůže při řešení problémů, ke kterým dochází z důvodu selhání rozpoznávání VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Jiné než číselné hodnoty ve vlastnosti maxSnapShots

Ve verzích před 9,20 vCenter odpojí při načtení jiné než číselné hodnoty pro vlastnost `snapshot.maxSnapShots` vlastnost na virtuálním počítači.

ID chyby 95126 je identifikován tento problém.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Řešení tohoto problému:

- Identifikujte virtuální počítač a nastavte hodnotu na číselnou hodnotu (virtuálního počítače upravit nastavení v systému vCenter).

Nebo

- Upgradujte konfigurační server na verzi 9.20 nebo novější.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problémy s konfigurací serveru proxy pro připojení k serveru vCenter

vCenter zjišťování respektuje systému výchozí nastavení proxy serveru nakonfigurovaná uživatelem systému. Služba DRA respektuje uživatelem zadané během instalace konfiguračního serveru pomocí sjednocené instalace Instalační služby systému nebo šablona OVA nastavení proxy serveru. 

Obecně platí proxy server slouží ke komunikaci k veřejným sítím; jako je například komunikace s Azure. Pokud je nakonfigurován server proxy a vCenter je v místním prostředí, nebude moci komunikovat s DRA.

Při výskytu tohoto problému se dojít k následující situace:

- VCenter server <vCenter> není dostupný, protože došlo k chybě: Vzdálený server vrátil chybu: (503) server není k dispozici
- VCenter server <vCenter> není dostupný, protože došlo k chybě: Vzdálený server vrátil chybu: Ke vzdálenému serveru se nelze připojit.
- Nelze se připojit k serveru vCenter/ESXi.

Řešení tohoto problému:

Stáhněte si [nástroj PsExec](https://aka.ms/PsExec). 

Pomocí nástroje PsExec pro přístup k systému uživatelský kontext a určit, jestli je nakonfigurovaná adresa proxy serveru. Pak může přidání vCenter do seznamu obcházení pomocí následujících postupů.

Pro konfiguraci zjišťování proxy serveru:

1. Otevřete aplikaci Internet Explorer v kontextu uživatele systému pomocí nástroje PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Upravte nastavení proxy serveru v aplikaci Internet Explorer obejít IP adresa serveru vCenter.
3. Restartujte službu tmanssvc.

DRA konfiguraci proxy serveru:

1. Otevřete příkazový řádek a otevřete složku Microsoft Azure Site Recovery Provider.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Z příkazového řádku spusťte následující příkaz.
   
   **DRCONFIGURATOR. Soubor EXE / configure /AddBypassUrls [IP adresa nebo plně kvalifikovaný název domény systému vCenter Server k dispozici v době přidání vCenter]**

4. Restartujte službu Zprostředkovatel DRA.

## <a name="next-steps"></a>Další postup

[Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
