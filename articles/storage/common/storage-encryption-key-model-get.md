---
title: Určení modelu šifrovacího klíče se používá pro účet úložiště
titleSuffix: Azure Storage
description: Pomocí portálu Azure, PowerShellu nebo Azure CLI zkontrolujte, jak se spravují šifrovací klíče pro účet úložiště. Klíče mohou spravovat společnost Microsoft (výchozí) nebo zákazník. Klíče spravované zákazníkem musí být uloženy v trezoru klíčů Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409847"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Určení modelu šifrovacího klíče azure storage se používá pro účet úložiště

Data ve vašem účtu úložiště se automaticky šifrují službou Azure Storage. Šifrování Azure Storage nabízí dvě možnosti správy šifrovacích klíčů na úrovni účtu úložiště:

- **Klíče spravované společností Microsoft.** Ve výchozím nastavení spravuje společnost Microsoft klíče používané k šifrování účtu úložiště.
- **Klíče spravované zákazníkem.** Volitelně můžete zvolit správu šifrovacích klíčů pro váš účet úložiště. Klíče spravované zákazníkem musí být uloženy v trezoru klíčů Azure.

Kromě toho můžete poskytnout šifrovací klíč na úrovni individuální požadavek pro některé operace úložiště objektů blob. Pokud je v požadavku zadán šifrovací klíč, tento klíč přepíše šifrovací klíč, který je aktivní v účtu úložiště. Další informace naleznete [v tématu Určení klíče poskytnutého zákazníkem na žádost o úložiště objektů Blob](../blobs/storage-blob-customer-provided-key.md).

Další informace o šifrovacích klíčích najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Kontrola modelu šifrovacího klíče pro účet úložiště

Chcete-li zjistit, zda účet úložiště používá klíče spravované společností Microsoft nebo klíče spravované zákazníkem pro šifrování, použijte jeden z následujících přístupů.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

Chcete-li zkontrolovat model šifrování pro účet úložiště pomocí portálu Azure, postupujte takto:

1. Na portálu Azure Portal přejděte k účtu úložiště.
1. Vyberte nastavení **Šifrování** a poznamenejte si to.

Následující obrázek znázorňuje účet úložiště, který je šifrován pomocí klíčů spravovaných společností Microsoft:

![Zobrazit účet zašifrovaný pomocí klíčů spravovaných společností Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

A následující obrázek ukazuje účet úložiště, který je šifrován pomocí klíčů spravovaných zákazníkem:

![Snímek obrazovky s nastavením šifrovacího klíče na Webu Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li zkontrolovat model šifrování pro účet úložiště pomocí prostředí PowerShell, zavolejte příkaz [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) a zkontrolujte vlastnost **KeySource** pro účet.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Pokud je hodnota **vlastnosti KeySource** , je `Microsoft.Storage`účet zašifrován pomocí klíčů spravovaných společností Microsoft. Pokud je hodnota **Vlastnosti KeySource** , je `Microsoft.Keyvault`účet zašifrován pomocí klíčů spravovaných zákazníkem.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Chcete-li zkontrolovat model šifrování pro účet úložiště pomocí azure CLI, zavolejte příkaz [az úložiště show](/cli/azure/storage/account#az-storage-account-show) a pak zkontrolujte vlastnost **keySource** pro účet.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Pokud je hodnota vlastnosti `Microsoft.Storage` **keySource** , je účet zašifrován pomocí klíčů spravovaných společností Microsoft. Pokud je `Microsoft.Keyvault`hodnota vlastnosti **keySource** , je účet zašifrován pomocí klíčů spravovaných zákazníkem.

---

## <a name="next-steps"></a>Další kroky

- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md)
- [Správa šifrování Azure Storage pomocí azure key vaultu pomocí klíčů Azure Key Vault](encryption-customer-managed-keys.md)