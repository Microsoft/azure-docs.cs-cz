---
title: Spuštění virtuálního počítače Azure se zaseklo na webu Windows Update| Dokumenty společnosti Microsoft
description: Zjistěte, jak tento problém vyřešit, když se při aktualizaci Windows zaseklo spuštění virtuálního počítače Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919426"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Spuštění virtuálního počítače Azure se zaseklo na aktualizaci Windows

Tento článek pomáhá vyřešit problém, když váš virtuální počítač (VM) se zasekl ve fázi Windows Update při spuštění. 


## <a name="symptom"></a>Příznak

 Virtuální ho virtuálního mísa systému Windows se nespustí. Když zkontrolujete snímky obrazovky v okně [Diagnostika spouštění,](../troubleshooting/boot-diagnostics.md) uvidíte, že spuštění se zaseklo v procesu aktualizace. Následují příklady zpráv, které můžete obdržet:

- Instalace Systému Windows ##% Nevypírejte počítač. To bude chvíli trvat, váš počítač se několikrát restartuje
- Udržujte počítač zapnutý, dokud to nebude hotové. Instalace aktualizace # #... 
- Nepodařilo se nám dokončit aktualizace Zrušení změn Nevypínejte počítač
- Chyba konfigurace aktualizací systému Windows Vrácení změn Nevypíte počítač
- Chyba < kód chyby > použití operací aktualizace ##### ##### (\Regist...)
- Chybová chyba < > použití aktualizačních operací ##### ##### ($$...)


## <a name="solution"></a>Řešení

V závislosti na počtu aktualizací, které jsou stále nainstalovány nebo vráceny zálohování, proces aktualizace může chvíli trvat. Virtuální ho ponechte v tomto stavu po dobu 8 hodin. Pokud virtuální počítač je stále v tomto stavu po uplynutí této doby, restartujte virtuální počítač z portálu Azure a zjistěte, jestli se můžete normálně spustit. Pokud tento krok nefunguje, vyzkoušejte následující řešení.

### <a name="remove-the-update-that-causes-the-problem"></a>Odebrání aktualizace, která způsobuje problém

1. Pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md). 
2. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).
3. Jakmile je disk operačního systému připojen k virtuálnímu počítači pro obnovení, spusťte **diskmgmt.msc** a otevřete správu disků a ujistěte se, že připojený disk je **ONLINE**. Poznamenejte si písmeno jednotky, které je přiřazeno připojenému disku operačního systému, který obsahuje složku \windows. Pokud je disk zašifrován, dešifrujte disk před pokračováním v dalších krocích v tomto dokumentu.

4. Otevřete instanci příkazového řádku se zvýšenými oprávněními (Spustit jako správce). Spuštěním následujícího příkazu získáte seznam balíčků aktualizací, které jsou na připojeném disku operačního systému:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Pokud je například připojený disk operačního systému jednotka F, spusťte následující příkaz:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Otevřete soubor C:\temp\Patch_level.txt a přečtěte si jej zdola nahoru. Vyhledejte aktualizaci, která je ve stavu **Instalace čeká na vyřízení** nebo **Odinstalace čeká na vyřízení.**  Následuje ukázka stavu aktualizace:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Odeberte aktualizaci, která způsobila problém:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Příklad: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > V závislosti na velikosti balíčku bude chvíli trvat, než nástroj DISM zpracuje odinstalaci. Obvykle bude proces dokončen do 16 minut.

7. [Odpojte disk operačního systému a znovu vytvořte virtuální hod](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Potom zkontrolujte, zda je problém vyřešen.
