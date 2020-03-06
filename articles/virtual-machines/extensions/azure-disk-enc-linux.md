---
title: Azure Disk Encryption pro Linux
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
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383203"
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

Používání produktu `aadClientSecret`:

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

Používání produktu `AADClientCertificate`:

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

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | řetězec |
| type | AzureDiskEncryptionForLinux | řetězec |
| typeHandlerVersion | 0.1, 1.1 | int |
| (schéma 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schéma 0,1) AADClientSecret | heslo | řetězec |
| (schéma 0,1) AADClientCertificate | thumbprint | řetězec |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Slovník JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | řetězec | 
| KeyEncryptionAlgorithm | ' RSA-VÝPLNĚ OAEP ', ' RSA-VÝPLNĚ OAEP-256 ', ' RSA1_5 ' | řetězec |
| KeyEncryptionKeyURL | url | řetězec |
| volitelné KeyVaultURL | url | řetězec |
| Passphrase | heslo | řetězec | 
| SequenceVersion | uniqueidentifier | řetězec |
| VolumeType | Operační systém, data, vše | řetězec |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony najdete v tématu [povolení šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pokyny najdete v nejnovější [dokumentaci k Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření virtuálních počítačů najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).