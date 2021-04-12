---
title: Řešení potíží s Azure Disk Encryption pro virtuální počítače se systémem Linux
description: Tento článek popisuje tipy pro řešení potíží pro Microsoft Azure šifrování disku pro virtuální počítače se systémem Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563366"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Azure Disk Encryption pro virtuální počítače se systémem Linux

Tato příručka je určena pro odborníky v oblasti IT, analytiky zabezpečení informací a správce cloudu, jejichž organizace používají Azure Disk Encryption. V tomto článku se dozvíte, jak vyřešit problémy související s šifrováním disku.

Než začnete s některým z následujících kroků, zajistěte, aby virtuální počítače, které se pokoušíte zašifrovat, byly mezi [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems), a že jste splnili všechny požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Řešení potíží s nástrojem pro šifrování disku s operačním systémem Linux

Před spuštěním šifrování disku operačního systému Linux musí být disk s operačním systémem odpojený pomocí procesu šifrování celého disku. Pokud se jednotka nemůže odpojit, zobrazí se chybová zpráva oznamující, že se nepovedlo odpojit... se může vyskytnout.

K této chybě může dojít, když se na virtuálním počítači zkouší šifrování disku s operačním systémem a prostředí, které bylo změněno z podporované Image Galerie uložených v síti. Odchylky z podporované image můžou narušovat schopnost rozšíření odpojit jednotku s operačním systémem. Příklady odchylek mohou zahrnovat následující položky:
- Přizpůsobené image už neodpovídají podporovanému schématu systému souborů nebo vytváření oddílů.
- Velké aplikace jako SAP, MongoDB, Apache Cassandra a Docker nejsou podporované, když jsou nainstalované a spuštěné v operačním systému před šifrováním. Azure Disk Encryption nemůže tyto procesy bezpečně vypnout, jak je potřeba při přípravě jednotky operačního systému pro šifrování disku. Pokud stále existují aktivní procesy, které obsluhují otevřené popisovače souborů na jednotku operačního systému, nelze jednotku operačního systému odpojit, což způsobí selhání šifrování jednotky operačního systému. 
- Vlastní skripty, které se spouštějí v čase ukončení a jsou v blízkosti povoleného šifrování, nebo v případě, že se na virtuálním počítači během procesu šifrování provádějí nějaké jiné změny. K tomuto konfliktu může dojít, když šablona Azure Resource Manager definuje vícenásobné rozšíření pro spuštění současně nebo když se rozšíření vlastních skriptů nebo jiná akce spouští současně pro šifrování disku. Tento problém může vyřešit serializace a izolace těchto kroků.
- Systém SELinux (Security Enhanced Linux) nebyl před povolením šifrování zakázán, takže se krok odpojení nezdařil. SELinux lze po dokončení šifrování znovu povolit.
- Disk s operačním systémem používá schéma LVM (Logical Volume Manager). I když je k dispozici omezená podpora LVM datových disků, není disk s operačním systémem LVM.
- Minimální požadavky na paměť nejsou splněné (doporučujeme 7 GB pro šifrování disků s operačním systémem).
- Datové jednotky se rekurzivně připevní do adresáře/mnt/(například/mnt/DATA1,/mnt/Data2,/DATA3 +/DATA3/DATA4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Aktualizace výchozího jádra pro Ubuntu 14,04 LTS

Bitová kopie Ubuntu 14,04 LTS je dodávána s výchozí verzí jádra 4,4. Tato verze jádra má známý problém, kdy nedostatek paměti Killer nesprávně ukončuje příkaz dd během procesu šifrování operačního systému. Tato chyba byla opravena v nejnovějším jádru systému Linux pro Azure. Chcete-li se této chybě vyhnout, před povolením šifrování v imagi aktualizujte na [Azure vyladěné jádro 4,15](https://packages.ubuntu.com/trusty/linux-azure) nebo novější pomocí následujících příkazů:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po restartování virtuálního počítače do nového jádra se dá potvrdit nová verze jádra pomocí:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualizovat agenty a verze rozšíření virtuálních počítačů Azure

U imagí virtuálních počítačů, které používají nepodporované verze agenta virtuálního počítače Azure, se operace Azure Disk Encryption nemusí zdařit. Image Linux s verzemi agentů staršími než 2.2.38 by se měly aktualizovat před povolením šifrování. Další informace najdete v tématu [Postup aktualizace agenta Azure Linux na virtuálním](../extensions/update-linux-agent.md) počítači a [minimální verzi podpory pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Také je vyžadována správná verze rozšíření agenta hosta Microsoft. Azure. Security. AzureDiskEncryption nebo Microsoft. Azure. Security. AzureDiskEncryptionForLinux. Verze rozšíření se automaticky udržují a aktualizují platformou, když se splní požadavky na agenta virtuálních počítačů Azure a použije se podporovaná verze agenta virtuálního počítače.

Přípona Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux je zastaralá a už není podporovaná.  

## <a name="unable-to-encrypt-linux-disks"></a>Nejde zašifrovat disky se systémem Linux.

V některých případech se šifrování disku se systémem Linux jeví jako zablokované na "spuštěném šifrování disku s operačním systémem" a je zakázaný SSH. Proces šifrování může trvat 3-16 hodin, než se dokončí na image v galerii akcií. Pokud se přidají datové disky s více terabajty, může tento proces trvat několik dní.

Sekvence šifrování disku operačního systému Linux odpojí jednotku s operačním systémem dočasně. Pak provede blokové šifrování celého disku s operačním systémem, než ho znovu připojí v zašifrovaném stavu. Šifrování disku se systémem Linux nepovoluje souběžné použití virtuálního počítače, zatímco probíhá šifrování. Charakteristiky výkonu virtuálního počítače můžou v době potřebné k dokončení šifrování výrazně zatěžovat. Tyto vlastnosti zahrnují velikost disku a to, jestli je účet úložiště Standard nebo Premium (SSD).

Při šifrování jednotky operačního systému vstoupí virtuální počítač do stavu údržby a zakáže SSH, aby se zabránilo jakémukoli přerušení probíhajícího procesu.  Chcete-li zjistit stav šifrování, použijte příkaz Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) a podívejte se do pole **ProgressMessage** . **ProgressMessage** bude hlásit řadu stavů, jako jsou data a disky s operačním systémem šifrované:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage** zůstane pro většinu procesu šifrování **spuštěným šifrováním disku s operačním systémem** .  Až se šifrování dokončí a bude úspěšné, **ProgressMessage** vrátí:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Po tom, co je tato zpráva k dispozici, se očekává, že šifrovaná jednotka operačního systému bude připravena k použití a že je virtuální počítač připravený k opětovnému použití.

Pokud se v průběhu tohoto procesu nezdařila informace o spuštění, zpráva o průběhu nebo zpráva o chybách, které zakázaly šifrování operačního systému, obnovte tento virtuální počítač do snímku nebo zálohy těsně před šifrováním. Příkladem zprávy je chyba "nezdařené odpojení", která je popsaná v této příručce.

Než se znovu pokusíte o šifrování, znovu vyhodnoťte vlastnosti virtuálního počítače a ujistěte se, že jsou splněné všechny požadavky.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Řešení potíží s Azure Disk Encryption za bránou firewall

Viz [šifrování disků v izolované síti](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Řešení potíží se stavem šifrování 

Portál může zobrazit disk jako zašifrovaný, i když byl v rámci virtuálního počítače nešifrovaný.  K tomu může dojít, když se k přímému rozšifrování disku z virtuálního počítače používají příkazy nízké úrovně, místo abyste používali příkazy pro správu Azure Disk Encryption vyšší úrovně.  Příkazy vyšší úrovně nešifrují jenom disk v rámci virtuálního počítače, ale mimo virtuální počítač aktualizují důležitá nastavení šifrování na úrovni platformy a nastavení rozšíření přidružená k virtuálnímu počítači.  Pokud tyto možnosti nejsou zachovány, platforma nebude moci nahlásit stav šifrování ani zřídit virtuální počítač správně.

Pokud chcete Azure Disk Encryption zakázat pomocí prostředí PowerShell, použijte [příkaz disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) následovaný rutinou [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Spuštění Remove-AzVMDiskEncryptionExtension před vypnutím šifrování selže.

Pokud chcete zakázat Azure Disk Encryption pomocí rozhraní příkazového řádku, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption).

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli o některých běžných problémech v Azure Disk Encryption a o tom, jak tyto problémy řešit. Další informace o této službě a jejích funkcích najdete v následujících článcích:

- [Použít šifrování disku v Azure Security Center](../../security-center/asset-inventory.md)
- [Šifrování neaktivních uložených dat v Azure](../../security/fundamentals/encryption-atrest.md)