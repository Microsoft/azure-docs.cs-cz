---
title: Přenos dat do služby soubory Azure pomocí Azure Import/Export | Dokumentace Microsoftu
description: Informace o vytvoření úlohy importu na webu Azure Portal k přenosu dat do služby soubory Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 09/10/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 1be7dd9ce537d0999ace01eafa46318e42df9f20
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732443"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Import dat do služby soubory Azure pomocí služby Azure Import/Export

Tento článek obsahuje podrobné pokyny o tom, jak bezpečně importovat velké objemy dat do soubory Azure pomocí služby Azure Import/Export. K importu dat, služba vyžaduje, aby dodat podporované disky obsahující data do datacentra Azure.  

Podporuje služba importu/exportu importovat pouze soubory Azure do služby Azure Storage. Export souborů Azure není podporován.

## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet úlohy importu přenášet data do soubory Azure, pečlivě zkontrolujte a dokončete následující seznam všech požadovaných součástí. Musíte mít:

- Máte aktivní předplatné Azure pomocí služby Import/Export.
- Máte alespoň jeden účet služby Azure Storage. Zobrazit seznam [nepodporuje účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [způsob vytvoření účtu úložiště](storage-quickstart-create-account.md).
- Používejte odpovídající počet disků [podporované typy](storage-import-export-requirements.md#supported-disks). 
- K dispozici systém Windows [verze operačního systému nepodporuje](storage-import-export-requirements.md#supported-operating-systems).
- [Stáhněte si WAImportExport verze 2](https://www.microsoft.com/download/details.aspx?id=55280) v systému Windows. Rozbalte do výchozí složky `waimportexport`. Například, `C:\WaImportExport`.
- Máte účet FedEx/DHL. 
    - Účet musí být platná, by měl mít vyrovnávání a musí mít vrácení funkce.
    - Generovat sledovací číslo pro úlohu exportu.
    - Každá úloha by měl mít samostatný sledovací číslo. Více úloh se stejným číslem sledování nejsou podporovány.
    - Pokud nemáte účet přepravce, přejděte na:
        - [Vytvoření účtu FedEX](https://www.fedex.com/en-us/create-account.html), nebo 
        - [Vytvoření účtu DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotky

Tento krok generuje soubor deníku. Soubor deníku obsahuje základní informace, jako je sériové číslo jednotky, šifrovací klíč a podrobnosti o účtu úložiště.

Proveďte následující kroky pro přípravu disků.

1. Připojte náš diskové jednotky systému Windows přes SATA konektory.
2. Vytvořte jeden svazek NTFS na každou jednotku. Přiřazení písmene jednotky svazku. Nepoužívejte přípojné body.
3. Upravit *dataset.csv* soubor v kořenové složce, ve kterém se nástroj nachází. V závislosti na tom, jestli chcete importovat soubor nebo složku nebo obojí, přidat položky *dataset.csv* souboru podobně jako v následujících příkladech.  

    - **Import souboru**: data ke kopírování v následujícím příkladu se nachází na jednotce C:. Váš soubor *MyFile1.txt* zkopírován do kořenového adresáře *MyAzureFileshare1*. Pokud *MyAzureFileshare1* buď neexistuje, vytvoří se v účtu Azure Storage. Struktura složek je zachováno.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Chcete importovat složku**: všechny soubory a složky v části *MyFolder2* jsou zkopírovány do sdílené složky rekurzivně. Struktura složek je zachováno.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Více položek, můžete provést ve stejném souboru odpovídající složky nebo soubory, které byly naimportovány. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
        ```
    Další informace o [připravuje se soubor CSV datovou sadu](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Upravit *driveset.csv* soubor v kořenové složce, ve kterém se nástroj nachází. Přidat položky *driveset.csv* souboru podobně jako v následujících příkladech. Soubor driveset má seznam disků a odpovídající písmena jednotek tak, aby nástroj správně vybrat seznamu disků, abyste byli připraveni.

    Tento příklad předpokládá, že jsou připojené dva disky a základní svazky systému souborů NTFS G:\ a H:\ jsou vytvořeny. H:\is nejsou šifrovaná, zatímco G: už zašifrovaná. Nástroj formátuje a zašifrování disku, který je hostitelem H:\ pouze (a ne G:\).

    - **Pro disk, který není zašifrovaný**: Zadejte *šifrovat* povolíte šifrování nástrojem BitLocker na disku.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **Pro disk, který je už zašifrovali**: Zadejte *AlreadyEncrypted* a zadat klíč Bitlockeru.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Více položek, můžete provést ve stejném souboru odpovídá několika jednotkách. Další informace o [připravuje se soubor CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Použití `PrepImport` možnost Kopírovat a připravit data na disk. První relace kopírování pro kopírování adresářů a/nebo soubory s novou relací kopírování spusťte následující příkaz:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Níže je uveden příklad importu.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. Soubor deníku s názvem, budete k dispozici `/j:` parametr, se vytvoří pro každé spuštění příkazového řádku. Každé jednotky, které připravíte má soubor deníku, který musí být odeslán při vytvoření úlohy importu. Jednotky bez deníku, které nejsou zpracovány soubory.

    > [!IMPORTANT]
    > - Neprovádějte žádné změny dat na diskové jednotky nebo soubor deníku po dokončení Příprava disku.

Další ukázky, přejděte na [ukázky pro soubory deníku](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu 

Proveďte následující kroky k vytvoření úlohy importu na webu Azure Portal.
1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy Import/export**. 

    ![Přejít na Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klikněte na tlačítko **vytvořit úlohu importu/exportu**.

    ![Klikněte na úlohu importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **Základy**:

    - Vyberte **Import do Azure**.
    - Zadejte popisný název úlohy importu. Tento název použijte ke sledování úloh, pokud nejsou v průběhu a po jejich dokončení.
        -  Tento název může obsahovat jenom malá písmena, číslice, spojovníky a podtržítka.
        -  Název musí začínat písmenem a nesmí obsahovat mezery. 
    - Vyberte předplatné.
    - Vyberte skupinu prostředků. 

        ![Vytvoření úlohy importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnosti úlohy**:
    
    - Nahrát soubory deníku, které jste vytvořili při předchozím [krok 1: Příprava jednotky](#step-1-prepare-the-drives). 
    - Vyberte účet úložiště, která data se naimportují do. 
    - Odkládací umístění se automaticky vyplní podle oblasti pro vybraný účet úložiště.
   
       ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **vrátí informace o expedici**:

    - Z rozevíracího seznamu vyberte operátorovi.
    - Zadejte číslo účtu dopravce platný, kterou jste vytvořili pomocí tohoto operátora. Tento účet Microsoft používá k odeslání jednotky zpět po dokončení úlohy import. 
    - Zadejte úplné a platné jméno kontaktní osoby, telefonu, e-mailu, adresu, Město, zip, stát/kraj a země/oblast.

        > [!TIP] 
        > Místo zadání e-mailovou adresu pro jednoho uživatele, zadejte e-mailů skupiny. Tím se zajistí, že budete dostávat oznámení, i když opustí správcem.

       ![Vytvoření úlohy importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. V **Souhrn**:

    - Zadejte datové centrum Azure dodací adresa pro přesouvání disky zpátky do Azure. Ujistěte se, že název úlohy a úplná adresa jsou uvedeny na Expediční štítek.
    - Klikněte na tlačítko **OK** k dokončení vytvoření úlohy importu.

        ![Vytvoření úlohy importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Odeslání disků do datacentra Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizace úlohy informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: Ověření nahrání dat do Azure

Sledujte úlohy do konce. Po dokončení úlohy ověřte, že vaše data nahráli do Azure. Odstraňte data v místním, až si ověříte, že načtení bylo úspěšné.

## <a name="samples-for-journal-files"></a>Ukázky pro soubory deníku

K **přidat další jednotky**, vytvořte nový soubor driveset a spusťte příkaz jak je uvedeno níže. 

Následné kopie relací na různých discích než je zadáno v *InitialDriveset CSV* soubor, zadejte nové driveset *CSV* souboru a zadejte jako hodnotu parametru `AdditionalDriveSet`. Použití **stejný soubor deníku** název a zadejte **nové ID relace**. Formát souboru AdditionalDriveset CSV je stejný jako formát InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Níže je uveden příklad importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Přidání dalších dat do stejné driveset, použijte příkaz PrepImport pro následné kopie relace zkopírovat další soubory/adresář.

Následné kopie relací na stejné jednotky pevného disku zadané v *InitialDriveset.csv* soubor, zadejte **stejný soubor deníku** název a zadejte **nové ID relace**; není nutné poskytnout klíč účtu úložiště.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Níže je uveden příklad importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Další postup

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na Import/Export](storage-import-export-requirements.md)


