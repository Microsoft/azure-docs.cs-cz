---
title: Použití Azure Data Box pro odesílání dat do úroveň objektu blob bloku – horká, studená, archivní - | V datech Microsoft Docs
description: Popisuje, jak pomocí Azure Data Box odeslat data na úroveň úložiště objektů blob bloku odpovídající například horké, studené ani archivní úrovně
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: 91cabd6601ca8d0c0eabfdb5726d4bd6b37492a6
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54216140"
---
# <a name="use-azure-data-box-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Odešlete data do příslušné úrovně objektů blob Azure Storage pomocí Azure Data Box

Azure Data Box přesunu velkých objemů dat do Azure přenosem proprietární úložné zařízení. Vyplnit zařízení s daty a vrátí jej. Data ze zařízení Data Box nahraje do výchozí úroveň přidružené k účtu úložiště. Data pak můžete přesunout na jinou vrstvu úložiště.

Tento článek popisuje, jak můžete přesunout data, která je odeslaný zařízení Data Box do horké, studené nebo archivní úroveň objektu blob.  

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Zvolte úroveň správného úložiště dat

Azure storage umožňuje tři různé úrovně pro ukládání dat v nejvíce cenově výhodnou – horké, studené nebo archivní. Úroveň horkého úložiště je optimalizovaná pro ukládání dat, která se využívají často. Horké úložiště má vyšší náklady na úložiště než ze studené a archivní úložiště, ale nejnižší náklady na přístup.

Úroveň studeného úložiště je pro zřídka využívaná data, která musí být uloženy minimálně za 30 dnů. Náklady na úložiště pro studená vrstva je nižší než úroveň horkého úložiště, ale poplatky za přístup k data jsou vysoké ve srovnání s vrstvou Hot.

Úroveň archivu Azure je v režimu offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou v archivní úložiště pro nejméně na 180 dnů. Podrobnosti o každém z těchto úrovní a cenový model, v části [srovnání vrstev úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#comparison-of-the-storage-tiers).

Data ze zařízení Data Box nahraje do vrstvy úložiště, která je přidružená k účtu úložiště. Když vytvoříte účet úložiště, můžete jako horké nebo studené úrovně přístupu. V závislosti na vzory přístupu vašich úloh a náklady, můžete přesunout data z výchozí úroveň na jinou vrstvu úložiště.

Může jenom úroveň data v úložišti objektů Blob storage nebo obecné účely v2 (GPv2) účty. Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Vybrat úroveň správného úložiště dat, prostudujte si důležité informace, najdete v [úložiště objektů Blob v Azure: Premium, vrstvy úložiště Hot, Cool a archiv](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Nastavit výchozí úroveň objektu blob

Výchozí úroveň objektu blob je určen při vytvoření účtu úložiště na webu Azure Portal. Po výběru typu úložiště jako účty GPv2 nebo Blob storage, můžete zadat atribut úrovně přístupu. Ve výchozím nastavení je vybrané vrstvu Hot.

Vrstvy se nedá zadat, pokud se pokoušíte vytvořit nový účet při řazení zařízení Data Box. Po vytvoření účtu můžete upravit nastavení účtu na portálu nastavit výchozí úroveň přístupu.

Alternativně můžete vytvořit účet úložiště nejprve s atributem úroveň Zadaný přístupový. Při vytváření objednávka zařízení Data Box, vyberte existující účet úložiště. Další informace o tom, jak nastavit výchozí úroveň objektu blob během vytváření účtu úložiště, přejděte na [vytvořit účet úložiště na webu Azure portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Přesun dat do jiné než výchozí úroveň

Po nahrání dat ze zařízení Data Box na výchozí úroveň můžete přesunout data na jiné než výchozí úroveň. Existují dva způsoby, jak tato data přesunout na jiné než výchozí úroveň.

- **Správu životního cyklu úložiště Azure Blob** -přístup na základě zásad můžete použít k automatickému vrstvení dat nebo po ukončení životního cyklu. Další informace najdete v části [Správa životního cyklu úložiště objektů Blob v Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skriptování** -povolit ovládání datových vrstev na úrovni objektů blob můžete použít skriptované přístup přes Azure PowerShell. Můžete volat `SetBlobTier` operace u objektu blob nastavit úroveň.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Nastavit na úrovni objektů blob pomocí Azure Powershellu

Následující kroky popisují, jak můžete nastavit úroveň objektu blob do archivní úrovně pomocí skriptu prostředí Azure PowerShell.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními. Ujistěte se, že spuštěné Powershellu 5.0 nebo vyšší. Zadejte:

   `$PSVersionTable.PSVersion`     

2. Přihlaste se k Azure Powershellu. 

   `Login-AzureRmAccount`  

3. Definujte proměnné, které pro účet úložiště, přístupový klíč, kontejner a kontext úložiště.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Získání všech objektů BLOB v kontejneru.

    `$blobs = Get-AzureStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Nastavte úroveň objektu všechny objekty BLOB v kontejneru do archivní úrovně.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Ukázkový výstup najdete níž:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzureRmAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzureStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
> [!TIP]
> Pokud chcete data pro archivaci na ingestování, nastavte výchozí úroveň účtu na aktivní. Pokud je výchozí vrstvou Cool, pak je penále 30denní předčasné odstranění Pokud data přesune do archivní úrovně okamžitě.

## <a name="next-steps"></a>Další postup

-  Zjistěte, jak adresu [běžné scénáře vrstvení dat s pravidly zásad životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

