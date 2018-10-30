---
title: Řešení potíží s obecné chybě protokolu RDP k virtuálnímu počítači s Windows v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit chybu obecné RDP k virtuálnímu počítači s Windows v Azure | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 701373efa3c3c22eb5969705927e1c0cc6e3f36b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215783"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Řešení potíží s RDP obecné chybě ve virtuálním počítači Azure

Tento článek popisuje obecná chyba, které můžete narazit při vytvoření připojení protokolu RDP (Remote Desktop) pro Windows virtuální počítač (počítače) v Azure.

## <a name="symptoms"></a>Příznaky

Když vytvoříte připojení ke vzdálené ploše do okna virtuálního počítače v Azure, můžete obdržet následující obecná chybová zpráva:

**Vzdálené plochy nelze připojit ke vzdálenému počítači pro některou z těchto důvodů:**

1. **Není povolen vzdálený přístup k serveru**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není dostupný v síti**

**Ujistěte se, že vzdálený počítač je zapnutý a připojený k síti a zda je povolen vzdálený přístup.**

## <a name="cause"></a>Příčina

Tento problém může dojít z následujících důvodů:

### <a name="cause-1"></a>Příčiny 1

Komponenta RDP je zakázaný následujícím způsobem:

- Na úrovni komponent
- Na úrovni naslouchací proces
- Na terminálový server
- Role Hostitel relace vzdálené plochy

### <a name="cause-2"></a>Příčiny 2

Vzdálená plocha (inicializace) neběží.

### <a name="cause-3"></a>Příčina 3

Naslouchací proces RDP je špatně nakonfigurovaný.

## <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém [zálohování disku s operačním systémem](../windows/snapshot-copy-managed-disk.md), a [připojte disk s operačním systémem zachránit virtuálního počítače](troubleshoot-recovery-disks-portal-windows.md)a pak postupujte podle možnosti řešení odpovídajícím způsobem, nebo zkuste řešení jeden po druhém.

### <a name="serial-console"></a>Konzola sériového portu

#### <a name="step-1-turn-on-remote-desk"></a>Krok 1: Zapněte vzdálenou helpdesku

1. Přístup [konzoly sériového portu](serial-console-windows.md) tak, že vyberete **podpora a řešení potíží** > **sériová konzola (Preview)**. Pokud je povolená funkce, na virtuálním počítači, může úspěšně připojit virtuální počítač.

2. Vytvořte nový kanál pro instanci CMD. Typ **CMD** spuštění kanálu se získat název kanálu.

3. Přepnout do kanálu spuštěnou instanci CMD, v tomto případě bude kanál 1.

   ```
   ch -si 1
   ```

4. Povolení vzdálené plochy přes objekt zásad skupiny zásady tak, že změníte následující zásady:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

5. Některé další klíče registru, které by mohly způsobit potíže, zkontrolujte hodnoty klíčů registru takto:

   1. Ujistěte se, že je povolená součást RDP.

      ```
      REM Get the local policy
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      Pokud zásada domény existuje, přepíše instalaci na místní zásady.

         - Pokud zásada domény uvádí, že protokol RDP je zakázaný (1), pak aktualizace zásad AD.
         - Pokud zásada domény uvádí, že protokol RDP je povoleno (0), není zapotřebí žádná aktualizace.

      Pokud zásada domény neexistuje a místní zásady určují, že je zakázaný protokol RDP (1), povolte protokol RDP s použitím následujícího příkazu:

         ```
         reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
         ```

   2. Zkontrolujte aktuální konfiguraci terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

   3. Pokud příkaz vrátí hodnotu 0, je zakázané terminálového serveru. Terminálový server. potom povolte následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

   4. Modul Terminálový Server je nastaven na režimu vyprazdňování, pokud je server, v terminálu serverové farmy (vzdálené plochy nebo Citrix). Zkontrolujte aktuální režim modulu terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

   5. Pokud příkaz vrátí hodnotu 1, modul terminálového serveru nastavená na režim vyprazdňování. Potom nastavte modulu na pracovní režim následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

   6. Zkontrolujte, zda se mohou připojit k terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

   7. Pokud příkaz vrátí hodnotu 1, nemůžete připojit k terminálového serveru. Potom povolte připojení následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```

   8. Zkontrolujte aktuální konfiguraci naslouchacího procesu protokolu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

   9. Pokud příkaz vrátí hodnotu 0, naslouchací proces RDP je zakázaný. Naslouchací proces potom povolte následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

   10. Zkontrolujte, zda se můžete připojit k naslouchací proces RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   11. Pokud příkaz vrátí hodnotu 1, nemůže připojit k naslouchací proces RDP. Potom povolte připojení následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

6. Restartujte virtuální počítač.

7. Ukončení CMD instance zadáním `exit`a potom stiskněte klávesu **Enter** dvakrát.

8. Restartujte virtuální počítač tak, že zadáte `restart`.

Pokud problém stále dochází, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolení služby Vzdálená plocha

Další informace najdete v tématu [služby Vzdálená plocha nespouští na Virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetování naslouchací proces RDP

Další informace najdete v tématu [vzdálené plochy odpojí často ve virtuálním počítači Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offline oprava

#### <a name="step-1-turn-on-remote-desk"></a>Krok 1: Zapněte vzdálenou helpdesku

> [!NOTE]  
> Předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit ji odpovídající hodnotou ve virtuálním počítači. Podregistry systému a softwaru je potřeba odpojit a pak připojit.

1. Spusťte instanci příkazového řádku se zvýšenými oprávněními a spusťte následující skripty na zachránit virtuálního počítače:

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM.hiv
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE.hiv

   REM Ensure that Terminal Server is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f

   REM Ensure Terminal Service is not set to Drain mode
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f

   REM Ensure Terminal Service has logon enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f

   REM Ensure the RDP Listener is not disabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f

   REM Ensure the RDP Listener accepts logons
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f

   REM RDP component is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f

   reg unload HKLM\BROKENSYSTEM
   reg unload HKLM\BROKENSOFTWARE
   ```

2. Připojte podregistry systému a softwaru.

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE
   ```

3. Pokud je virtuální počítač k doméně, může být zakázáno RDP na úrovni zásad. Chcete-li ověřit, jestli je tento případ, zkontrolujte následující klíč registru:

   ```
   HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
   ```

4. Pokud tato hodnota klíče je nastavena na hodnotu 1, protokolu RDP je zakázána zásadami.

5. Povolení vzdálené plochy přes objekt zásad skupiny zásady tak, že změníte následující zásady:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

6. Pokud tento klíč registru neexistuje, zkontrolujte následující klíč registru:

   ```
   HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections
   ```

7. Pokud tento klíč nastavený na hodnotu 1, je RDP na řadě vy vypnuté. Změňte hodnotu klíče na hodnotu 0.
8. Odpojte disk od zachránit virtuálního počítače.
9. [Vytvořit nový virtuální počítač z disku](../windows/create-vm-specialized.md).

Pokud problém stále dochází, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolení služby Vzdálená plocha

Další informace najdete v tématu [služby Vzdálená plocha nespouští na Virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetování naslouchací proces RDP

Další informace najdete v tématu [vzdálené plochy odpojí často ve virtuálním počítači Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
