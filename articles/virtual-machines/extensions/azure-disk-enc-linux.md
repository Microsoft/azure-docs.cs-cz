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
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: adf0ce6c8424d2350578d9bfd19c70ebb15fc473
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389860"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pro Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá technologii dm-crypt subsystém v Linuxu poskytují šifrování celého disku s [vyberte Azure Linuxových distribucí](https://aka.ms/adelinux).  Toto řešení je integrovaná s Azure Key Vault a Správa disku šifrovacích klíčů a tajných kódů.

## <a name="prerequisites"></a>Požadavky

Úplný seznam všech požadovaných součástí najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operační systém

Azure Disk Encryption je momentálně podporovaný na vyberte distribucích a verzích.  Zobrazit [nejčastější dotazy týkající se Azure Disk Encryption](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) seznam distribucí systému Linux, které jsou podporovány.

### <a name="internet-connectivity"></a>Připojení k internetu

Azure Disk Encryption pro Linux vyžaduje připojení k Internetu pro přístup k Active Directory, služby Key Vault, úložiště a koncových bodů správy balíčků.  Další informace najdete v tématu [požadované součásti služby Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Schéma rozšíření

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

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.Azure.Security | řetězec |
| type | AzureDiskEncryptionForLinux | řetězec |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | tomuto: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | identifikátor GUID | 
| AADClientSecret | heslo | řetězec |
| AADClientCertificate | Kryptografický otisk | řetězec |
| DiskFormatQuery | {"dev_path": "", "název": "","file_system": ""} | Slovník JSON |
| EncryptionOperation | EnableEncryption EnableEncryptionFormatAll | řetězec | 
| KeyEncryptionAlgorithm | "RSA OAEP", "RSA 256 OAEP", "RSA1_5. | řetězec |
| KeyEncryptionKeyURL | url | řetězec |
| KeyVaultURL | url | řetězec |
| Přístupové heslo | heslo | řetězec | 
| SequenceVersion | UniqueIdentifier | řetězec |
| VolumeType | Operačního systému, dat, všechny | řetězec |

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