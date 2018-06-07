---
title: K přenosu dat do Azure BLOB pomocí Azure Import/Export | Microsoft Docs
description: Naučte se vytvořit import a export úloh na portálu Azure k přenosu dat do a z Azure BLOB.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: fe9292459134972b44037a58235cdd817030a956
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660742"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Import dat do Azure Blob Storage pomocí služby Azure Import/Export

Tento článek obsahuje podrobné pokyny o tom, jak používat službu Azure Import/Export bezpečně importovat velké objemy dat do úložiště objektů Blob Azure. K importu dat do Azure BLOB, služba vyžaduje, abyste dodávat šifrované disky obsahující data do datového centra Azure.  

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohu importu k přenosu dat do Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí pro tuto službu. Postupujte takto:

- Máte aktivní předplatné Azure, který lze použít pro službu Import/Export.
- Máte alespoň jeden účet úložiště Azure se kontejner úložiště. Zobrazit seznam [podporovaný účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [postup vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account). Informace o kontejneru úložiště, přejděte na [vytvořit kontejner úložiště](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Používejte odpovídající počet disky [podporované typy](storage-import-export-requirements.md#supported-disks). 
- K dispozici systém Windows [verze operačního systému podporován](storage-import-export-requirements.md#supported-operating-systems). 
- Povolte nástroj BitLocker v systému Windows. V tématu [jak zapnout BitLocker](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) v systému Windows. Rozbalte do výchozí složky `waimportexportv1`. Například, `C:\WaImportExportV1`.


## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotky

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako jsou jednotky sériové číslo, šifrovací klíč a podrobnosti o účtu úložiště. 

Proveďte následující kroky při přípravě jednotky.

1.  Diskové jednotky se připojte k systému Windows prostřednictvím SATA konektory.
1.  Vytvořte jeden svazek NTFS na každém disku. Přiřadíte písmeno jednotky svazku. Nepoužívejte přípojné body.
2.  Povolíte šifrování nástrojem BitLocker na svazku systému souborů NTFS. Pokud používáte systém Windows Server, postupujte podle pokynů v [jak zapnout BitLocker v systému Windows Server 2012 R2](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopírování dat do zašifrovaného svazku. Pomocí přetahování nebo Robocopy nebo takový nástroj pro kopírování.
4.  Otevřete okno Powershellu nebo příkazového řádku s oprávněními správce. Chcete-li změnit adresář ke složce rozbalené, spusťte následující příkaz:
    
    `cd C:\WaImportExportV1`
5.  Získat klíč nástroje BitLocker na jednotce, spusťte následující příkaz:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  Příprava na disku, spusťte následující příkaz. **V závislosti na velikosti dat může to trvat několik hodin na dnů.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    Soubor deníku se vytvoří ve stejné složce, kde jste spustili nástroj. Také jsou vytvořeny dva další soubory – *.xml* soubor (složka kterém jste spustili nástroj) a *jednotky manifest.xml* soubor (složka, kde jsou uložena data).
    
    Použité parametry jsou popsané v následující tabulce:

    |Možnost  |Popis  |
    |---------|---------|
    |/j:     |Název souboru deníku s příponou .jrn. Soubor deníku se generuje na jednotku. Doporučujeme použít sériové číslo disku jako název souboru deníku.         |
    |/ID:     |ID relace. Pro každou instanci příkaz pomocí čísla jedinečná relace.      |
    |/Sk:     |Klíč účtu úložiště Azure.         |
    |/ t:     |Písmeno jednotky disk, který se má odeslat. Například jednotky `D`.         |
    |/BK:     |Klíč nástroje BitLocker pro jednotku. Jeho číselné heslo z výstupu ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Následuje písmeno disku budou zaslány `:\`. Například, `D:\`.         |
    |/dstdir:     |Název cílový kontejner ve službě Azure Storage.         |
    |/skipwrite:     |Možnost, která určuje, že se žádná nová data potřeba kopírovat a existující data na disku je nutné připravit.          |
7. Opakujte předchozí krok pro každý disk, který potřebuje k dodání. Soubor deníku s zadaný název se vytvoří pro každé spuštění příkazového řádku.
    
    > [!IMPORTANT]
    > - Společně s soubor deníku `<Journal file name>_DriveInfo_<Drive serial ID>.xml` soubor se také vytvoří ve stejné složce, kde tento nástroj se nachází. Soubor .xml je použít místo souboru deníku při vytvoření úlohy, pokud je soubor deníku příliš velký. 

## <a name="step-2-create-an-import-job"></a>Krok 2: Vytvoření úlohy importu

Proveďte následující kroky k vytvoření úlohy importu v portálu Azure.

1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy importu a exportu**. 
    
    ![Přejděte do úlohy importu a exportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klikněte na tlačítko **úlohy importu a exportu vytvořit**.

    ![Klikněte na úlohu vytvořit Import/export](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. V **Základy**:

    - Vyberte **importu do Azure**.
    - Zadejte popisný název úlohy importu. Použijte název sledovat průběh úloh.
        - Název může obsahovat jenom malá písmena, číslice, pomlčky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.  

    ![Vytvoření úlohy importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnosti úlohy**:

    - Odešlete soubory deníku jednotky, které jste získali během přípravy kroku jednotky. Pokud `waimportexport.exe version1` se používá, nahrát jeden soubor pro každou jednotku, který jste připravili. Pokud velikost souboru deníku překročí 2 MB, pak můžete použít `<Journal file name>_DriveInfo_<Drive serial ID>.xml` také vytvořit souborem deníku. 
    - Vyberte cílový účet úložiště, kde budou uložena data. 
    - Odkládací umístění se automaticky vyplní podle oblasti vybrat účet úložiště.
   
   ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **vrátí informace o přesouvání**:

    - Z rozevíracího seznamu vyberte zařadit.
    - Zadejte číslo účtu platný operátora, kterou jste vytvořili pomocí tohoto operátora. Společnost Microsoft používá tento účet pro odeslání jednotky vám po dokončení importu úlohu. Pokud nemáte číslo účtu, vytvořte [FedEx](http://www.fedex.com/us/oadr/) nebo [DHL](http://www.dhl.com/) poskytovatel účtu.
    - Zadejte úplný a platné jméno kontaktní osoby, telefon, e-mailu, adresu, města, zip, Kraj a země nebo oblast.

    ![Vytvoření úlohy importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. V **Souhrn**:

    - Zkontrolujte informace o úlohách, které jsou uvedené v souhrnu. Poznamenejte si název úlohy a datovém centru Azure přesouvání adresa pro odeslání disky zpět do Azure. Tyto informace se později používá na přesouvání popisek.
    - Klikněte na tlačítko **OK** pro vytvoření úlohy importu.

    ![Vytvoření úlohy importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

## <a name="step-3-ship-the-drives"></a>Krok 3: Odeslání jednotky 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizace úlohy s informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="next-steps"></a>Další postup

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na Import/Export](storage-import-export-requirements.md)


