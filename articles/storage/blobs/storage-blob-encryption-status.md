---
title: Ověřte stav šifrování objektu blob-Azure Storage
description: Naučte se pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI ověřit, jestli je zadaný objekt BLOB zašifrovaný. Pokud objekt BLOB není zašifrovaný, Naučte se používat AzCopy k vynucení šifrování stažením a znovu nahráním objektu BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878557"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Zkontroluje stav šifrování objektu BLOB.

Všechny objekty blob bloku, doplňovací objekty blob nebo objekty blob stránky, které byly zapsány do Azure Storage po 20. října 2017, jsou šifrovány pomocí šifrování Azure Storage. Objekty blob vytvořené před tímto datem budou i nadále zašifrovány procesem na pozadí.

V tomto článku se dozvíte, jak zjistit, jestli je zadaný objekt BLOB zašifrovaný.

## <a name="check-a-blobs-encryption-status"></a>Zkontroluje stav šifrování objektu BLOB.

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete zjistit, jestli je objekt BLOB zašifrovaný bez kódu.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Chcete-li použít Azure Portal ke kontrole, zda byl objekt BLOB zašifrován, postupujte podle následujících kroků:

1. Na portálu Azure Portal přejděte k účtu úložiště.
1. Vyberte **kontejnery** a přejděte na seznam kontejnerů v účtu.
1. Vyhledejte objekt BLOB a zobrazte kartu s **přehledem** .
1. Zobrazení vlastnosti **šifrovaného serveru** . Pokud je nastaveno na **true**, jak je znázorněno na následujícím obrázku, je objekt BLOB zašifrovaný. Všimněte si, že vlastnosti objektu BLOB obsahují také datum a čas vytvoření objektu BLOB.

    ![Snímek obrazovky ukazující, jak kontrolovat vlastnost šifrovanou pro server v Azure Portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete pomocí PowerShellu ověřit, jestli je objekt BLOB zašifrovaný, podívejte se na vlastnost **IsServerEncrypted** objektu BLOB. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Chcete-li zjistit, kdy byl objekt BLOB vytvořen, zkontrolujte hodnotu vlastnosti **Vytvořeno** :

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete pomocí Azure CLI ověřit, jestli je objekt BLOB zašifrovaný, podívejte se na vlastnost **IsServerEncrypted** objektu BLOB. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Chcete-li zjistit, kdy byl objekt BLOB vytvořen, zkontrolujte hodnotu vlastnosti **Vytvořeno** .

---

## <a name="force-encryption-of-a-blob"></a>Vynutit šifrování objektu BLOB

Pokud objekt blob, který se vytvořil před 20. října 2017, ještě není zašifrovaný procesem na pozadí, můžete vynutit, aby se šifrování nacházelo hned stažením a znovu nahráním objektu BLOB. To lze provést jednoduchým způsobem pomocí AzCopy.

K stažení objektu blob do místního systému souborů pomocí AzCopy použijte následující syntaxi:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Pokud chcete znovu nahrát objekt blob do Azure Storage pomocí AzCopy, použijte následující syntaxi:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Další informace o použití AzCopy ke kopírování dat objektů BLOB najdete v tématu [přenos dat pomocí služby AzCopy a BLOB Storage](../common/storage-use-azcopy-v10.md#transfer-data).

## <a name="next-steps"></a>Další kroky

[Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
