---
title: Přenos dat do objektů BLOB systému Azure pomocí Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit import a export projektů na webu Azure portal k přenosu dat do a z objektů BLOB Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 39287e4d90e4f6f42dd1f79d5457853e253bd736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737052"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Import dat do úložiště objektů Blob v Azure pomocí služby Azure Import/Export

Tento článek obsahuje podrobné pokyny o tom, jak bezpečně importovat velké objemy dat do úložiště objektů Blob v Azure pomocí služby Azure Import/Export. K importu dat do objektů BLOB Azure, služba vyžaduje, aby dodat šifrované disky obsahující data do datacentra Azure.  

## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet úlohy importu pro přenos dat do služby Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí pro tuto službu. Musíte mít:

- Máte aktivní předplatné Azure, který lze použít pro službu Import/Export.
- Máte alespoň jeden účet úložiště Azure se kontejner úložiště. Zobrazit seznam [nepodporuje účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). 
    - Informace o vytvoření nového účtu úložiště najdete v tématu [způsob vytvoření účtu úložiště](storage-quickstart-create-account.md). 
    - Informace o kontejneru úložiště, přejděte na [vytvořit kontejner úložiště](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Používejte odpovídající počet disků [podporované typy](storage-import-export-requirements.md#supported-disks). 
- K dispozici systém Windows [verze operačního systému nepodporuje](storage-import-export-requirements.md#supported-operating-systems). 
- Povolení nástroje BitLocker v systému Windows. Zobrazit [jak zapnout BitLocker](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) v systému Windows. Rozbalte do výchozí složky `waimportexportv1`. Například, `C:\WaImportExportV1`.
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

1.  Připojte diskové jednotky systému Windows přes SATA konektory.
1.  Vytvořte jeden svazek NTFS na každou jednotku. Přiřazení písmene jednotky svazku. Nepoužívejte přípojné body.
2.  Povolíte šifrování nástrojem BitLocker na svazku NTFS. Pokud používáte systém Windows Server, postupujte podle pokynů v [jak zapnout BitLocker ve Windows serveru 2012 R2](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopírování dat do zašifrovaného svazku. Pomocí přetahování nebo Robocopy nebo takový nástroj pro kopírování.
4.  Otevřete okno Powershellu nebo příkazového řádku s oprávněními správce. Přejít ke složce rozbaleny, spusťte následující příkaz:
    
    `cd C:\WaImportExportV1`
5.  Pokud chcete získat klíče Bitlockeru na jednotce, spusťte následující příkaz:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  Příprava na disk, spusťte následující příkaz. **V závislosti na velikosti dat může to trvat několik hodin na dny.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    Soubor deníku se vytvoří ve stejné složce, kam jste nástroj spustili. Také se vytvoří dva další soubory – *.xml* soubor (složku, ve kterém spouštíte nástroj) a *jednotky manifest.xml* soubor (složku, ve které se nachází data).
    
    Parametry použité jsou popsány v následující tabulce:

    |Možnost  |Popis  |
    |---------|---------|
    |/j:     |Název souboru deníku s příponou .jrn. Na jednotku je vygenerován soubor deníku. Doporučujeme použít jako název souboru deníku sériové číslo disku.         |
    |/ID:     |ID relace. Pro každou instanci tohoto příkazu použijte jedinečné relace číslo.      |
    |/Sk:     |Klíč účtu úložiště Azure.         |
    |t:     |Písmeno jednotky disku k odeslání. Třeba jednotka `D`.         |
    |/BK:     |Klíč nástroje BitLocker pro jednotku. Jeho číselné heslo z výstupu ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Písmeno jednotky disku budou zaslány, za nímž následuje `:\`. Například, `D:\`.         |
    |/dstdir:     |Název cílový kontejner ve službě Azure Storage.         |
    |/skipwrite:     |Abyste byli připraveni je možnost, která určuje, že neexistuje žádná nová data muset zkopírovat a existující data na disku.          |
7. Opakujte předchozí krok u každého disku, který potřebuje k odeslání. Pro každé spuštění příkazového řádku se vytvoří soubor deníku se zadaným názvem.
    
    > [!IMPORTANT]
    > - Spolu s soubor deníku `<Journal file name>_DriveInfo_<Drive serial ID>.xml` souboru se také vytvoří ve stejné složce, ve kterém se nástroj nachází. Soubor XML se používá místo soubor deníku při vytvoření úlohy, pokud je příliš velký soubor deníku. 

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu

Proveďte následující kroky k vytvoření úlohy importu na webu Azure Portal.

1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy Import/export**. 
    
    ![Přejít na úlohy Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klikněte na tlačítko **vytvořit úlohu importu/exportu**.

    ![Klikněte na úlohu vytvoření importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **Základy**:

    - Vyberte **Import do Azure**.
    - Zadejte popisný název úlohy importu. Použijte název sledovat průběh úlohy.
        - Název může obsahovat jenom malá písmena, číslice, spojovníky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.  

    ![Vytvoření úlohy importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnosti úlohy**:

    - Nahrajte soubory deníku jednotky, které jste získali během kroku přípravy jednotky. Pokud `waimportexport.exe version1` byl použit, nahrát jeden soubor pro každou jednotku, kterou jste připravili. Pokud velikost deníku soubor je větší než 2 MB, pak můžete použít `<Journal file name>_DriveInfo_<Drive serial ID>.xml` také vytvoří se soubor deníku. 
    - Vyberte cílový účet úložiště, ve kterém budou uložena data. 
    - Odkládací umístění se automaticky vyplní podle oblasti pro vybraný účet úložiště.
   
   ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **vrátí informace o expedici**:

    - Z rozevíracího seznamu vyberte operátorovi.
    - Zadejte číslo účtu dopravce platný, kterou jste vytvořili pomocí tohoto operátora. Tento účet Microsoft používá k odeslání jednotky zpět po dokončení úlohy import. Pokud nemáte číslo účtu, vytvořte [FedEx](http://www.fedex.com/us/oadr/) nebo [DHL](http://www.dhl.com/) účtu dopravce.
    - Zadejte úplné a platné jméno kontaktní osoby, telefonu, e-mailu, adresu, Město, zip, stát/kraj a země/oblast. 
        
        > [!TIP] 
        > Místo zadání e-mailovou adresu pro jednoho uživatele, zadejte e-mailů skupiny. Tím se zajistí, že budete dostávat oznámení, i když opustí správcem.

    ![Vytvoření úlohy importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. V **Souhrn**:

    - Projděte si informace o úlohách, které jsou k dispozici v souhrnu. Poznamenejte si název úlohy a datového centra Azure dodací adresa dodávat disky zpátky do Azure. Tyto informace se použije v pozdější na Expediční štítek.
    - Klikněte na tlačítko **OK** k vytvoření úlohy importu.

    ![Vytvoření úlohy importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Krok 3: Odeslání jednotky 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizace úlohy informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: Ověření nahrání dat do Azure

Sledujte úlohy do konce. Po dokončení úlohy ověřte, že vaše data nahráli do Azure. Odstraňte data v místním, až si ověříte, že načtení bylo úspěšné.

## <a name="next-steps"></a>Další postup

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na Import/Export](storage-import-export-requirements.md)


