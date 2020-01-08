---
title: Určení modelu šifrovacího klíče, který se používá pro účet úložiště
titleSuffix: Azure Storage
description: Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete zjistit, jak se šifrovací klíče spravují pro účet úložiště. Klíče může spravovat Microsoft (výchozí) nebo zákazník. Klíče spravované zákazníkem musí být uloženy v Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3806fead9226978c277e87f3d97b14ee38d9552d
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665410"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Určete, který Azure Storage model šifrovacího klíče se používá pro účet úložiště.

Data v účtu úložiště se automaticky šifrují pomocí Azure Storage. Azure Storage šifrování nabízí dvě možnosti správy šifrovacích klíčů na úrovni účtu úložiště:

- **Klíče spravované společností Microsoft.** Ve výchozím nastavení spravuje Microsoft klíče používané k zašifrování účtu úložiště.
- **Klíče spravované zákazníkem.** Volitelně můžete zvolit správu šifrovacích klíčů pro váš účet úložiště. Klíče spravované zákazníkem musí být uloženy v Azure Key Vault.

Navíc můžete zadat šifrovací klíč na úrovni individuální žádosti pro některé operace BLOB Storage. Při zadání šifrovacího klíče v žádosti tento klíč přepíše šifrovací klíč, který je aktivní v účtu úložiště. Další informace najdete v tématu [určení klíče poskytnutého zákazníkem v požadavku na úložiště objektů BLOB](../blobs/storage-blob-customer-provided-key.md).

Další informace o šifrovacích klíčích najdete v tématu [Azure Storage Encryption for data v klidovém umístění](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Ověřte model šifrovacího klíče pro účet úložiště.

K určení, jestli účet úložiště používá pro šifrování klíče spravované Microsoftem nebo klíče spravované zákazníkem, použijte jeden z následujících přístupů.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

Pokud chcete ověřit model šifrování pro účet úložiště pomocí Azure Portal, postupujte podle těchto kroků:

1. Na portálu Azure Portal přejděte k účtu úložiště.
1. Vyberte nastavení **šifrování** a poznamenejte si nastavení.

Následující obrázek ukazuje účet úložiště, ve kterém se klíče spravované zákazníkem používají pro šifrování:

![Snímek obrazovky s nastavením šifrovacího klíče v Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Pokud chcete ověřit model šifrování pro účet úložiště pomocí PowerShellu, zavolejte příkaz [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) a potom zkontrolujte vlastnost **source** daného účtu.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Pokud je hodnota vlastnosti **zdroje** `Microsoft.Storage`, je účet zašifrovaný pomocí klíčů spravovaných Microsoftem. Pokud je hodnota vlastnosti **zdroje** `Microsoft.Keyvault`, je účet zašifrovaný pomocí klíčů spravovaných zákazníkem.

# <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

Pokud chcete ověřit model šifrování pro účet úložiště pomocí rozhraní příkazového řádku Azure, zavolejte příkaz [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) a pak zkontrolujte vlastnost **source** účtu.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Pokud je hodnota vlastnosti **zdroje** `Microsoft.Storage`, je účet zašifrovaný pomocí klíčů spravovaných Microsoftem. Pokud je hodnota vlastnosti **zdroje** `Microsoft.Keyvault`, je účet zašifrovaný pomocí klíčů spravovaných zákazníkem.

---

## <a name="next-steps"></a>Další kroky

[Azure Storage šifrování dat v klidovém umístění](storage-service-encryption.md)
