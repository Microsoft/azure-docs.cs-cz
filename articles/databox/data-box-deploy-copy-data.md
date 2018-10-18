---
title: Kopírování dat do Microsoft Azure Data Boxu | Microsoft Docs
description: Zjistěte, jak zkopírovat data do Azure Data Boxu.
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
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: d606b7b536147b7e1d96bfb6cebf2522e5b4f85f
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078846"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Kurz: Kopírování dat do Azure Data Boxu 

Tento kurz popisuje, jak se pomocí místního webového uživatelského rozhraní připojit z hostitelského počítače k Data Boxu, zkopírovat do něj data z hostitelského počítače a pak připravit Data Box k odeslání.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu
> * Příprava k odeslání Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Nastavení Azure Data Boxu](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE. Pokud nemáte k dispozici připojení 10 GbE, je možné použít datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování. 

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

V závislosti na vybraném účtu úložiště vytvoří Data Box až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jednu sdílenou složku pro účet úložiště Premium nebo účet úložiště objektů blob. 

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

Představte si následující příklad. 

- Účet úložiště: *Mystoracct*
- Sdílená složka pro objekty blob bloku: *Mystoracct_BlockBlob/my-container/blob*
- Sdílení složka pro objekty blob stránky: *Mystoracct_PageBlob/my-container/blob*
- Sdílená složka pro soubory: *Mystoracct_AzFile/my-share*

Postup připojení a kopírování se může lišit v závislosti na tom, jestli je Data Box připojený k hostiteli s Windows Serverem nebo hostiteli s Linuxem.

### <a name="connect-via-smb"></a>Připojení přes protokol SMB 

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště. 

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Získejte přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště (v následujícím příkladu je to Mystoracct). Pro přístup ke sdíleným složkám použijte cestu `\\<IP of the device>\ShareName`. V závislosti na formátu vašich dat se připojte ke sdíleným složkám (použijte název sdílené složky) na následující adrese: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Pokud se chcete ke sdíleným složkám připojit z hostitelského počítače, otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Klikněte na **OK**. Tím se otevře Průzkumník souborů. Sdílené složky by se teď měly zobrazit jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5.  **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Občas se u složek může zobrazit šedý křížek. Křížek neoznačuje chybový stav. Aplikace složky označuje příznakem kvůli sledování stavu.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Připojení přes systém souborů NFS 

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu. 

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `10.161.23.130`, sdílená složka `Mystoracct_Blob` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat. Před kopírováním dat si nezapomeňte přečíst následující důležité informace:

- Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení úložiště Azure a Data Boxu](data-box-limits.md). 
- Pokud data nahrávaná Data Boxem zároveň nahrávají jiné aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
- Doporučujeme, abyste nepoužívali protokol SMB a systém souborů NFS současně a abyste nekopírovali stejná data do stejného cíle v Azure. V takových případech není možné určit konečný výsledek.

### <a name="copy-data-via-smb"></a>Kopírování dat pomocí protokolu SMB

Po připojení ke sdílené složce SMB zahajte kopírování dat. 

Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. Použijte následující příkaz:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Atributy jsou popsané v následující tabulce.
    
|Atribut  |Popis  |
|---------|---------|
|/e     |Zkopíruje podadresáře včetně prázdných adresářů.         |
|/r:     |Určuje počet opakovaných pokusů při neúspěšném kopírování.         |
|/w:     |Určuje dobu čekání mezi opakovanými pokusy v sekundách.         |
|/is     |Zahrne stejné soubory.         |
|/nfl     |Určuje, že se nemají protokolovat názvy souborů.         |
|/ndl    |Určuje, že se nemají protokolovat názvy adresářů.        |
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

### <a name="copy-data-via-nfs"></a>Kopírování dat přes systém souborů NFS

Pokud používáte hostitelský počítač s Linuxem, použijte podobný nástroj pro kopírování jako Robocopy. Mezi dostupné alternativy v Linuxu patří [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) nebo [Ultracopier](https://ultracopier.first-world.info/).  

Jednou z nejlepších možností, jak zkopírovat adresář, je příkaz `cp`. Další informace o jeho použití najdete na [manuálových stránkách pro příkaz cp](http://man7.org/linux/man-pages/man1/cp.1.html).

Pokud používáte možnost rsync ke kopírování s více vlákny, postupujte podle těchto pokynů:

 - V závislosti na systému souborů, který používá váš klient Linuxu, nainstalujte balíček **CIFS Utils** nebo **NFS Utils**.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Nainstalujte **Rsync** a **Parallel** (liší se v závislosti na distribuované verzi Linuxu).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Vytvořte přípojný bod.

    `sudo mkdir /mnt/databox`

 - Připojte svazek.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Zrcadlete adresářovou strukturu složky.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Zkopírujte soubory. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     V tomto příkazu parametr j určuje úroveň paralelizace a hodnota X počet paralelních kopií.

     Doporučujeme začít s 16 paralelními kopiemi a zvyšovat počet vláken v závislosti na dostupnosti prostředků.

## <a name="prepare-to-ship"></a>Příprava k odeslání

Posledním krokem je připravit zařízení k odeslání. V tomto kroku se všechny sdílené složky zařízení převedou do režimu offline. Jakmile zahájíte přípravu zařízení k odeslání, ztratíte přístup ke sdíleným složkám.
1. Přejděte do části **Připravit k odeslání** a klikněte na **Zahájit přípravu**. 
   
    ![Příprava k odeslání 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Kontrolní součty se standardně počítají na řádku během přípravě k odeslání. Výpočet kontrolního součtu může v závislosti na velikosti vašich dat chvíli trvat. Klikněte na **Zahájit přípravu**.
    1. Během přípravy k odeslání se sdílené složky zařízení převedou do režimu offline a zařízení se uzamkne.
        
        ![Příprava k odeslání 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. Po dokončení přípravy zařízení se stav zařízení změní na *Připraveno k odeslání*. 
        
        ![Příprava k odeslání 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Stáhněte si seznam souborů (manifest), které se v tomto procesu zkopírovaly. Později můžete tento seznam použít k ověření nahrání souborů do Azure.
        
        ![Příprava k odeslání 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Vypněte zařízení. Přejděte na stránku **Vypnout nebo restartovat** a klikněte na **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.
4. Odpojte kabely. Dalším krokem je odeslat zařízení do Microsoftu.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu
> * Příprava k odeslání Data Boxu

V dalším kurzu se dozvíte, jak Data Box nastavit a zkopírovat do něj data.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

