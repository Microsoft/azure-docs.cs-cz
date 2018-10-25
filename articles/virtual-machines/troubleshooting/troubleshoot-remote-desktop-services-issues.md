---
title: Vzdálená plocha nespouští na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak při řešení problémů se vzdálenou plochou při připojení k virtuálnímu počítači | Dokumentace Microsoftu
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989178"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Vzdálená plocha nespouští na Virtuálním počítači Azure

Tento článek popisuje postup řešení potíží s připojení do virtuálního počítače (virtuální počítač Azure), když není při spuštění nebo selhání spuštění služby Vzdálená plocha (inicializace).

>[!NOTE]
>Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manager. Doporučujeme použít tento model pro nových nasazení namísto pomocí modelu nasazení classic.

## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači se vyskytnout následující scénáře:

- Snímek obrazovky virtuálního počítače ukazuje, že je operační systém plně načtený a čeká se přihlašovací údaje.
- Všechny aplikace na virtuálním počítači jsou dostupné a fungují podle očekávání.
- Virtuální počítač reaguje na připojení protokolu TCP na portu Microsoft protokolu RDP (Remote Desktop) (výchozí 3389).
- Zobrazí se výzva k zadání přihlašovacích údajů při pokusu o vytvoření připojení k protokolu RDP.

## <a name="cause"></a>Příčina

K tomuto problému dochází, protože na virtuálním počítači není spuštěná Vzdálená plocha. Příčiny mohou záviset na následující scénáře:

- Inicializace služby byl nastaven na **zakázané**.
- Služba inicializace padá nebo přestanou reagovat.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte jednu z následujících řešení nebo zkuste řešení jeden po druhém:

### <a name="solution-1-using-the-serial-console"></a>Řešení 1: Pomocí konzole sériového portu

1. Přístup [konzoly sériového portu](serial-console-windows.md) tak, že vyberete **podpora a řešení potíží** > **sériová konzola (Preview)**. Pokud je povolená funkce, na virtuálním počítači, může úspěšně připojit virtuální počítač.

2. Vytvořte nový kanál pro instanci CMD. Typ **CMD** spuštění kanálu se získat název kanálu.

3. Přepnout do kanálu spuštěnou instanci CMD, v tomto případě bude kanál 1.

   ```
   ch -si 1
   ```

4. Stisknutím klávesy **Enter** znovu a zadejte platné uživatelské jméno a heslo (místní nebo doménový ID) pro virtuální počítač.

5. Dotaz na stav služby inicializace.

   ```
   sc query TermService
   ```

6. Pokud se zobrazí stav služby **Zastaveno**, pokuste se spustit službu.

   ```
   sc start TermService
   ```

7. Dotazování na službu znovu, abyste měli jistotu, že je služba úspěšně spuštěna.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Řešení 2: Povolení služby pomocí virtuální počítač pro obnovení

[Zálohování disku s operačním systémem](../windows/snapshot-copy-managed-disk.md) a [připojení disku s operačním systémem k zachránit VM](../windows/troubleshoot-recovery-disks-portal.md). Potom spusťte instanci příkazového řádku se zvýšenými oprávněními a spustíte následující skripty na zachránit virtuálního počítače:

>[!NOTE]
>Předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit ji odpovídající hodnotou ve virtuálním počítači. Až to uděláte, odpojit disk od virtuální počítač pro obnovení a [znovu vytvořit svůj virtuální počítač](../windows/create-vm-specialized.md). Další informace o řešení, můžete použít **řešení 1** protože konzole sériového portu byla povolena.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
