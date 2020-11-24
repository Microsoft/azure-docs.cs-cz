---
title: Řešení potíží s agentem hosta v Azure Linux
description: Řešení potíží s agentem hosta v systému Azure Linux nepracuje s problémy
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549928"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Řešení potíží s agentem hosta v Azure Linux

[Agent hosta Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) je agent virtuálního počítače (VM). Umožňuje virtuálnímu počítači komunikovat s řadičem prostředků infrastruktury (základní fyzický server, na kterém je hostovaný virtuální počítač), na IP adrese 168.63.129.16. Tato IP adresa je virtuální veřejná IP adresa, která usnadňuje komunikaci. Další informace najdete v tématu [co je IP adresa 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Kontroluje se stav a verze agenta.

Viz https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Řešení potíží s agentem virtuálního počítače, který není ve stavu připraveno

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Krok 1 zkontroluje, jestli je spuštěná služba agenta hosta Azure Linux.

V závislosti na distribuce může být název služby walinuxagent nebo waagent:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Pokud vidíte služby a jsou spuštěné, restartujte službu a zjistěte, jestli je problém vyřešený. Pokud jsou služby zastaveny, spusťte je a počkejte několik minut. Potom zkontrolujte, zda je **Stav agenta** hlášen jako **připravený**. Pokud chcete tento problém vyřešit, obraťte se na [Podpora Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Krok 2: ověření, jestli je povolená Automatická aktualizace

Toto nastavení najdete v/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Další informace o tom, jak aktualizovat agenta Azure Linux, najdete v tématu. https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Krok 3 – ověření, jestli se virtuální počítač může připojit k řadiči prostředků infrastruktury

Pomocí nástroje, jako je například kudrlinkou, otestujte, jestli se virtuální počítač může připojit k 168.63.129.16 na portech 80, 32526 a 443. Pokud se virtuální počítač nepřipojí podle očekávání, ověřte, jestli je v místní bráně firewall na VIRTUÁLNÍm počítači otevřená odchozí komunikace přes porty 80, 443 a 32526. Pokud je tato IP adresa zablokovaná, agent virtuálního počítače může v různých scénářích vykazovat neočekávané chování.

## <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Události pro řešení potíží s agentem hosta v Azure Linux se zaznamenávají do následujících souborů protokolu:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nepovedlo se připojit k IP adrese WireServer (IP adresa hostitele). 

Všimněte si následujících záznamů o chybách v/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Výsledcích**

Virtuální počítač se nemůže připojit k IP adrese WireServer na hostitelském serveru.

**Řešení**

1. Vzhledem k tomu, že IP adresa WireServer není dosažitelná, připojte se k virtuálnímu počítači pomocí SSH a potom se pokuste získat přístup k následující adrese URL ze sady kudrlinkou: http://168.63.129.16/?comp=versions 
1. Vyhledejte všechny problémy, které by mohly být způsobeny bránou firewall, proxy serverem nebo jiným zdrojem, které by mohly blokovat přístup k IP adrese 168.63.129.16.
1. Ověřte, jestli Linux softwaru iptables nebo brána firewall jiného výrobce blokuje přístup k portům 80, 443 a 32526. Další informace o tom, proč by se tato adresa neměla zablokovat, najdete v tématu [co je IP adresa 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Další kroky

Pokud chcete dále řešit potíže s "agentem hosta Windows Azure nefunguje", obraťte se na [podporu Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
