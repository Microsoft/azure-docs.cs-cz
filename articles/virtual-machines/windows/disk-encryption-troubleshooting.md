---
title: Průvodce odstraňováním potíží s Azure Disk Encryption
description: Tento článek popisuje tipy pro řešení potíží pro Microsoft Azure šifrování disku pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 057aba3e49d32694563f412101be499342f2aad0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550599"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Azure Disk Encryption

Tato příručka je určena pro odborníky v oblasti IT, analytiky zabezpečení informací a správce cloudu, jejichž organizace používají Azure Disk Encryption. V tomto článku se dozvíte, jak vyřešit problémy související s šifrováním disku.

Než začnete s některým z následujících kroků, zajistěte, aby virtuální počítače, které se pokoušíte zašifrovat, byly mezi [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems), a že jste splnili všechny požadavky:

- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Požadavky na zásady skupiny](disk-encryption-overview.md#group-policy-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Řešení potíží s Azure Disk Encryption za bránou firewall

Pokud je připojení omezeno pomocí brány firewall, požadavku serveru proxy nebo skupiny zabezpečení sítě (NSG), může dojít k přerušení možnosti rozšíření k provedení potřebných úkolů. Příčinou tohoto narušení může být stavové zprávy, například "stav rozšíření není na virtuálním počítači k dispozici". V očekávaných scénářích se šifrování nepodařilo dokončit. Následující části obsahují některé běžné problémy firewallu, které můžete prozkoumat.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna použitá nastavení skupiny zabezpečení sítě musí stále umožňovat, aby koncový bod splňoval popsané [požadavky](disk-encryption-overview.md#networking-requirements) na konfiguraci sítě pro šifrování disků.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault za bránou firewall

Když se povolí šifrování s [přihlašovacími údaji Azure AD](disk-encryption-windows-aad.md#), cílový virtuální počítač musí umožňovat připojení ke koncovým bodům Azure Active Directory i k Key Vault koncovým bodům. Aktuální Azure Active Directory koncové body ověřování jsou uchovávány v oddílech 56 a 59 [Microsoft 365 adres URL a v dokumentaci rozsahů IP adres](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Pokyny pro Key Vault najdete v dokumentaci týkající se [přístupu Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
Virtuální počítač musí být schopný získat přístup ke koncovému bodu [služby metadat instance Azure](../windows/instance-metadata-service.md) , který používá známou Nesměrovatelné IP adresy ( `169.254.169.254` ), ke kterým se dá přistupovat jenom z virtuálního počítače.  Konfigurace proxy serveru, které mění místní přenos HTTP na tuto adresu (například přidání řádku s přesměrováním X), nejsou podporovány.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Řešení potíží s Windows serverem 2016 Server Core

V jádru serveru Windows Server 2016 není ve výchozím nastavení dostupná součást BdeHdCfg. Tuto součást vyžaduje Azure Disk Encryption. Používá se k rozdělení systémového svazku ze svazku s operačním systémem, který se provádí jenom jednou pro životní čas virtuálního počítače. Tyto binární soubory se během pozdějších operací šifrování nevyžadují.

Pokud chcete tento problém obejít, zkopírujte následující čtyři soubory z virtuálního počítače datového centra Windows Server 2016 do stejného umístění na jádru serveru:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Zadejte následující příkaz:

   ```
   bdehdcfg.exe -target default
   ```

1. Tento příkaz vytvoří systémový oddíl 550-MB. Restartujte systém.

1. Pomocí nástroje DiskPart zkontrolujte svazky a pak pokračujte.  

Například:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Řešení potíží se stavem šifrování 

Portál může zobrazit disk jako zašifrovaný, i když byl v rámci virtuálního počítače nešifrovaný.  K tomu může dojít, když se k přímému rozšifrování disku z virtuálního počítače používají příkazy nízké úrovně, místo abyste používali příkazy pro správu Azure Disk Encryption vyšší úrovně.  Příkazy vyšší úrovně nešifrují jenom disk v rámci virtuálního počítače, ale mimo virtuální počítač aktualizují důležitá nastavení šifrování na úrovni platformy a nastavení rozšíření přidružená k virtuálnímu počítači.  Pokud tyto možnosti nejsou zachovány, platforma nebude moci nahlásit stav šifrování ani zřídit virtuální počítač správně.

Pokud chcete Azure Disk Encryption zakázat pomocí prostředí PowerShell, použijte [příkaz disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) následovaný rutinou [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Spuštění Remove-AzVMDiskEncryptionExtension před vypnutím šifrování selže.

Pokud chcete zakázat Azure Disk Encryption pomocí rozhraní příkazového řádku, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## 



## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli o některých běžných problémech v Azure Disk Encryption a o tom, jak tyto problémy řešit. Další informace o této službě a jejích funkcích najdete v následujících článcích:

- [Použít šifrování disku v Azure Security Center](../../security-center/asset-inventory.md)
- [Šifrování neaktivních uložených dat v Azure](../../security/fundamentals/encryption-atrest.md)