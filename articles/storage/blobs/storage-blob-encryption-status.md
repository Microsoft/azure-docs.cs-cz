---
title: Kontrola stavu šifrování objektu blob – Azure Storage
description: Zjistěte, jak pomocí azure portálu, PowerShellu nebo Azure CLI zkontrolovat, jestli je daný objekt blob šifrovaný. Pokud objekt blob není šifrovaný, přečtěte si, jak pomocí AzCopy vynutit šifrování stažením a opětovným nahráním objektu blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707599"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Kontrola stavu šifrování objektu blob

Každý objekt blob bloku, objekt blob připojení nebo objekt blob stránky, který byl zapsán do služby Azure Storage po 20. Objekty BLOB vytvořené před tímto datem jsou nadále šifrovány procesem na pozadí.

Tento článek ukazuje, jak zjistit, zda byl daný objekt blob zašifrován.

## <a name="check-a-blobs-encryption-status"></a>Kontrola stavu šifrování objektu blob

Pomocí portálu Azure, PowerShellu nebo Azure CLI určete, jestli je objekt blob zašifrovaný bez kódu.

### <a name="azure-portal"></a>[Portál Azure](#tab/portal)

Chcete-li pomocí portálu Azure zkontrolovat, jestli byl objekt blob zašifrovaný, postupujte takto:

1. Na portálu Azure Portal přejděte k účtu úložiště.
1. Výběrem **možnosti Kontejnery** přejdete na seznam kontejnerů v účtu.
1. Vyhledejte objekt blob a zobrazte jeho kartu **Přehled.**
1. Zobrazení vlastnosti **Server Encrypted.** Pokud **True**, jak je znázorněno na následujícím obrázku, pak objekt blob je zašifrována. Všimněte si, že vlastnosti objektu blob také zahrnují datum a čas, kdy byl objekt blob vytvořen.

    ![Snímek obrazovky znázorňující kontrolu služby Šifrovaná server na webu Azure Portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li pomocí prostředí PowerShell zkontrolovat, zda byl objekt blob zašifrován, zkontrolujte vlastnost **IsServerEncrypted objektu** blob. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Chcete-li zjistit, kdy byl objekt blob vytvořen, zkontrolujte hodnotu **vlastnosti Vytvořeno:**

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Chcete-li použít Azure CLI ke kontrole, zda byl objekt blob zašifrován, zkontrolujte vlastnost **IsServerEncrypted objektu** blob. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Chcete-li zjistit, kdy byl objekt blob vytvořen, zkontrolujte hodnotu **vytvořené** vlastnosti.

---

## <a name="force-encryption-of-a-blob"></a>Vynucení šifrování objektu blob

Pokud objekt blob, který byl vytvořen před 20 října 2017 ještě nebyl zašifrován procesem na pozadí, můžete vynutit šifrování dojít okamžitě stažením a znovu nahrát objekt blob. Jednoduchý způsob, jak to udělat, je s AzCopy.

Chcete-li stáhnout objekt blob do místního systému souborů pomocí aplikace AzCopy, použijte následující syntaxi:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Chcete-li objekt blob znovu nahrát do Služby Azure Storage pomocí azcopy, použijte následující syntaxi:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Další informace o kopírování dat objektů blob pomocí AzCopy naleznete v [tématu Přenos dat pomocí úložiště Objektů BloB](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Další kroky

[Šifrování Azure Storage pro data v klidovém stavu](../common/storage-service-encryption.md)
