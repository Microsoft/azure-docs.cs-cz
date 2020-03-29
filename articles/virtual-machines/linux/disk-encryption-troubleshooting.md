---
title: Poradce při potížích s šifrováním disku Azure pro virtuální počítače s Linuxem
description: Tento článek obsahuje tipy pro řešení potíží s microsoft azure šifrování disků pro virtuální počítače s Linuxem.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970491"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží s šifrováním disku Azure

Tato příručka je určen pro odborníky v oblasti IT, analytiky zabezpečení informací a správce cloudu, jejichž organizace používají Azure Disk Encryption. Tento článek vám pomůže s odstraňováním potíží souvisejících s šifrováním disku.

Před přijetím některého z následujících kroků nejprve ujistěte se, že virtuální počítače, které se pokoušíte zašifrovat, patří mezi [podporované velikosti virtuálních počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems)a že jste splnili všechny požadavky:

- [Další požadavky na virtuální chod](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na vytváření sítí](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Poradce při potížích s šifrováním disku operačního systému Linux

Šifrování disku operačního systému Linux (OS) musí před spuštěním jednotky operačního systému zrušit proces šifrování celého disku. Pokud se nemůže odpojit disk, chybová zpráva "se nepodařilo odpojit po ..." pravděpodobně dojde.

K této chybě může dojít při pokusu o šifrování disku operačního systému na virtuálním počítači s prostředím, které bylo změněno z podporované honosné galerie. Odchylky od podporovaného obrázku mohou narušit schopnost rozšíření odpojit jednotku operačního systému. Příklady odchylek mohou zahrnovat následující položky:
- Přizpůsobené bitové kopie již neodpovídají podporovanému systému souborů nebo schématu dělení.
- Velké aplikace jako SAP, MongoDB, Apache Cassandra a Docker nejsou podporovány, když jsou nainstalovány a spuštěny v ospřed šifrováním. Azure Disk Encryption nemůže tyto procesy bezpečně vypnout podle potřeby při přípravě jednotky operačního systému pro šifrování disku. Pokud stále existují aktivní procesy, které drží otevřené popisovače souborů na jednotce operačního systému, jednotku operačního systému nelze odpojit, což vede k selhání šifrování jednotky operačního systému. 
- Vlastní skripty, které běží v těsné blízkosti časové vzdálenosti k šifrování je povoleno, nebo pokud jsou prováděny jiné změny na virtuálním počítači během procesu šifrování. K tomuto konfliktu může dojít, když šablona Azure Resource Manager definuje více rozšíření pro současné spuštění nebo když se vlastní rozšíření skriptu nebo jiná akce spustí současně na šifrování disku. Serializace a izolace těchto kroků může problém vyřešit.
- Bezpečnostní Enhanced Linux (SELinux) nebyl zakázán před povolením šifrování, takže krok odpojit selže. SELinux lze znovu povolit po dokončení šifrování.
- Disk operačního systému používá schéma Správce logických svazků (LVM). Přestože je k dispozici omezená podpora datového disku LVM, disk operačního systému LVM není.
- Minimální požadavky na paměť nejsou splněny (7 GB je navrženo pro šifrování disku operačního systému).
- Datové jednotky jsou rekurzivně připojeny pod adresář /mnt/ nebo navzájem (například /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Aktualizace výchozího jádra pro Ubuntu 14.04 LTS

Obrázek Ubuntu 14.04 LTS je dodáván s výchozí verzí jádra 4.4. Tato verze jádra má známý problém, ve kterém out of Memory Killer nesprávně ukončí příkaz dd během procesu šifrování operačního systému. Tato chyba byla opravena v nejnovějším azure laděném linuxovém jádře. Chcete-li se této chybě vyhnout, před povolením šifrování na bitové kopii aktualizujte na [azure laděné jádro 4.15](https://packages.ubuntu.com/trusty/linux-azure) nebo novější pomocí následujících příkazů:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po restartování virtuálního virtuálního zařízení do nového jádra lze novou verzi jádra potvrdit pomocí:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualizace agenta virtuálních strojů Azure a rozšíření verze

Operace Azure Disk Encryption může selhat na image virtuálního počítače pomocí nepodporovaných verzí agenta virtuálních počítačů Azure. Linuxové image s verzemi agenta staršími než 2.2.38 by měly být před povolením šifrování aktualizovány. Další informace najdete v [tématu Jak aktualizovat Agenta Azure Linuxu na virtuálním počítači](../extensions/update-linux-agent.md) a [minimální verze podpory pro agenty virtuálních strojů v Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Je také vyžadována správná verze rozšíření agenta hosta Microsoft.Azure.Security.AzureDiskEncryption nebo Microsoft.Azure.Security.AzureDiskEncryptionForLinux. Verze rozšíření jsou udržovány a aktualizovány automaticky platformou, když jsou splněny předpoklady agenta virtuálního počítače Azure a používá se podporovaná verze agenta virtuálního počítače.

Rozšíření Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux bylo zastaralé a již není podporováno.  

## <a name="unable-to-encrypt-linux-disks"></a>Nelze šifrovat disky Linuxu.

V některých případech se zdá, že šifrování disku Linux uvízne na "Šifrování disku operačního systému spuštěno" a SSH je zakázán. Proces šifrování může trvat 3-16 hodin, než se dokončí na obrázku galerie akcií. Pokud jsou přidány datové disky o velikosti více terabajtů, může proces trvat několik dní.

Sekvence šifrování disku operačního systému Linux dočasně odpojí jednotku operačního systému. Poté provádí blok po bloku šifrování celého disku operačního systému, než jej znovu připojí v šifrovaném stavu. Šifrování disku linuxu neumožňuje souběžné použití virtuálního počítače v průběhu šifrování. Charakteristiky výkonu virtuálního soudu může mít významný vliv na čas potřebný k dokončení šifrování. Mezi tyto charakteristiky patří velikost disku a to, zda je účet úložiště standardní nebo premium (SSD) úložiště.

Chcete-li zkontrolovat stav šifrování, zosuňte pole **ProgressMessage** vrácené z příkazu [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Zatímco jednotka operačního systému je šifrována, virtuální ho důchek přejde do stavu obsluhy a zakáže SSH, aby se zabránilo narušení probíhajícího procesu. **Zpráva EncryptionInProgress** hlásí po většinu času během šifrování probíhá. O několik hodin později zpráva **VMRestartPending** vás vyzve k restartování virtuálního počítače. Například:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Po zobrazení výzvy k restartování virtuálního počítače a po restartování virtuálního počítače musíte počkat 2 až 3 minuty na restartování a na poslední kroky, které mají být provedeny na cíl. Stavová zpráva se změní po dokončení šifrování. Po této zprávě je k dispozici, šifrované jednotky operačního systému se očekává, že bude připraven k použití a virtuální ho virtuálního realitu je připraven k použití znovu.

V následujících případech doporučujeme obnovit virtuální ho znovu na snímek nebo zálohu pořízenou bezprostředně před šifrováním:
   - Pokud se sekvence restartování, popsané výše, nestane.
   - Pokud informace o spuštění, zpráva o průběhu nebo jiné indikátory chyb hlásí, že šifrování operačního systému se nezdařilo uprostřed tohoto procesu. Příkladem zprávy je chyba "nepodařilo se odpojit", která je popsána v této příručce.

Před dalším pokusem přehodnotit charakteristiky virtuálního virtuálního soudu a ujistěte se, že jsou splněny všechny požadavky.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Poradce při potížích s šifrováním disku Azure za bránou firewall

Viz [Šifrování disku v izolované síti](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Poradce při potížích se stavem šifrování 

Portál může zobrazit disk jako zašifrovaný i poté, co byl nezašifrován v rámci virtuálního počítače.  K tomu může dojít, když se příkazy nižší úrovně používají k přímému odšifrování disku z virtuálního počítače, namísto použití příkazů pro správu azure disk encryption vyšší úrovně.  Příkazy vyšší úrovně nejen dešifrují disk z virtuálního počítače, ale mimo virtuální počítače také aktualizují důležitá nastavení šifrování na úrovni platformy a nastavení rozšíření přidružená k virtuálnímu počítače.  Pokud tyto nejsou udržovány v zarovnání, platforma nebude moci hlásit stav šifrování nebo zřídit virtuální ho virtuálního média správně.   

Chcete-li zakázat šifrování disku Azure pomocí prostředí PowerShell, použijte [příkaz Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) následovaný [příkazem Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Spuštění remove-AzVMDiskEncryptionExtension před zakázáním šifrování se nezdaří.

Chcete-li zakázat šifrování disku Azure pomocí příkazového příkazu k příkazu příkazu cli, použijte [šifrování vaslužby az zakázat](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli více o některých běžných problémech v Azure Disk Encryption a jak tyto problémy vyřešit. Další informace o této službě a jejích možnostech naleznete v následujících článcích:

- [Použití šifrování disku v Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Šifrování dat Azure v klidovém stavu](../../security/fundamentals/encryption-atrest.md)
