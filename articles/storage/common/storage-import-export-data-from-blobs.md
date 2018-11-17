---
title: Export dat z Azure BLOB pomocí Azure Import/Export | Dokumentace Microsoftu
description: Informace o vytvoření úlohy exportu na webu Azure portal k přenosu dat z objektů BLOB Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: faf8852df8b50c43affe32ede0f1e96d0bb80d3d
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821238"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Export dat z úložiště objektů Blob v Azure pomocí služby Azure Import/Export
Tento článek obsahuje podrobné pokyny o tom, jak bezpečně export velkých objemů dat z úložiště objektů Blob v Azure pomocí služby Azure Import/Export. Služba vyžaduje, abyste zasílejte prázdné disky do datacentra Azure. Službu exportuje data z vašeho účtu úložiště do jednotky a potom odešle zpět jednotky.

## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet úlohy exportu pro přenos dat z Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadovaných součástí pro tuto službu. Musíte mít:

- Máte aktivní předplatné Azure, který lze použít pro službu Import/Export.
- Máte alespoň jeden účet služby Azure Storage. Zobrazit seznam [nepodporuje účty úložiště a typy úložiště pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v tématu [způsob vytvoření účtu úložiště](storage-quickstart-create-account.md).
- Používejte odpovídající počet disků [podporované typy](storage-import-export-requirements.md#supported-disks).
- Máte účet FedEx/DHL.  
    - Účet musí být platná, by měl mít vyrovnávání a musí mít vrácení funkce.
    - Generovat sledovací číslo pro úlohu exportu.
    - Každá úloha by měl mít samostatný sledovací číslo. Více úloh se stejným číslem sledování nejsou podporovány. 
    - Pokud nemáte účet přepravce, přejděte na:
        - [Vytvoření účtu FedEX](https://www.fedex.com/en-us/create-account.html), nebo 
        - [Vytvoření účtu DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1: Vytvoření úlohy exportu

Proveďte následující kroky k vytvoření úlohy exportu na webu Azure Portal.

1. Přihlaste se k https://portal.azure.com/.
2. Přejděte na **všechny služby > úložiště > úlohy Import/export**. 

    ![Přejít na úlohy Import/export](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klikněte na tlačítko **vytvořit úlohu importu/exportu**.

    ![Klikněte na úlohu importu/exportu](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. V **Základy**:
    
    - Vyberte **Export z Azure**. 
    - Zadejte popisný název pro úlohu exportu. Použijte název, kterou chcete sledovat průběh úlohy. 
        - Název může obsahovat jenom malá písmena, číslice, spojovníky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery. 
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.

        ![Základní informace](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. V **podrobnosti úlohy**:

    - Vyberte účet úložiště, ve které se nachází data, která mají být exportovány. Použijte účet úložiště blízko, kde se nachází.
    - Umístění dropoff se automaticky vyplní podle oblasti pro vybraný účet úložiště. 
    - Zadejte data objektů blob, který chcete exportovat z vašeho účtu úložiště do prázdné jednotku nebo jednotky. 
    - Zvolit **exportovat všechny** datům objektu blob na účtu úložiště.
    
         ![Exportovat vše](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Můžete určit, které kontejnery a objekty BLOB k exportu.
        - **K určení objektu blob k exportu**: použijte **rovno** selektor. Zadejte relativní cestu k objektu blob, počínaje název kontejneru. Použití *$root* určit kořenový kontejner.
        - **Chcete-li určit všechny objekty BLOB začínajících prefixem**: použití **začíná** selektor. Zadejte předponu, začínající lomítkem (/). Předpona, která může být předpony názvu kontejneru, název kontejneru dokončení nebo kontejner úplný název, za nímž následuje předponu názvu objektu blob. Cesty k objektům blob v platném formátu, aby se zabránilo chybám při zpracování, je nutné zadat, jak je znázorněno na tomto snímku obrazovky. Další informace najdete v tématu [příklady cesty k objektům blob platný](#examples-of-valid-blob-paths). 
   
           ![Exportovat vybrané kontejnery a objekty BLOB](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Můžete exportovat ze souboru seznamu objektů blob.

        ![Export ze souboru seznamu objektů blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Pokud objekt blob export se používá při kopírování dat, služba Azure Import/Export pořídí snímek objektu blob a zkopíruje snímek.
 

4. V **vrátí informace o expedici**:

    - Z rozevíracího seznamu vyberte operátorovi.
    - Zadejte číslo účtu dopravce platný, kterou jste vytvořili pomocí tohoto operátora. Tento účet Microsoft používá k odeslání jednotky zpět po dokončení úlohy import. 
    - Zadejte úplné a platné jméno kontaktní osoby, telefonu, e-mailu, adresu, Město, zip, stát/kraj a země/oblast.

        > [!TIP] 
        > Místo zadání e-mailovou adresu pro jednoho uživatele, zadejte e-mailů skupiny. Tím se zajistí, že budete dostávat oznámení, i když opustí správcem.
   
5. V **Souhrn**:

    - Projděte si podrobnosti o úloze.
    - Poznamenejte si název úlohy a zadané datové centrum Azure dodací adresa pro přesouvání disků do Azure. 

        > [!NOTE] 
        > Vždy odesílat disků do datacentra, které jste si poznamenali na webu Azure Portal. Pokud disky jsou odeslány do nesprávného datacenter, úlohy nebude zpracováno.

    - Klikněte na tlačítko **OK** k dokončení vytvoření úlohy exportu.

## <a name="step-2-ship-the-drives"></a>Krok 2: Odeslání jednotky

Pokud si nejste jisti požadovaný počet jednotek, je nutné, přejděte [zkontrolovat požadovaný počet jednotek](#check-the-number-of-drives). Pokud víte, požadovaný počet jednotek, přejděte k dodávání jednotky.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Aktualizace úlohy informace o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Krok 4: Přijímat disky
Když řídicím panelu hlásí, že úloha byla dokončena, disky se dodávají se vám a sledovací číslo pro dodávku je k dispozici na portálu.

1. Po přijetí jednotky se exportovaná data, musíte získat klíče Bitlockeru k odemknutí jednotky. Přejděte na úlohu exportu na webu Azure Portal. Klikněte na tlačítko **Import/Export** kartu. 
2. Vyberte a klikněte na vaše úloha exportu ze seznamu. Přejděte na **klíče Bitlockeru** a zkopírování klíče.
   
   ![Zobrazovat klíče Bitlockeru pro úlohu exportu](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Použijte klíče Bitlockeru k odemknutí disky.

Export byl dokončen. V tuto chvíli úlohy můžete odstranit nebo ho získá automaticky odstraněny po 90 dnech.


## <a name="check-the-number-of-drives"></a>Zkontrolujte požadovaný počet jednotek

To *volitelné* krok pomůže Určuje požadovaný počet jednotek nezbytný k dokončení úlohy exportu. Tento krok proveďte v systému Windows [verze operačního systému nepodporuje](storage-import-export-requirements.md#supported-operating-systems).

1. [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) v systému Windows. 
2. Rozbalte do výchozí složky `waimportexportv1`. Například, `C:\WaImportExportV1`.
3. Otevřete okno Powershellu nebo příkazového řádku s oprávněními správce. Přejít ke složce rozbaleny, spusťte následující příkaz:
    
    `cd C:\WaImportExportV1`

4. Pokud chcete zkontrolovat počet disků potřebný pro vybrané objekty BLOB, spusťte následující příkaz:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry jsou popsány v následující tabulce:
    
    |Parametr příkazového řádku|Popis|  
    |--------------------------|-----------------|  
    |**/logdir:**|Volitelné. Adresář protokolu. Souborů podrobného protokolování se zapisují do tohoto adresáře. Pokud není zadán, aktuální adresář se používá jako adresář protokolu.|  
    |**/sn:**|Povinné Název účtu úložiště pro úlohu exportu.|  
    |**/Sk:**|Požadováno, pouze pokud není zadán sdíleného přístupového podpisu kontejneru. Klíč účtu pro účet úložiště pro úlohu exportu.|  
    |**/csas:**|Požadováno, pouze pokud není zadaný klíč účtu úložiště. Sdíleného přístupového podpisu kontejneru pro výpis objektů BLOB nelze exportovat do úlohy exportu.|  
    |**/ ExportBlobListFile:**|Povinné Cesta k souboru XML soubor obsahující seznam cesty k objektům blob nebo objekt blob předpony cesty pro objekty BLOB nelze exportovat. Formát souboru používaný v `BlobListBlobPath` prvek [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace rozhraní REST API služby Import/Export.|  
    |**/ DriveSize:**|Povinné Velikost disků pro úlohu exportu *třeba*, 500 GB, 1,5 TB.|  

    Najdete v článku [příklad příkazu PreviewExport](#example-of-previewexport-command).
 
5. Zkontrolujte, jestli je možné čtení/zápisu pro jednotky, které budou dostupné pro úlohu exportu.

### <a name="example-of-previewexport-command"></a>Příklad příkazu PreviewExport

Následující příklad ukazuje, `PreviewExport` příkaz:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Export souboru seznamu objektů blob může obsahovat názvy objektů blob a objektů blob předpony, jak je znázorněno zde:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Nástroje Import/Export Azure vypíše všechny objekty BLOB export a vypočítá jak se zabalit do jednotky zadané velikosti, s ohledem všechny nezbytné režijní náklady a pak odhadne požadovaný počet jednotek, které jsou potřeba pro uložení objektů BLOB a informace o využití disku.  
  
Tady je příklad výstupu s informační protokoly vynechán:  
  
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

## <a name="examples-of-valid-blob-paths"></a>Příklady cesty k objektům blob platný

Následující tabulka uvádí příklady cesty k objektům blob platné:
   
   | Selektor | Cesta k objektu BLOB | Popis |
   | --- | --- | --- |
   | Začíná |/ |Exportuje všechny objekty BLOB v účtu úložiště |
   | Začíná |/$root / |Exportuje všechny objekty BLOB v kontejneru kořenové |
   | Začíná |/Book |Exportuje všechny objekty BLOB v kontejneru, který začíná předponou **knihy** |
   | Začíná |/Music/ |Exportuje všechny objekty BLOB v kontejneru **Hudba** |
   | Začíná |/ Hudba/láskou |Exportuje všechny objekty BLOB v kontejneru **Hudba** , začínat předponou **máte rádi.** |
   | Je rovno |$root/logo.bmp |Exporty blob **logo.bmp** v kořenovém kontejneru |
   | Je rovno |videos/Story.MP4 |Exporty blob **story.mp4** v kontejneru **videa** |

## <a name="next-steps"></a>Další postup

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na Import/Export](storage-import-export-requirements.md)


