---
title: K exportu dat z Azure BLOB pomocí Azure Import/Export | Microsoft Docs
description: Naučte se vytvářet úlohy exportu na portálu Azure k přenosu dat z Azure BLOB.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: eb41708c7446b3139758678c9247ffbb11da8b40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660741"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Export dat z Azure Blob storage pomocí služby Azure Import/Export
Tento článek obsahuje podrobné pokyny o tom, jak používat službu Azure Import/Export bezpečně export velkých objemů dat z Azure Blob storage. Služba vyžaduje, abyste dodávat prázdný jednotek pro datové centrum Azure. Služba exportuje data z vašeho účtu úložiště do jednotky a pak se dodává jednotky zpět.

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohy exportu k přenosu dat z Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí pro tuto službu. Postupujte takto:

- Máte aktivní předplatné Azure, který lze použít pro službu Import/Export.
- Máte alespoň jeden účet úložiště Azure. Zobrazit seznam [podporovaný účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [postup vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account).
- Používejte odpovídající počet disky [podporované typy](storage-import-export-requirements.md#supported-disks).

## <a name="step-1-create-an-export-job"></a>Krok 1: Vytvoření úlohy exportu

Proveďte následující kroky k vytvoření úlohy exportu na portálu Azure.

1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy importu a exportu**. 

    ![Přejděte do úlohy importu a exportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klikněte na tlačítko **úlohy importu a exportu vytvořit**.

    ![Klikněte na úlohu importu/exportu](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. V **Základy**:
    
    - Vyberte **exportovat z Azure**. 
    - Zadejte popisný název pro úlohu export. Použijte název, který jste se rozhodli sledovat průběh úloh. 
        - Název může obsahovat jenom malá písmena, číslice, pomlčky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery. 
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.

        ![Základy](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. V **podrobnosti úlohy**:

    - Vyberte účet úložiště, které se nachází data, která mají být exportovány. 
    - Odkládací umístění se automaticky vyplní podle oblasti vybrat účet úložiště. 
    - Zadejte data objektů blob, který chcete exportovat z vašeho účtu úložiště do prázdné jednotku nebo jednotky. 
    - Zvolit **exportovat všechny** dat v účtu úložiště objektů blob.
    
         ![Exportovat veškerý](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Můžete zadat, které kontejnery a objekty BLOB pro export.
        - **Chcete-li určit objekt blob pro export**: použití **rovno** selektor. Zadejte relativní cestu do objektu blob, počínaje název kontejneru. Použití *$root* k určení Kořenový kontejner.
        - **K určení všech objektů BLOB od s předponou**: použití **začíná** selektor. Zadejte předponu, počínaje lomítkem '/'. Předpona, která může být předponu název kontejneru, název dokončení kontejneru nebo název dokončení kontejneru, za nímž následuje předponu názvu objektu blob. Cesty objektů blob v platném formátu. abyste se vyhnuli chybám při zpracování, je nutné zadat, jak je vidět na tomto snímku obrazovky. Další informace najdete v tématu [příklady platný objekt blob cesty](#examples-of-valid-blob-paths). 
   
           ![Exportovat vybrané kontejnery a objekty BLOB](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Můžete exportovat ze souboru seznamu objektů blob.

        ![Export ze souboru seznamu objektů blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Pokud při kopírování dat se používá objekt blob pro export, služba Azure Import/Export pořídí snímek objektu blob a zkopíruje snímku.
 

4. V **vrátí informace o přesouvání**:

    - Z rozevíracího seznamu vyberte zařadit.
    - Zadejte číslo účtu platný operátora, kterou jste vytvořili pomocí tohoto operátora. Společnost Microsoft používá tento účet pro odeslání jednotky vám po dokončení importu úlohu. 
    - Zadejte úplný a platné jméno kontaktní osoby, telefon, e-mailu, adresu, města, zip, Kraj a země nebo oblast.
   
5. V **Souhrn**:

    - Zkontrolujte podrobnosti úlohy.
    - Poznamenejte si adresu přesouvání úlohy název a zadaný datové centrum Azure pro přesouvání disků do Azure. 
    - Klikněte na tlačítko **OK** k dokončení vytvoření úlohy exportu.

## <a name="step-2-ship-the-drives"></a>Krok 2: Odeslání jednotky

Pokud neznáte počet jednotek, které potřebujete, přejděte k [Zkontrolujte počet jednotek](#check-the-number-of-drives). Pokud znáte počet jednotek, přejděte k odeslání jednotky.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Aktualizace úlohy s informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Krok 4: Přijímat disky
Když na řídicím panelu hlásí, že tato úloha skončí, disky jsou sice vám a číslo sledování dodávky je k dispozici na portálu.

1. Jakmile se zobrazí na discích s exportovanými daty, které je potřeba získat klíče Bitlockeru k odemknutí jednotky. Přejděte do úlohy exportu na portálu Azure. Klikněte na tlačítko **importu a exportu** kartě. 
2. Vyberte a klepněte export úlohy ze seznamu. Přejděte na **klíče Bitlockeru** a zkopírujte tyto klíče.
   
   ![Zobrazit klíče nástroje BitLocker pro úlohu export](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Odemknout disky pomocí klíče Bitlockeru.

Export je dokončena. V tomto okamžiku můžete odstranit úlohy nebo ji získá automaticky odstraněny po 90 dnech.


## <a name="check-the-number-of-drives"></a>Zkontrolujte počet jednotek

To *volitelné* krok pomáhá určuje počet jednotek, které jsou nezbytné pro úlohu exportu. Tento krok proveďte v systému Windows spuštěná [verze operačního systému podporován](storage-import-export-requirements.md#supported-operating-systems).

1. [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) v systému Windows. 
2. Rozbalte do výchozí složky `waimportexportv1`. Například, `C:\WaImportExportV1`.
3. Otevřete okno Powershellu nebo příkazového řádku s oprávněními správce. Chcete-li změnit adresář ke složce rozbalené, spusťte následující příkaz:
    
    `cd C:\WaImportExportV1`

4. Pokud chcete zkontrolovat počet disků, které jsou potřebné pro vybrané objekty BLOB, spusťte následující příkaz:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry jsou popsané v následující tabulce:
    
    |Parametr příkazového řádku|Popis|  
    |--------------------------|-----------------|  
    |**/logdir:**|Volitelné. K adresáři protokolů. Podrobné soubory protokolu se zapisují do tohoto adresáře. Pokud není zadaný, aktuální adresář se používá jako adresář protokolu.|  
    |**/sn:**|Povinná hodnota. Název účtu úložiště pro úlohy exportu.|  
    |**/Sk:**|Povinné, pokud není zadán sdíleného přístupového podpisu kontejneru. Klíč účtu pro účet úložiště pro úlohy exportu.|  
    |**/csas:**|Povinné, pokud není zadán klíč účtu úložiště. Kontejner SAS pro výpis objekty BLOB ve úloha exportu.|  
    |**/ ExportBlobListFile:**|Povinná hodnota. Cesta k souboru XML soubor obsahující seznam objektů blob cesty nebo objektu blob předpony cestu pro export objektů BLOB. Formát souboru, který je používán `BlobListBlobPath` element v [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace importu/exportu služby REST API.|  
    |**/ DriveSize:**|Povinná hodnota. Velikost jednotky použijte pro úlohy exportu, *například*, 500 GB, 1,5 TB.|  

    V tématu [příklad příkaz PreviewExport](#example-of-previewexport-command).
 
5. Zkontrolujte, jestli je můžete pro čtení a zápis na discích, které bude dodáno pro úlohu export.

### <a name="example-of-previewexport-command"></a>Příklad PreviewExport příkazu

Následující příklad ukazuje `PreviewExport` příkaz:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Seznam souboru exportu objektu blob může obsahovat názvy objektů blob a objektů blob předpony, jak je vidět tady:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Nástroj Azure Import/Export obsahuje seznam všech objektů blob pro export a vypočítá postup pack je do jednotky po zadanou velikost vezme v úvahu všechny nezbytné režijní náklady a pak odhadne počet jednotek, které jsou potřebné pro uložení objektů BLOB a informace o využití disku.  
  
Tady je příklad výstupu s informační protokoly vynechání:  
  
```  
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

## <a name="examples-of-valid-blob-paths"></a>Příklady platný objekt blob cesty

Následující tabulka uvádí příklady cesty platný objekt blob:
   
   | Selektor | Cesta k objektu BLOB | Popis |
   | --- | --- | --- |
   | Začíná |/ |Exportuje všech objektů BLOB v účtu úložiště |
   | Začíná |/$root / |Exportuje všech objektů BLOB v kontejneru kořenové |
   | Začíná |/Book |Exportuje všech objektů BLOB v kontejneru, který začíná předponu **adresáře** |
   | Začíná |/Music/ |Exportuje všech objektů BLOB v kontejneru **Hudba** |
   | Začíná |/ Hudba/láska |Exportuje všech objektů BLOB v kontejneru **Hudba** které začínají předponou **rádi** |
   | Rovno |$root/logo.bmp |Export objektu blob **logo.bmp** v kořenovém kontejneru |
   | Rovno |videos/Story.MP4 |Export objektu blob **story.mp4** v kontejneru **videa** |

## <a name="next-steps"></a>Další postup

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na Import/Export](storage-import-export-requirements.md)


