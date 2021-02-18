---
title: Export dat z objektů blob Azure pomocí Azure import/export | Microsoft Docs
description: Naučte se vytvářet úlohy exportu v Azure Portal pro přenos dat z objektů blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8ccc7b641e2bfcb4ea8733b9d4f793229c430bc0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652861"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Službu Azure Import/Export můžete použít k exportu dat z úložiště objektů blob v Azure.

Tento článek poskytuje podrobné pokyny, jak pomocí služby importu a exportu Azure bezpečně exportovat velké objemy dat z úložiště objektů BLOB v Azure. Služba vyžaduje, abyste dodali prázdné jednotky v datacentru Azure. Služba exportuje data z vašeho účtu úložiště na jednotky a pak dohraje jednotky zpátky.

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohu exportu pro přenos dat z Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadavků pro tuto službu.
Musíte:

- Mít aktivní předplatné Azure, které se dá použít pro službu import/export.
- Mít aspoň jeden Azure Storage účet. Podívejte se na seznam [podporovaných účtů úložiště a typů úložiště pro službu import/export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
- Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
- Mít účet FedEx/DHL. Pokud chcete použít nosný operátor jiný než FedEx/DHL, obraťte se na Azure Data Box provozní tým na adrese `adbops@microsoft.com` .
  - Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení expedice.
  - Vygenerujte sledovací číslo pro úlohu exportu.
  - Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným sledovacím číslem se nepodporuje.
  - Pokud nemáte účet dopravce, přečtěte si:
    - [Vytvořte účet FedEx](https://www.fedex.com/en-us/create-account.html)nebo
    - [Vytvořte účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1: vytvoření úlohy exportu

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li vytvořit úlohu exportu v Azure Portal, proveďte následující kroky.

1. Přihlaste se k <https://portal.azure.com/> .
2. Vyhledejte **úlohy import/export**.

    ![Vyhledat úlohy importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Vyberte **+ Nový**.

    ![Vyberte + nová a vytvořte novou. ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **Základní informace**:

    - Vyberte **exportovat z Azure**.
    - Zadejte popisný název úlohy exportu. Použijte název, který zvolíte ke sledování průběhu úloh.
        - Název může obsahovat jenom malá písmena, číslice, spojovníky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.

        ![Základy](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. V **podrobnostech úlohy**:

    - Vyberte účet úložiště, ve kterém se nacházejí data, která se mají exportovat. Na místě, kde se nacházíte, použijte účet úložiště.
    - Umístění dropoff se automaticky vyplní podle oblasti vybraného účtu úložiště.
    - Určete data objektu blob, která chcete exportovat z účtu úložiště, na svou prázdnou jednotku nebo jednotky.
    - Vyberte, chcete-li **exportovat všechna** data objektů BLOB v účtu úložiště.

         ![Exportovat vše](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - Můžete určit kontejnery a objekty blob k exportu.
        - **Chcete-li určit objekt BLOB k exportu**: použijte selektor se **stejnou hodnotou** . Zadejte relativní cestu k objektu BLOB počínaje názvem kontejneru. Pro určení kořenového kontejneru použijte *$root* .
        - **Zadání všech objektů BLOB počínaje předponou**: použijte **začátek s** selektorem. Zadejte předponu začínající lomítkem (/). Prefixem může být Předpona názvu kontejneru, úplný název kontejneru nebo úplný název kontejneru následovaný prefixem názvu objektu BLOB. Aby se předešlo chybám při zpracování, jak je znázorněno na tomto snímku obrazovky, je nutné zadat cesty objektů BLOB v platném formátu. Další informace najdete v tématu [Příklady platných cest objektů BLOB](#examples-of-valid-blob-paths).

           ![Exportovat vybrané kontejnery a objekty blob](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Exportovat můžete ze souboru seznamu objektů BLOB.

        ![Exportovat ze souboru seznamu objektů BLOB](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Pokud se objekt blob, který se má exportovat, používá během kopírování dat, služba Azure import/export pořizuje snímek objektu BLOB a zkopíruje snímek.

6. V **informace o expedici zpět**:

    - V rozevíracím seznamu vyberte přepravce. Pokud chcete použít operátor jiného než FedEx/DHL, vyberte z rozevíracího seznamu existující možnost. Kontaktujte Azure Data Box provozní tým `adbops@microsoft.com`  s informacemi, které se týkají přepravce, kterého plánujete použít.
    - Zadejte platné číslo účtu dopravce, který jste vytvořili pomocí tohoto dopravce. Společnost Microsoft používá tento účet k dodávání jednotek zpátky po dokončení úlohy exportu.
    - Zadejte celé a platné kontaktní jméno, telefonní číslo, e-mail, ulici, město, PSČ, kraj a zemi/oblast.

        > [!TIP]
        > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

7. V **souhrnu**:

    - Zkontrolujte podrobnosti úlohy.
    - Poznamenejte si název úlohy a zadáte dodací adresu Azure datacentra pro expedici disků do Azure.

        > [!NOTE]
        > Disky vždy odešlete do datového centra, které jste si poznamenali v Azure Portal. Pokud jsou disky dodávány do špatného datového centra, úloha nebude zpracována.

    - Kliknutím na tlačítko **OK** dokončete vytváření úlohy exportu.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujících kroků můžete vytvořit úlohu exportu v Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Vytvoření úlohy

1. Pomocí příkazu [AZ Extension Add](/cli/azure/extension#az_extension_add) přidejte rozšíření [AZ import-export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Seznam umístění, ze kterých můžete přijímat disky, získáte pomocí příkazu [AZ import-export Location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Spusťte následující příkaz [AZ import-export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) a vytvořte úlohu exportu, která používá váš stávající účet úložiště:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

   Tato úloha exportuje všechny objekty BLOB v účtu úložiště. Objekt BLOB pro export můžete zadat nahrazením této hodnoty pro **--Export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Tato hodnota parametru exportuje objekt BLOB s názvem *logo.bmp* v kořenovém kontejneru.

   Máte také možnost vybrat všechny objekty BLOB v kontejneru pomocí předpony. Nahraďte tuto hodnotu pro **--Export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Další informace najdete v tématu [Příklady platných cest objektů BLOB](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Pokud se objekt blob, který se má exportovat, používá během kopírování dat, služba Azure import/export pořizuje snímek objektu BLOB a zkopíruje snímek.

1. Pomocí příkazu [AZ import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) zobrazíte všechny úlohy pro skupinu prostředků myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Chcete-li aktualizovat úlohu nebo zrušit úlohu, spusťte příkaz [AZ import-export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pomocí následujících kroků můžete vytvořit úlohu exportu v Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> I když je modul PowerShell **AZ. ImportExport** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Vytvoření úlohy

1. Pokud chcete získat seznam umístění, ze kterých můžete přijímat disky, použijte rutinu [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Spuštěním následujícího příkladu [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) vytvořte úlohu exportu, která používá váš stávající účet úložiště:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

   Tato úloha exportuje všechny objekty BLOB v účtu úložiště. Objekt BLOB pro export můžete zadat nahrazením této hodnoty pro **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Tato hodnota parametru exportuje objekt BLOB s názvem *logo.bmp* v kořenovém kontejneru.

   Máte také možnost vybrat všechny objekty BLOB v kontejneru pomocí předpony. Tuto hodnotu nahraďte parametrem **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Další informace najdete v tématu [Příklady platných cest objektů BLOB](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Pokud se objekt blob, který se má exportovat, používá během kopírování dat, služba Azure import/export pořizuje snímek objektu BLOB a zkopíruje snímek.

1. Pomocí rutiny [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) zobrazte všechny úlohy pro skupinu prostředků myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Pokud chcete aktualizovat úlohu nebo zrušit úlohu, spusťte rutinu [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Krok 2: dodání jednotek

Pokud nevíte, kolik jednotek potřebujete, použijte [kontrolu počtu jednotek](#check-the-number-of-drives). Pokud znáte počet jednotek, pokračujte v dodávání jednotek.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: aktualizace úlohy pomocí informací o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Krok 4: přijetí disků

Když se řídicí panel dokončí, dokončí se vám disky a na portálu jsou k dispozici sledovací číslo pro expedici.

1. Jakmile obdržíte jednotky s exportovanými daty, je třeba získat klíče nástroje BitLocker k odemknutí jednotek. Přejít na úlohu exportu v Azure Portal. Klikněte na kartu **Import/export** .
2. V seznamu vyberte a klikněte na úlohu exportu. Přejít na **šifrování** a zkopírujte klíče.

   ![Zobrazit klíče nástroje BitLocker pro úlohu exportu](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. K odemknutí disků použijte klíče nástroje BitLocker.

Export byl dokončen.

## <a name="step-5-unlock-the-disks"></a>Krok 5: odemknutí disků

K odemknutí jednotky použijte následující příkaz:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Tady je příklad ukázkového vstupu.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

V tuto chvíli můžete úlohu odstranit nebo ji nechat opustit. Úlohy se automaticky odstraní po 90 dnech.

## <a name="check-the-number-of-drives"></a>Kontrolovat počet jednotek

Tento *volitelný* krok vám pomůže určit počet jednotek vyžadovaných pro úlohu exportu. Tento krok proveďte v systému Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).

1. [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/download/details.aspx?id=42659) v systému Windows.
2. Rozbalte do výchozí složky `waimportexportv1` . Například, `C:\WaImportExportV1`.
3. Otevřete okno PowerShellu nebo příkazového řádku s oprávněními správce. Chcete-li změnit adresář na složku unzip, spusťte následující příkaz:

   `cd C:\WaImportExportV1`

4. Pokud chcete zjistit počet disků vyžadovaných pro vybrané objekty blob, spusťte následující příkaz:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry jsou popsány v následující tabulce:

    |Parametr příkazového řádku|Description|
    |--------------------------|-----------------|
    |**/logdir:**|Nepovinný parametr. Adresář protokolu. Podrobné soubory protokolu se zapisují do tohoto adresáře. Pokud tento parametr nezadáte, použije se jako adresář protokolu aktuální adresář.|
    |**SN**|Povinná hodnota. Název účtu úložiště pro úlohu exportu|
    |**SM**|Požadováno jenom v případě, že není zadané SAS kontejneru. Klíč účtu pro účet úložiště pro úlohu exportu|
    |**/csas:**|Požadováno jenom v případě, že není zadaný klíč účtu úložiště. SAS kontejneru pro výpis objektů blob, které se mají exportovat v úloze exportu|
    |**/ExportBlobListFile:**|Povinná hodnota. Cesta k souboru XML obsahujícímu seznam cest objektů BLOB nebo předpony cesty objektů BLOB pro objekty blob, které se mají exportovat Formát souboru, který se používá v `BlobListBlobPath` prvku v operaci [Put úlohy](/rest/api/storageimportexport/jobs) služby Import/export REST API.|
    |**/DriveSize:**|Povinná hodnota. Velikost jednotek, které se mají použít pro úlohu exportu, *např.* 500 GB, 1,5 TB.|

    Podívejte se [na příklad příkazu PreviewExport](#example-of-previewexport-command).

5. Ověřte, zda můžete číst a zapisovat na jednotky, které budou odeslány pro úlohu exportu.

### <a name="example-of-previewexport-command"></a>Příklad příkazu PreviewExport

Následující příklad demonstruje `PreviewExport` příkaz:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Soubor výpisu objektů BLOB může obsahovat názvy objektů BLOB a předpony objektů blob, jak je znázorněno zde:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Nástroj pro import/export v Azure vypíše všechny objekty blob, které se mají exportovat, a vypočítá, jak se mají zabalit na jednotky zadané velikosti, přičemž se vezme v úvahu veškerá potřebná režie a pak vyhodnotí počet jednotek potřebných k uložení objektů BLOB a informace o využití jednotky.

Tady je příklad výstupu s informačními protokoly vynechánými:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Příklady platných cest objektů BLOB

V následující tabulce jsou uvedeny příklady platných cest objektů BLOB:

   | Volič | Cesta objektu BLOB | Description |
   | --- | --- | --- |
   | Začíná na |/ |Exportuje všechny objekty BLOB v účtu úložiště. |
   | Začíná na |/$root/ |Exportuje všechny objekty BLOB v kořenovém kontejneru. |
   | Začíná na |/book |Vyexportuje všechny objekty BLOB v jakémkoli kontejneru, který začíná prefixovou **knihou** . |
   | Začíná na |skladby |Exportuje všechny objekty BLOB v kontejneru **hudba** . |
   | Začíná na |/music/love |Exportuje všechny objekty BLOB v kontejneru s **hudbou** , které začínají na **skvělém** čísle. |
   | Je rovno |$root/logo.bmp |Export objektu BLOB **logo.bmp** do kořenového kontejneru |
   | Je rovno |videa/story.mp4 |Exportuje **story.mp4** objektů BLOB ve **videích** o kontejnerech. |

## <a name="next-steps"></a>Další kroky

- [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
- [Kontrola požadavků na import/export](storage-import-export-requirements.md)
