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
ms.author: danis
ms.openlocfilehash: f9b8c64aadf813ab1157820fc4051d48989e48c4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449764"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pro Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá nástroj Bitlocker k šifrování celého disku poskytují na virtuálních počítačích Azure s Windows.  Toto řešení je integrovaná s Azure Key Vault a Správa disku šifrovací klíče a tajné kódy ve vašem předplatném služby key vault. 

## <a name="prerequisites"></a>Požadavky

Úplný seznam všech požadovaných součástí najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Operační systém

Seznam aktuálně verzí Windows, naleznete v tématu [požadované součásti služby Azure Disk Encryption](../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="internet-connectivity"></a>Připojení k internetu

Azure Disk Encryption vyžaduje připojení k Internetu pro přístup k Active Directory, služby Key Vault, úložiště a koncových bodů správy balíčků.  Další informace o nastavení zabezpečení sítě najdete v tématu [požadované součásti služby Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

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
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.Azure.Security | řetězec |
| type | AzureDiskEncryptionForWindows| řetězec |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (volitelné) AADClientID | tomuto: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | identifikátor GUID | 
| (volitelné) AADClientSecret | heslo | řetězec |
| (volitelné) AADClientCertificate | Kryptografický otisk | řetězec |
| EncryptionOperation | EnableEncryption | řetězec | 
| KeyEncryptionAlgorithm | RSA OAEP | řetězec |
| KeyEncryptionKeyURL | url | řetězec |
| KeyVaultURL | url | řetězec |
| SequenceVersion | UniqueIdentifier | řetězec |
| VolumeType | Operačního systému, dat, všechny | řetězec |

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