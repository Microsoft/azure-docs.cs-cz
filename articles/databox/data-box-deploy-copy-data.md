---
title: Kopírování dat do vaší aplikace Microsoft Azure Data Box přes SMB | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do Azure Data Box prostřednictvím protokolu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 2af94deaedbafdfa638f5deb3150f1e7f711a238
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093513"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Kurz: Kopírování dat do služby Azure Data Box prostřednictvím protokolu SMB

Tento kurz popisuje, jak se připojit k a kopírování dat z hostitelského počítače pomocí místního webového uživatelského rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **dodáno**.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, použijte odkaz 1 GbE dat ale rychlosti kopírování bude mít vliv.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

Založené na vybraný účet úložiště, zařízení Data Box vytvoří až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jednu sdílenou složku pro účet úložiště Premium nebo účet úložiště objektů blob.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

V následující tabulce jsou uvedeny cesty UNC ke sdílené složky na vaše zařízení Data Box a Azure Storage cestu URL, kterého se nahrávají data. Konečná adresa URL služby Azure Storage cesta může být odvozena z cesty UNC sdílené složky.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty BLOB bloku Azure | <li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty BLOB stránky Azure  | <li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Pokud používáte počítač s Windows serverem hostitele, postupujte podle těchto kroků se připojíte k zařízení Data Box.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště. 

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Pro přístup ke složkám přidružené k účtu úložiště (*devicemanagertest1* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat, sdílené cesty jsou následující:
    - Azure objekt blob bloku – `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure objektů blob stránky – `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Soubory Azure – `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Klikněte na tlačítko **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Teď byste měli vidět sdílené složky jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.
    
     

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení ke sdíleným složkám zařízení Data Box, dalším krokem je zkopírovat data. Než začnete kopírování dat, projděte si následující aspekty:

- Ujistěte se, že zkopírovat data do sdílené složky, které odpovídají formátu příslušná data. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky do objektů blob stránky. Pokud formát dat neshoduje s typem příslušné sdílené složky, pak v pozdějším kroku, nahrát data do Azure se nezdaří.
-  Při kopírování dat, ujistěte se, že velikost dat odpovídá omezení velikosti podle [služby Azure storage a omezení zařízení Data Box](data-box-limits.md).
- Pokud data nahrávaná Data Boxem zároveň nahrávají jiné aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
- Doporučujeme:
    - Nepoužíváte protokolu SMB a systému souborů NFS ve stejnou dobu.
    - Zkopírujte stejná data do stejného cíle end v Azure. 
     
  V těchto případech nelze určit konečný výsledek.
- Vždy vytvořte složku pro soubory, které chcete kopírovat v rámci sdílené složky a potom zkopírujte soubory do této složky. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data nahrají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.

Po připojení ke sdílené složce protokolu SMB, začněte kopírovat data. Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. Použijte následující příkaz:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Atributy jsou popsané v následující tabulce.
    
|Atribut  |Popis  |
|---------|---------|
|/e     |Zkopíruje podadresáře včetně prázdných adresářů.         |
|/r:     |Určuje počet opakovaných pokusů při neúspěšném kopírování.         |
|/w:     |Určuje dobu čekání mezi opakovanými pokusy v sekundách.         |
|/is     |Zahrne stejné soubory.         |
|/nfl     |Určuje, že názvy souborů nejsou protokolovány.         |
|/ndl    |Určuje, že názvy adresářů se neprotokolují.        |
|/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
|/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
|/fft     | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
|/b     | Zkopíruje soubory v režimu zálohování.        |
|/z    | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
| /zb     | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
|/efsraw     | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
|log+:<LogFile>| Připojí výstup k existujícímu souboru protokolu.|    
 
Následující příklad ukazuje výstup příkazu robocopy, který slouží ke kopírování souborů do Data Boxu.
    
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
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
    C:\Users>
       

Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy.

|    Platforma    |    Vetšinou malé soubory (< 512 kB)                           |    Většinou středně velké soubory (512 kB až 1 MB)                      |    Většinou velké soubory (> 1 MB)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 relace Robocopy <br> 16 vláken na relaci    |    3 relace Robocopy <br> 16 vláken na relaci    |    2 relace Robocopy <br> 24 vláken na relaci    |  |


Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže.

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
   ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)



## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

