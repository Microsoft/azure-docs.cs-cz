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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: cc82109c60ac077d9e92e4d4a52abdaf8577771f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971179"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption pro Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overview

Azure Disk Encryption využívá podsystém dm-crypt v systému Linux, který poskytuje úplné šifrování disků na [vybraných distribucích systému Azure Linux](../linux/disk-encryption-overview.md).  Toto řešení je integrované s Azure Key Vault pro správu klíčů a tajných kódů disku.

## <a name="prerequisites"></a>Předpoklady

Úplný seznam požadovaných součástí najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](../linux/disk-encryption-overview.md), konkrétně v následujících oddílech:

- [Podporované virtuální počítače a operační systémy](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Další požadavky na virtuální počítače](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Požadavky na síť](../linux/disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schéma rozšíření

Existují dvě verze schématu rozšíření pro Azure Disk Encryption (ADE):
- v 1.1 – novější Doporučené schéma, které nepoužívá vlastnosti Azure Active Directory (AAD).
- v 0,1 – starší schéma, které vyžaduje vlastnosti Azure Active Directory (AAD). 

Chcete-li vybrat cílové schéma, `typeHandlerVersion` musí být vlastnost nastavena na hodnotu stejné jako verze schématu, kterou chcete použít.

### <a name="schema-v11-no-aad-recommended"></a>Schéma v 1.1: žádné AAD (doporučeno)

Doporučuje se schéma v 1.1 a nevyžaduje Azure Active Directory (AAD) vlastností.

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


### <a name="schema-v01-with-aad"></a>Schéma v 0,1: s AAD 

Schéma 0,1 vyžaduje `AADClientID` a `AADClientSecret` `AADClientCertificate` .

Používá se `AADClientSecret` :

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

Používá se `AADClientCertificate` :

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

| Název | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| vydavatel | Microsoft. Azure. Security | řetězec |
| typ | AzureDiskEncryptionForLinux | řetězec |
| typeHandlerVersion | 1,1, 0,1 | int |
| (schéma 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schéma 0,1) AADClientSecret | heslo | řetězec |
| (schéma 0,1) AADClientCertificate | kryptografický | řetězec |
| volitelné (schéma 0,1) Hesel | heslo | řetězec |
| DiskFormatQuery | {"dev_path": "", "Name": "", "file_system": ""} | Slovník JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | řetězec | 
| (volitelné – výchozí RSA – výplně OAEP) KeyEncryptionAlgorithm | ' RSA-VÝPLNĚ OAEP ', ' RSA-VÝPLNĚ OAEP-256 ', ' RSA1_5 ' | řetězec |
| KeyVaultURL | url | řetězec |
| KeyVaultResourceId | url | řetězec |
| volitelné KeyEncryptionKeyURL | url | řetězec |
| volitelné KekVaultResourceId | url | řetězec |
| volitelné SequenceVersion | uniqueidentifier | řetězec |
| VolumeType | Operační systém, data, vše | řetězec |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony založeného na schématu v 1.1 najdete v článku Azure pro rychlý Start [– šablona 201-Encrypting-Running-Linux-VM-bez AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Příklad nasazení šablony založeného na schématu v 0,1 najdete v tématu šablona Azure pro rychlý Start [201-Encrypted-Running-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače.
> - Při šifrování svazků se systémem Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco probíhá šifrování, aby nedocházelo k blokování jakýchkoli otevřených souborů, ke kterým bude potřeba během procesu šifrování dostat. Ke kontrole průběhu použijte rutinu [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) prostředí PowerShell nebo příkaz pro [šifrování virtuálního počítače zobrazit](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Pro tento proces může trvat několik hodin, než se 30 GB svazek s operačním systémem a další čas šifrování datových svazků. Doba šifrování datového svazku bude úměrná velikosti a množství datových svazků, pokud se nepoužije možnost šifrovat formát ALL. 
> - Zakázání šifrování u virtuálních počítačů se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporována. 

>[!NOTE]
> I když `VolumeType` je parametr nastavený na vše, datové disky se zašifrují jenom v případě, že jsou správně připojené.

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Azure Disk Encryption](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). 

Případně můžete zasouborovat incident podpory Azure. Přejít na [podporu Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

* Další informace o rozšíření virtuálních počítačů najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).
* Další informace o Azure Disk Encryption pro Linux najdete v tématu [virtuální počítače se systémem Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).