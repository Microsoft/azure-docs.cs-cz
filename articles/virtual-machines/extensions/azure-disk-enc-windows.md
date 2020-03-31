---
title: Šifrování disku Azure pro Windows
description: Nasazuje Azure Disk Encryption do virtuálního počítače s Windows pomocí rozšíření virtuálního počítače.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066867"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Šifrování disku Azure pro Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá nástroj BitLocker k zajištění šifrování celého disku na virtuálních počítačích Azure se systémem Windows.  Toto řešení je integrované s Azure Key Vault pro správu šifrovacíklíče disku a tajných kódů v předplatném trezoru klíčů. 

## <a name="prerequisites"></a>Požadavky

Úplný seznam požadavků najdete v tématu [Azure Disk Encryption pro virtuální počítače s Windows](../windows/disk-encryption-overview.md), konkrétně v následujících částech:

- [Podporované virtuální počítače a operační systémy](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na vytváření sítí](../windows/disk-encryption-overview.md#networking-requirements)
- [Požadavky zásad skupiny](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schéma rozšíření

Existují dvě verze schématu rozšíření pro šifrování disku Azure (ADE):
- v2.2 – novější doporučené schéma, které nepoužívá vlastnosti služby Azure Active Directory (AAD).
- v1.1 – starší schéma, které vyžaduje vlastnosti služby Azure Active Directory (AAD). 

Chcete-li vybrat cílové schéma, `typeHandlerVersion` musí být vlastnost nastavena stejně jako verze schématu, které chcete použít.

### <a name="schema-v22-no-aad-recommended"></a>Schéma v2.2: Žádné AAD (doporučeno)

Schéma v2.2 se doporučuje pro všechny nové virtuální počítače a nevyžaduje vlastnosti služby Azure Active Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schéma v1.1: s AAD 

Schéma 1.1 vyžaduje `aadClientID` a buď `aadClientSecret` `AADClientCertificate` nebo a není doporučeno pro nové virtuální aplikace.

Použití `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
| type | AzureDiskEncryption | řetězec |
| typeHandlerVersion | 2.2, 1.1 | řetězec |
| (schéma 1.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Identifikátor guid | 
| (schéma 1.1) AADClientSecret | heslo | řetězec |
| (schéma 1.1) AADClientCertificate | Miniatura | řetězec |
| Operace šifrování | EnableEncryption, EnableEncryptionFormatAll | řetězec | 
| (volitelně - výchozí RSA-OAEP) Algoritmus šifrování kláves | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | řetězec |
| Adresa KeyVaultURL | url | řetězec |
| KeyVaultResourceId | url | řetězec |
| (nepovinné) Adresa KeyEncryptionKeyURL | url | řetězec |
| (nepovinné) KekVaultResourceId | url | řetězec |
| (nepovinné) PosloupnostVerze | uniqueidentifier | řetězec |
| Typ svazku | Operační hod, Data, Vše | řetězec |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony na základě schématu v2.2 najdete v tématu Azure QuickStart Template [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Příklad nasazení šablony na základě schématu v1.1 najdete v tématu Azure QuickStart Template [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Také `VolumeType` pokud je parametr nastaven na všechny, datové disky budou šifrovány pouze v případě, že jsou správně formátovány. 

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Řešení potíží naleznete v [průvodci řešením potíží s šifrováním disku Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/community/). 

Případně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

* Další informace o rozšířeních najdete [v tématu Rozšíření a funkce virtuálních strojů pro Windows](features-windows.md).
* Další informace o azure disk encryption pro Windows najdete v [tématu virtuální počítače s Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
