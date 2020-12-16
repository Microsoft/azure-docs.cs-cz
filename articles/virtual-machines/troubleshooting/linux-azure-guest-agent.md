---
title: Řešení potíží s agentem Azure Linux
description: Vyřešte problémy s agentem Azure Linux.
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
ms.openlocfilehash: 247324c30bbe0edaef78c0b0d5e6a6d593e8cac9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586393"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Řešení potíží s agentem Azure Linux

[Agent Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) umožňuje virtuálnímu počítači komunikovat s řadičem prostředků infrastruktury (základní fyzický server, na kterém je virtuální počítač hostovaný) na IP adrese 168.63.129.16.

>[!NOTE]
>Tato IP adresa je virtuální veřejná IP adresa, která usnadňuje komunikaci a neměla by být blokovaná. Další informace najdete v tématu [co je IP adresa 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Před zahájením

Zkontrolujte stav agenta a jeho verzi, abyste se ujistili, že je stále podporovaná. Podívejte se na podporu [Minimální verze pro agenty virtuálních počítačů v Azure](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version) , abyste zkontrolovali podporu verzí, nebo si Projděte [WALinuxAgent Nejčastější dotazy](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) , kde najdete postup pro vyhledání stavu a verze.

## <a name="troubleshoot-a-not-ready-status"></a>Řešení potíží se stavem Nepřipraveno

1. Zkontrolujte stav služby agenta Azure Linux a ujistěte se, že je spuštěný. Název služby může být **walinuxagent** nebo **waagent**.

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

   Pokud je služba spuštěná, restartujte ji a problém vyřešte. Pokud je služba zastavená, spusťte ji, počkejte pár minut a pak znovu zkontrolujte stav.

1. Ujistěte se, že je povolená Automatická aktualizace. V **/etc/waagent.conf** ověřte nastavení automatické aktualizace.

   ```
   AutoUpdate.Enabled=y
   ```

   Další informace o tom, jak aktualizovat agenta Azure Linux, najdete v tématu [Postup aktualizace agenta Azure Linux na virtuálním počítači](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent).

1. Ujistěte se, že se virtuální počítač může připojit k řadiči prostředků infrastruktury. Pomocí nástroje, jako je například kudrlinkou, otestujte, jestli se virtuální počítač může připojit k 168.63.129.16 na portech 80, 443 a 32526. Pokud se virtuální počítač nepřipojí podle očekávání, ověřte, jestli je v místní bráně firewall na VIRTUÁLNÍm počítači otevřená odchozí komunikace přes porty 80, 443 a 32526. Pokud je tato IP adresa zablokovaná, může se agent virtuálního počítače zobrazit neočekávané chování.

## <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Události pro řešení potíží s agentem Azure Linux se zaznamenávají do souboru **/var/log/waagent.log** .

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nepovedlo se připojit k IP adrese WireServer (IP adresa hostitele).

V souboru **/var/log/waagent.log** se zobrazí následující chyba, když se virtuální počítač nemůže připojit k IP adrese WireServer na hostitelském serveru.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Řešení tohoto problému:

* Připojte se k virtuálnímu počítači pomocí SSH a potom se pokuste získat přístup k následující adrese URL ze složené aplikace: http://168.63.129.16/?comp=versions .
* Vyhledejte všechny problémy, které by mohly být způsobené bránou firewall, proxy serverem nebo jiným zdrojem, který by mohl blokovat přístup k IP adrese 168.63.129.16.
* Ověřte, jestli Linux softwaru iptables nebo brána firewall jiného výrobce blokuje přístup k portům 80, 443 a 32526.

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit potíže s agenty Azure Linux, obraťte se na [podporu Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
