---
title: Posílání dat do horké, studené a archivní úrovně objektů BLOB přes Azure Data Box/Azure Data Box Heavy
description: Popisuje, jak pomocí Azure Data Box nebo Azure Data Box Heavy odesílat data do příslušné vrstvy úložiště objektů blob bloku, jako je Hot, studená nebo archivní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125060"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Pomocí Azure Data Box nebo Azure Data Box Heavy odesílat data do příslušné Azure Storage úrovně objektu BLOB

Azure Data Box do Azure přenáší velké objemy dat, a to prostřednictvím přípravního úložného zařízení. Zařízení naplníte daty a vrátíte ho. Data z Data Box se nahrají do výchozí úrovně přidružené k účtu úložiště. Data pak můžete přesunout do jiné úrovně úložiště.

Tento článek popisuje, jak mohou být data nahraná pomocí Data Box přesunuta do horké, studené nebo archivní úrovně objektu BLOB. Tento článek se týká Azure Data Box i Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Výběr správné úrovně úložiště pro vaše data

Azure Storage umožňuje třem různým vrstvám ukládat data v nákladově efektivním způsobu – horká, studená nebo archivní. Horká úroveň úložiště je optimalizovaná pro ukládání dat, ke kterým dochází často. Horké úložiště má vyšší náklady na úložiště než studené a archivní úložiště, ale nejnižší náklady na přístup.

Studená úroveň úložiště je pro zřídka používaná data, která je potřeba uložit aspoň po dobu 30 dnů. Náklady na úložiště pro studenou úroveň jsou nižší než u horké vrstvy úložiště, ale poplatky za přístup k datům jsou v porovnání s horkou úrovní vysoké.

Úroveň archivu Azure je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup. Tato úroveň je určená pro data, která zůstávají v archivním úložišti minimálně 180 dní. Podrobnosti o každé z těchto vrstev a cenového modelu najdete v [porovnání s vrstvami úložiště](../storage/blobs/storage-blob-storage-tiers.md).

Data z Data Box nebo Data Box Heavy se nahrají do vrstvy úložiště, která je přidružená k účtu úložiště. Když vytváříte účet úložiště, můžete nastavit úroveň přístupu jako horkou nebo studenou. V závislosti na vzoru přístupu ke svému zatížení a nákladům můžete přesunout tato data z výchozí úrovně na jinou vrstvu úložiště.

Data úložiště objektů můžete vrstvit jenom v účtech BLOB Storage nebo Pro obecné účely v2 (GPv2). Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Pokud si chcete vybrat správnou vrstvu úložiště pro vaše data, přečtěte si podrobné pokyny ve [službě Azure Blob Storage: úrovně Premium, Hot, studená a archivní](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Nastavení výchozí úrovně objektu BLOB

Výchozí úroveň objektu BLOB se určí při vytvoření účtu úložiště v Azure Portal. Po výběru typu úložiště jako GPv2 nebo BLOB Storage se dá zadat atribut úrovně přístupu. Ve výchozím nastavení je vybrána úroveň Hot.

Pokud se pokoušíte vytvořit nový účet při objednávání Data Box nebo Data Box Heavy, nelze vrstvy zadat. Po vytvoření účtu můžete změnit účet na portálu a nastavit výchozí úroveň přístupu.

Případně můžete vytvořit účet úložiště nejprve se zadaným atributem úrovně přístupu. Při vytváření Data Box nebo Data Box Heavy objednávky vyberte existující účet úložiště. Další informace o tom, jak nastavit výchozí úroveň objektu BLOB během vytváření účtu úložiště, najdete [v tématu Vytvoření účtu úložiště v Azure Portal](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Přesun dat do jiné než výchozí úrovně

Jakmile se data z Data Box zařízení nahrají do výchozí úrovně, možná budete chtít data přesunout do jiné než výchozí úrovně. Existují dva způsoby, jak přesunout tato data do jiné než výchozí úrovně.

- **Správa životního cyklu Azure Blob Storage** – k automatickému vynechání dat na úrovni nebo jejich vypršení platnosti můžete použít přístup založený na zásadách. Další informace najdete v [části Správa životního cyklu úložiště objektů BLOB v Azure](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Skriptování** – k povolení vrstvení na úrovni objektů blob můžete použít skriptový přístup prostřednictvím Azure PowerShell. Můžete zavolat `SetBlobTier` operaci pro nastavení vrstvy objektu BLOB.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Nastavení úrovně objektu BLOB pomocí Azure PowerShell

Následující kroky popisují, jak můžete nastavit, aby se vrstva objektů BLOB nastavila jako archivní pomocí skriptu Azure PowerShell.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními. Ujistěte se, že máte spuštěný PowerShell 5,0 nebo vyšší. Zadejte:

   `$PSVersionTable.PSVersion`     

2. Přihlaste se k Azure PowerShell. 

   `Login-AzAccount`  

3. Definujte proměnné pro účet úložiště, přístupový klíč, kontejner a kontext úložiště.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Načte všechny objekty BLOB v kontejneru.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Nastavte vrstvu všech objektů BLOB v kontejneru k archivaci.

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
   > Pokud chcete, aby se data archivovali při ingestování, nastavte výchozí úroveň účtu na Hot. Pokud je výchozí úroveň studená, pak při okamžitém přesunu dat do archivu dojde k ne30dennímu odstranění pokuty.

## <a name="next-steps"></a>Další kroky

-  Naučte se řešit [běžné scénáře datových vrstev pomocí pravidel zásad životního cyklu](../storage/blobs/storage-lifecycle-management-concepts.md#examples) .