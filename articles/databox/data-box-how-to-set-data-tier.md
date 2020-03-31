---
title: Odesílání dat na horkou, studenou, archivní úroveň objektů blob přes Azure Data Box/Azure Data Box Heavy
description: Popisuje, jak pomocí Azure Data Box nebo Azure Data Box Heavy odesílat data do příslušné úrovně úložiště objektů blob bloku, jako je horká, studená nebo archivní
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560367"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Použití Azure Data Box nebo Azure Data Box Heavy k odesílání dat na příslušnou úroveň objektů blob úložiště Azure

Azure Data Box přesune velké množství dat do Azure tak, že vám doručuje proprietární úložné zařízení. Naplníte zařízení daty a vrátíte je. Data z Data Box se nahrají na výchozí úroveň přidruženou k účtu úložiště. Potom můžete přesunout data do jiné vrstvy úložiště.

Tento článek popisuje, jak data, která je odeslána data box lze přesunout do hot, studené nebo archivní objekt blob vrstvy. Tento článek se vztahuje na Azure Data Box a Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Výběr správné úrovně úložiště pro vaše data

Azure storage umožňuje tři různé úrovně pro ukládání dat nákladově nejefektivnějším způsobem – hot, cold nebo archive. Úroveň horkého úložiště je optimalizována pro ukládání dat, ke kterým se často přistupuje. Úložiště za tepla má vyšší náklady na úložiště než úložiště Cool a Archive, ale nejnižší náklady na přístup.

Úroveň úložiště Cool je pro zřídka přístupná data, která je třeba uložit po dobu minimálně 30 dnů. Náklady na úložiště pro studenou vrstvu jsou nižší než u úrovně horkého úložiště, ale poplatky za přístup k datům jsou vysoké ve srovnání s úrovní Hot.

Úroveň Azure Archive je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup. Tato úroveň je určena pro data, která zůstávají v archivním úložišti po dobu minimálně 180 dnů. Podrobnosti o každé z těchto úrovní a cenový model najdete v [porovnání úrovní úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Data z datové schránky nebo datové schránky Těžké se nahrají do vrstvy úložiště, která je přidružena k účtu úložiště. Při vytváření účtu úložiště můžete zadat úroveň přístupu jako horkou nebo studenou. V závislosti na přístupu vašeho pracovního vytížení a nákladů můžete tato data přesunout z výchozí vrstvy na jinou úroveň úložiště.

Můžete pouze vrstvy dat úložiště objektů v úložišti objektů blob nebo pro obecné účely v2 (GPv2) účty. Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Pokud chcete vybrat správnou úroveň úložiště pro vaše data, přečtěte si důležité informace podrobně popsané v [úložišti objektů Blob Azure: Úrovně úložiště Premium, Hot, Cool a Archiv .](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

## <a name="set-a-default-blob-tier"></a>Nastavení výchozí vrstvy objektu blob

Výchozí vrstva objektu blob se zaznamená při vytvoření účtu úložiště na webu Azure Portal. Jakmile je typ úložiště vybrán jako úložiště GPv2 nebo Blob, lze zadat atribut vrstvy Access. Ve výchozím nastavení je vybrána úroveň Hot.

Úrovně nelze zadat, pokud se pokoušíte vytvořit nový účet při objednávání datové schránky nebo datové schránky Heavy. Po vytvoření účtu můžete upravit účet na portálu a nastavit výchozí úroveň přístupu.

Případně nejprve vytvoříte účet úložiště s zadaným atributem přístupové vrstvy. Při vytváření objednávky Data Box nebo Data Box Heavy vyberte existující účet úložiště. Další informace o tom, jak nastavit výchozí vrstvu objektu blob během vytváření účtu úložiště, najdete v [části Vytvoření účtu úložiště na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Přesunutí dat na nevýchozí úroveň

Po nahrání dat ze zařízení Data Box na výchozí úroveň můžete chtít přesunout data na jiné než výchozí úroveň. Tato data lze přesunout na úroveň, která není výchozí, dva způsoby.

- **Správa životního cyklu úložiště objektů blob Azure** – pomocí přístupu založeného na zásadách můžete automaticky vrstvit data nebo vypršení jejich platnosti na konci jeho životního cyklu. Další informace najdete v části [Správa životního cyklu úložiště objektů blob Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skriptování** – můžete použít skriptovaný přístup přes Azure PowerShell k povolení vrstvení na úrovni objektu blob. Můžete volat `SetBlobTier` operaci nastavit vrstvu na objekt blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Nastavení vrstvy objektu blob pomocí Azure PowerShellu

Následující kroky popisují, jak můžete nastavit vrstvu objektu blob na Archiv pomocí skriptu Azure PowerShell.

1. Otevřete relaci prostředí Windows PowerShell se zvýšenými oprávněními. Ujistěte se, že vaše spuštěné Prostředí PowerShell 5.0 nebo vyšší. Zadejte:

   `$PSVersionTable.PSVersion`     

2. Přihlaste se k Azure PowerShellu. 

   `Login-AzAccount`  

3. Definujte proměnné pro účet úložiště, přístupový klíč, kontejner a kontext úložiště.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Získejte všechny kuličky v kontejneru.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Nastavte úroveň všech objektů BLOB v kontejneru na Archivovat.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Ukázkový výstup je uveden níže:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Pokud chcete, aby se data archivovala při ingestování, nastavte výchozí úroveň účtu na Horko. Pokud je výchozí úroveň Cool, pak je 30-denní předčasné odstranění trest, pokud data přesune do archivu okamžitě.

## <a name="next-steps"></a>Další kroky

-  Zjistěte, jak řešit [běžné scénáře vrstvení dat pomocí pravidel zásad životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

