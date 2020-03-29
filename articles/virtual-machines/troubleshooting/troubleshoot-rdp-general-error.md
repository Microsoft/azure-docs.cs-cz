---
title: Poradce při potížích s obecnou chybou RDP u virtuálního počítače s Windows v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit obecné chyby RDP s virtuálním počítačem s Windows v Azure | Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058001"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Poradce při potížích s obecnou chybou RDP v virtuálním počítači Azure

Tento článek popisuje obecnou chybu, ke které může dojít při připojení protokolu RDP (RDP) k virtuálnímu počítači (VM) systému Windows v Azure.

## <a name="symptom"></a>Příznak

Když vytvoříte připojení RDP k virtuálnímu počítači okna v Azure, může se zobrazit následující obecná chybová zpráva:

**Vzdálená plocha se nemůže připojit ke vzdálenému počítači z jednoho z těchto důvodů:**

1. **Vzdálený přístup k serveru není povolen.**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není v síti k dispozici.**

**Zkontrolujte, zda je vzdálený počítač zapnutý a připojený k síti a zda je povolen vzdálený přístup.**

## <a name="cause"></a>Příčina

K tomuto problému může dojít z důvodu následujících příčin:

### <a name="cause-1"></a>Příčina 1

Součást RDP je zakázána následujícím způsobem:

- Na úrovni komponent
- Na úrovni posluchače
- Na terminálovém serveru
- V roli Hostitele relací vzdálené plochy

### <a name="cause-2"></a>Příčina 2

Služba Vzdálená plocha (TermService) není spuštěna.

### <a name="cause-3"></a>Příčina 3

Naslouchací proces PROTOKOLU RDP je nesprávně nakonfigurován.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, [zálohujte disk operačního systému](../windows/snapshot-copy-managed-disk.md)a [připojte disk operačního systému k záchrannému virtuálnímu počítače](troubleshoot-recovery-disks-portal-windows.md)a postupujte podle pokynů.

### <a name="serial-console"></a>Sériová konzola

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Krok 1: Otevření instance CMD v konzole Serial Console

1. Přístup k [konzole Sériové konzole](serial-console-windows.md) výběrem **možnosti Podpora & poradce při potížích se sériovým** > **konzolí (Preview)**. Pokud je tato funkce povolená na virtuálním počítači, můžete úspěšně připojit virtuální počítač.

2. Vytvořte nový kanál pro instanci CMD. Zadejte **CMD** pro spuštění kanálu, abyste získali název kanálu.

3. Přepněte na kanál, který spouštějící instanci CMD, v tomto případě by měl být kanál 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Krok 2: Zkontrolujte hodnoty klíčů registru RDP:

1. Zkontrolujte, zda je RDP zakázáno policií.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Pokud zásady domény existují, je přepsáno nastavení místní ch odsekanou.
      - Pokud zásady domény uvádějí, že je rdp zakázán (1), aktualizujte zásady služby AD z řadiče domény.
      - Pokud zásady domény uvádí, že rdp je povolena (0), pak není potřeba žádná aktualizace.
      - Pokud zásady domény neexistují a místní zásady uvádějí, že rdp je zakázáno (1), povolte prv pomocí následujícího příkazu: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Zkontrolujte aktuální konfiguraci terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Pokud příkaz vrátí hodnotu 0, je terminálový server zakázán. Poté povolte terminálový server následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Modul terminálového serveru je nastaven do režimu vypouštění, pokud je server na farmě terminálového serveru (RDS nebo Citrix). Zkontrolujte aktuální režim modulu terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Pokud příkaz vrátí hodnotu 1, je modul terminálového serveru nastaven do režimu vypouštění. Poté nastavte modul do pracovního režimu takto:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Zkontrolujte, zda se můžete připojit k terminálovému serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Pokud příkaz vrátí hodnotu 1, nemůžete se připojit k terminálovému serveru. Potom povolte připojení následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Zkontrolujte aktuální konfiguraci naslouchací proces PROTOKOLU RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Pokud příkaz vrátí hodnotu 0, je naslouchací proces PROTOKOLU RDP zakázán. Potom povolte naslouchací proces následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Zkontrolujte, zda se můžete připojit k naslouchací proces PROTOKOLU RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Pokud příkaz vrátí hodnotu 1, nelze se připojit k naslouchací proces protokolu RDP. Potom povolte připojení následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Restartujte virtuální počítač.

8. Ukončení instance CMD zadáním `exit`a podvěma stisknutím klávesy Enter stiskněte **klávesu Enter.**

9. Restartujte virtuální počítač `restart`zadáním a pak se připojte k virtuálnímu počítači.

Pokud k problému stále dochází, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolení služeb vzdálené plochy

Další informace najdete v [tématu Vzdálená plocha nezačíná na virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Obnovení naslouchací proces protokolu RDP

Další informace najdete v [tématu Vzdálená plocha odpojuje často v Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Oprava offline

#### <a name="step-1-turn-on-remote-desktop"></a>Krok 1: Zapnutí vzdálené plochy

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.
3. Zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému disku operačního systému.
4. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.
5. Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**). Spusťte následující skripty. V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro váš virtuální počítač.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Pokud je virtuální virtuální měna připojena k doméně, zkontrolujte následující klíč registru a zjistěte, zda existuje zásada skupiny, která zakáže protokol RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Pokud je tato hodnota klíče nastavena na hodnotu 1, znamená to, že zásada rdp je zakázána. Chcete-li povolit vzdálenou plochu prostřednictvím zásad skupiny zásad, změňte z řadiče domény následující zásady:

   
      **Konfigurace počítače\Zásady\Šablony pro správu:**

      Definice zásad\Součásti systému Windows\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Připojení\Umožnit uživatelům vzdálené připojení pomocí služby Vzdálená plocha
  
1. Odpojte disk od záchranného virtuálního počítače.
1. [Vytvořte nový virtuální virtuální počítače z disku](../windows/create-vm-specialized.md).

Pokud k problému stále dochází, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolení služeb vzdálené plochy

Další informace najdete v [tématu Vzdálená plocha nezačíná na virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Obnovení naslouchací proces protokolu RDP

Další informace najdete v [tématu Vzdálená plocha odpojuje často v Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
