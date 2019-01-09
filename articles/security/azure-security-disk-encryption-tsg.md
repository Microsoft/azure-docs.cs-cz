---
title: Řešení potíží – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek poskytuje tipy pro řešení potíží pro Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 01/08/2018
ms.custom: seodec18
ms.openlocfilehash: 36ecfe8942d263ed84e430b01727743ed2cad00c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103161"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží Azure Disk Encryption

Tento průvodce je pro odborníky v oblasti IT, analytikům zabezpečení informací a správcům cloudů, jejichž organizace používat Azure Disk Encryption. Tento článek je pomoci při řešení problémů souvisejících s disk encryption.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Řešení potíží s šifrování disku operačního systému Linux

Šifrování disku operačního systému (OS) Linux musí před spuštěním procesu šifrování celého disku odpojit jednotku operačního systému. Pokud ho nelze odpojit jednotku, chybová zpráva o "se nepodařilo odpojit po..." by mohla nastat.

Této chybě může dojít, když zkusí se šifrování disku operačního systému v prostředí cílového virtuálního počítače, který se změnil z podporovaných uložených Galerie obrázků. Odchylky od podporované image může narušovat tohoto rozšíření možnost odpojit jednotku operačního systému. Příklady odchylky může obsahovat následující položky:
- Přizpůsobené Image už neodpovídá podporovaném systému souborů nebo schéma vytváření oddílů.
- Velké aplikace, jako je například SAP, MongoDB, Apache Cassandra a Docker nejsou podporovány, když budou nainstalovány a spuštěny v operačním systému před šifrování. Azure Disk Encryption se nepodařilo vypnout tyto procesy bezpečně podle potřeby při přípravě jednotky operačního systému pro šifrování disku. Pokud stále aktivní procesy, které drží otevřené popisovače souborů na jednotce operačního systému, jednotka operačního systému nemůže být odpojeny, což vede k selhání při šifrování jednotky operačního systému. 
- Vlastní skripty, která spustí v blízkosti čas uzavření šifrování povolené, nebo pokud jakýkoli jiný nemění na virtuálním počítači během procesu šifrování. Tomuto konfliktu může dojít, pokud šablonu Azure Resource Manager definuje několik rozšíření současného provádění nebo pokud se rozšíření vlastních skriptů nebo jiné akce je spouštěný souběžně k šifrování disku. Serializace a izolaci těchto kroků mohou problém vyřešit.
- Před povolením šifrování, tak selhání kroku odpojení bylo nezakázala Linux rozšířeného zabezpečení (SELinux). Po dokončení šifrování může SELinux opětovně povolena.
- Disk s operačním systémem používá schéma Správce logických svazků (LVM). I když omezenou podporu pro disky data LVM je k dispozici, není disk s operačním systémem LVM.
- Nejsou splněny požadavky na minimální velikost paměti (7 GB je určeno pro šifrování disku operačního systému).
- Datové jednotky jsou rekurzivně připojený v adresáři /mnt/ nebo mezi sebou (například /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Další Azure Disk Encryption [požadavky](azure-security-disk-encryption-prerequisites.md) nejsou splněny pro Linux.

## <a name="unable-to-encrypt"></a>Nelze zašifrovat

V některých případech je zakázána Linux šifrování disku se zdá být zaseknuto v "Zahájeno šifrování disku operačního systému" a SSH. Proces šifrování může trvat přibližně 3 – 16 hodin na základní Galerie obrázků. Pokud přidáte více terabajt velikosti datových disků, tento proces může trvat dny.

Pořadí šifrování disku operačního systému Linux dočasně odpojí jednotky operačního systému. Potom provede blok po bloku šifrování celého disku s operačním systémem, než ji opět připojí ji ve stavu zašifrované. Na rozdíl od Azure Disk Encryption ve Windows Linux Disk Encryption neumožňuje souběžné použití objektu virtuálního počítače probíhá šifrování. Výkonové charakteristiky virtuálního počítače může být velký rozdíl čas potřebný k dokončení šifrování. Tyto vlastnosti obsahovat velikost disku a zda je standardní účet úložiště nebo úložiště úrovně premium (SSD).

Pokud chcete zkontrolovat stav šifrování, dotazovat **zpráva o průběhu** pole vrácená [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) příkazu. Při šifrované jednotky operačního systému virtuálního počítače přejde do stavu údržby a zakáže abychom zabránili případnému přerušení k aktuálnímu procesu SSH. **EncryptionInProgress** zprávy sestavy pro většinu času, zatímco probíhá šifrování. Několik hodin, **VMRestartPending** zobrazí se výzva k restartování virtuálního počítače. Příklad:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
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
Při připojení je omezena bránu firewall, proxy požadavek nebo nastavení skupinu zabezpečení sítě, může být narušeno možnost rozšíření a provádět požadované úlohy. Toto narušení může vést k stavové zprávy, jako je například "Stav rozšíření ve virtuálním počítači není k dispozici." V očekávaných případech nepovede dokončit šifrování. Následující části mají některé běžné problémy brány firewall, které může prozkoumat.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna nastavení skupiny zabezpečení sítě, které jsou použity musí umožňovat stále koncového bodu podle zdokumentovaných síťovou konfiguraci [požadavky](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) pro šifrování disku.

### <a name="azure-key-vault-behind-a-firewall"></a>Služba Azure Key Vault za bránou firewall
Virtuální počítač musí být schopni přistupovat k trezoru klíčů. Přečtěte si pokyny k přístupu do služby key vault za bránou firewall, která [Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md) tým bude. 

### <a name="azure-instance-metadata-service"></a>Služba Azure Instance Metadata 
Virtuální počítač musí mít přístup k [služby Azure Instance Metadata](../virtual-machines/windows/instance-metadata-service.md) koncový bod, který používá známá nesměrovatelných adres IP (`169.254.169.254`), který je přístupný pouze z v rámci virtuálního počítače.

### <a name="linux-package-management-behind-a-firewall"></a>Správa balíčků Linux za bránou firewall

Za běhu Azure Disk Encryption pro Linux spoléhá na systém správy balíčků distribuce cíl pro nainstalování nezbytných součástí potřebné před povolením šifrování. Pokud na nastavení brány firewall brání tomu nebudou moct stáhnout a nainstalovat tyto součásti virtuálního počítače, se očekává následující chyby. Postupy konfigurace pro tento systém správy balíčků můžou lišit podle distribuce. V systému Red Hat pokud proxy server je vyžadována, je třeba jistotu, že správce předplatného a yumu jsou nastavena správně. Další informace najdete v tématu [postupy řešení potíží Správce předplatného a yumu](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Řešení potíží s Windows Server 2016 Server Core

V systému Windows Server 2016 Server Core není k dispozici ve výchozím nastavení komponenta bdehdcfg. Tato součást vyžaduje Azure Disk Encryption. Používá se k rozdělení systémový svazek ze svazku operačního systému, která se provádí jenom jednou, dobu života virtuálního počítače. Tyto binární soubory se nevyžadují během novější operace šifrování.

Chcete-li tento problém obejít, zkopírujte následující čtyři soubory z virtuálního počítače dat Windows serveru 2016 System Center do stejného umístění na jádru serveru:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Zadejte následující příkaz:

   ```
   bdehdcfg.exe -target default
   ```

   3. Tento příkaz vytvoří 550 MB systémový oddíl. Restartování systému.

   4. Pomocí nástroje DiskPart zkontrolujte svazky a pak pokračujte.  

Příklad:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se dozvěděli informace o některé běžné problémy v Azure Disk Encryption a řešení těchto potíží. Další informace o této službě a její možnosti najdete v následujících článcích:

- [Použít šifrování disku ve službě Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Azure data šifrování v klidovém stavu](azure-security-encryption-atrest.md)
