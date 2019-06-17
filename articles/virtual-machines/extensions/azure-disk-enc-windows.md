---
title: Azure Disk Encryption pro Windows | Dokumentace Microsoftu
description: Azure Disk Encryption nasadí do virtuálního počítače s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: ff77f9fc017627143b14544af03d0d5e80813db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051705"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pro Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá nástroj BitLocker k šifrování celého disku poskytují na virtuálních počítačích Azure s Windows.  Toto řešení je integrovaná s Azure Key Vault a Správa disku šifrovací klíče a tajné kódy ve vašem předplatném služby key vault. 

## <a name="prerequisites"></a>Požadavky

Úplný seznam všech požadovaných součástí najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operační systém

Seznam aktuálně verzí Windows, naleznete v tématu [požadované součásti služby Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Připojení k internetu

Azure Disk Encryption vyžaduje připojení k Internetu pro přístup k Active Directory, služby Key Vault, úložiště a koncových bodů správy balíčků.  Další informace o nastavení zabezpečení sítě najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Rozšíření schémat

Existují dvě schémata pro Azure Disk Encryption: v1.1, novější, doporučené schéma, které nepoužívají vlastnosti v Azure Active Directory (AAD) a v0.1, starší schéma, které vyžaduje vlastnosti AAD. Je nutné použít verzi schématu odpovídající rozšíření, kterou používáte: v1.1 schématu pro AzureDiskEncryption rozšíření verze 1.1, v0.1 schématu pro AzureDiskEncryption verze rozšíření 0,1.

### <a name="schema-v11-no-aad-recommended"></a>Schéma v1.1: Žádné AAD (doporučeno)

Schéma v1.1 je doporučeno a nevyžaduje, aby vlastnosti služby Azure Active Directory.

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


### <a name="schema-v01-with-aad"></a>Schéma v0.1: v AAD 

Vyžaduje schéma 0,1 `aadClientID` a buď `aadClientSecret` nebo `AADClientCertificate`.

Pomocí `aadClientSecret`:

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

Pomocí `AADClientCertificate`:

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

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 | int |
| (schéma 0,1) AADClientID | tomuto: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | identifikátor GUID | 
| (schéma 0,1) AADClientSecret | password | string |
| (schéma 0,1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Slovník JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | "RSA OAEP", "RSA 256 OAEP", "RSA1_5. | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| (volitelné) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Operačního systému, dat, všechny | string |

## <a name="template-deployment"></a>Nasazení šablon
Příklad nasazení šablony najdete v tématu [ vytvořit nový virtuální počítač šifrovaný Windows z image z Galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pokyny najdete v nejnovější aktualizaci [dokumentace k Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Odkazovat [Průvodce řešením problémů pro Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup
Další informace o rozšířeních najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Windows](features-windows.md).
