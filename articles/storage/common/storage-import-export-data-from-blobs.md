---
title: Použití importu a exportu Azure k exportu dat z objektů Blob Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak na webu Azure Portal vytvářet úlohy exportu pro přenos dat z objektů Blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a5afa6439caa6b7c1572447e3b212f3357bf296a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282507"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Službu Azure Import/Export můžete použít k exportu dat z úložiště objektů blob v Azure.

Tento článek obsahuje podrobné pokyny, jak používat službu Azure Import/Export k bezpečnému exportu velkého množství dat z úložiště objektů blob Azure. Služba vyžaduje, abyste do datového centra Azure doručovat prázdné jednotky. Služba exportuje data z vašeho účtu úložiště na jednotky a potom je vrátí zpět.

## <a name="prerequisites"></a>Požadavky

Před vytvořením úlohy exportu pro přenos dat z azure blob storage pečlivě zkontrolujte a vyplňte následující seznam předpokladů pro tuto službu.
Musíte:

- Mít aktivní předplatné Azure, které lze použít pro službu Import/Export.
- Mít alespoň jeden účet Azure Storage. Podívejte se na seznam [podporovaných účtů úložiště a typů úložišť pro službu Import/Export](storage-import-export-requirements.md). Informace o vytvoření nového účtu úložiště najdete v [tématu Jak vytvořit účet úložiště](storage-account-create.md).
- Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
- Měj účet FedEx/DHL. Pokud chcete použít jiného operátora než FedEx/DHL, kontaktujte tým Azure Data Box Operations na adrese `adbops@microsoft.com`.
  - Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení zásilky.
  - Vygenerujte sledovací číslo pro úlohu exportu.
  - Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným sledovacím číslem není podporováno.
  - Pokud nemáte účet dopravce, přejděte na:
    - [Vytvořte si účet FedEX](https://www.fedex.com/en-us/create-account.html)nebo
    - [Vytvořte si účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1: Vytvoření úlohy exportu

Chcete-li vytvořit úlohu exportu na webu Azure Portal, proveďte následující kroky.

1. Přihlaste https://portal.azure.com/se k souboru .
2. Přejděte na **Všechny služby > úlohy importu > exportu**.

    ![Přejít na úlohy importu a exportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klepněte na **tlačítko Vytvořit úlohu importu nebo exportu**.

    ![Klikněte na Importovat nebo exportovat úlohu.](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. V **základech**:

    - Vyberte **Export z Azure**.
    - Zadejte popisný název úlohy exportu. Použijte název, který zvolíte ke sledování průběhu úloh.
        - Název může obsahovat pouze malá písmena, číslice, pomlčky a podtržítka.
        - Název musí začínat písmenem a nesmí obsahovat mezery.
    - Vyberte předplatné.
    - Zadejte nebo vyberte skupinu prostředků.

        ![Základy](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. V **podrobnostech o úloze**:

    - Vyberte účet úložiště, ve kterém se nacházejí data, která mají být exportována. Použijte účet úložiště v blízkosti místa, kde se nacházíte.
    - Umístění dropoff je automaticky naplněno na základě oblasti vybraného účtu úložiště.
    - Zadejte data objektu blob, která chcete exportovat z účtu úložiště do prázdné jednotky nebo jednotek.
    - Zvolte **exportovat všechna** data objektu blob v účtu úložiště.

         ![Exportovat vše](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Můžete určit, které kontejnery a objekty BLOB chcete exportovat.
        - **Určení objektu blob k exportu**: Použijte volič **Rovná se.** Zadejte relativní cestu k objektu blob, počínaje názvem kontejneru. Pomocí *$root* určete kořenový kontejner.
        - **Chcete-li určit všechny objekty BLOB začínající předponou**: Použijte volič **Začíná s.** Zadejte předponu začínající lomítkem '/'. Předponou může být předpona názvu kontejneru, úplný název kontejneru nebo úplný název kontejneru následovaný předponou názvu objektu blob. Je nutné zadat cesty objektu blob v platném formátu, aby se zabránilo chybám během zpracování, jak je znázorněno na tomto snímku obrazovky. Další informace naleznete v [tématu Příklady platných cest objektů blob](#examples-of-valid-blob-paths).

           ![Export vybraných kontejnerů a objektů BLOB](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Exportovat můžete ze souboru seznamu objektů blob.

        ![Export ze souboru seznamu objektů blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Pokud objekt blob, který má být exportován, se používá během kopírování dat, služba Import/Export Azure pořídí snímek objektu blob a zkopíruje snímek.

6. V **zpáteční přepravní informace**:

    - V rozevíracím seznamu vyberte operátora. Pokud chcete použít jiného operátora než FedEx/DHL, zvolte stávající možnost z rozbalovací nabídky. Obraťte se na `adbops@microsoft.com` azure data box operations team na informace týkající se dopravce, který plánujete použít.
    - Zadejte platné číslo účtu dopravce, které jste vytvořili s tímto dopravcem. Společnost Microsoft používá tento účet k odeslání jednotek zpět k vám po dokončení exportní úlohy.
    - Uveďte úplné a platné kontaktní jméno, telefon, e-mail, adresu, město, zip, stát/provincii a zemi/oblast.

        > [!TIP]
        > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinový e-mail. Tím zajistíte, že budete dostávat oznámení i v případě, že správce odejde.

7. V **souhrnu**:

    - Zkontrolujte podrobnosti o úloze.
    - Poznamenejte si název úlohy a zadejte doručovací adresu datového centra Azure pro odesílání disků do Azure.

        > [!NOTE]
        > Disky vždy odesílejte do datového centra uvedeného na webu Azure Portal. Pokud jsou disky dodány do nesprávného datového centra, úloha nebude zpracována.

    - Chcete-li dokončit vytváření úloh exportu, klepněte na tlačítko **OK.**

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Krok 2: Odeslání disků

Pokud neznáte potřebný počet jednotek, přejděte na [seznam Zkontrolovat počet jednotek](#check-the-number-of-drives). Pokud znáte počet jednotek, pokračujte k odeslání disků.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Aktualizace úlohy informacemi o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Krok 4: Příjem disků

Když řídicí panel hlásí, že úloha je dokončena, disky jsou odeslány k vám a sledovací číslo pro zásilku je k dispozici na portálu.

1. Po obdržení jednotek s exportovanými daty je třeba získat klíče nástroje BitLocker k odemknutí jednotek. Přejděte na úlohu exportu na webu Azure Portal. Klikněte na kartu **Importovat nebo exportovat.**
2. Vyberte a klikněte na úlohu exportu ze seznamu. Přejděte na **Šifrování** a zkopírujte klíče.

   ![Zobrazit klíče nástroje BitLocker pro úlohu exportu](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. Pomocí kláves nástroje BitLocker odemkněte disky.

Export je dokončen.

## <a name="step-5-unlock-the-disks"></a>Krok 5: Odemknutí disků

Pokud používáte verzi 1.4.0.300 nástroje WAImportExport, odemkněte jednotku pomocí následujícího příkazu:

    `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`  

Pokud používáte starší verze nástroje, odemkněte jednotku pomocí dialogového okna Nástrojem bitlocker.

V tomto okamžiku můžete úlohu odstranit nebo ji opustit. Úlohy se automaticky odstraní po 90 dnech.

## <a name="check-the-number-of-drives"></a>Zkontrolujte počet jednotek

Tento *volitelný* krok vám pomůže určit počet jednotek požadovaných pro úlohu exportu. Tento krok proveďte v systému Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).

1. [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/download/details.aspx?id=42659) v systému Windows.
2. Rozbalte výchozí složku `waimportexportv1`. Například, `C:\WaImportExportV1`.
3. Otevřete powershellnebo okno příkazového řádku s oprávněními správce. Chcete-li změnit adresář na rozbalenou složku, spusťte následující příkaz:

    `cd C:\WaImportExportV1`

4. Chcete-li zkontrolovat počet disků požadovaných pro vybrané objekty BLOB, spusťte následující příkaz:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry jsou popsány v následující tabulce:

    |Parametr příkazového řádku|Popis|  
    |--------------------------|-----------------|  
    |**/logdir:**|Nepovinný parametr. Adresář protokolu. Podrobné soubory protokolu jsou zapsány do tohoto adresáře. Pokud není zadán, aktuální adresář se používá jako adresář protokolu.|  
    |**/sn:**|Povinná hodnota. Název účtu úložiště pro úlohu exportu.|  
    |**/sk:**|Povinné pouze v případě, že není zadán kontejner SAS. Klíč účtu pro účet úložiště pro úlohu exportu.|  
    |**/csas:**|Povinné pouze v případě, že není zadán klíč účtu úložiště. Kontejner SAS pro výpis objektů BLOB, které mají být exportovány v úloze exportu.|  
    |**/ExportBlobListFile:**|Povinná hodnota. Cesta k souboru XML obsahující seznam cest objektů blob nebo předpon cesty objektů blob pro objekty BLOB, které mají být exportovány. Formát souboru použitý `BlobListBlobPath` v prvku v operaci [Put Job](/rest/api/storageimportexport/jobs) rozhraní REST API služby Import/Export.|  
    |**/DriveSize:**|Povinná hodnota. Velikost jednotek, které mají být používány pro úlohu exportu, *například*500 GB, 1,5 TB.|  

    Podívejte se na [příklad příkazu PreviewExport](#example-of-previewexport-command).

5. Zkontrolujte, zda můžete číst nebo zapisovat na jednotky, které budou dodány pro úlohu exportu.

### <a name="example-of-previewexport-command"></a>Příklad příkazu PreviewExport

Následující příklad ukazuje `PreviewExport` příkaz:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

Soubor seznamu objektů blob exportu může obsahovat názvy objektů blob a předpony objektů BLOB, jak je znázorněno zde:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Nástroj pro import a export Azure obsahuje seznam všech objektů BLOB, které mají být exportovány, a vypočítá, jak je zabalit do jednotek zadané velikosti s přihlédnutím k nezbytné režii a poté odhadne počet jednotek potřebných k uložení objektů BLOB a informací o využití jednotky.  

Zde je příklad výstupu s informační protokoly vynechány:  

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

## <a name="examples-of-valid-blob-paths"></a>Příklady platných cest objektů blob

V následující tabulce jsou uvedeny příklady platných cest objektů blob:

   | Volič | Cesta k blob | Popis |
   | --- | --- | --- |
   | Začíná |/ |Exportuje všechny objekty BLOB v účtu úložiště. |
   | Začíná |/$root/ |Exportuje všechny objekty BLOB v kořenovém kontejneru. |
   | Začíná |/kniha |Exportuje všechny objekty BLOB v libovolném kontejneru, který začíná **předponou knihy** |
   | Začíná |/hudba/ |Exportuje všechny objekty BLOB v kontejnerové **hudbě.** |
   | Začíná |/hudba/láska |Exportuje všechny objekty BLOB v **nádobě,** která začíná předponou **lásky** |
   | Rovno |$root/logo.bmp |Exportuje **soubor logo blob.bmp** v kořenovém kontejneru. |
   | Rovno |videa/příběh.mp4 |Exportuje objekt blob **story.mp4** v kontejnerových **videích.** |

## <a name="next-steps"></a>Další kroky

- [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
- [Zkontrolovat požadavky na import a export](storage-import-export-requirements.md)
