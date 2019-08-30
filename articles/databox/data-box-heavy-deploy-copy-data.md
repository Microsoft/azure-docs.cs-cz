---
title: Kurz kopírování dat pomocí protokolu SMB v Azure Data Box Heavy | Microsoft Docs
description: Naučte se, jak kopírovat data do Azure Data Box Heavy přes SMB.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 8cb763766ebb151ad1c59b63a33a63493a4f0069
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164375"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Kurz: Kopírování dat do Azure Data Box Heavy přes protokol SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopírovat data do Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

V tomto kurzu se dozvíte, jak se připojit a kopírovat data z hostitelského počítače pomocí místního webového uživatelského rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k Data Box Heavy
> * Kopírovat data do Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Data ze zdrojového serveru můžete zkopírovat do svého Data Box přes SMB, NFS, REST, službu kopírování dat nebo na spravované disky.

V každém případě se ujistěte, že název sdílené složky a složky a velikost dat následují podle pokynů popsaných v [Azure Storage a data box Heavych omezeních služeb](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili [jste kurz: Nastavte Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Dostali jste Data Box Heavy se **doručí**stav objednávky na portálu.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Box Heavy. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Pro nejrychlejší rychlost kopírování je možné využít paralelní připojení 2 40-GbE (jeden na uzel). Pokud nemáte k dispozici připojení 40-GbE, doporučujeme, abyste měli aspoň 2 10 připojení (jedna na jeden uzel).
   

## <a name="connect-to-data-box-heavy-shares"></a>Připojení k Data Box Heavy sdíleným složkám

V závislosti na vybraném účtu úložiště Data Box Heavy vytvoří až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jedna sdílená složka pro Premium Storage.
- Jedna sdílená složka pro účet úložiště objektů BLOB.

Tyto sdílené složky se vytvářejí na obou uzlech zařízení.

V části objekty blob bloku a sdílené složky objektů blob stránky:
- Entity první úrovně jsou kontejnery.
- Entity druhé úrovně jsou objekty blob.

V části sdílené složky pro soubory Azure:
- Entity první úrovně jsou sdílené složky.
- Entity druhé úrovně jsou soubory.

V následující tabulce je uvedena cesta UNC ke sdíleným složkám na Data Box Heavy a adresa URL cesty Azure Storage, kam se data nahrají. Konečnou adresu URL pro cestu Azure Storage lze odvodit z cesty ke sdílené složce UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL Azure Storage:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Postup připojení pomocí klienta se systémem Windows nebo Linux se liší.

> [!NOTE]
> Použijte stejný postup k paralelnímu připojení k oběma uzlům zařízení.

### <a name="connect-on-a-windows-system"></a>Připojení v systému Windows

Pokud používáte hostitelský počítač s Windows serverem, připojte se k Data Box Heavy pomocí těchto kroků.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště.

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Pokud chcete získat přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště (*databoxe2etest* v následujícím příkladu) z hostitelského počítače, otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Objekt blob bloku Azure –`\\10.100.10.100\databoxe2etest_BlockBlob`
    - Objekt blob stránky Azure –`\\10.100.10.100\databoxe2etest_PageBlob`
    - Soubory Azure –`\\10.100.10.100\databoxe2etest_AzFile`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Kliknutím na **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Nyní byste měli vidět sdílené složky jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složka vytvořená v části objekty blob bloku a sdílené objekty blob stránky představuje kontejner, do kterého se data nahrají jako objekty blob. Soubory nemůžete kopírovat přímo do *kořenové* složky v účtu úložiště.
    
### <a name="connect-on-a-linux-system"></a>Připojení v systému Linux

Pokud používáte klienta se systémem Linux, připojte sdílenou složku SMB pomocí následujícího příkazu.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` Parametr je verze protokolu SMB, kterou podporuje váš hostitel pro Linux. Připojte v příkazu výše příslušnou verzi.

Verze protokolu SMB, které podporuje Data Box Heavy, najdete v části [podporované systémy souborů pro klienty se systémem Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopírovat data do Data Box Heavy

Až budete připojeni ke sdíleným složkám Data Box Heavy, je dalším krokem kopírování dat.

### <a name="copy-considerations"></a>Požadavky na kopírování

Než začnete s kopírováním dat, přečtěte si následující skutečnosti:

- Nezapomeňte zkopírovat data do sdílených složek, které odpovídají příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky do objektu blob stránky.

    Pokud se formát dat neshoduje s odpovídajícím typem sdílené složky, pak se v pozdějším kroku nahrávání dat do Azure nezdaří.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezení velikosti popsané v [úložišti Azure a omezeních data box Heavy](data-box-heavy-limits.md).
- Pokud jsou data odesílaná pomocí Data Box Heavy souběžně nahraná jinými aplikacemi mimo Data Box Heavy, může to vést k selhání úlohy a poškození dat.
- Doporučujeme:
    - Současně nepoužíváte protokoly SMB i NFS.
    - Zkopírujte stejná data do stejného koncového cíle v Azure.
     
  V těchto případech nelze určit konečný výsledek.
- Vždy vytvořte složku pro soubory, které máte v úmyslu zkopírovat do sdílené složky, a poté soubory zkopírujte do této složky. Složka vytvořená v části objekty blob bloku a sdílené objekty blob stránky představuje kontejner, do kterého se data nahrají jako objekty blob. Soubory nemůžete kopírovat přímo do *kořenové* složky v účtu úložiště.

Po připojení ke sdílené složce SMB spusťte kopii dat.

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
    |/nfl    |Určuje, že se názvy souborů neprotokolují.         |
    |/ndl    |Určuje, že názvy adresářů se neprotokolují.        |
    |/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
    |/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
    |/fft    | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
    |/b      | Zkopíruje soubory v režimu zálohování.        |
    |/z      | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
    | /zb    | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
    |/efsraw | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
    |log +:\<soubor protokolu >| Připojí výstup k existujícímu souboru protokolu.|
    
 
    Následující příklad ukazuje výstup příkazu Robocopy ke zkopírování souborů do Data Box Heavy.

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

2. Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy. (Níže uvedená čísla reprezentují scénáře nejlepšího případu.)

    | Platforma    | Vetšinou malé soubory (< 512 kB)    | Většinou střední soubory 512 KB-1 MB  | Většinou velké soubory (> 1 MB)                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy relací <br> 24 vláken na relaci | 6 Robocopy relací <br> 16 vláken na relaci | 6 Robocopy relací <br> 16 vláken na relaci |


    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

3. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je.

    ![Zobrazit zkopírované soubory](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Při kopírování dat:

    - Názvy souborů, velikosti a formáty jsou ověřeny, aby se zajistilo, že splňují omezení objektu a úložiště Azure a také zásady vytváření názvů souborů a kontejnerů Azure.
    - Aby se zajistila integrita dat, je kontrolní součet také vypočítán jako vložený.

    Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Vyberte ikonu šipky a Stáhněte soubory s chybami.

    ![Stáhnout soubory s chybou](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Další informace najdete v tématu [zobrazení protokolů chyb během kopírování dat do data box Heavy](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [řešení potíží s data box Heavy](data-box-troubleshoot.md).

5. Otevřete soubor chyb v programu Poznámkový blok. Následující chybový soubor indikuje, že data nejsou správně zarovnána.

    ![Otevřít soubor s chybou](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    V případě objektu blob stránky musí být data zarovnaná 512 bajtů. Po odebrání těchto dat se chyba vyřeší, jak je znázorněno na následujícím snímku obrazovky.

    ![Chyba byla vyřešena](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Po dokončení kopírování přejdete na zobrazení stránky **řídicího panelu** . Ověřte použité místo a volné místo na vašem zařízení.
    
    ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Zopakováním výše uvedených kroků zkopírujte data do druhého uzlu zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box Heavy témata, jako například:

> [!div class="checklist"]
> * Připojení k Data Box Heavy
> * Kopírovat data do Data Box Heavy


Přejděte k dalšímu kurzu, kde se dozvíte, jak odeslat Data Box Heavy zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Box Heavy Microsoftu](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopírování dat pomocí protokolu SMB

1. Pokud používáte hostitele Windows, připojte se ke sdíleným složkám SMB pomocí následujícího příkazu:

    `\\<IP address of your device>\ShareName`

2. Pokud chcete získat přihlašovací údaje ke sdílené složce, přejděte na stránku **Connect & copy** (Připojit a kopírovat) v místním webovém uživatelském rozhraní Data Boxu.

3. K kopírování dat do sdílených složek použijte nástroj pro kopírování souborů kompatibilní s protokolem SMB, jako je například Robocopy.

Podrobné pokyny najdete [v kurzu: Kopírovat data do Azure Data Box přes protokol](data-box-heavy-deploy-copy-data.md)SMB.

### <a name="copy-data-via-nfs"></a>Kopírování dat přes systém souborů NFS

1. Pokud používáte hostitele systému souborů NFS, připojte sdílené složky systému souborů NFS pomocí následujícího příkazu:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Přihlašovací údaje pro přístup ke sdílené složce získáte tak, že přejdete na **stránku připojit & kopírování** v místním webovém uživatelském rozhraní data box Heavy.
3. Pomocí `cp` příkazu `rsync` nebo zkopírujte data. 
4. Opakujte tyto kroky pro připojení a zkopírování dat do druhého uzlu Data Box Heavy.

Podrobné pokyny najdete [v kurzu: Kopírování dat do Azure Data Box přes systém](data-box-heavy-deploy-copy-data-via-nfs.md)souborů NFS.

### <a name="copy-data-via-rest"></a>Kopírování dat pomocí REST

1. Pokud chcete kopírovat data pomocí Data Box BLOB Storage přes rozhraní REST API, můžete se připojit přes *protokol HTTP* nebo *https*.
2. Pokud chcete kopírovat data do Data Box BLOB Storage, můžete použít AzCopy.
3. Opakujte tyto kroky pro připojení a zkopírování dat do druhého uzlu Data Box Heavy.

Podrobné pokyny najdete [v kurzu: Zkopírujte data do Azure Data Box BLOB Storage přes rozhraní REST](data-box-heavy-deploy-copy-data-via-rest.md)API.

### <a name="copy-data-via-data-copy-service"></a>Kopírování dat prostřednictvím služby kopírování dat

1. Chcete-li kopírovat data pomocí služby kopírování dat, je třeba vytvořit úlohu. V místním webovém uživatelském rozhraní Data Box Heavy klikněte na **spravovat > kopírovat Data > vytvořit**.
2. Vyplňte parametry a vytvořte úlohu.
3. Opakujte tyto kroky pro připojení a zkopírování dat do druhého uzlu Data Box Heavy.

Podrobné pokyny najdete [v kurzu: Pomocí služby kopírování dat zkopírujte data do Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Kopírování dat na spravované disky

1. Při seřazení zařízení Data Box Heavy byste měli jako cíl úložiště vybrat spravované disky.
2. K Data Box Heavy se můžete připojit přes sdílené složky SMB nebo NFS.
3. Data pak můžete kopírovat prostřednictvím nástrojů SMB nebo NFS.
4. Opakujte tyto kroky pro připojení a zkopírování dat do druhého uzlu Data Box Heavy.

Podrobné pokyny najdete [v kurzu: Používejte Data Box k těžkému importování dat jako spravovaných](data-box-heavy-deploy-copy-data-from-vhds.md)disků v Azure.

::: zone-end


