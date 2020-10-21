---
title: Kurz kopírování dat do Azure Data Boxu přes protokol SMB | Microsoft Docs
description: V tomto kurzu se naučíte, jak se pomocí SMB s místním webovým uživatelským rozhraním připojit z hostitelského počítače k Azure Data Boxu a zkopírovat do něj data.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9427ec4530ac249d5b8059d04fc85f1183c0081c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123887"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Kurz: Kopírování dat do Azure Data Boxu přes protokol SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Kopírování dat do Azure Data Boxu

::: zone-end

::: zone target="docs"

Tento kurz popisuje, jak se pomocí místního webového uživatelského rozhraní připojit k hostitelskému počítači a zkopírovat z něj data.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Nastavení Azure Data Boxu](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
   * Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
   * Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte k dispozici připojení 10 GbE, použijte datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

V závislosti na vybraném účtu úložiště vytvoří Data Box až:

* Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
* Jednu sdílenou složku pro každou službu Storage úrovně Premium.
* Jednu sdílenou složku pro každý účet úložiště objektů blob.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
|Typy úložišť v Azure  | Sdílené složky Data Boxů            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Výběrem **SMB** získáte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště. 

    ![Získání přihlašovacích údajů pro sdílené složky SMB](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Vyberte **OK**.
    
    ![Získání uživatelského jména a hesla pro sdílenou složku](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Pokud chcete získat přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště (*utsac1* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Objekt blob bloku Azure – `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Objekt blob stránky Azure – `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Soubory Azure – `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Výběrem **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Sdílené složky by se teď měly zobrazit jako složky.
    
    ![Sdílené složky zobrazené v Průzkumníku souborů](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.
    
Pokud používáte klienta Linuxu, připojte sdílenou složku SMB pomocí následujícího příkazu. Níže uvedený parametr vers představuje verzi protokolu SMB, kterou podporuje váš hostitel s Linuxem. Do následujícího příkazu vložte odpovídající verzi. Verze protokolu SMB, které Data Box podporuje, najdete v tématu věnovaném [podporovaným systémům souborů pro klienty Linuxu](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients). 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat. Než začnete s kopírováním dat, projděte si následující důležité informace:

* Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Virtuální pevné disky je potřeba zkopírovat do objektu blob stránky. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
* Vždy ve sdílené složce vytvořte složku pro soubory, které chcete kopírovat, a potom je do této složky zkopírujte. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory se nedají zkopírovat přímo do složky *root* v účtu úložiště.
* Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku věnovaném [omezením velikosti účtu úložiště Azure](data-box-limits.md#azure-storage-account-size-limits).
* Pokud chcete při přenosu do Azure Files zachovat metadata (seznamy ACL, časová razítka a atributy souborů), postupujte podle pokynů v tématu [Zachování seznamů ACL, atributů a časových razítek souborů pomocí Azure Data Boxu](data-box-file-acls-preservation.md).  
* Pokud data nahrávaná Data Boxem zároveň nahrává jiná aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
* Naše doporučení:
  * Nepoužívejte protokol SMB a systém souborů NFS současně.
  * Stejná data kopírujte do stejného cíle v Azure.
  V těchto případech není možné určit konečný výsledek.

> [!IMPORTANT]
> Než budete moct potvrdit, že zařízení Data Box převedlo data do Azure Storage, ujistěte se, že si uchováváte kopii zdrojových dat.

Po připojení ke sdílené složce SMB spusťte kopírování dat. Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. Použijte následující příkaz:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Atributy jsou popsané v následující tabulce.
    
|Atribut  |Popis  |
|---------|---------|
|/e     |Zkopíruje podadresáře včetně prázdných adresářů.         |
|/r:     |Určuje počet opakovaných pokusů při neúspěšném kopírování.         |
|/w:     |Určuje dobu čekání mezi opakovanými pokusy v sekundách.         |
|/is     |Zahrne stejné soubory.         |
|/nfl     |Určuje, že se neprotokolují názvy souborů.         |
|/ndl    |Určuje, že se neprotokolují názvy adresářů.        |
|/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
|/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
|/fft     | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
|/b     | Zkopíruje soubory v režimu zálohování.        |
|/z    | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
| /zb     | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
|/efsraw     | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
|log+:\<LogFile>| Připojí výstup k existujícímu souboru protokolu.|    
 
Následující příklad ukazuje výstup příkazu robocopy, který slouží ke kopírování souborů do Data Boxu.

```output
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
```

Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy.

|    Platforma    |    Vetšinou malé soubory (< 512 kB)                           |    Většinou středně velké soubory (512 kB až 1 MB)                      |    Většinou velké soubory (> 1 MB)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 relace Robocopy <br> 16 vláken na relaci    |    3 relace Robocopy <br> 16 vláken na relaci    |    2 relace Robocopy <br> 24 vláken na relaci    |

Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

Pokud během procesu kopírování dojde k nějakým chybám, zobrazí se oznámení.

![Oznámení o chybě kopírování pro operaci Připojit a kopírovat](media/data-box-deploy-copy-data/view-errors-1.png)

Vyberte **Stáhnout seznam problémů**.

![Stažení a zobrazení chyb pro operaci Připojit a kopírovat 2](media/data-box-deploy-copy-data/view-errors-2.png)

Otevřete tento seznam, projděte si podrobnosti o chybě a vyberte adresu URL pro zobrazení doporučeného řešení.

![Stažení a zobrazení chyb pro operaci Připojit a kopírovat 3](media/data-box-deploy-copy-data/view-errors-3.png)

Další informace najdete v tématu věnovaném [zobrazení protokolů chyb při kopírování dat do Data Boxu](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [Řešení potíží s Data Boxem](data-box-troubleshoot.md).

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.

![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Data ze zdrojového serveru do Data Boxu můžete zkopírovat přes protokol SMB, systém souborů NFS, rozhraní REST, službu kopírování dat nebo na spravované disky.

V každém případě se ujistěte, že názvy sdílených složek a složek a velikost data vyhovují pokynům popsaným v tématu [Omezení služby Azure Storage a Data Boxu](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Kopírování dat pomocí protokolu SMB

Kopírování dat přes SMB:

1. Pokud používáte hostitele s Windows, použijte k připojení ke sdíleným složkám SMB následující příkaz:

    `\\<IP address of your device>\ShareName`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.
3. Ke kopírování dat do sdílených složek použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. 

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu přes protokol SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Kopírování dat přes systém souborů NFS

Kopírování dat přes NFS:

1. Pokud používáte hostitele systému souborů NFS, použijte k připojení sdílených složek systému souborů NFS k Data Boxu následující příkaz:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.
3. Ke zkopírování dat použijte příkaz `cp` nebo `rsync`.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu přes systém souborů NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Kopírování dat přes rozhraní REST

Kopírování dat přes rozhraní REST:

1. Pokud chcete kopírovat data s využitím úložiště objektů blob Data Boxu přes rozhraní REST API, můžete se připojit přes *http* nebo *https*.
2. Ke kopírování dat do úložiště objektů blob Data Boxu můžete použít AzCopy.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do úložiště objektů blob Azure Data Boxu přes rozhraní REST API](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Kopírování dat prostřednictvím služby kopírování dat

Kopírování dat prostřednictvím služby kopírování dat:

1. Pokud chcete kopírovat data pomocí služby kopírování dat, musíte vytvořit úlohu. V místním webovém uživatelském rozhraní vašeho Data Boxu přejděte do části **Správa > Kopírování dat > Vytvořit**.
2. Vyplňte parametry a vytvořte úlohu.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu pomocí služby kopírování dat](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Kopírování dat na spravované disky

Kopírování dat na spravované disky:

1. Při objednávání zařízení Data Box byste měli jako cíl úložiště vybrat spravované disky.
2. K Data Boxu se můžete připojit přes sdílené složky SMB nebo NFS.
3. Data pak můžete kopírovat pomocí nástrojů pro práci s protokolem SMB nebo systémem souborů NFS.

Podrobné pokyny najdete v článku [Kurz: Použití Data Boxu k importu dat v podobě spravovaných disků v Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu

V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

::: zone-end