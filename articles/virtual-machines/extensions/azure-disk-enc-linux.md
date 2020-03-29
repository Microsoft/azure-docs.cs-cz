---
title: Šifrování disku Azure pro Linux
description: Nasazuje Azure Disk Encryption pro Linux do virtuálního počítače pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066919"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Šifrování disku Azure pro Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá podsystém dm-crypt v Linuxu k zajištění úplného šifrování disku na [vybraných distribucích Azure Linuxu](https://aka.ms/adelinux).  Toto řešení je integrované s Azure Key Vault pro správu šifrovacíklíče disku a tajných kódů.

## <a name="prerequisites"></a>Požadavky

Úplný seznam požadavků najdete v tématu [Azure Disk Encryption for Linux VMs](../linux/disk-encryption-overview.md), konkrétně v následujících částech:

- [Podporované virtuální počítače a operační systémy](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Další požadavky na virtuální mísu](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Požadavky na vytváření sítí](../linux/disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schéma rozšíření

Existují dvě verze schématu rozšíření pro šifrování disku Azure (ADE):
- v1.1 – novější doporučené schéma, které nepoužívá vlastnosti služby Azure Active Directory (AAD).
- v0.1 – starší schéma, které vyžaduje vlastnosti služby Azure Active Directory (AAD). 

Chcete-li vybrat cílové schéma, `typeHandlerVersion` musí být vlastnost nastavena stejně jako verze schématu, které chcete použít.

### <a name="schema-v11-no-aad-recommended"></a>Schéma v1.1: Žádné AAD (doporučeno)

Schéma v1.1 se doporučuje a nevyžaduje vlastnosti Služby Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schéma v0.1: s AAD 

Schéma 0.1 vyžaduje `AADClientID` a buď `AADClientSecret` `AADClientCertificate`nebo .

Použití `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Použití `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| vydavatel | Microsoft.Azure.Zabezpečení | řetězec |
| type | AzureDiskEncryptionForLinux | řetězec |
| typeHandlerVersion | 1.1, 0.1 | int |
| (schéma 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Identifikátor guid | 
| (schéma 0.1) AADClientSecret | heslo | řetězec |
| (schéma 0.1) AADClientCertificate | Miniatura | řetězec |
| (nepovinné) (schéma 0.1) Heslo | heslo | řetězec |
| Program DiskFormatQuery | {"dev_path":","jméno":"","file_system":""} | JSON slovník |
| Operace šifrování | EnableEncryption, EnableEncryptionFormatAll | řetězec | 
| (volitelně - výchozí RSA-OAEP) Algoritmus šifrování kláves | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | řetězec |
| Adresa KeyVaultURL | url | řetězec |
| KeyVaultResourceId | url | řetězec |
| (nepovinné) Adresa KeyEncryptionKeyURL | url | řetězec |
| (nepovinné) KekVaultResourceId | url | řetězec |
| (nepovinné) PosloupnostVerze | uniqueidentifier | řetězec |
| Typ svazku | Operační hod, Data, Vše | řetězec |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony na základě schématu v1.1 najdete v tématu Azure Quickstart Template [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Příklad nasazení šablony na základě schématu v0.1 najdete v tématu Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače.
> - Při šifrování svazků operačního systému Linux by měl být virtuální počítač považován za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco šifrování probíhá, aby se zabránilo problémům, které blokují všechny otevřené soubory, které budou muset být přístupné během procesu šifrování. Chcete-li zkontrolovat průběh, použijte rutinu [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell nebo příkaz příkazu [příkazu](/cli/azure/vm/encryption#az-vm-encryption-show) příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu Tento proces může trvat několik hodin pro svazek operačního systému 30 GB a další čas pro šifrování datových svazků. Doba šifrování svazku dat bude úměrná velikosti a množství datových svazků, pokud nebude použit ašifrovací formát všechny možnosti. 
> - Zakázání šifrování na virtuálních počítačích s Linuxem je podporované jenom pro datové svazky. Není podporovánna svazcích dat nebo operačního systému, pokud byl svazek operačního systému zašifrován. 

>[!NOTE]
> Také `VolumeType` pokud je parametr nastaven na všechny, datové disky budou šifrovány pouze v případě, že jsou správně připojeny.

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Řešení potíží naleznete v [průvodci řešením potíží s šifrováním disku Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/community/). 

Případně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

* Další informace o rozšířeních virtuálních virtuálních zařízení najdete [v tématu Rozšíření virtuálních strojů a funkce pro Linux](features-linux.md).
* Další informace o Azure Disk Encryption for Linux najdete v [tématu Virtuální počítače s Linuxem](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
