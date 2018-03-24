---
title: Řešení potíží Azure Disk Encryption | Microsoft Docs
description: Tento článek obsahuje tipy k řešení potíží pro Microsoft Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: DevTiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: d49da52b3c45970c797150b7ff2bc6b699967977
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodci odstraňováním potíží Azure Disk Encryption

Tento průvodce je pro odborníky v oblasti IT, analytikům zabezpečení informací a můžou správci cloudové jejichž organizace používat Azure Disk Encryption a pokyny k odstraňování problémů související s diskem šifrování.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Řešení potíží s šifrování disku operačního systému Linux

Šifrování disku operačního systému (OS) Linux musí odpojit disk operačního systému před spuštěním procesu šifrování celého disku. Pokud ho nelze odpojit jednotku, chybová zpráva z "se nepodařilo odpojit po..." je pravděpodobně nastane.

Tato chyba je nejčastěji dochází při pokusu o šifrování disku operačního systému na cílové prostředí virtuálního počítače, který byl změněn nebo se změnila z jeho podporovaných uložených Galerie image. Příklady odchylky od podporované bitovou kopii, která může narušovat rozšíření možnost Odpojit disk operačního systému zahrnují následující:
- Přizpůsobené Image už neodpovídá podporovaném systému souborů nebo schéma rozdělení oddílů.
- Velké aplikace například SAP, MongoDB, Apache Cassandra a Docker nejsou podporovány, když jsou nainstalovány a spuštěny v operačním systému před šifrování.  Azure Disk Encryption se nemůže bezpečně vypnout tyto procesy podle potřeby v rámci přípravy na jednotce operačního systému pro šifrování disku.  Pokud jsou stále aktivní procesy podržíte otevřených popisovačů souborů na jednotce operačního systému, jednotka operačního systému nemůže nepřipojené, což vede k selhání při šifrování jednotky operačního systému. 
- Vlastní skripty, které běží v blízkosti zavřít čas je povolené šifrování, nebo pokud jakékoliv změny jsou prováděny ve virtuálním počítači během procesu šifrování. Tomuto konfliktu může dojít, když šablonu Azure Resource Manager definuje několik rozšíření provést současně, nebo když rozšíření vlastních skriptů nebo jiným běží současně pro šifrování disku. Tento problém může vyřešit serializaci a izolace tyto kroky.
- Linux rozšířeného zabezpečení (SELinux) nebyla zakázané před povolením šifrování, takže odpojení kroku dojde k chybě. Po dokončení šifrování můžete SELinux opětovně povolena.
- Disk operačního systému používá schéma Manager logické svazek (LVM). I když je k dispozici omezená podpora disku data LVM, disk operačního systému LVM není.
- Nejsou splněné požadavky na minimální množství paměti (7 GB je navržený pro šifrování disku operačního systému).
- Datové jednotky jsou rekurzivně připojeny v adresáři /mnt/ nebo navzájem (například /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Další Azure Disk Encryption [požadavky](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pro Linux nejsou splněny.

## <a name="unable-to-encrypt"></a>Nelze zašifrovat

V některých případech je zakázána Linux šifrování disku se zdá být zablokované ve "Spuštění šifrování disku operačního systému" a SSH. Proces šifrování může trvat mezi 3-16 hodin, na bitovou kopii uložených galerie. Pokud budou přidávána velikosti terabajtů více datových disků, proces může trvat dnů.

Pořadí šifrování disku operačního systému Linux dočasně odpojí jednotku operačního systému. Potom provede blok po bloku šifrování celého disku operačního systému, než ji opět připojí ho v jeho šifrovaného stavu. Na rozdíl od Azure Disk Encryption v systému Windows Linux šifrování disku není povoleno pro souběžné používání virtuálního počítače při šifrování probíhá. Výkonové charakteristiky virtuálního počítače mohou být velký rozdíl čas potřebný k dokončení šifrování. Tyto vlastnosti zahrnují velikosti disku a zda je standardní účet úložiště nebo úložiště úrovně premium (SSD).

Pokud chcete zkontrolovat stav šifrování, dotazování **ProgressMessage** vrácená z pole [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) příkaz. Během šifrované jednotky operačního systému, virtuální počítač vstupuje do stavu údržby a zakáže SSH, aby se zabránilo přerušení na probíhající proces. **EncryptionInProgress** zprávy sestavy pro většinu času, když probíhá šifrování. Později, několik hodin **VMRestartPending** zobrazí se výzva k restartování virtuálního počítače. Příklad:


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

Po zobrazení výzvy k restartování virtuálního počítače a po restartování virtuálního počítače, je nutné počkat 2 – 3 minuty, restartování a poslední postup provést v cíli. Zpráva stav se změní při šifrování je nakonec dokončete. Po této zprávy je k dispozici, na šifrované jednotce operačního systému musí být připravený k použití a virtuální počítač je připraven k znovu použít.

V těchto případech doporučujeme obnovení virtuálního počítače do snímku nebo zálohy bezprostředně před šifrování:
   - Pokud pořadí restartování popsané dříve neodehrává.
   - Pokud spouštěcí informace, zprávu o průběhu nebo sestava indikátory další Chyba šifrování operačního systému se nezdařilo uprostřed tento proces. Příklad zprávy, které je "se nepodařilo odpojit" chyby, který je popsaný v tomto průvodci.

Před dalším pokusu o přehodnocovat vlastnosti virtuálního počítače a ujistěte se, že jsou splněny všechny požadavky.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Řešení potíží s Azure Disk Encryption za bránou firewall
Při připojení je omezené na základě brána firewall, proxy požadavek nebo nastavení zabezpečení skupiny (NSG) sítě, může být přerušeny možnost rozšíření potřebné úkoly. Tato doba přerušení může mít za následek stavové zprávy, jako je například "Stav rozšíření ve virtuálním počítači není k dispozici." Očekávaný způsoby šifrování nepodaří dokončit. V následujících mít některé běžné problémy brány firewall, které může prozkoumat.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna nastavení skupiny zabezpečení sítě, které se použijí musí umožňovat stále koncový bod podle zdokumentovaných síťovou konfiguraci [požadavky](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) šifrování disku.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault za bránou firewall
Virtuální počítač musí být schopen získat přístup k trezoru klíčů. Najdete pokyny k přístupu k trezoru klíčů za bránou firewall, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) udržuje týmu.

### <a name="linux-package-management-behind-a-firewall"></a>Linux správy balíčků za bránou firewall

V době běhu Azure Disk Encryption pro Linux spoléhá na systém správy cílového distribučního balíčku pro instalaci potřebné součásti, které před povolením šifrování. Pokud v nastavení brány firewall zabránit virtuální počítač mohli ke stažení a instalaci těchto součástí, se očekává následné selhání. Postup konfigurace tento systém správy balíčků se může lišit podle distribuce. Na Red Hat pokud proxy server je nutné, zajistěte, aby odběr manager a yum jsou správně nastavena. Další informace najdete v tématu [postup řešení potíží Správce předplatného a yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Řešení potíží s jádra serveru systému Windows Server 2016

Součást bdehdcfg není k dispozici ve výchozím nastavení, na jádru serveru Windows Server 2016. Tato součást vyžaduje Azure Disk Encryption. Slouží k rozdělení na systémovém svazku ze svazku operačního systému, která se provádí jenom jednou po dobu životnosti virtuálního počítače. Tyto binární soubory nejsou nutné během novější operace šifrování.

Chcete-li vyřešit tento problém, kopírovat 4 následující soubory z virtuálního počítače Windows serveru 2016 Data Center do stejného umístění, v jádru serveru:

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

   4. Pomocí nástroje DiskPart kontrolovat svazky, a poté pokračujte.  

Příklad:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>Řešení potíží s stav šifrování

Pokud stav očekávané šifrování neodpovídá obsah hlášení na portálu, najdete v následujícím článku podpory: [stav šifrování se nesprávně zobrazí na portálu pro správu Azure](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por)

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, informace o některé běžné problémy v Azure Disk Encryption a informace o řešení těchto problémů. Další informace o této služby a jeho funkce najdete v následujících článcích:

- [Použít šifrování disku v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Šifrování virtuálního počítače Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Šifrování dat Azure v klidovém stavu](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
