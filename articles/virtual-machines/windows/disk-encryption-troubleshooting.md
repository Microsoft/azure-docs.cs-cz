---
title: Průvodce odstraňováním potíží s šifrováním disku Azure
description: Tento článek obsahuje tipy pro řešení potíží s šifrováním disku Microsoft Azure pro virtuální počítače s Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d4e76f4d02b0287770243bfddf995a19f90d232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749446"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Průvodce odstraňováním potíží s šifrováním disku Azure

Tato příručka je určen pro odborníky v oblasti IT, analytiky zabezpečení informací a správce cloudu, jejichž organizace používají Azure Disk Encryption. Tento článek vám pomůže s odstraňováním potíží souvisejících s šifrováním disku.

Před přijetím některého z následujících kroků nejprve ujistěte se, že virtuální počítače, které se pokoušíte zašifrovat, patří mezi [podporované velikosti virtuálních počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems)a že jste splnili všechny požadavky:

- [Požadavky na vytváření sítí](disk-encryption-overview.md#networking-requirements)
- [Požadavky na zásady skupiny](disk-encryption-overview.md#group-policy-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Poradce při potížích s šifrováním disku Azure za bránou firewall

Pokud je připojení omezeno nastavením brány firewall, požadavku na server proxy nebo skupiny zabezpečení sítě (NSG), může být schopnost rozšíření provádět potřebné úlohy narušena. Toto narušení může mít za následek stavové zprávy, jako je například "Stav rozšíření není k dispozici na virtuálním počítači." V očekávaných scénářích šifrování se nepodaří dokončit. Následující části mají některé běžné problémy s bránou firewall, které můžete prozkoumat.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna použitá nastavení skupiny zabezpečení sítě musí koncovému bodu stále umožňovat splnění [předpokladů](disk-encryption-overview.md#networking-requirements) pro konfiguraci sítě pro šifrování disku.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault za bránou firewall

Když je šifrování povoleno s [přihlašovacími údaji Azure AD](disk-encryption-windows-aad.md#), cílový virtuální počítač musí povolit připojení ke koncovým bodům služby Azure Active Directory i koncovým bodům trezoru klíčů. Aktuální koncové body ověřování služby Azure Active Directory se udržují v oddílech 56 a 59 adres [URL office 365 a dokumentaci k rozsahům IP adres.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Pokyny k trezoru klíčů jsou k dispozici v dokumentaci o tom, jak získat přístup k [trezoru klíčů Azure za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure 
Virtuální počítač musí mít přístup ke koncovému bodu [služby Metadat instance Azure,](../windows/instance-metadata-service.md) který`169.254.169.254`používá známou nesměrovatelnou IP adresu ( ), ke které lze přistupovat pouze z virtuálního počítače.  Konfigurace serveru proxy, které mění místní přenos protokolu HTTP na tuto adresu (například přidání hlavičky X-Forwarded-For), nejsou podporovány.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Poradce při potížích s jádrem systému Windows Server 2016 Server

V systému Windows Server 2016 Server Core není komponenta bdehdcfg ve výchozím nastavení dostupná. Tato součást je vyžadována šifrováním disku Azure. Používá se k rozdělení objemu systému z objemu operačního systému, který se provádí pouze jednou po dobu životnosti virtuálního soudu. Tyto binární soubory nejsou vyžadovány během pozdějších operací šifrování.

Chcete-li tento problém vyřešit, zkopírujte následující čtyři soubory z virtuálního počítače datového centra Windows Server 2016 do stejného umístění na webu Core:

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

1. Tento příkaz vytvoří systémový oddíl o mb 550 MB. Restartujte systém.

1. Pomocí programu DiskPart zkontrolujte svazky a pokračujte.  

Například:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Poradce při potížích se stavem šifrování 

Portál může zobrazit disk jako zašifrovaný i poté, co byl nezašifrován v rámci virtuálního počítače.  K tomu může dojít, když se příkazy nižší úrovně používají k přímému odšifrování disku z virtuálního počítače, namísto použití příkazů pro správu azure disk encryption vyšší úrovně.  Příkazy vyšší úrovně nejen dešifrují disk z virtuálního počítače, ale mimo virtuální počítače také aktualizují důležitá nastavení šifrování na úrovni platformy a nastavení rozšíření přidružená k virtuálnímu počítače.  Pokud tyto nejsou udržovány v zarovnání, platforma nebude moci hlásit stav šifrování nebo zřídit virtuální ho virtuálního média správně.   

Chcete-li zakázat šifrování disku Azure pomocí prostředí PowerShell, použijte [příkaz Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) následovaný [příkazem Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Spuštění remove-AzVMDiskEncryptionExtension před zakázáním šifrování se nezdaří.

Chcete-li zakázat šifrování disku Azure pomocí příkazového příkazu k příkazu příkazu cli, použijte [šifrování vaslužby az zakázat](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli více o některých běžných problémech v Azure Disk Encryption a jak tyto problémy vyřešit. Další informace o této službě a jejích možnostech naleznete v následujících článcích:

- [Použití šifrování disku v Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Šifrování dat Azure v klidovém stavu](../../security/fundamentals/encryption-atrest.md)
