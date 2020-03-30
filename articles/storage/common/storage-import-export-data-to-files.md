---
title: Použití importu a exportu Azure k přenosu dat do souborů Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak na webu Azure Portal vytvořit úlohy importu pro přenos dat do souborů Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268298"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Import dat do služby Soubory Azure pomocí služby Azure Import/Export

Tento článek obsahuje podrobné pokyny, jak používat službu Import a export Azure k bezpečnému importu velkého množství dat do souborů Azure. Chcete-li importovat data, služba vyžaduje, abyste do datového centra Azure doručovali podporované diskové jednotky obsahující vaše data.  

Služba Import a export podporuje jenom import souborů Azure do úložiště Azure. Export souborů Azure není podporován.

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohu importu pro přenos dat do souborů Azure, pečlivě zkontrolujte a vyplňte následující seznam předpokladů. Musíte:

- Měj aktivní předplatné Azure, které se bude používat se službou Import a Export.
- Mít alespoň jeden účet Azure Storage. Podívejte se na seznam [podporovaných účtů úložiště a typů úložišť pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v [tématu Jak vytvořit účet úložiště](storage-account-create.md).
- Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
- Mít systém Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).
- [Stáhněte si WAImportExport verze 2](https://aka.ms/waiev2) v systému Windows. Rozbalte výchozí složku `waimportexport`. Například, `C:\WaImportExport`.
- Měj účet FedEx/DHL. Pokud chcete použít jiného operátora než FedEx/DHL, kontaktujte tým Azure Data Box Operations na adrese `adbops@microsoft.com`.  
    - Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení zásilky.
    - Vygenerujte sledovací číslo pro úlohu exportu.
    - Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným sledovacím číslem není podporováno.
    - Pokud nemáte účet dopravce, přejděte na:
        - [Vytvořte si účet FedEX](https://www.fedex.com/en-us/create-account.html)nebo
        - [Vytvořte si účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotek

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako je sériové číslo jednotky, šifrovací klíč a podrobnosti o účtu úložiště.

Při přípravě jednotek proveďte následující kroky.

1. Připojte naše diskové jednotky k systému Windows pomocí konektorů SATA.
2. Vytvořte na každé jednotce jeden svazek NTFS. Přiřaďte svazku písmeno jednotky. Nepoužívejte přípojné body.
3. Upravte soubor *dataset.csv* v kořenové složce, ve které je nástroj umístěn. V závislosti na tom, zda chcete importovat soubor nebo složku nebo obojí, přidejte položky do souboru *dataset.csv* podobně jako v následujících příkladech.  

   - **Import souboru**: V následujícím příkladu jsou data, která chcete kopírovat, umístěna na jednotce F: . Soubor *MyFile1.txt* je zkopírován do kořenového adresáře *MyAzureFileshare1*. Pokud *MyAzureFileshare1* neexistuje, vytvoří se v účtu Azure Storage. Struktura složek je zachována.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Import složky**: Všechny soubory a složky v části *MyFolder2* se rekurzivně zkopírují do sdílené složky. Struktura složek je zachována.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Ve stejném souboru, který odpovídá importovaným složkám nebo souborům, lze vytvořit více položek.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Další informace o [přípravě souboru CSV datové sady](storage-import-export-tool-preparing-hard-drives-import.md).


4. Upravte soubor *driveset.csv* v kořenové složce, ve které je nástroj umístěn. Přidejte položky do souboru *driveset.csv* podobné následujícím příkladům. Soubor sady jednotek obsahuje seznam disků a odpovídající písmena jednotek, aby nástroj mohl správně vybrat seznam disků, které mají být připraveny.

    Tento příklad předpokládá, že jsou připojeny dva disky a základní svazky NTFS G:\ a H:\ jsou vytvořeny. H:\není šifrována, zatímco G: je již šifrována. Nástroj formátuje a šifruje disk, který je hostitelem H:\ pouze (a ne\)G: .

   - **Pro disk, který není zašifrován**: Zadejte *šifrovat,* chcete-li na disku povolit šifrování nástrojem BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Pro disk, který je již zašifrován**: Zadejte *již šifrované* a zadejte klíč nástroje BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Ve stejném souboru, který odpovídá více jednotkám, lze vytvořit více položek. Další informace o [přípravě souboru CSV sady jednotek](storage-import-export-tool-preparing-hard-drives-import.md).

5. Pomocí `PrepImport` možnosti zkopírujte a připravte data na diskovou jednotku. Pro první relaci kopírování ke kopírování adresářů nebo souborů s novou relací kopírování spusťte následující příkaz:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Příklad importu je uveden níže.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Soubor deníku s názvem, `/j:` který jste zadali s parametrem, je vytvořen pro každé spuštění příkazového řádku. Každá jednotka, kterou připravíte, obsahuje soubor deníku, který musí být odeslán při vytváření úlohy importu. Jednotky bez souborů deníku nejsou zpracovány.

    > [!IMPORTANT]
    > - Po dokončení přípravy disku neupravujte data na diskových jednotkách ani v souboru deníku.

Další ukázky naleznete v [ukázkách souborů deníku](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu

Chcete-li vytvořit úlohu importu na webu Azure Portal, proveďte následující kroky.
1. Přihlaste https://portal.azure.com/se k souboru .
2. Přejděte na **Všechny služby > úlohy importu > exportu**.

    ![Přejít na Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klepněte na **tlačítko Vytvořit úlohu importu nebo exportu**.

    ![Klikněte na Importovat nebo exportovat úlohu.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **základech**:

    - Vyberte **Importovat do Azure**.
    - Zadejte popisný název úlohy importu. Tento název slouží ke sledování úloh, které probíhají a jsou dokončeny.
        -  Tento název může obsahovat pouze malá písmena, číslice, pomlčky a podtržítka.
        -  Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Vyberte skupinu prostředků.

        ![Vytvořit úlohu importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnostech o úloze**:

    - Nahrajte soubory deníku, které jste vytvořili během předchozího [kroku 1: Příprava jednotek](#step-1-prepare-the-drives).
    - Vyberte účet úložiště, do kterého budou data importována.
    - Umístění dropoff je automaticky naplněno na základě oblasti vybraného účtu úložiště.

       ![Vytvořit úlohu importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **zpáteční přepravní informace**:

    - V rozevíracím seznamu vyberte dopravce. Pokud chcete použít jiného operátora než FedEx/DHL, zvolte stávající možnost z rozbalovací nabídky. Obraťte se na `adbops@microsoft.com` azure data box operations team na informace týkající se dopravce, který plánujete použít.
    - Zadejte platné číslo účtu dopravce, které jste vytvořili s tímto dopravcem. Společnost Microsoft používá tento účet k odeslání jednotek zpět k vám po dokončení importu úlohy.
    - Uveďte úplné a platné kontaktní jméno, telefon, e-mail, adresu, město, zip, stát/provincii a zemi/oblast.

        > [!TIP]
        > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinový e-mail. Tím zajistíte, že budete dostávat oznámení i v případě, že správce odejde.

       ![Vytvořit úlohu importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. V **souhrnu**:

    - Zadejte dodací adresu datového centra Azure pro odesílání disků zpět do Azure. Ujistěte se, že název úlohy a úplná adresa jsou uvedeny na přepravním štítku.
    - Chcete-li dokončit vytváření úloh importu, klepněte na tlačítko **OK.**

        ![Vytvořit úlohu importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Odeslání disků do datového centra Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizace úlohy informacemi o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: Ověření nahrávání dat do Azure

Sledování úlohy až do dokončení. Po dokončení úlohy ověřte, že se vaše data nahrála do Azure. Místní data odstraňte až po ověření, že nahrávání bylo úspěšné.

## <a name="samples-for-journal-files"></a>Ukázky pro soubory deníku

Chcete-li **přidat další jednotky**, vytvořte nový soubor sady jednotek a spusťte příkaz, jak je uvedeno níže.

Pro následné relace kopírování na různé diskové jednotky, než je uvedeno v souboru *InitialDriveset .csv,* `AdditionalDriveSet`zadejte nový soubor *CSV* sady jednotek a zadejte jej jako hodnotu parametru . Použijte stejný název **souboru deníku** a zadejte **nové ID relace**. Formát souboru AdditionalDriveset CSV je stejný jako ve formátu InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Příklad importu je uveden níže.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Chcete-li přidat další data do stejné sady jednotek, použijte příkaz PrepImport pro následné relace kopírování ke kopírování dalších souborů nebo adresářů.

Pro následné relace kopírování na stejné pevné disky zadané v souboru *InitialDriveset.csv* zadejte stejný název **souboru deníku** a zadejte **nové ID relace**; není nutné zajišťovat klíč účtu úložiště.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Příklad importu je uveden níže.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Další kroky

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Zkontrolovat požadavky na import a export](storage-import-export-requirements.md)
