---
title: Kurz kopírování dat na Azure Data Box Disk | Microsoft Docs
description: V tomto kurzu se naučíte, zkopírovat data z hostitelského počítače do Azure Data Box Disku a potom vygenerovat kontrolní součty k ověření jejich integrity.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d964efd6d9923190a6fef92c91d357a8a650572d
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766832"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Kurz: Kopírování dat na Azure Data Box Disk a jejich ověření

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Kopírování dat na Azure Data Box Disk a jejich ověření

Po připojení a odemknutí disků na ně můžete zkopírovat data ze zdrojového datového serveru. Po dokončení kopírování dat byste je měli ověřit, abyste se ujistili, že se úspěšně nahrají do Azure.

::: zone-end

::: zone target="docs"

Tento kurz popisuje, jak zkopírovat data z hostitelského počítače a potom vygenerovat kontrolní součty k ověření jejich integrity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření dat

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:
- Dokončili jste [Kurz: Instalace a konfigurace Azure Data Box Disku](data-box-disk-deploy-set-up.md).
- Vaše disky jsou odemknuté a připojené ke klientskému počítači.
- Klientský počítač, který se používá ke kopírování dat na disky, musí používat [podporovaný operační systém](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
- Ujistěte se, že zamýšlený typ úložiště pro vaše data odpovídá [podporovaným typům úložiště](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Projděte si [omezení spravovaných disků v omezeních velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Kopírování dat na disky

Před kopírováním dat na disky si projděte následující důležité informace:

- Je vaší povinností ujistit se, že jste data zkopírovali do složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do složky určené pro objekty blob bloku. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pozdější nahrávání dat do Azure se nezdaří.
- Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení Azure Storage a Data Box Disku](data-box-disk-limits.md).
- Pokud data nahrávaná Data Box Diskem zároveň nahrávají jiné aplikace mimo Data Box Disk, může to způsobit selhání úlohy nahrávání a poškození dat.

   > [!IMPORTANT]
   >  Pokud jste při vytváření objednávky jako jeden z cílů úložiště zadali spravované disky, platí pro vás informace v následující části.

- Ve skupině prostředků napříč všemi předem vytvořenými složkami a všemi Data Box Disky můžete mít pouze jeden spravovaný disk s daným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků. Pokud mají virtuální pevné disky stejný název, na spravovaný disk se převede pouze jeden virtuální pevný disk s tímto názvem. Ostatní virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky.
- Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud virtuální pevné disky zkopírujete mimo tyto složky nebo do složky, kterou jste vytvořili, nahrají se do účtu Azure Storage jako objekty blob stránky, a ne jako spravované disky.
- K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. Dynamické virtuální pevné disky, rozdílové virtuální pevné disky ani soubory VHDX se nepodporují.


Pokud chcete připojit počítač k Data Box Disku a zkopírovat z něj data, proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky. Seznam předem vytvořených složek a podsložek v jednotce se liší v závislosti na vybraných možnostech při vytváření objednávky Data Box Disku. Pokud předem vytvořená složka neexistuje, nevytvářejte ji, protože data zkopírovaná do složky vytvořené uživatelem se nepovede nahrát do Azure.

    |Vybraný cíl úložiště  |Typ účtu úložiště|Typ přípravného účtu úložiště |Složky a podsložky  |
    |---------|---------|---------|------------------|
    |Účet úložiště     |GPv1 nebo GPv2                 | Není k dispozici | BlockBlob <br> PageBlob <br> AzureFile        |
    |Účet úložiště     |Účet úložiště objektů blob         | Není k dispozici | BlockBlob        |
    |Spravované disky     |Není k dispozici | GPv1 nebo GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Účet úložiště <br> Spravované disky     |GPv1 nebo GPv2 | GPv1 nebo GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Účet úložiště <br> Spravované disky    |Účet úložiště objektů blob | GPv1 nebo GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Níže je uvedený snímek obrazovky s příkladem objednávky se zadaným účtem úložiště GPv2:

    ![Obsah diskové jednotky](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Zkopírujte data, která chcete importovat, jako objekty blob bloku do složky *BlockBlob*. Data jako VHD/VHDX podobně zkopírujte do složky *PageBlob* a vstupní data do složky *AzureFile*.

    V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory ve složkách BlockBlob a PageBlob se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Všechny soubory v kontejneru `$root` se vždycky nahrají jako objekty blob bloku.

   Zkopírujte soubory do složky ve složce *AzureFile*. Podsložka ve složce *AzureFile* vytvoří sdílenou složku. Soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.

    Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.

    > [!IMPORTANT]
    > Všechny kontejnery, objekty blob a názvy souborů by měly být v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.

3. Při kopírování souborů se ujistěte, že velikost souborů nepřekračuje ~4,7 TiB v případě objektů blob bloku, ~8 TiB v případě objektů blob stránky a ~1 TiB v případě souborů Azure. 
4. Ke kopírování dat můžete použít funkci přetahování v Průzkumníku souborů. Ke kopírování dat můžete taky použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí následujícího příkazu Robocopy se dá zahájit několik úloh kopírování najednou:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Následující tabulka obsahuje parametry a možnosti příkazu:
    
    |Parametry/možnosti  |Popis |
    |--------------------|------------|
    |Zdroj            | Určuje cestu ke zdrojovému adresáři.        |
    |Cíl       | Určuje cestu k cílovému adresáři.        |
    |/E                  | Zkopíruje podadresáře včetně prázdných adresářů. |
    |/MT[:N]             | Vytvoří vícevláknové kopie s N vlákny, kde N je celé číslo mezi 1 a 128. <br>Výchozí hodnota N je 8.        |
    |/R: \<N>             | Určuje počet opakovaných pokusů při neúspěšném kopírování. Výchozí hodnota N je 1 000 000 (jeden milion opakování).        |
    |/W: \<N>             | Určuje dobu čekání mezi opakovanými pokusy v sekundách. Výchozí hodnota N je 30 (čeká se 30 sekund).        |
    |/NFL                | Určuje, že se nemají protokolovat názvy souborů.        |
    |/NDL                | Určuje, že se nemají protokolovat názvy adresářů.        |
    |/FFT                | Přebírá časy systému souborů FAT (s přesností na 2 sekundy).        |
    |/Log:\<Log File>     | Zapíše výstup stavu do souboru protokolu (přepíše existující soubor protokolu).         |

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
 
    Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy.

    |    Platforma    |    Vetšinou malé soubory (< 512 kB)                           |    Většinou středně velké soubory (512 kB až 1 MB)                      |    Většinou velké soubory (> 1 MB)                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 relace Robocopy* <br> 16 vláken na relaci    |    2 relace Robocopy* <br> 16 vláken na relaci    |    2 relace Robocopy* <br> 16 vláken na relaci    |
    
    **V každé relaci Robocopy se podporuje maximálně 7 000 adresářů a 150 milionů souborů.*
    
    >[!NOTE]
    > Výše navrhované parametry vycházejí z prostředí sloužícího k internímu testování.
    
    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

6. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu jsou uložené v umístění, které udává příkaz robocopy.
 
### <a name="split-and-copy-data-to-disks"></a>Rozdělení a kopírování dat na disky

Tento volitelný postup můžete použít v případě, že používáte více disků a máte velkou datovou sadu, kterou je potřeba rozdělit a zkopírovat mezi všechny disky. Nástroj Data Box Split Copy pomáhá rozdělit a kopírovat data na počítači s Windows.

>[!IMPORTANT]
> Nástroj Data Box Split Copy vaše data také ověřuje. Pokud ke kopírování dat použijete nástroj Data Box Split Copy, můžete přeskočit [krok ověření](#validate-data).
> Nástroj Split Copy se nepodporuje u spravovaných disků.

1. Ujistěte se, že na počítači s Windows máte stažený nástroj Data Box Split Copy, který je extrahovaný v místní složce. Tento nástroj se stáhnul při stahování sady nástrojů Data Box Disk pro Windows.
2. Otevřete Průzkumníka souborů. Poznamenejte si písmeno jednotky zdroje dat a písmeno jednotky přiřazené k Data Box Disku. 

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifikujte zdrojová data, která se mají zkopírovat. Například v tomto případě:
    - Identifikovala se následující data objektu blob bloku.

         ![Rozdělení kopírování dat 2](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Identifikovala se následující data objektu blob stránky.

         ![Rozdělení kopírování dat 3](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Přejděte do složky s extrahovaným softwarem. V této složce vyhledejte soubor `SampleConfig.json`. Jedná se o soubor jen pro čtení, který můžete upravit a uložit.

   ![Rozdělení kopírování dat 4](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Upravte soubor `SampleConfig.json`.
 
   - Zadejte název úlohy. Tím se na Data Box Disku vytvoří složka, ze které se nakonec stane kontejner v účtu úložiště Azure přidruženém k těmto diskům. Název úlohy musí být v souladu se zásadami vytváření názvů kontejnerů v Azure. 
   - Do souboru `SampleConfigFile.json` zadejte zdrojovou cestu a poznamenejte si její formát. 
   - Zadejte písmena jednotek odpovídající cílovým diskům. Data se načtou ze zdrojové cesty a zkopírují se mezi několik disků.
   - Zadejte cestu pro soubory protokolů. Ve výchozím nastavení se odesílají do aktuálního adresáře, ve kterém se nachází soubor `.exe`.

     ![Rozdělení kopírování dat 5](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Pokud chcete zkontrolovat formát souboru, přejděte do nástroje `JSONlint`. Uložte soubor jako `ConfigFile.json`. 

     ![Rozdělení kopírování dat 6](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Otevřete okno příkazového řádku. 

8. Spusťte `DataBoxDiskSplitCopy.exe`. Typ

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Rozdělení kopírování dat 7](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Stiskněte Enter a pokračujte v provádění skriptu.

    ![Rozdělení kopírování dat 8](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Jakmile se datová sada rozdělí a zkopíruje, zobrazí se souhrn nástroje Split Copy pro relaci kopírování. Ukázkový výstup najdete níž.

    ![Rozdělení kopírování dat 9](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Ověřte, že se data rozdělila mezi cílové disky. 
 
    ![Rozdělení kopírování dat 10](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Rozdělení kopírování dat 11](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Když blíže prozkoumáte obsah jednotky `n:`, uvidíte, že se vytvořily dvě podsložky odpovídající datům ve formátu objektu blob bloku a objektu blob stránky.
    
     ![Rozdělení kopírování dat 12](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Pokud operace kopírování selže, použijte k obnovení a pokračování následující příkaz:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Pokud se při používání nástroje Split Copy zobrazí chyby, přejděte k tématu věnovanému [řešení potíží s chybami nástroje Split Copy](data-box-disk-troubleshoot-data-copy.md).

Po dokončení kopírování dat můžete přejít k jejich ověření. Pokud jste použili nástroj Split Copy, přeskočte ověření (nástroj Split Copy také provádí ověřování) a přejděte k dalšímu kurzu.


## <a name="validate-data"></a>Ověření dat

Pokud jste ke kopírování dat nepoužili nástroj Split Copy, budete svá data muset ověřit. Data ověříte následujícím postupem.

1. Spusťte soubor `DataBoxDiskValidation.cmd` pro ověření kontrolního součtu ve složce *DataBoxDiskImport* na jednotce. Tato možnost je k dispozici jenom pro prostředí Windows. Uživatelé Linuxu musí ověřit, že zdrojová data, která se kopírují na disk, splňují příslušné [požadavky](https://docs.microsoft.com/azure/databox/data-box-disk-limits).
    
    ![Výstup ověřovacího nástroje Data Box Disku](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Zvolte příslušnou možnost. **Doporučujeme vždy ověřit soubory a vygenerovat kontrolní součty výběrem možnosti 2**. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Po dokončení skriptu ukončete příkazové okno. Pokud během ověřování a generování kontrolního součtu došlo k chybě, budete na to upozorněni a obdržíte odkaz na protokoly chyb.

    ![Výstup kontrolního součtu](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Mezi dvěma spuštěními nástroj resetujte.
    > - Možnost 1 použijte v případě zpracování velkých datových sad obsahujících malé soubory (v řádu kB). Tato možnost ověřuje pouze soubory, protože generování kontrolního součtu může trvat velmi dlouho a výkon může být velmi pomalý.

3. Pokud používáte víc disků, spusťte příkaz pro každý disk.

Pokud se při ověřování zobrazí chyby, přečtěte si téma věnované [řešení potíží s chybami ověření](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření integrity dat

V dalším kurzu se dozvíte, jak poslat Data Box Disk zpátky a ověřit nahrání dat do Azure.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu zpátky Microsoftu](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>Kopírování dat na disky

Pokud chcete připojit počítač k Data Box Disku a zkopírovat z něj data, proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky. Seznam předem vytvořených složek a podsložek v jednotce se liší v závislosti na vybraných možnostech při vytváření objednávky Data Box Disku.
2. Zkopírujte data do složek odpovídajících příslušnému formátu dat. Například nestrukturovaná data zkopírujte do složky *BlockBlob*, data VHD nebo VHDX do složky *PageBlob* a soubory do složky *AzureFile*. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pozdější nahrávání dat do Azure se nezdaří.

    - Ujistěte se, že všechny kontejnery, objekty blob a soubory jsou v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) a [omezeními velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Při nedodržení těchto pravidel nebo omezení se odesílání dat do Azure nezdaří.     
    - Pokud jsou jedním z cílů úložiště ve vaší objednávce spravované disky, projděte si zásady vytváření názvů pro [spravované disky](data-box-disk-limits.md#managed-disk-naming-conventions).
    - V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory ve složkách *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru $root v účtu Azure Storage. Všechny soubory v kontejneru $root se vždycky nahrají jako objekty blob bloku.
    - Ve složce *AzureFile* vytvořte podsložku. Tato podsložka se namapuje na sdílenou složku v cloudu. Zkopírujte soubory do této podsložky. Soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.
    - Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.

3. Ke kopírování můžete využít přetahování v Průzkumníku souborů nebo jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí následujícího příkazu se dá zahájit několik úloh kopírování najednou:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu jsou uložené v umístění, které udává příkaz robocopy.

Tuto volitelnou proceduru [rozdělení a kopírování](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) využijte v případě, že používáte více disků a máte velkou datovou sadu, kterou je potřeba rozdělit a zkopírovat mezi všechny disky.

### <a name="validate-data"></a>Ověření dat

Při ověřování dat postupujte následovně.

1. Spusťte soubor `DataBoxDiskValidation.cmd` pro ověření kontrolního součtu ve složce *DataBoxDiskImport* na jednotce.
2. Pomocí možnosti 2 ověřte soubory a vygenerujte kontrolní součty. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Pokud během ověřování a generování kontrolního součtu došlo k chybě, budete na to upozorněni a obdržíte odkaz na protokoly chyb.

    Další informace o ověřování dat najdete v tématu věnovaném [ověřování dat](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data). Pokud při ověřování dojde k chybám, přečtěte si téma věnované [řešení potíží s chybami ověření](data-box-disk-troubleshoot.md).

::: zone-end
