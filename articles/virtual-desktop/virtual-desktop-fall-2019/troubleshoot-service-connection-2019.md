---
title: Poradce při potížích s virtuálním počítačem s Windows s připojením služby – Azure
description: Řešení problémů při nastavování připojení klienta v prostředí klienta virtuální plochy Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01aff34839cc7385834468a08f30696efe84561f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614769"
---
# <a name="windows-virtual-desktop-service-connections"></a>Připojení ke službě virtuální plochy Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../troubleshoot-service-connection.md).

Pomocí tohoto článku můžete vyřešit problémy s připojením klienta k virtuální ploše Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžete nám sdělit svůj názor a diskutovat o službě Virtual Desktop Windows s produktovým týmem a dalšími aktivními členy komunity na [technické komunitě pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Uživatel se připojuje, ale nic se nezobrazuje (bez kanálu).

Uživatel může spustit klienty vzdálené plochy a je schopen ho ověřit, ale uživatel nevidí žádné ikony v informačním kanálu webového zjišťování.

Pomocí tohoto příkazového řádku zkontrolujte, jestli se uživateli, který nahlásí problémy, přiřadí ke skupinám aplikací:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Potvrďte, že se uživatel přihlašuje se správnými přihlašovacími údaji.

Pokud je webový klient používán, zkontrolujte, zda nejsou k dispozici žádné problémy s přihlašovacími údaji v mezipaměti.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Virtuální počítače s Windows 10 Enterprise s více relacemi nereagují

Pokud virtuální počítač neodpovídá a nemůžete k němu přistupovat prostřednictvím protokolu RDP, budete ho muset vyřešit pomocí diagnostické funkce zkontrolováním stavu hostitele.

Chcete-li zjistit stav hostitele, spusťte tuto rutinu:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Pokud je `NoHeartBeat`stav hostitele, znamená to, že virtuální počítač neodpovídá a Agent nemůže komunikovat se službou Virtual Desktop systému Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Existuje několik věcí, pomocí kterých můžete opravit stav prezenčního signálu.

### <a name="update-fslogix"></a>Aktualizovat FSLogix

Pokud vaše FSLogix není aktuální, obzvláště pokud je to verze 2.9.7205.27375 of frxdrvvt. sys, může to způsobit zablokování. Nezapomeňte [aktualizovat FSLogix na nejnovější verzi](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Zakázat BgTaskRegistrationMaintenanceTask

Pokud aktualizace FSLogix nefunguje, může to být tím, že BiSrv komponenta vyčerpá systémové prostředky během týdenní úlohy údržby. Dočasně zakažte úlohu údržby tím, že zakážete BgTaskRegistrationMaintenanceTask pomocí jedné z těchto dvou metod:

- Přejděte do nabídky Start a vyhledejte **Plánovač úloh**. Přejděte do **knihovny** > **Plánovač úloh Microsoft** > **Windows** > **BrokerInfrastructure**. Vyhledejte úlohu s názvem **BgTaskRegistrationMaintenanceTask**. Když ho najdete, klikněte na něj pravým tlačítkem a v rozevírací nabídce vyberte **Zakázat** .
- Otevřete nabídku příkazového řádku jako správce a spusťte následující příkaz:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview-2019.md).
- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues-2019.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell-2019.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
