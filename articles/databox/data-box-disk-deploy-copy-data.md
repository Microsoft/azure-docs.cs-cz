---
title: Kopírování dat na Microsoft Azure Data Box Disk | Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak zkopírovat data na Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010816"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Kurz: Kopírování dat na Azure Data Box Disk a jejich ověření

Tento kurz popisuje, jak zkopírovat data z hostitelského počítače a potom vygenerovat kontrolní součty k ověření jejich integrity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření integrity dat

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:
- Máte za sebou [Kurz: Instalace a konfigurace Azure Data Box Disku](data-box-disk-deploy-set-up.md).
- Vaše disky jsou rozbalené a zapnuté.
- Máte hostitelský počítač, ze kterého chcete zkopírovat data na disky. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-disk-system-requirements.md).
    - Musí na něm být [nainstalované prostředí Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855).
    - Musí na něm být [nainstalované rozhraní .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653).


## <a name="copy-data-to-disks"></a>Kopírování dat na disky

Pokud chcete připojit počítač k Data Box Disku a zkopírovat z něj data, proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky. 

    ![Zobrazení obsahu jednotky](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Zkopírujte data, která chcete importovat, jako objekty blob bloku do složky BlockBlob. Data jako VHD/VHDX podobně zkopírujte do složky PageBlob. 

    V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory ve složkách BlockBlob a PageBlob se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Všechny soubory v kontejneru `$root` se vždycky nahrají jako objekty blob bloku.

    Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.

    Při volbě názvů kontejnerů a objektů blob se řiďte požadavky na názvy v Azure.

    |Entita   |Zásady  |
    |---------|---------|
    |Názvy kontejnerů pro objekty blob bloku a objekty blob stránky     |Musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a pomlčky (-). Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. Názvy nesmí obsahovat po sobě jdoucí pomlčky. <br>Musí se jednat o platný název DNS o délce 3 až 63 znaků.          |
    |Názvy objektů blob bloku a objektů blob stránky    |Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. <br>Název objektu blob musí mít délku 1 až 1024 znaků.<br>Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek.<br>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře.         |

    > [!IMPORTANT] 
    > Všechny kontejnery a objekty blob by měly být v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.

3. Při kopírování souborů se ujistěte, že velikost souborů nepřekračuje ~4,7 TiB v případě objektů blob bloku a 8 TiB v případě objektů blob stránky. 
4. Ke kopírování dat můžete použít funkci přetahování v Průzkumníku souborů. Ke kopírování dat můžete taky použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí následujícího příkazu Robocopy se dá zahájit několik úloh kopírování najednou:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Následující tabulka obsahuje parametry a možnosti příkazu:
    
    |Parametry/možnosti  |Popis |
    |--------------------|------------|
    |<Source>            | Určuje cestu ke zdrojovému adresáři.        |
    |<Destination>       | Určuje cestu k cílovému adresáři.        |
    |/E                  | Zkopíruje podadresáře včetně prázdných adresářů. |
    |/MT[:N]             | Vytvoří vícevláknové kopie s N vlákny, kde N je celé číslo mezi 1 a 128. <br>Výchozí hodnota N je 8.        |
    |/R: <N>             | Určuje počet opakovaných pokusů při neúspěšném kopírování. Výchozí hodnota N je 1 000 000 (jeden milion opakování).        |
    |/W: <N>             | Určuje dobu čekání mezi opakovanými pokusy v sekundách. Výchozí hodnota N je 30 (čeká se 30 sekund).        |
    |/NFL                | Určuje, že se nemají protokolovat názvy souborů.        |
    |/NDL                | Určuje, že se nemají protokolovat názvy adresářů.        |
    |/FFT                | Přebírá časy systému souborů FAT (s přesností na 2 sekundy).        |
    |/Log:<Log File>     | Zapíše výstup stavu do souboru protokolu (přepíše existující soubor protokolu).         |

    Je možné použít více disků současně a na každém disku může běžet několik úloh. 

6. Během provádění úlohy můžete zkontrolovat stav kopírování. Následující příklad ukazuje výstup příkazu robocopy, který slouží ke kopírování souborů na Data Box Disk.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu jsou uložené v umístění, které udává příkaz robocopy.
 


> [!IMPORTANT]
> - Je vaší povinností ujistit se, že jste data zkopírovali do složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do složky určené pro objekty blob bloku. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pozdější nahrávání dat do Azure se nezdaří.
> -  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení Azure Storage a Data Box Disku](data-box-disk-limits.md). 
> - Pokud data nahrávaná Data Box Diskem zároveň nahrávají jiné aplikace mimo Data Box Disk, může to způsobit selhání úlohy nahrávání a poškození dat.

## <a name="verify-data-integrity"></a>Ověření integrity dat

Pokud chcete ověřit integritu dat, proveďte následující kroky.

1. Spusťte příkaz `AzureExpressDiskService.ps1`, který provede ověření kontrolního součtu. V Průzkumníku souborů přejděte v jednotce do složky *AzureImportExport*. Klikněte pravým tlačítkem a zvolte **Spustit v prostředí PowerShell**. 

    ![Spuštění kontrolního součtu](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Po dokončení skriptu se zobrazí souhrn procesu kontroly integrity dat s časem dokončení procesu. Okno příkazového řádku zavřete stisknutím klávesy **Enter**.

    ![Výstup kontrolního součtu](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Pokud používáte víc disků, spusťte příkaz pro každý disk.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření integrity dat

V dalším kurzu se dozvíte, jak poslat Data Box Disk zpátky a ověřit nahrání dat do Azure.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu zpátky Microsoftu](./data-box-disk-deploy-picked-up.md)

