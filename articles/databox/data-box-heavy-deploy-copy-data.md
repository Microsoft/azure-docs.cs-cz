---
title: Kurz kopírování dat do Azure Data Boxu Heavy přes protokol SMB | Microsoft Docs
description: V tomto kurzu se naučíte, jak se pomocí SMB s místním webovým uživatelským rozhraním připojit z hostitelského počítače k Azure Data Boxu Heavy a zkopírovat do něj data.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 77dc64d9660f9a0bf66559c4a5a976362cf1acd0
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951634"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Kurz: Kopírování dat do Azure Data Boxu Heavy přes protokol SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopírování dat do Azure Data Boxu Heavy

::: zone-end

::: zone target = "docs"

Tento kurz popisuje, jak se pomocí místního webového uživatelského rozhraní připojit k hostitelskému počítači a zkopírovat z něj data.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy

::: zone-end

::: zone target = "chromeless"

Data ze zdrojového serveru do Data Boxu můžete zkopírovat přes protokol SMB, systém souborů NFS, rozhraní REST, službu kopírování dat nebo na spravované disky.

V každém případě se ujistěte, že názvy sdílených složek a složek a velikost data vyhovují pokynům popsaným v tématu [Omezení služby Azure Storage a Data Boxu Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Nastavení Azure Data Boxu Heavy](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box Heavy a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu Heavy. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Největší rychlosti kopírování je možné dosáhnout použitím dvou paralelních připojení 40 GbE (jedno na uzel). Pokud nemáte k dispozici připojení 40 GbE, doporučujeme použít alespoň dvě připojení 10 GbE (jedno na uzel).
   

## <a name="connect-to-data-box-heavy-shares"></a>Připojení ke sdíleným složkám Data Boxu Heavy

V závislosti na vybraném účtu úložiště vytvoří Data Box Heavy až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jednu sdílenou složku pro každou službu Storage úrovně Premium.
- Jednu sdílenou složku pro každý účet úložiště objektů blob.

Tyto sdílené složky se vytvoří na obou uzlech zařízení.

V rámci sdílených složek objektů blob bloku a objektů blob stránky:
- Entity první úrovně jsou kontejnery.
- Entity druhé úrovně jsou objekty blob.

V rámci sdílených složek pro službu Azure Files:
- Entity první úrovně jsou sdílené složky.
- Entity druhé úrovně jsou soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu Heavy a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
| Storage           | Cesta UNC                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Postup připojení se u klientů Windows a Linuxu liší.

> [!NOTE]
> Pokud se chcete paralelně připojit k oběma uzlům zařízení, použijte stejný postup.

### <a name="connect-on-a-windows-system"></a>Připojení v systému Windows

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu Heavy pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště.

    ![Získání přihlašovacích údajů sdílené složky](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 2](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Pokud chcete získat přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště (*databoxe2etest* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Objekt blob bloku Azure – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Objekt blob stránky Azure – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Soubory Azure – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Kliknutím na **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Sdílené složky by se teď měly zobrazit jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.
    
### <a name="connect-on-a-linux-system"></a>Připojení v systému Linux

Pokud používáte klienta Linuxu, připojte sdílenou složku SMB pomocí následujícího příkazu.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

Parametr `vers` představuje verzi protokolu SMB, kterou podporuje váš hostitel s Linuxem. Do předchozího příkazu vložte odpovídající verzi.

Verze protokolu SMB, které Data Box Heavy podporuje, najdete v tématu věnovaném [podporovaným systémům souborů pro klienty Linuxu](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do Data Boxu Heavy

Po připojení ke sdíleným složkám Data Boxu Heavy je dalším krokem zkopírování dat.

### <a name="copy-considerations"></a>Důležité informace o kopírování

Než začnete s kopírováním dat, projděte si následující důležité informace:

- Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Virtuální pevné disky je potřeba zkopírovat do objektu blob stránky.

    Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení úložiště Azure a Data Boxu Heavy](data-box-heavy-limits.md).
- Pokud data nahrávaná Data Boxem Heavy zároveň nahrávají jiné aplikace mimo Data Box Heavy, může to způsobit selhání úlohy nahrávání a poškození dat.
- Naše doporučení:
    - Nepoužívejte protokol SMB a systém souborů NFS současně.
    - Stejná data kopírujte do stejného cíle v Azure.
     
  V těchto případech není možné určit konečný výsledek.
- Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.

Po připojení ke sdílené složce SMB zahajte kopírování dat.

1. Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. Použijte následující příkaz:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Atributy jsou popsané v následující tabulce.
    
    |Atribut  |Popis  |
    |---------|---------|
    |/e      |Zkopíruje podadresáře včetně prázdných adresářů.         |
    |/r:     |Určuje počet opakovaných pokusů při neúspěšném kopírování.         |
    |/w:     |Určuje dobu čekání mezi opakovanými pokusy v sekundách.         |
    |/is     |Zahrne stejné soubory.         |
    |/nfl    |Určuje, že se neprotokolují názvy souborů.         |
    |/ndl    |Určuje, že se neprotokolují názvy adresářů.        |
    |/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
    |/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
    |/fft    | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
    |/b      | Zkopíruje soubory v režimu zálohování.        |
    |/z      | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
    | /zb    | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
    |/efsraw | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
    |log+:\<LogFile>| Připojí výstup k existujícímu souboru protokolu.|
    
 
    Následující příklad ukazuje výstup příkazu robocopy, který slouží ke kopírování souborů do Data Boxu Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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

2. Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy. (Následující čísla představují ideální scénáře.)

    | Platforma    | Vetšinou malé soubory (< 512 kB)    | Většinou středně velké soubory (512 kB až 1 MB)  | Většinou velké soubory (> 1 MB)                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 relací Robocopy <br> 24 vláken na relaci | 6 relací Robocopy <br> 16 vláken na relaci | 6 relací Robocopy <br> 16 vláken na relaci |


    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

3. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je.

    ![Zobrazení zkopírovaných soborů](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Při kopírování dat dojde k následujícímu:

    - Ověří se názvy, velikosti a formáty souborů, aby se zajistilo, že odpovídají omezením úložiště a objektů Azure a splňují zásady vytváření názvů kontejnerů a souborů Azure.
    - Aby se zajistila integrita dat, počítá se i kontrolní součet.

    Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Pokud si chcete soubory s chybami, vyberte ikonu šipky.

    ![Stažení souborů s chybami](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Další informace najdete v tématu věnovaném [zobrazení protokolů chyb při kopírování dat do Data Boxu Heavy](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [Řešení potíží s Data Boxem Heavy](data-box-troubleshoot.md).

5. Otevřete soubor s chybami v Poznámkovém bloku. Následující soubor s chybami značí, že data nejsou správně zarovnaná.

    ![Otevření souboru s chybami](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    V případě objektů blob stránky musí být data zarovnaná do bloků po 512 bajtech. Po odebrání těchto dat se chyba vyřeší, jak je znázorněno na následujícím snímku obrazovky.

    ![Vyřešená chyba](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Po dokončení kopírování přejděte na stránku **Zobrazení řídicího panelu**. Zkontrolujte využité a volné místo na zařízení.
    
    ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Zopakujte výše uvedený postup a zkopírujte data na druhý uzel zařízení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Boxem Heavy, mezi která patří:

> [!div class="checklist"]
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy


V dalším kurzu se dozvíte, jak Data Box Heavy odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu heavy do Microsoftu](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopírování dat pomocí protokolu SMB

1. Pokud používáte hostitele s Windows, použijte k připojení ke sdíleným složkám SMB následující příkaz:

    `\\<IP address of your device>\ShareName`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.

3. Ke kopírování dat do sdílených složek použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu přes protokol SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Kopírování dat přes systém souborů NFS

1. Pokud používáte hostitele systému souborů NFS, použijte k připojení sdílených složek systému souborů NFS následující příkaz:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, v místním webovém uživatelském rozhraní Data Boxu Heavy přejděte na stránku **Připojit a kopírovat**.
3. Ke zkopírování dat použijte příkaz `cp` nebo `rsync`. 
4. Opakováním těchto kroků se připojte k druhému uzlu Data Boxu Heavy a zkopírujte na něj data.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu přes systém souborů NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Kopírování dat přes rozhraní REST

1. Pokud chcete kopírovat data s využitím úložiště objektů blob Data Boxu přes rozhraní REST API, můžete se připojit přes *http* nebo *https*.
2. Ke kopírování dat do úložiště objektů blob Data Boxu můžete použít AzCopy.
3. Opakováním těchto kroků se připojte k druhému uzlu Data Boxu Heavy a zkopírujte na něj data.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do úložiště objektů blob Azure Data Boxu přes rozhraní REST API](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Kopírování dat prostřednictvím služby kopírování dat

1. Pokud chcete kopírovat data pomocí služby kopírování dat, musíte vytvořit úlohu. V místním webovém uživatelském rozhraní vašeho Data Boxu Heavy přejděte do části **Správa > Kopírování dat > Vytvořit**.
2. Vyplňte parametry a vytvořte úlohu.
3. Opakováním těchto kroků se připojte k druhému uzlu Data Boxu Heavy a zkopírujte na něj data.

Podrobné pokyny najdete v článku [Kurz: Kopírování dat do Azure Data Boxu Heavy pomocí služby kopírování dat](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Kopírování dat na spravované disky

1. Při objednávání zařízení Data Box Heavy byste měli jako cíl úložiště vybrat spravované disky.
2. K Data Boxu Heavy se můžete připojit přes sdílené složky SMB nebo NFS.
3. Data pak můžete kopírovat pomocí nástrojů pro práci s protokolem SMB nebo systémem souborů NFS.
4. Opakováním těchto kroků se připojte k druhému uzlu Data Boxu Heavy a zkopírujte na něj data.

Podrobné pokyny najdete v článku [Kurz: Použití Data Boxu Heavy k importu dat v podobě spravovaných disků v Azure](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


