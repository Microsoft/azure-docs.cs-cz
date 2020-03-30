---
title: Licenční server Vzdálené plochy není po připojení k virtuálnímu počítači Azure k dispozici | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy se selháním programu RDP, protože není k dispozici žádný licenční server vzdálené plochy | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088519"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Licenční server vzdálené plochy není k dispozici, když se připojíte k virtuálnímu počítači Azure

Tento článek pomáhá vyřešit problém, když se nemůžete připojit k virtuálnímu počítači Azure (VM), protože k dispozici není žádný licenční server vzdálené plochy, který by poskytoval licenci.

## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači (VM) narazíte na následující scénáře:

- Snímek obrazovky virtuálního aplikace ukazuje, že operační systém je plně načten a čeká na pověření.
- Při pokusu o vytvoření připojení protokolu RDP (Microsoft Remote Desktop Protocol) se zobrazí následující chybové zprávy:

  - Vzdálená relace byla odpojena, protože k dispozici nejsou žádné licenční servery vzdálené plochy, které by poskytovaly licenci.

  - Není k dispozici žádný licenční server vzdálené plochy. Služba Vzdálená plocha přestane fungovat, protože tento počítač uplynul po uplynutí doby odkladu a nekontaktoval alespoň platný licenční server systému Windows Server 2008. Tuto zprávu vyberte, chcete-li otevřít konfiguraci hostitelského serveru relací VP, abyste použili diagnostiku licencování.

K virtuálnímu virtuálnímu virtuálnímu zařízení se však můžete normálně připojit pomocí relace správy:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Příčina

K tomuto problému dochází, pokud licenční server vzdálené plochy není k dispozici pro poskytnutí licence ke spuštění vzdálené relace. Může to být způsobeno několika scénáři, i když role hostitele relací vzdálené plochy byla nastavena na virtuálním počítači:

- V prostředí nikdy neexistovala licenční role vzdálené plochy a doba odkladu, 180 dní, je u konce.
- V prostředí byla nainstalována licence vzdálené plochy, ale nikdy se neaktivovala.
- Licence vzdálené plochy v prostředí nemá vstřikované licence klientského přístupu (CALs) pro nastavení připojení.
- V prostředí byla nainstalována licence vzdálené plochy. K dispozici jsou cal, ale nebyly správně nakonfigurovány.
- Licence vzdálené plochy obsahuje licence CAL a byla aktivována. Některé další problémy na licenčním serveru Vzdálené plochy mu však brání v poskytování licencí v prostředí.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, [zálohujte disk operačního systému](../windows/snapshot-copy-managed-disk.md) a postupujte takto:

1. Připojte se k virtuálnímu virtuálnímu virtuálnímu zařízení pomocí relace správy:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Pokud se nemůžete připojit k virtuálnímu počítači pomocí relace správy, můžete použít [konzolu Serial Console virtuálního počítače](serial-console-windows.md) v Azure pro přístup k virtuálnímu počítači následujícím způsobem:

    1. Přístup k konzole Sériové konzole výběrem **možnosti Podpora & poradce při potížích se sériovým** > **konzolí (Preview)**. Pokud je tato funkce povolená na virtuálním počítači, můžete úspěšně připojit virtuální počítač.

    2. Vytvořte nový kanál pro instanci CMD. Zadejte **CMD** pro spuštění kanálu a získání názvu kanálu.

    3. Přepněte na kanál, na který běží instance CMD. V tomto případě by měl být kanál 1:

       ```
       ch -si 1
       ```

    4. **Vyberte** zadat znovu a zadejte platné uživatelské jméno a heslo, místní nebo doménové ID, pro virtuální počítače.

2. Zkontrolujte, jestli má virtuální počítač povolenou roli hostitele relací vzdálené plochy. Pokud je role povolena, ujistěte se, že funguje správně. Otevřete instanci CMD se zvýšenými oprávněními a postupujte takto:

    1. Pomocí následujícího příkazu zkontrolujte stav role Hostitele relací vzdálené plochy:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Pokud tento příkaz vrátí hodnotu 0, znamená to, že role je zakázána a můžete přejít ke kroku 3.

    2. Pomocí následujícího příkazu zkontrolujte zásady a podle potřeby překonfigurujte:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Pokud **LicensingMode** hodnota je nastavena na jinou hodnotu než 4, na uživatele, nastavte hodnotu na 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Pokud hodnota **SpecifiedLicenseServers** neexistuje nebo obsahuje nesprávné informace o licenčním serveru, změňte ji následujícím způsobem:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Po provedené změny v registru restartujte virtuální počítač.

    4. Pokud nemáte názvy CAL, odeberte roli Hostitele relací vzdálené plochy. Pak bude PRV nastavenzpět k normálu. Umožňuje jenom dvě souběžná připojení RDP k virtuálnímu virtuálnímu virtuálnímu soudu:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Pokud má virtuální počítač roli licencování vzdálené plochy a nepoužívá se, můžete ji také odebrat:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Ujistěte se, že se virtuální počítač může připojit k licenčnímu serveru Vzdálené plochy. Připojení k portu 135 mezi virtuálním virtuálním serverem a licenčním serverem můžete otestovat: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Pokud v prostředí není žádný licenční server vzdálené plochy a chcete jej, můžete [nainstalovat službu role licencování vzdálené plochy](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Poté [nakonfigurujte licenci služby RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Pokud je licenční server vzdálené plochy nakonfigurován a v pořádku, zkontrolujte, zda je licenční server Vzdálené plochy aktivován pomocí licencí CAL.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) abyste problém vyřešili.
