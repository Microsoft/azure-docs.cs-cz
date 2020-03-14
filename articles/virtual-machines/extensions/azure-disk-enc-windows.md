---
title: Azure Disk Encryption pro Windows
description: Nasadí Azure Disk Encryption na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250644"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pro Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá BitLocker k poskytování úplného šifrování disků na virtuálních počítačích Azure s Windows.  Toto řešení je integrované s Azure Key Vault pro správu klíčů a tajných kódů disků v předplatném trezoru klíčů. 

## <a name="prerequisites"></a>Požadavky

Úplný seznam požadovaných součástí najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](../linux/disk-encryption-overview.md), konkrétně v následujících oddílech:

- [Azure Disk Encryption pro virtuální počítače se systémem Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](../windows/disk-encryption-overview.md#networking-requirements)
- [Zásady skupiny požadavky](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schémat rozšíření

Existují dva schémat rozšíření Windows AzureDiskEncryption: v 2.2, novější, Doporučené schéma, které nepoužívá vlastnosti Azure Active Directory (AAD) a v 1.1, starší schéma, které vyžaduje vlastnosti AAD. Je nutné použít verzi schématu odpovídající příponě, kterou používáte: Schema v 2.2 pro rozšíření AzureDiskEncryption verze 2,2, Schema v 1.1 pro rozšíření AzureDiskEncryption verze 1,1.

### <a name="schema-v22-no-aad-recommended"></a>Schéma v 2.2: žádné AAD (doporučeno)

Schéma v 2.2 se doporučuje pro všechny nové virtuální počítače a nevyžaduje Azure Active Directory vlastnosti.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>Schéma v 1.1: s AAD 

Schéma 1,1 vyžaduje `aadClientID` a buď `aadClientSecret` nebo `AADClientCertificate`, a nedoporučuje se pro nové virtuální počítače.

Používání produktu `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Datový typ |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| typ | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1,1, 2,2 | string |
| (schéma 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schéma 1,1) AADClientSecret | heslo | string |
| (schéma 1,1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Slovník JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | ' RSA-VÝPLNĚ OAEP ', ' RSA-VÝPLNĚ OAEP-256 ', ' RSA1_5 ' | string |
| KeyEncryptionKeyURL | Adresa URL | string |
| KeyVaultURL | Adresa URL | string |
| (volitelné) Passphrase | heslo | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Operační systém, data, vše | string |

## <a name="template-deployment"></a>Nasazení šablony
Příklad nasazení šablony najdete v tématu [Vytvoření nového šifrovaného virtuálního počítače s Windows z image z Galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pokyny najdete v nejnovější [dokumentaci k Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení problémů

Informace najdete v [Průvodci odstraňováním potíží s Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Windows](features-windows.md).
