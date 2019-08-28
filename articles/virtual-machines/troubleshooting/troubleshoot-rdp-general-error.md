---
title: Řešení obecné chyby protokolu RDP na virtuální počítač s Windows v Azure | Microsoft Docs
description: Přečtěte si, jak řešit obecnou chybu protokolu RDP na virtuální počítač s Windows v Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: abff12e1a304c51cb0df394534c7da0a35518008
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089800"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Řešení obecné chyby protokolu RDP na virtuálním počítači Azure

Tento článek popisuje obecnou chybu, ke které může dojít při vytváření připojení protokol RDP (Remote Desktop Protocol) (RDP) k virtuálnímu počítači s Windows (VM) v Azure.

## <a name="symptom"></a>Příznak

Když provedete připojení RDP k virtuálnímu počítači s Windows v Azure, může se zobrazit následující obecná chybová zpráva:

**Vzdálené plochy nelze připojit ke vzdálenému počítači pro některou z těchto důvodů:**

1. **Není povolen vzdálený přístup k serveru**

2. **Vzdálený počítač je vypnutý.**

3. **Vzdálený počítač není dostupný v síti**

**Ujistěte se, že vzdálený počítač je zapnutý a připojený k síti a zda je povolen vzdálený přístup.**

## <a name="cause"></a>Příčina

K tomuto problému může dojít z důvodu následujících příčin:

### <a name="cause-1"></a>Příčiny 1

Komponenta RDP je zakázaná následujícím způsobem:

- Na úrovni součásti
- Na úrovni naslouchacího procesu
- Na terminálovém serveru
- Role Hostitel relace vzdálené plochy

### <a name="cause-2"></a>Příčiny 2

Služba Vzdálená plocha (TermService) není spuštěná.

### <a name="cause-3"></a>Příčina 3

Naslouchací proces RDP je nesprávně nakonfigurovaný.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, zazálohujte [disk operačního systému](../windows/snapshot-copy-managed-disk.md)a [Připojte disk s operačním systémem k záchrannému virtuálnímu počítači](troubleshoot-recovery-disks-portal-windows.md)a pak postupujte podle pokynů.

### <a name="serial-console"></a>Sériová konzola

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Krok 1: Otevřít instanci CMD v Sériová konzola

1. Přístup ke [konzole sériového portu](serial-console-windows.md) výběrem možnosti **Podpora & řešení potíží** > **sériová konzola (Preview)** . Pokud je funkce na virtuálním počítači povolená, můžete virtuální počítač úspěšně připojit.

2. Vytvoří nový kanál pro instanci CMD. Zadáním **příkazu cmd** spusťte kanál a získejte název kanálu.

3. Přepněte na kanál, ve kterém je spuštěná instance CMD, v tomto případě by měl být kanál 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Krok 2: Ověřte hodnoty klíčů registru RDP:

1. Ověřte, jestli jsou zásady zakázané protokolem RDP zakázané.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Pokud zásady domény existují, nastavení místní zásady se přepíše.
      - Pokud zásada domény uvádí, že je zakázaný protokol RDP (1), aktualizujte zásady služby AD z řadiče domény.
      - Pokud zásada domény uvádí, že je povolený protokol RDP (0), není potřeba žádná aktualizace.
      - Pokud zásady domény neexistují a místní zásady mají stav zakázáno RDP (1), povolte protokol RDP pomocí následujícího příkazu: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Ověřte aktuální konfiguraci terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Pokud příkaz vrátí hodnotu 0, je terminálový server zakázán. Potom povolte Terminálový Server následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Pokud je server v serverové farmě Terminálové služby (RDS nebo Citrix), je modul terminálového serveru nastaven na režim vyprázdnění. Ověřte aktuální režim modulu terminálového serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Pokud příkaz vrátí hodnotu 1, modul terminálového serveru je nastaven na režim vyprázdnění. Potom nastavte modul na pracovní režim následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Ověřte, zda se můžete připojit k terminálovému serveru.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Pokud příkaz vrátí hodnotu 1, nemůžete se připojit k terminálovému serveru. Pak připojení povolte následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Ověřte aktuální konfiguraci naslouchacího procesu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Pokud příkaz vrátí hodnotu 0, naslouchací proces protokolu RDP je zakázán. Potom povolte naslouchací proces následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Ověřte, zda se můžete připojit ke naslouchacího procesu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Pokud příkaz vrátí hodnotu 1, nemůžete se připojit k naslouchacímu procesu RDP. Pak připojení povolte následujícím způsobem:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Restartujte virtuální počítač.

8. Ukončete instanci cmd zadáním `exit`a potom stiskněte dvakrát klávesu **ENTER** .

9. Restartujte virtuální počítač zadáním `restart`a pak se připojte k virtuálnímu počítači.

Pokud problém přetrvává, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolit službu Vzdálená plocha

Další informace najdete v tématu [Vzdálená plocha se nespouští na virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetovat naslouchací proces protokolu RDP

Další informace najdete v tématu [Časté připojení vzdálené plochy na virtuálním počítači Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Oprava offline

#### <a name="step-1-turn-on-remote-desktop"></a>Krok 1: Zapnout vzdálenou plochu

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.
3. Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
4. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.
5. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**). Spusťte následující skripty. V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.

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

6. Pokud je virtuální počítač připojený k doméně, podívejte se do následujícího klíče registru, kde zjistíte, jestli existuje zásada skupiny, která zakáže protokol RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Pokud je tato hodnota klíče nastavená na hodnotu 1, znamená to, že zásada zakáže protokol RDP. Pokud chcete povolit vzdálenou plochu prostřednictvím zásad objektu zásad skupiny, změňte následující zásady z řadiče domény:

   
      **Šablony počítače \ \ šablony:**

      Zásada definitions\Windows, Vzdálená plocha relace Host\Connections\Allow uživatelé pro připojení vzdáleně pomocí služby Vzdálená plocha
  
1. Odpojte disk od záchranného virtuálního počítače.
1. [Vytvořte nový virtuální počítač z disku](../windows/create-vm-specialized.md).

Pokud problém přetrvává, přejděte ke kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Povolit službu Vzdálená plocha

Další informace najdete v tématu [Vzdálená plocha se nespouští na virtuálním počítači Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetovat naslouchací proces protokolu RDP

Další informace najdete v tématu [Časté připojení vzdálené plochy na virtuálním počítači Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktujte podporu

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.
