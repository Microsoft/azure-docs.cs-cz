---
title: Použití Azure import/export k přenosu dat do služby soubory Azure | Microsoft Docs
description: Naučte se vytvářet úlohy importu v Azure Portal pro přenos dat do souborů Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 44473efbfb1c07c628c939fd05805ed92e691736
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651813"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Import dat do služby Soubory Azure pomocí služby Azure Import/Export

Tento článek poskytuje podrobné pokyny, jak pomocí služby importu a exportu v Azure bezpečně importovat velké objemy dat do souborů Azure. K importu dat služba vyžaduje, abyste dodali podporované diskové jednotky obsahující vaše data do datacentra Azure.

Služba import/export podporuje pouze import souborů Azure do Azure Storage. Export souborů Azure se nepodporuje.

## <a name="prerequisites"></a>Požadavky

Před vytvořením úlohy importu pro přenos dat do souborů Azure pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí. Musíte:

- Mít aktivní předplatné Azure pro použití se službou Import/export.
- Mít aspoň jeden Azure Storage účet. Podívejte se na seznam [podporovaných účtů úložiště a typů úložiště pro službu import/export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
- Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
- Má systém Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).
- [Stáhněte si WAImportExport verze 2](https://aka.ms/waiev2) v systému Windows. Rozbalte do výchozí složky `waimportexport` . Například, `C:\WaImportExport`.
- Mít účet FedEx/DHL. Pokud chcete použít nosný operátor jiný než FedEx/DHL, obraťte se na Azure Data Box provozní tým na adrese `adbops@microsoft.com` .
    - Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení expedice.
    - Vygenerujte sledovací číslo pro úlohu exportu.
    - Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným sledovacím číslem se nepodporuje.
    - Pokud nemáte účet dopravce, přečtěte si:
        - [Vytvořte účet FedEx](https://www.fedex.com/en-us/create-account.html)nebo
        - [Vytvořte účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotek

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako jsou sériové číslo jednotky, šifrovací klíč a podrobnosti účtu úložiště.

K přípravě jednotek proveďte následující kroky.

1. Připojte naše diskové jednotky k systému Windows přes konektory SATA.
2. Na každé jednotce vytvořte jeden svazek NTFS. Přiřaďte ke svazku písmeno jednotky. Nepoužívejte mountpoints.
3. Upravte soubor *dataset.csv* v kořenové složce, ve které se nástroj nachází. V závislosti na tom, jestli chcete importovat soubor nebo složku nebo obojí, přidejte do souboru *dataset.csv* položky podobné následujícím příkladům.

   - **Import souboru**: v následujícím příkladu se data ke zkopírování nacházejí v jednotce F:. Soubor *MyFile1.txt*  je zkopírován do kořenového adresáře *MyAzureFileshare1*. Pokud *MyAzureFileshare1* neexistuje, vytvoří se v účtu Azure Storage. Struktura složek je zachována.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Import složky**: všechny soubory a složky v *MyFolder2* jsou rekurzivně zkopírovány do sdílení souborů. Struktura složek je zachována.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Ve stejném souboru, který odpovídá složkám nebo souborům, které jsou importovány, lze vytvořit více položek.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Přečtěte si další informace o [přípravě souboru CSV datové sady](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Upravte soubor *driveset.csv* v kořenové složce, ve které se nástroj nachází. Přidejte položky do souboru *driveset.csv* podobně jako v následujících příkladech. Soubor driveset obsahuje seznam disků a odpovídajících písmen jednotek, aby nástroj mohl správně vybrat seznam disků, které mají být připraveny.

    V tomto příkladu se předpokládá, že jsou připojené dva disky a základní svazky NTFS G:\ a H:\ jsou vytvořeny. H:\is není zašifrováno, zatímco G: je již zašifrováno. Nástroj formátuje a šifruje disk, který je hostitelem H:\ pouze (a ne G: \) .

   - **Pro disk, který není zašifrovaný**: Určete *šifrování* , aby se na disku povolilo šifrování bitlockerem.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Pro disk, který je už zašifrovaný**: zadejte *AlreadyEncrypted* a zadejte klíč BitLockeru.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Ve stejném souboru, který odpovídá více jednotkám, lze vytvořit více položek. Přečtěte si další informace o [přípravě souboru CSV driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Použijte `PrepImport` možnost kopírování a Příprava dat na diskovou jednotku. Pro první relaci kopírování pro kopírování adresářů nebo souborů s novou relací kopírování spusťte následující příkaz:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Příklad importu je uveden níže.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Soubor deníku s názvem, který jste zadali s `/j:` parametrem, je vytvořen pro každé spuštění příkazového řádku. Každá jednotka, kterou připravíte, má soubor deníku, který se musí nahrát při vytváření úlohy importu. Jednotky bez souborů deníku nejsou zpracovány.

    > [!IMPORTANT]
    > - Po dokončení přípravy disku neměňte data na diskových jednotkách ani v souboru deníku.

Další ukázky najdete v [ukázkách pro soubory deníku](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: vytvoření úlohy importu

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Provedením následujících kroků vytvořte v Azure Portal úlohu importu.
1. Přihlaste se k https://portal.azure.com/ .
2. Vyhledejte **úlohy import/export**.

    ![Vyhledat úlohy importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Vyberte **+ Nový**.

    ![Pokud chcete vytvořit nový, vyberte nový. ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **Základní informace**:

    - Vyberte **importovat do Azure**.
    - Zadejte popisný název úlohy importu. Pomocí tohoto názvu můžete sledovat vaše úlohy, když probíhá, a až se dokončí.
        -  Tento název může obsahovat jenom malá písmena, číslice, spojovníky a podtržítka.
        -  Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Vyberte skupinu prostředků.

        ![Vytvořit úlohu importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

3. V **podrobnostech úlohy**:

    - Nahrajte soubory deníku, které jste vytvořili v předchozím [kroku 1: Příprava jednotek](#step-1-prepare-the-drives).
    - Vyberte účet úložiště, do kterého se budou data importovat.
    - Umístění dropoff se automaticky vyplní podle oblasti vybraného účtu úložiště.

       ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

4. V **informace o expedici zpět**:

    - V rozevíracím seznamu vyberte přepravce. Pokud chcete použít operátor jiného než FedEx/DHL, vyberte z rozevíracího seznamu existující možnost. Kontaktujte Azure Data Box provozní tým `adbops@microsoft.com`  s informacemi, které se týkají přepravce, kterého plánujete použít.
    - Zadejte platné číslo účtu dopravce, který jste vytvořili pomocí tohoto dopravce. Společnost Microsoft používá tento účet k dodávání jednotek zpátky po dokončení úlohy importu.
    - Zadejte celé a platné kontaktní jméno, telefonní číslo, e-mail, ulici, město, PSČ, kraj a zemi/oblast.

        > [!TIP]
        > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

       ![Vytvořit úlohu importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)


5. V **souhrnu**:

    - Poskytněte dodací adresu datacentra Azure pro expedici disků zpátky do Azure. Ujistěte se, že název úlohy a úplná adresa jsou uvedené na štítku pro expedici.
    - Vytvoření úlohy importu dokončíte kliknutím na **OK** .

        ![Vytvořit úlohu importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujících kroků vytvořte v Azure CLI úlohu importu.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Vytvoření úlohy

1. Pomocí příkazu [AZ Extension Add](/cli/azure/extension#az_extension_add) přidejte rozšíření [AZ import-export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Můžete použít existující skupinu prostředků nebo ji vytvořit. Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Můžete použít existující účet úložiště nebo ho vytvořit. Pokud chcete vytvořit účet úložiště, spusťte příkaz [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Seznam umístění, do kterých můžete dodávat disky, získáte pomocí příkazu [AZ import-export Location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. K získání umístění pro vaši oblast použijte příkaz [AZ import-export Location show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) :

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Spusťte následující příkaz [AZ import-export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) a vytvořte úlohu importu:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.


1. Pomocí příkazu [AZ import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) zobrazíte všechny úlohy pro skupinu prostředků myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Chcete-li aktualizovat úlohu nebo zrušit úlohu, spusťte příkaz [AZ import-export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pomocí následujících kroků vytvořte v Azure PowerShell úlohu importu.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> I když je modul PowerShell **AZ. ImportExport** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Vytvoření úlohy

1. Můžete použít existující skupinu prostředků nebo ji vytvořit. Pokud chcete vytvořit skupinu prostředků, spusťte rutinu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Můžete použít existující účet úložiště nebo ho vytvořit. Pokud chcete vytvořit účet úložiště, spusťte rutinu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) :

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Chcete-li získat seznam umístění, do kterých lze dodávat disky, použijte rutinu [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Pomocí `Get-AzImportExportLocation` rutiny s `Name` parametrem Získejte umístění pro vaši oblast:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Spuštěním následujícího příkladu [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) vytvořte úlohu importu:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
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
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

1. Pomocí rutiny [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) zobrazte všechny úlohy pro skupinu prostředků myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Pokud chcete aktualizovat úlohu nebo zrušit úlohu, spusťte rutinu [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: odeslání jednotek do datacentra Azure

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: aktualizace úlohy pomocí informací o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: ověření nahrávání dat do Azure

Sledujte úlohu k dokončení. Po dokončení úlohy ověřte, že se data nahrála do Azure. Místní data odstraňte až po ověření úspěšného odeslání.

## <a name="samples-for-journal-files"></a>Ukázky pro soubory deníku

Chcete-li **Přidat další jednotky**, vytvořte nový soubor driveset a spusťte příkaz následujícím způsobem.

Pro následné relace kopírování na jiné diskové jednotky, než je určeno v souboru *InitialDriveset. csv* , zadejte nový soubor driveset *. csv* a poskytněte ho jako hodnotu parametru `AdditionalDriveSet` . Použijte **stejný název souboru deníku** a zadejte **nové ID relace**. Formát souboru CSV AdditionalDriveset je stejný jako formát InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Příklad importu je uveden níže.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Chcete-li přidat další data do stejného driveset, použijte příkaz PrepImport pro následné relace kopírování ke zkopírování dalších souborů/adresářů.

Pro následné relace kopírování na stejné jednotky pevného disku zadané v souboru *InitialDriveset.csv* zadejte **stejný název souboru deníku** a zadejte **nové ID relace**. není nutné zadávat klíč účtu úložiště.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Příklad importu je uveden níže.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Další kroky

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na import/export](storage-import-export-requirements.md)