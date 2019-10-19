---
title: Azure Disk Encryption pro Linux | Microsoft Docs
description: Nasadí Azure Disk Encryption pro Linux do virtuálního počítače pomocí rozšíření virtuálního počítače.
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
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 1801ca8bd59153de81ef680253da229e2bd4a338
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597867"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pro Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá podsystém dm-crypt v systému Linux, který poskytuje úplné šifrování disků na [vybraných distribucích systému Azure Linux](https://aka.ms/adelinux).  Toto řešení je integrované s Azure Key Vault pro správu klíčů a tajných kódů disku.

## <a name="prerequisites"></a>Předpoklady

Úplný seznam požadovaných součástí najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](../linux/disk-encryption-overview.md), konkrétně v následujících oddílech:

- [Azure Disk Encryption pro virtuální počítače se systémem Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Další požadavky na virtuální počítače](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Požadavky na síť](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Schémat rozšíření

K dispozici jsou dvě schémat pro Azure Disk Encryption: v 1.1, novější, Doporučené schéma, které nepoužívá vlastnosti Azure Active Directory (AAD) a v 0,1, starší schéma, které vyžaduje vlastnosti AAD. Je nutné použít verzi schématu odpovídající příponě, kterou používáte: Schema v 1.1 pro rozšíření AzureDiskEncryptionForLinux verze 1,1, Schema v 0,1 pro rozšíření AzureDiskEncryptionForLinux verze 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Schéma v 1.1: žádné AAD (doporučeno)

Doporučuje se schéma v 1.1 a nevyžaduje Azure Active Directory vlastnosti.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schéma v 0,1: s AAD 

Schéma 0,1 vyžaduje `aadClientID` a buď `aadClientSecret`, nebo `AADClientCertificate`.

Použití `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Použití `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| Microsoft | Microsoft. Azure. Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (schéma 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Hlavních | 
| (schéma 0,1) AADClientSecret | heslo | string |
| (schéma 0,1) AADClientCertificate | kryptografický | string |
| DiskFormatQuery | {"dev_path": "", "Name": "", "file_system": ""} | Slovník JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | "RSA-VÝPLNĚ OAEP", "RSA-VÝPLNĚ OAEP-256", "RSA1_5" | string |
| keyEncryptionKeyURL | url | string |
| volitelné KeyVaultURL | url | string |
| Hesel | heslo | string | 
| sequenceVersion | uniqueidentifier | string |
| volumeType | Operační systém, data, vše | string |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony najdete v tématu [povolení šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Pokyny najdete v nejnovější [dokumentaci k Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření virtuálních počítačů najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).