---
title: Licenční server vzdálené plochy není k dispozici, když se připojujete k virtuálnímu počítači Azure | Microsoft Docs
description: Naučte se řešit potíže s chybami protokolu RDP, protože není k dispozici žádný licenční server vzdálené plochy | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 6b5864d40d2a4d8f8d6cf404df29f909a73f04e2
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832039"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Licenční server vzdálené plochy není k dispozici, když se připojíte k virtuálnímu počítači Azure.

Tento článek pomáhá vyřešit problém, když se nemůžete připojit k virtuálnímu počítači Azure, protože není k dispozici žádný licenční server vzdálené plochy pro poskytnutí licence.

## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači se můžete setkat s následujícími scénáři:

- Snímek obrazovky virtuálního počítače ukazuje, že operační systém je plně načtený a čeká na přihlašovací údaje.
- Při pokusu o vytvoření připojení protokolu RDP (Vzdálená plocha Microsoft Protocol) se zobrazí následující chybové zprávy:

  - Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy pro poskytnutí licence.

  - Není k dispozici žádný licenční server vzdálené plochy. Služba Vzdálená plocha přestane fungovat, protože tento počítač je po uplynutí období odkladu a nekontaktoval alespoň platný licenční server s Windows Serverem 2008. Výběrem této zprávy otevřete Hostitel relace VP konfigurace serveru pro použití diagnostiky licencování.

K virtuálnímu počítači se ale můžete připojit normálně pomocí relace pro správu:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud licenční server vzdálené plochy není k dispozici k poskytnutí licence ke spuštění vzdálené relace. Může to být způsobeno několika scénáři, i když se na virtuálním počítači nastavila Hostitel relace vzdálené plochy role:

- V prostředí neexistovala role Licencování vzdálené plochy a období odkladu, 180 dnů, je nad.
- V prostředí se nainstalovala licence na vzdálenou plochu, ale nikdy se neaktivuje.
- Licence na vzdálenou plochu v prostředí nemá pro nastavení připojení licence klientského přístupu (CAL).
- V prostředí se nainstalovala licence na vzdálenou plochu. K dispozici jsou licence CAL, které nejsou správně nakonfigurované.
- Licence na vzdálenou plochu má licence CAL a byla aktivována. Některé další problémy na licenčním serveru vzdálené plochy ale brání v poskytování licencí v prostředí.

## <a name="solution"></a>Řešení

Tento problém vyřešíte tak, [že zazálohujte disk s operačním systémem](../windows/snapshot-copy-managed-disk.md) a prodržíte tyto kroky:

1. Připojte se k virtuálnímu počítači pomocí relace pro správu:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Pokud se k virtuálnímu počítači nemůžete připojit pomocí relace pro správu, můžete k tomuto virtuálnímu [počítači použít sériovou konzolu virtuálních počítačů v Azure](serial-console-windows.md) , a to následujícím způsobem:

    1. Přístup ke konzole sériového portu výběrem možnosti **Podpora & řešení potíží**  >  **sériová konzola (Preview)**. Pokud je funkce na virtuálním počítači povolená, můžete virtuální počítač úspěšně připojit.

    2. Vytvoří nový kanál pro instanci CMD. Zadáním **příkazu cmd** spusťte kanál a získejte název kanálu.

    3. Přepněte na kanál, který spouští instanci CMD. V takovém případě by měl být kanál 1:

       ```
       ch -si 1
       ```

    4. Znovu vyberte **ENTER** a zadejte platné uživatelské jméno a heslo, místní nebo ID domény pro virtuální počítač.

2. Ověřte, jestli je na virtuálním počítači povolená role Hostitel relace vzdálené plochy. Pokud je role povolená, ujistěte se, že funguje správně. Otevřete instanci příkazového řádku se zvýšenými oprávněními a postupujte podle těchto kroků:

    1. Pomocí následujícího příkazu můžete ověřit stav role Hostitel relace vzdálené plochy:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Pokud tento příkaz vrátí hodnotu 0, znamená to, že je role zakázaná, a můžete přejít ke kroku 3.

    2. Pomocí následujícího příkazu ověřte zásady a proveďte jejich konfiguraci podle potřeby:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode

        reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Pokud je hodnota **LicensingMode** nastavená na jinou hodnotu než 4, na uživatele a pak nastavte hodnotu na 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Pokud hodnota **SpecifiedLicenseServers** neexistuje nebo obsahuje nesprávné informace o licenčním serveru, změňte ji takto:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Po provedení změn v registru restartujte virtuální počítač.

    4. Pokud licence CAL nemáte, odeberte Hostitel relace vzdálené plochy roli. Pak se protokol RDP nastaví zpět na normální. Povoluje jenom dvě souběžná připojení RDP k virtuálnímu počítači:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Pokud má virtuální počítač roli licencování vzdálené plochy a nepoužívá se, můžete tuto roli odebrat taky:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Ujistěte se, že se virtuální počítač může připojit k licenčnímu serveru vzdálené plochy. Můžete otestovat připojení k portu 135 mezi virtuálním počítačem a licenčním serverem: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Pokud v prostředí není žádný licenční server vzdálené plochy a potřebujete ho, můžete [nainstalovat službu role Licencování vzdálené plochy](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). Pak [nakonfigurujte licencování VP](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Pokud je licenční server vzdálené plochy nakonfigurovaný a v pořádku, ujistěte se, že je licenční server vzdálené plochy aktivovaný pomocí licencí CAL.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli problém vyřešit.
