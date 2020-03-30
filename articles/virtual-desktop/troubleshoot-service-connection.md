---
title: Poradce při potížích s připojením služby Windows Virtual Desktop – Azure
description: Jak vyřešit problémy při nastavení připojení klientů v prostředí klienta Virtuální plocha systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127460"
---
# <a name="windows-virtual-desktop-service-connections"></a>Připojení služby Virtuální desktop systému Windows

Tento článek slouží k řešení problémů s připojením klienta Virtuální plocha systému Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžete nám poskytnout zpětnou vazbu a diskutovat o službě Windows Virtual Desktop Service s produktovým týmem a dalšími aktivními členy komunity v [komunitě Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Uživatel se připojí, ale nic se nezobrazí (bez zdroje)

Uživatel může spustit klienty vzdálené plochy a je schopen ověřit, ale uživatel nevidí žádné ikony ve zdroji zjišťování webu.

Pomocí tohoto příkazového řádku zkontrolujte, zda byl uživatel, který oznamoval problémy, přiřazen skupinám aplikací:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Zkontrolujte, zda se uživatel přihlašuje se správnými přihlašovacími údaji.

Pokud je webový klient používán, zkontrolujte, zda neexistují žádné problémy s pověřeními uložená v mezipaměti.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Virtuální počítače s Windows 10 Enterprise s více relacemi nereagují

Pokud virtuální počítač nereaguje a nemáte k němu přístup prostřednictvím rdp, budete ho muset vyřešit pomocí funkce diagnostiky kontrolou stavu hostitele.

Chcete-li zkontrolovat stav hostitele, spusťte tuto rutinu:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Pokud je `NoHeartBeat`stav hostitele , znamená to, že virtuální počítač neodpovídá a agent nemůže komunikovat se službou Windows Virtual Desktop.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Existuje několik věcí, které můžete udělat pro opravu stavu NoHeartBeat.

### <a name="update-fslogix"></a>Aktualizace FSLogix

Pokud váš FSLogix není aktuální, zejména pokud je to verze 2.9.7205.27375 frxdrvvt.sys, může to způsobit zablokování. Nezapomeňte [aktualizovat FSLogix na nejnovější verzi](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Zakázat bgtaskregistraceúdržbaúkol

Pokud aktualizace FSLogix nefunguje, problém může být, že komponenta BiSrv je vyčerpání systémových prostředků během týdenní úlohy údržby. Dočasně zakázat úlohu údržby zakázáním BgTaskRegistrationMaintenanceTask s jedním z těchto dvou metod:

- Přejděte do nabídky Start a vyhledejte **Plánovač úloh**. Přejděte do **knihovny plánovače** > úloh**Microsoft** > **Windows** > **BrokerInfrastructure**. Vyhledejte úkol s názvem **BgTaskRegistrationMaintenanceTask**. Až ho najdete, klikněte na něj pravým tlačítkem myši a v rozevírací nabídce vyberte **Zakázat.**
- Otevřete nabídku příkazového řádku jako správce a spusťte následující příkaz:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při vytváření fondu klientů a hostitelů v prostředí Virtuální plochy systému Windows najdete v [tématu Vytvoření klientského a hostitelského fondu](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů při používání PowerShellu s Windows Virtual Desktop najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
