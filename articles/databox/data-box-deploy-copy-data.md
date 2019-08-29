---
title: Kurz kopírování dat pomocí protokolu SMB v Azure Data Box | Microsoft Docs
description: Naučte se, jak kopírovat data do Azure Data Box přes SMB.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: alkohli
ms.openlocfilehash: 9f5ccc255310ca42ef39586860c0861b945ac6e9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098888"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Kurz: Kopírování dat do Azure Data Box přes protokol SMB

::: zone-end

::: zone target="chromeless"

# <a name="copy-data-to-azure-data-box"></a>Kopírovat data do Azure Data Box

::: zone-end

::: zone target="docs"

V tomto kurzu se dozvíte, jak se připojit a kopírovat data z hostitelského počítače pomocí místního webového uživatelského rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili [jste kurz: Nastavte Azure Data Box](data-box-deploy-set-up.md).
2. Dostali jste Data Box se **doručí**stav objednávky na portálu.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud připojení k 10 GbE není k dispozici, použijte linku s 1 1GB daty, ale bude to mít vliv na rychlost kopírování.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

V závislosti na vybraném účtu úložiště Data Box vytvoří až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jedna sdílená složka pro Premium Storage.
- Jedna sdílená složka pro účet úložiště objektů BLOB.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

V následující tabulce je uvedena cesta UNC ke sdíleným složkám na Data Box a adresa URL cesty Azure Storage, kam se data nahrají. Konečnou adresu URL pro cestu Azure Storage lze odvodit z cesty ke sdílené složce UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Pokud používáte hostitelský počítač s Windows serverem, připojte se k Data Box pomocí těchto kroků.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště. 

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Pokud chcete získat přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště (*devicemanagertest1* v následujícím příkladu) z hostitelského počítače, otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Objekt blob bloku Azure –`\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Objekt blob stránky Azure –`\\10.126.76.172\devicemanagertest1_PageBlob`
    - Soubory Azure –`\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Kliknutím na **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Nyní byste měli vidět sdílené složky jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složka vytvořená v části objekty blob bloku a sdílené objekty blob stránky představuje kontejner, do kterého se data nahrají jako objekty blob. Soubory nemůžete kopírovat přímo do *kořenové* složky v účtu úložiště.
    
Pokud používáte klienta se systémem Linux, připojte sdílenou složku SMB pomocí následujícího příkazu. Níže uvedený parametr "." je verze protokolu SMB, kterou podporuje váš hostitel pro Linux. V níže uvedeném příkazu připojte správnou verzi. Verze SMB, které Data Box podporuje, najdete v tématu [podporované systémy souborů pro klienty se systémem Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) . 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Až budete připojeni ke sdíleným složkám Data Box, je dalším krokem kopírování dat. Než začnete s kopírováním dat, přečtěte si následující skutečnosti:

- Nezapomeňte zkopírovat data do sdílených složek, které odpovídají příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky do objektu blob stránky. Pokud se formát dat neshoduje s odpovídajícím typem sdílené složky, pak se v pozdějším kroku nahrávání dat do Azure nezdaří.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezení velikosti popsané v [úložišti Azure a omezeních data box](data-box-limits.md).
- Pokud data nahrávaná Data Boxem zároveň nahrávají jiné aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
- Doporučujeme:
    - Současně nepoužíváte protokoly SMB i NFS.
    - Zkopírujte stejná data do stejného koncového cíle v Azure. 
     
  V těchto případech nelze určit konečný výsledek.
- Vždy vytvořte složku pro soubory, které máte v úmyslu zkopírovat do sdílené složky, a poté soubory zkopírujte do této složky. Složka vytvořená v části objekty blob bloku a sdílené objekty blob stránky představuje kontejner, do kterého se data nahrají jako objekty blob. Soubory nemůžete kopírovat přímo do *kořenové* složky v účtu úložiště.

Po připojení ke sdílené složce SMB spusťte kopii dat. Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. Použijte následující příkaz:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Atributy jsou popsané v následující tabulce.
    
|Atribut  |Popis  |
|---------|---------|
|/e     |Zkopíruje podadresáře včetně prázdných adresářů.         |
|/r:     |Určuje počet opakovaných pokusů při neúspěšném kopírování.         |
|/w:     |Určuje dobu čekání mezi opakovanými pokusy v sekundách.         |
|/is     |Zahrne stejné soubory.         |
|/nfl     |Určuje, že se názvy souborů neprotokolují.         |
|/ndl    |Určuje, že názvy adresářů se neprotokolují.        |
|/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
|/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
|/fft     | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
|/b     | Zkopíruje soubory v režimu zálohování.        |
|/z    | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
| /zb     | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
|/efsraw     | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
|log +:\<soubor protokolu >| Připojí výstup k existujícímu souboru protokolu.|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 relace Robocopy <br> 16 vláken na relaci    |    3 relace Robocopy <br> 16 vláken na relaci    |    2 relace Robocopy <br> 24 vláken na relaci    |


Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Další informace najdete v tématu [zobrazení protokolů chyb během kopírování dat do data box](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [řešení potíží s data box](data-box-troubleshoot.md).

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
   ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Data ze zdrojového serveru můžete zkopírovat do svého Data Box přes SMB, NFS, REST, službu kopírování dat nebo na spravované disky.

V každém případě se ujistěte, že název sdílené složky a složky a velikost dat následují podle pokynů popsaných v [Azure Storage a data Boxch omezeních služeb](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Kopírování dat pomocí protokolu SMB

1. Pokud používáte hostitele Windows, připojte se ke sdíleným složkám SMB pomocí následujícího příkazu:

    `\\<IP address of your device>\ShareName`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.
3. K kopírování dat do sdílených složek použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, jako je například Robocopy. 

Podrobné pokyny najdete [v kurzu: Kopírovat data do Azure Data Box přes protokol](data-box-deploy-copy-data.md)SMB.

## <a name="copy-data-via-nfs"></a>Kopírování dat přes systém souborů NFS

1. Pokud používáte hostitele systému souborů NFS, připojte sdílené složky systému souborů NFS na Data Box pomocí následujícího příkazu:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.
3. Pomocí `cp` příkazu `rsync` nebo zkopírujte data.

Podrobné pokyny najdete [v kurzu: Kopírování dat do Azure Data Box přes systém](data-box-deploy-copy-data-via-nfs.md)souborů NFS.

## <a name="copy-data-via-rest"></a>Kopírování dat pomocí REST

1. Pokud chcete kopírovat data pomocí Data Box BLOB Storage přes rozhraní REST API, můžete se připojit přes *protokol HTTP* nebo *https*.
2. Pokud chcete kopírovat data do Data Box BLOB Storage, můžete použít AzCopy.

Podrobné pokyny najdete [v kurzu: Zkopírujte data do Azure Data Box BLOB Storage přes rozhraní REST](data-box-deploy-copy-data-via-nfs.md)API.

## <a name="copy-data-via-data-copy-service"></a>Kopírování dat prostřednictvím služby kopírování dat

1. Chcete-li kopírovat data pomocí služby kopírování dat, je třeba vytvořit úlohu. V místním webovém uživatelském rozhraní Data Box klikněte na **spravovat > kopírovat Data > vytvořit**. 
2. Vyplňte parametry a vytvořte úlohu.

Podrobné pokyny najdete [v kurzu: Pomocí služby kopírování dat zkopírujte data do Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Kopírování dat na spravované disky

1. Při seřazení zařízení Data Box byste měli jako cíl úložiště vybrat spravované disky.
2. K Data Box se můžete připojit přes sdílené složky SMB nebo NFS.
3. Data pak můžete kopírovat prostřednictvím nástrojů SMB nebo NFS.

Podrobné pokyny najdete [v kurzu: Pomocí Data Box můžete do Azure](data-box-deploy-copy-data-from-vhds.md)importovat data jako spravované disky.

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


Přejděte k dalšímu kurzu, kde se dozvíte, jak odeslat Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

::: zone-end

