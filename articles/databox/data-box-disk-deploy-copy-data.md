---
title: Kurz kopírování dat do Azure Data Box Disk | Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak zkopírovat data na Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 336cc7dae00d06e38e4be8671f1cb11ed73e5edc
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414639"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Kurz: Kopírovat data Azure Data Box Disk a ověřit

Tento kurz popisuje, jak zkopírovat data z hostitelského počítače a potom vygenerovat kontrolní součty k ověření jejich integrity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření dat

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:
- Dokončili [jste kurz: Nainstalujte a nakonfigurujte Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Vaše disky jsou odemknuté a připojené ke klientskému počítači.
- Klientský počítač, který se používá ke kopírování dat na disky, musí používat [podporovaný operační systém](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Ujistěte se, že zamýšlený typ úložiště pro vaše data odpovídá [podporovaným typům úložiště](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Zkontrolujte [omezení na spravovaných discích v omezeních velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Kopírování dat na disky

Před kopírováním dat na disky si Projděte následující skutečnosti:

- Je vaší povinností ujistit se, že jste data zkopírovali do složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do složky určené pro objekty blob bloku. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pozdější nahrávání dat do Azure se nezdaří.
- Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení Azure Storage a Data Box Disku](data-box-disk-limits.md).
- Pokud data nahrávaná Data Box Diskem zároveň nahrávají jiné aplikace mimo Data Box Disk, může to způsobit selhání úlohy nahrávání a poškození dat.

   > [!IMPORTANT]
   >  Pokud jste při vytváření objednávky zadali spravované disky jako jedno z cílových umístění úložiště, je možné použít následující oddíl.

- V rámci všech předdefinovaných složek a všech Data Box Disk můžete mít ve skupině prostředků jenom jeden spravovaný disk se zadaným názvem. To znamená, že virtuální pevné disky nahrané do předvytvořených složek musí mít jedinečné názvy. Ujistěte se, že se daný název neshoduje s existujícím spravovaným diskem ve skupině prostředků. Pokud mají virtuální pevné disky stejné názvy, převede se na spravovaný disk s tímto názvem jenom jeden VHD. Ostatní virtuální pevné disky se nahrají jako objekty blob stránky do pracovního účtu úložiště.
- Virtuální pevné disky vždycky zkopírujte do jedné z předvytvořených složek. Pokud zkopírujete virtuální pevné disky mimo tyto složky nebo do složky, kterou jste vytvořili, virtuální pevné disky se nahrají do Azure Storage účtu jako objekty blob stránky a ne spravované disky.
- Pouze pevné virtuální pevné disky lze odeslat pro vytvoření spravovaných disků. Dynamické virtuální pevné disky, rozdílové VHD nebo soubory VHDX nejsou podporovány.


Pokud chcete připojit počítač k Data Box Disku a zkopírovat z něj data, proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky. Seznam předdefinovaných složek a podsložek v jednotce se liší v závislosti na možnostech vybraných při umístění Data Box Diskho pořadí.

    |Cíl vybraného úložiště  |Typ účtu úložiště|Typ účtu přípravného úložiště |Složky a podsložky  |
    |---------|---------|---------|------------------|
    |Účet úložiště     |GPv1 nebo GPv2                 | Není k dispozici | BlockBlob <br> PageBlob <br> AzureFile        |
    |Účet úložiště     |Účet úložiště objektů BLOB         | Není k dispozici | BlockBlob        |
    |Spravované disky     |Není k dispozici | GPv1 nebo GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Účet úložiště <br> Spravované disky     |GPv1 nebo GPv2 | GPv1 nebo GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Účet úložiště <br> Spravované disky    |Účet úložiště objektů BLOB | GPv1 nebo GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Ukázkový snímek obrazovky objednávky, kde se zadal účet úložiště GPv2, je uvedený níže:

    ![Obsah diskové jednotky](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Zkopírujte data, která je třeba importovat jako objekty blob bloku do složky *BlockBlob* . Podobně zkopírujte data, jako jsou VHD/VHDX, do složky *PageBlob* a data ve složce *AzureFile* .

    V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory ve složkách BlockBlob a PageBlob se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Všechny soubory v kontejneru `$root` se vždycky nahrají jako objekty blob bloku.

   Zkopírujte soubory do složky ve složce *AzureFile* . Podsložka v rámci složky *AzureFile* vytvoří sdílenou složku. Soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.

    Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.

    > [!IMPORTANT]
    > Všechny kontejnery, objekty BLOB a názvy souborů by měly odpovídat konvencím [pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.

3. Při kopírování souborů zajistěte, aby soubory nepřesáhly ~ 4,7 TiB pro objekty blob bloku, ~ 8 TiB pro objekty blob stránky a ~ 1 TiB pro soubory Azure. 
4. Ke kopírování dat můžete použít funkci přetahování v Průzkumníku souborů. Ke kopírování dat můžete taky použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí následujícího příkazu Robocopy se dá zahájit několik úloh kopírování najednou:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Následující tabulka obsahuje parametry a možnosti příkazu:
    
    |Parametry/možnosti  |Popis |
    |--------------------|------------|
    |Source            | Určuje cestu ke zdrojovému adresáři.        |
    |Cíl       | Určuje cestu k cílovému adresáři.        |
    |/E                  | Zkopíruje podadresáře včetně prázdných adresářů. |
    |/MT[:N]             | Vytvoří vícevláknové kopie s N vlákny, kde N je celé číslo mezi 1 a 128. <br>Výchozí hodnota N je 8.        |
    |Í \<N>             | Určuje počet opakovaných pokusů při neúspěšném kopírování. Výchozí hodnota N je 1 000 000 (jeden milion opakování).        |
    |/W: \<N>             | Určuje dobu čekání mezi opakovanými pokusy v sekundách. Výchozí hodnota N je 30 (čeká se 30 sekund).        |
    |/NFL                | Určuje, že se nemají protokolovat názvy souborů.        |
    |/NDL                | Určuje, že se nemají protokolovat názvy adresářů.        |
    |/FFT                | Přebírá časy systému souborů FAT (s přesností na 2 sekundy).        |
    |/Log:\<> souboru protokolu     | Zapíše výstup stavu do souboru protokolu (přepíše existující soubor protokolu).         |

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
    |    Data Box Disk        |    4 relace nástroje Robocopy * <br> 16 vláken na relaci    |    2 Robocopy relací * <br> 16 vláken na relaci    |    2 Robocopy relací * <br> 16 vláken na relaci    |
    
    **Každá relace Robocopy může mít maximálně 7 000 adresářů a 150 000 000 souborů.*
    
    >[!NOTE]
    > Výše navržené parametry jsou založené na prostředí, které se používá při interním testování.
    
    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

6. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu se nacházejí v příkazu Robocopy, jak jsou uvedeny.
 
### <a name="split-and-copy-data-to-disks"></a>Rozdělení a kopírování dat na disky

Tento volitelný postup můžete použít v případě, že používáte více disků a máte velkou datovou sadu, kterou je potřeba rozdělit a zkopírovat mezi všechny disky. Nástroj Data Box Split Copy pomáhá rozdělit a kopírovat data na počítači s Windows.

>[!IMPORTANT]
> Nástroj pro Data Box rozdělené kopírování také ověřuje vaše data. Použijete-li nástroj Data Box rozdělit kopírování pro kopírování dat, můžete přeskočit [krok ověření](#validate-data).
> Nástroj pro dělení na kopírování není u spravovaných disků podporován.

1. Ujistěte se, že na počítači s Windows máte stažený nástroj Data Box Split Copy, který je extrahovaný v místní složce. Tento nástroj se stáhnul při stahování sady nástrojů Data Box Disk pro Windows.
2. Otevřete Průzkumníka souborů. Poznamenejte si písmeno jednotky zdroje dat a písmeno jednotky přiřazené k Data Box Disku. 

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifikujte zdrojová data, která se mají zkopírovat. Například v tomto případě:
    - Identifikovala se následující data objektu blob bloku.

         ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Identifikovala se následující data objektu blob stránky.

         ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Přejděte do složky s extrahovaným softwarem. `SampleConfig.json` Vyhledejte soubor v této složce. Jedná se o soubor jen pro čtení, který můžete upravit a uložit.

   ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. `SampleConfig.json` Upravte soubor.
 
   - Zadejte název úlohy. Tím se na Data Box Disku vytvoří složka, ze které se nakonec stane kontejner v účtu úložiště Azure přidruženém k těmto diskům. Název úlohy musí být v souladu se zásadami vytváření názvů kontejnerů v Azure. 
   - Zadejte zdrojovou cestu, která poznamenejte formát cesty v `SampleConfigFile.json`. 
   - Zadejte písmena jednotek odpovídající cílovým diskům. Data se načtou ze zdrojové cesty a zkopírují se mezi několik disků.
   - Zadejte cestu pro soubory protokolů. Ve výchozím nastavení se pošle do aktuálního adresáře, kde `.exe` se nachází.

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Formát souboru ověříte tak, že přejdete `JSONlint`na. Uložte soubor jako `ConfigFile.json`. 

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Otevřete okno příkazového řádku. 

8. `DataBoxDiskSplitCopy.exe`Spusťte. type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Stiskněte Enter a pokračujte v provádění skriptu.

    ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Jakmile se datová sada rozdělí a zkopíruje, zobrazí se souhrn nástroje Split Copy pro relaci kopírování. Ukázkový výstup najdete níž.

    ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Ověřte, že se data rozdělila mezi cílové disky. 
 
    ![Rozdělit data kopírování – rozdělit data![](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Pokud prohlížíte obsah `n:` jednotky dále, uvidíte, že jsou vytvořeny dvě podsložky, které odpovídají datům objektů blob bloku a formátu objektů blob stránky.
    
     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Pokud operace kopírování selže, použijte k obnovení a pokračování následující příkaz:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Pokud se zobrazí chyby pomocí nástroje pro rozdělené kopírování, přejděte na téma Jak [řešit potíže s rozděleným kopírováním nástrojů](data-box-disk-troubleshoot-data-copy.md).

Po dokončení kopírování dat můžete pokračovat v ověřování dat. Pokud jste použili nástroj pro rozdělené kopírování, přeskočte ověření (Nástroj pro rozdělený kopírování) a přejděte k dalšímu kurzu.


## <a name="validate-data"></a>Ověření dat

Pokud jste k kopírování dat nepoužili Nástroj pro dělení na kopírování, budete potřebovat ověřit data. Data ověříte následujícím postupem.

1. Spusťte soubor `DataBoxDiskValidation.cmd` pro ověření kontrolního součtu ve složce *DataBoxDiskImport* na jednotce.
    
    ![Výstup ověřovacího nástroje Data Box Disku](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Zvolte příslušnou možnost. **Doporučujeme vždy ověřit soubory a vygenerovat kontrolní součty výběrem možnosti 2**. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Po dokončení skriptu ukončete příkazové okno. Pokud během ověřování a generování kontrolního součtu došlo k chybě, budete na to upozorněni a obdržíte odkaz na protokoly chyb.

    ![Výstup kontrolního součtu](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Obnovte nástroj mezi dvěma spuštěními.
    > - Možnost 1 použijte při práci s velkou datovou sadou obsahující malé soubory (~ aktualizací KB). Tato možnost ověřuje pouze soubory, protože generování kontrolního součtu může trvat příliš dlouho a výkon může být velmi pomalý.

3. Pokud používáte víc disků, spusťte příkaz pro každý disk.

Pokud se během ověřování zobrazí chyby, přečtěte si téma [řešení chyb při ověřování](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření integrity dat

V dalším kurzu se dozvíte, jak poslat Data Box Disk zpátky a ověřit nahrání dat do Azure.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu zpátky Microsoftu](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-disks"></a>Kopírování dat na disky

K připojení a zkopírování dat z počítače do Data Box Disk proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky. Seznam předdefinovaných složek a podsložek v jednotce se liší v závislosti na možnostech vybraných při umístění Data Box Diskho pořadí.
2. Zkopírujte data do složek odpovídajících příslušnému formátu dat. Zkopírujte například nestrukturovaná data do složky pro složku *BlockBlob* , data VHD nebo VHDX do složky *PageBlob* a soubory do *AzureFile*. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pak se v pozdějším kroku nahrávání dat do Azure nezdařila.

    - V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory v rámci složek *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru $root pod účtem Azure Storage. 
    - Všechny soubory v kontejneru $root jsou vždy odeslány jako objekty blob bloku.
    - Zkopírujte soubory do složky ve složce *AzureFile* . Podsložka v rámci složky *AzureFile* vytvoří sdílenou složku. Soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.
    - Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.
    - Pokud se vaše objednávka Managed Disks jako jedna z cílů úložiště, přečtěte si téma zásady vytváření názvů pro [spravované disky](data-box-disk-limits.md#managed-disk-naming-conventions).

    > [!IMPORTANT]
    > Všechny kontejnery, objekty BLOB a soubor by měly splňovat [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) a [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Pokud tato pravidla nebo omezení nedodržují, nahrávání dat do Azure se nezdaří.

3. K kopírování dat použijte funkci přetažení pomocí Průzkumníka souborů nebo jakéhokoli nástroje pro kopírování souborů kompatibilního s protokolem SMB, jako je například Robocopy. Pomocí následujícího příkazu lze iniciovat více úloh kopírování:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu se nacházejí v příkazu Robocopy, jak jsou uvedeny.

Pokud používáte více disků a máte velkou datovou sadu, která musí být rozdělená a zkopírovaná na všech discích, použijte nepovinný postup [rozdělení a kopírování](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) .

## <a name="validate-data"></a>Ověření dat

Při ověřování dat proveďte následující kroky.

1. Spusťte soubor `DataBoxDiskValidation.cmd` pro ověření kontrolního součtu ve složce *DataBoxDiskImport* na jednotce.
2. Pomocí možnosti 2 Ověřte soubory a vygenerujte kontrolní součty. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Pokud během ověřování a generování kontrolního součtu došlo k chybě, budete na to upozorněni a obdržíte odkaz na protokoly chyb.

    Pokud se během ověřování zobrazí chyby, přečtěte si téma [řešení chyb při ověřování](data-box-disk-troubleshoot.md).

::: zone-end
