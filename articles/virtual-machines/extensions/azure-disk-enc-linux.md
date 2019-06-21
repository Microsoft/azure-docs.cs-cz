---
title: Azure Disk Encryption pro Linux | Dokumentace Microsoftu
description: Nasadí Azure Disk Encryption pro Linux do virtuálního počítače pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 05d20e75cf8f0c84936ff4e5dfa42d60678f6ffc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295334"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pro Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá technologii dm-crypt subsystém v Linuxu poskytují šifrování celého disku s [vyberte Azure Linuxových distribucí](https://aka.ms/adelinux).  Toto řešení je integrovaná s Azure Key Vault a Správa disku šifrovacích klíčů a tajných kódů.

## <a name="prerequisites"></a>Požadavky

Úplný seznam všech požadovaných součástí najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operační systém

Azure Disk Encryption je momentálně podporovaný na vyberte distribucích a verzích.  Zobrazit [Azure Disk Encryption podporované operační systémy: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) seznam distribucí systému Linux, které jsou podporovány.

### <a name="internet-connectivity"></a>Připojení k internetu

Azure Disk Encryption pro Linux vyžaduje připojení k Internetu pro přístup k Active Directory, služby Key Vault, úložiště a koncových bodů správy balíčků.  Další informace najdete v tématu [požadované součásti služby Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Rozšíření schémat

Existují dvě schémata pro Azure Disk Encryption: v1.1, novější, doporučené schéma, které nepoužívají vlastnosti v Azure Active Directory (AAD) a v0.1, starší schéma, které vyžaduje vlastnosti AAD. Je nutné použít verzi schématu odpovídající rozšíření, kterou používáte: v1.1 schématu pro AzureDiskEncryptionForLinux rozšíření verze 1.1, v0.1 schématu pro AzureDiskEncryptionForLinux verze rozšíření 0,1.
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

Pomocí `AADClientCertificate`:

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
| (volitelné) KeyVaultURL | url | string |
| Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Operačního systému, dat, všechny | string |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony najdete v tématu [povolit šifrování na spuštěný virtuální počítač s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pokyny najdete v nejnovější aktualizaci [dokumentace k Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Řešení potíží, najdete [Průvodce řešením problémů pro Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup

Další informace o rozšíření virtuálních počítačů najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Linux](features-linux.md).