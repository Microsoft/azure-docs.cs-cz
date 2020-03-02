---
title: Řešení potíží – Azure Disk Encryption pro virtuální počítače se systémem Linux
description: Tento článek popisuje tipy pro řešení potíží pro Microsoft Azure šifrování disku pro virtuální počítače se systémem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: dd21b6520dc68a6f7faa5500054b2865556e3dfb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205904"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží Azure Disk Encryption

Tento průvodce je pro odborníky v oblasti IT, analytikům zabezpečení informací a správcům cloudů, jejichž organizace používat Azure Disk Encryption. Tento článek je pomoci při řešení problémů souvisejících s disk encryption.

Než začnete s některým z následujících kroků, zajistěte, aby virtuální počítače, které se pokoušíte zašifrovat, byly mezi [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems), a že jste splnili všechny požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Řešení potíží s šifrování disku operačního systému Linux

Šifrování disku operačního systému (OS) Linux musí před spuštěním procesu šifrování celého disku odpojit jednotku operačního systému. Pokud ho nelze odpojit jednotku, chybová zpráva o "se nepodařilo odpojit po..." by mohla nastat.

K této chybě může dojít, když se na virtuálním počítači zkouší šifrování disku s operačním systémem a prostředí, které bylo změněno z podporované Image Galerie uložených v síti. Odchylky od podporované image může narušovat tohoto rozšíření možnost odpojit jednotku operačního systému. Příklady odchylky může obsahovat následující položky:
- Přizpůsobené Image už neodpovídá podporovaném systému souborů nebo schéma vytváření oddílů.
- Velké aplikace, jako je například SAP, MongoDB, Apache Cassandra a Docker nejsou podporovány, když budou nainstalovány a spuštěny v operačním systému před šifrování. Azure Disk Encryption se nepodařilo vypnout tyto procesy bezpečně podle potřeby při přípravě jednotky operačního systému pro šifrování disku. Pokud stále aktivní procesy, které drží otevřené popisovače souborů na jednotce operačního systému, jednotka operačního systému nemůže být odpojeny, což vede k selhání při šifrování jednotky operačního systému. 
- Vlastní skripty, která spustí v blízkosti čas uzavření šifrování povolené, nebo pokud jakýkoli jiný nemění na virtuálním počítači během procesu šifrování. Tomuto konfliktu může dojít, pokud šablonu Azure Resource Manager definuje několik rozšíření současného provádění nebo pokud se rozšíření vlastních skriptů nebo jiné akce je spouštěný souběžně k šifrování disku. Serializace a izolaci těchto kroků mohou problém vyřešit.
- Před povolením šifrování, tak selhání kroku odpojení bylo nezakázala Linux rozšířeného zabezpečení (SELinux). Po dokončení šifrování může SELinux opětovně povolena.
- Disk s operačním systémem používá schéma Správce logických svazků (LVM). I když omezenou podporu pro disky data LVM je k dispozici, není disk s operačním systémem LVM.
- Nejsou splněny požadavky na minimální velikost paměti (7 GB je určeno pro šifrování disku operačního systému).
- Datové jednotky jsou rekurzivně připojený v adresáři /mnt/ nebo mezi sebou (například /mnt/data1, /mnt/data2, /data3 + /data3/data4).

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

V některých případech je zakázána Linux šifrování disku se zdá být zaseknuto v "Zahájeno šifrování disku operačního systému" a SSH. Proces šifrování může trvat přibližně 3 – 16 hodin na základní Galerie obrázků. Pokud přidáte více terabajt velikosti datových disků, tento proces může trvat dny.

Pořadí šifrování disku operačního systému Linux dočasně odpojí jednotky operačního systému. Potom provede blok po bloku šifrování celého disku s operačním systémem, než ji opět připojí ji ve stavu zašifrované. Šifrování disku se systémem Linux nepovoluje souběžné použití virtuálního počítače, zatímco probíhá šifrování. Výkonové charakteristiky virtuálního počítače může být velký rozdíl čas potřebný k dokončení šifrování. Tyto vlastnosti obsahovat velikost disku a zda je standardní účet úložiště nebo úložiště úrovně premium (SSD).

Chcete-li zjistit stav šifrování, proveďte dotaz na pole **ProgressMessage** , které se vrátí z příkazu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Při šifrované jednotky operačního systému virtuálního počítače přejde do stavu údržby a zakáže abychom zabránili případnému přerušení k aktuálnímu procesu SSH. Zpráva **EncryptionInProgress** zprávy pro většinu času, kdy probíhá šifrování. Po několika hodinách později se zobrazí zpráva **VMRestartPending** s výzvou k restartování virtuálního počítače. Příklad:


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

Poté, co se zobrazí výzva k restartování virtuálního počítače a po restartování virtuálního počítače, musíte počkat, 2 až 3 minuty, pro restartování a poslední postup provést v cíli. Zpráva stav se změní při šifrování je nakonec dokončí. Poté, co tato zpráva je k dispozici, až bude připravená k použití se očekává šifrované jednotky operačního systému a virtuálního počítače je připravená k použití znovu.

V těchto případech doporučujeme obnovení virtuálního počítače do snímku nebo zálohy provedené bezprostředně před šifrování:
   - Pokud tomu tak není pořadí restartování, je popsáno výše.
   - Pokud se informace o spuštění, zpráva o průběhu nebo jiných ukazatelů výkonu chyba hlásit šifrování operačního systému se nezdařilo během tohoto procesu. Příklad zprávy se chyba "se nepodařilo odpojit", který je popsaný v tomto průvodci.

Před dalším pokusem přehodnotit vlastnosti virtuálního počítače a ujistěte se, že jsou splněné všechny požadavky.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Řešení potíží s Azure Disk Encryption za bránou firewall

Viz [šifrování disků v izolované síti](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Řešení potíží se stavem šifrování 

Portál může zobrazit disk jako zašifrovaný, i když byl v rámci virtuálního počítače nešifrovaný.  K tomu může dojít, když se k přímému rozšifrování disku z virtuálního počítače používají příkazy nízké úrovně, místo abyste používali příkazy pro správu Azure Disk Encryption vyšší úrovně.  Příkazy vyšší úrovně nešifrují jenom disk v rámci virtuálního počítače, ale mimo virtuální počítač aktualizují důležitá nastavení šifrování na úrovni platformy a nastavení rozšíření přidružená k virtuálnímu počítači.  Pokud tyto možnosti nejsou zachovány, platforma nebude moci nahlásit stav šifrování ani zřídit virtuální počítač správně.   

Pokud chcete Azure Disk Encryption zakázat pomocí prostředí PowerShell, použijte [příkaz disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) následovaný rutinou [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Spuštění Remove-AzVMDiskEncryptionExtension před zakázáním šifrování selže.

Pokud chcete zakázat Azure Disk Encryption pomocí rozhraní příkazového řádku, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli informace o některé běžné problémy v Azure Disk Encryption a řešení těchto potíží. Další informace o této službě a její možnosti najdete v následujících článcích:

- [Použít šifrování disku v Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Šifrování dat Azure v klidovém umístění](../../security/fundamentals/encryption-atrest.md)
