---
title: Pomocí Azure Import/Export k přenosu dat do Azure Files | Microsoft Docs
description: Naučte se vytvářet úlohy importu v portálu Azure k přenosu dat do Azure Files.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 4349b471f960e7844511c473bffcd2177a34e055
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660747"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Import dat do Azure soubory pomocí služby Azure Import/Export

Tento článek obsahuje podrobné pokyny o tom, jak používat službu Azure Import/Export bezpečně importovat velké objemy dat do Azure Files. K importu dat, služba vyžaduje, abyste dodávat podporované disky obsahující data do datového centra Azure.  

Podporuje službu Import/Export importovat pouze souborů Azure do Azure Storage. Soubory Azure export není podporován.

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohu importu k přenosu dat do Azure souborů, pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí. Postupujte takto:

- Máte aktivní předplatné Azure používat se službou importu a exportu.
- Máte alespoň jeden účet úložiště Azure. Zobrazit seznam [podporovaný účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [postup vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account).
- Používejte odpovídající počet disky [podporované typy](storage-import-export-requirements.md#supported-disks). 
- K dispozici systém Windows [verze operačního systému podporován](storage-import-export-requirements.md#supported-operating-systems).
- [Stáhněte si WAImportExport verze 2](https://www.microsoft.com/download/details.aspx?id=55280) v systému Windows. Rozbalte do výchozí složky `waimportexport`. Například, `C:\WaImportExport`.


## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotky

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako jsou jednotky sériové číslo, šifrovací klíč a podrobnosti o účtu úložiště.

Proveďte následující kroky při přípravě jednotky.

1. Naše diskové jednotky se připojte k systému Windows prostřednictvím SATA konektory.
2. Vytvořte jeden svazek NTFS na každém disku. Přiřadíte písmeno jednotky svazku. Nepoužívejte přípojné body.
3. Změnit *dataset.csv* souboru do kořenové složky, které se nástroj nachází. V závislosti na tom, jestli chcete importovat soubor nebo složku nebo obojí, přidání položek *dataset.csv* souboru podobná následující příklady.  

    - **Chcete-li importovat soubor**: data pro kopírování v následujícím příkladu se nachází na jednotce C:. Váš soubor *MyFile1.txt* se zkopíruje do kořenového adresáře *MyAzureFileshare1*. Pokud *MyAzureFileshare1* neexistuje, je vytvořen v účtu úložiště Azure. Struktura složek je zachován.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Chcete-li importovat do složky**: všechny soubory a složky v části *MyFolder2* jsou rekurzivně zkopírován do sdílení souborů. Struktura složek je zachován.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Několik záznamů, můžete provést ve stejném souboru odpovídající složek nebo souborů, které jsou importovány. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    Další informace o [soubor CSV datovou sadu se připravuje](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Změnit *driveset.csv* souboru do kořenové složky, které se nástroj nachází. Přidání položek *driveset.csv* souboru podobná následující příklady. Soubor driveset má seznam disků a odpovídající písmena jednotek, aby tento nástroj můžete vybrat správně seznam disků, abyste byli připraveni.

    Tento příklad předpokládá, že jsou připojené dva disky a vytvářejí G:\ a H:\ základní svazků systému souborů NTFS. H:\is při G: už je šifrovaný není zašifrovaná. Nástroj naformátuje a šifruje disku, který je hostitelem H:\ pouze (a ne G:\).

    - **Pro disk, který není šifrován**: Zadejte *šifrovat* povolíte šifrování nástrojem BitLocker na disku.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **Pro disk, který už je šifrovaný**: Zadejte *AlreadyEncrypted* a zadejte klíč nástroje BitLocker.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Několik záznamů, můžete provést ve stejném souboru odpovídající více jednotek. Další informace o [Příprava souboru CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Použití `PrepImport` možnost Kopírovat a připravit data na disk. Pro první relaci kopírování ke kopírování adresářů a souborů s novou relací kopírování spusťte následující příkaz:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Níže je uveden příklad importu.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. Deník soubor s názvem uvedená v `/j:` parametr, bude vytvořena pro každé spuštění příkazového řádku. Každé jednotky, které připravíte má deníku soubor, který musí být odeslán při vytváření úlohy importu. Disky bez deníku, které nejsou předmětem zpracování souborů.

    > [!IMPORTANT]
    > - Neprovádějte žádné změny dat na diskové jednotky nebo soubor deníku po dokončení Příprava disku.

Další ukázky, přejděte na [ukázky pro soubory deníku](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu 

Proveďte následující kroky k vytvoření úlohy importu v portálu Azure.
1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy importu a exportu**. 

    ![Přejděte k importu a exportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klikněte na tlačítko **úlohy importu a exportu vytvořit**.

    ![Klikněte na úlohu importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **Základy**:

    - Vyberte **importu do Azure**.
    - Zadejte popisný název úlohy importu. Tento název používejte ke sledování úloh v době, kdy jsou v průběhu a po jejich dokončení.
        -  Tento název může obsahovat jenom malá písmena, číslice, pomlčky a podtržítka.
        -  Název musí začínat písmenem a nesmí obsahovat mezery. 
    - Vyberte předplatné.
    - Vyberte skupinu prostředků. 

        ![Vytvoření úlohy importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnosti úlohy**:
    
    - Odesílat soubory deníku, které jste vytvořili při předchozím [krok 1: Příprava jednotky](#step-1-prepare-the-drives). 
    - Vyberte účet úložiště, který bude importovat data. 
    - Odkládací umístění se automaticky vyplní podle oblasti vybrat účet úložiště.
   
       ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **vrátí informace o přesouvání**:

    - V rozevíracím seznamu vyberte zařadit.
    - Zadejte číslo účtu platný operátora, kterou jste vytvořili pomocí tohoto operátora. Společnost Microsoft používá tento účet pro odeslání jednotky vám po dokončení importu úlohu. 
    - Zadejte úplný a platné jméno kontaktní osoby, telefon, e-mailu, adresu, města, zip, Kraj a země nebo oblast.

       ![Vytvoření úlohy importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. V **Souhrn**:

    - Zadejte datovém centru Azure přesouvání adresu pro přesouvání disků zpět do Azure. Ujistěte se, že název úlohy a úplnou adresu se zmiňují v popisku přesouvání.
    - Klikněte na tlačítko **OK** k dokončení vytvoření úlohy importu.

        ![Vytvoření úlohy importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Odeslání jednotek pro datové centrum Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizace úlohy s informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="samples-for-journal-files"></a>Ukázky pro soubory deníku

K **přidat další jednotky**, vytvořte nový soubor driveset a spusťte příkaz jak je uvedeno níže. 

Pro následné kopírování relace na jiný pevných discích, než je zadáno v *InitialDriveset .csv* souboru, zadejte nové driveset *.csv* souboru a zadejte jako hodnotu parametru `AdditionalDriveSet`. Použití **stejný soubor deníku** název a zadejte **nové ID relace**. Formát souboru AdditionalDriveset CSV je stejný jako formát InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Níže je uveden příklad importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Pokud chcete přidat další data do stejné driveset, použijte příkaz PrepImport pro následné kopírování relace pro kopírování další soubory nebo adresáře.

Pro následné kopírování relace na stejné jednotky pevného disku zadaná v *InitialDriveset.csv* souboru, zadejte **stejný soubor deníku** název a zadejte **nové ID relace**; je potřeba zadat klíč účtu úložiště.

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


