---
title: Kurz ke kopírování dat prostřednictvím protokolu SMB v Azure Data Box náročné | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do vaší Azure Data Box náročné prostřednictvím protokolu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 1c45e06159e4c2850efa2d3ab3290647961fb7e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592419"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Kurz: Kopírování dat do služby Azure Data Box náročné prostřednictvím protokolu SMB

Tento kurz popisuje, jak se připojit k a kopírování dat z hostitelského počítače pomocí místního webového uživatelského rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Azure Data Box náročné nastavit](data-box-deploy-set-up.md).
2. Jste přijali vaše náročné pole Data a stav objednávky na portálu je **dodáno**.
3. Máte hostitelský počítač, který obsahuje data, který chcete zkopírovat do Data Box náročné. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Pro nejrychlejší kopírování rychlosti můžete využít dvě připojení 10GbE 40 (jeden do každého uzlu) paralelně. Pokud nemáte k dispozici připojení 10GbE 40, doporučujeme, že máte alespoň dva 10 GbE připojení (jeden do každého uzlu).

## <a name="connect-to-data-box-heavy-shares"></a>Připojte se k velkým pole Data sdílené složky

Založené na vybraný účet úložiště, datové pole v případě velkého vytvoří až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jedna sdílená složka pro premium storage.
- Jedna sdílená složka pro účet služby blob storage.

Tyto prostředky jsou vytvářeny na obou uzlech zařízení.

V rámci sdílené složky objektů blob bloku blob a stránky:
- Na první úrovni entity jsou kontejnery.
- Druhé úrovně entity jsou objekty BLOB.

V rámci sdílené složky pro soubory Azure:
- Na první úrovni entity jsou sdílené složky.
- Druhé úrovně entity jsou soubory.

V následující tabulce jsou uvedeny cesty UNC ke sdílené složky na vaše Data Box zátěži a Azure Storage cestu adresy URL kterého se nahrávají data. Konečná adresa URL služby Azure Storage cesta může být odvozena z cesty UNC sdílené složky.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty BLOB bloku Azure | <li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty BLOB stránky Azure  | <li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdílené složky: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Postup připojení pomocí Windows nebo Linuxem klienta se liší.

> [!NOTE]
> Postupujte podle stejných kroků pro připojení k uzlům zařízení paralelně.

### <a name="connect-on-a-windows-system"></a>Připojení v systému Windows

Pokud používáte počítač s Windows serverem hostitele, postupujte podle těchto kroků se připojíte k velkým pole Data.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získejte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště.

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Pro přístup ke složkám přidružené k účtu úložiště (*databoxe2etest* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Azure objekt blob bloku – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure objektů blob stránky – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Soubory Azure – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Klikněte na tlačítko **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Teď byste měli vidět sdílené složky jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.
    
### <a name="connect-on-a-linux-system"></a>Připojení v systému Linux

Použití klienta systému Linux, pomocí následujícího příkazu připojte sdílenou složku SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` Parametr je verze protokolu SMB, která podporuje hostitele se systémem Linux. Zařaďte příslušnou verzi ve výše uvedeném příkazu.

Verze protokolu SMB, která podporuje náročné pole dat, naleznete v tématu [podporované souborové systémy pro klienty Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do služby Data Box Heavy

Po připojení ke sdíleným složkám náročné pole dat, dalším krokem je zkopírovat data.

### <a name="copy-considerations"></a>Důležité informace o kopírování

Než začnete kopírování dat, projděte si následující aspekty:

- Ujistěte se, že zkopírovat data do sdílené složky, které odpovídají formátu příslušná data. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky do objektů blob stránky.

    Pokud formát dat neshoduje s typem příslušné sdílené složky, pak v pozdějším kroku, nahrát data do Azure se nezdaří.
-  Při kopírování dat, ujistěte se, že velikost dat odpovídá omezení velikosti podle [služby Azure storage a Data Box náročné omezení](data-box-heavy-limits.md).
- Pokud se data, která se nahrává podle těžké pole Data, současně odešle jiné aplikace mimo náročné pole dat, pak to může způsobit poškození nahrávání úlohy selhání a data.
- Doporučujeme:
    - Nepoužíváte protokolu SMB a systému souborů NFS ve stejnou dobu.
    - Zkopírujte stejná data do stejného cíle end v Azure.
     
  V těchto případech nelze určit konečný výsledek.
- Vždy vytvořte složku pro soubory, které chcete kopírovat v rámci sdílené složky a potom zkopírujte soubory do této složky. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data nahrají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.

Po připojení ke sdílené složce protokolu SMB, začněte kopírovat data.

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
    |/nfl    |Určuje, že názvy souborů nejsou protokolovány.         |
    |/ndl    |Určuje, že názvy adresářů se neprotokolují.        |
    |/np     |Určuje, že se nezobrazí průběh operace kopírování (počet dosud zkopírovaných souborů nebo adresářů). Zobrazení průběhu výrazně snižuje výkon.         |
    |/MT     | Určuje, že se má použít multithreading. Doporučený počet vláken je 32 nebo 64. Tato možnost se nepoužívá u zašifrovaných souborů. Možná bude nutné oddělit zašifrované a nezašifrované soubory. Kopírování s jedním vláknem však výrazně snižuje výkon.           |
    |/fft    | Slouží k omezení členitosti časových razítek v jakémkoli systému souborů.        |
    |/b      | Zkopíruje soubory v režimu zálohování.        |
    |/z      | Zkopíruje soubory v režimu restartování. Tuto možnost použijte v případě, že je prostředí nestabilní. Tato možnost snižuje propustnost kvůli dodatečnému protokolování.      |
    | /zb    | Použije režim restartování. Pokud se přístup odepře, použije tato možnost režim zálohování. Tato možnost snižuje propustnost kvůli vytváření kontrolních bodů.         |
    |/efsraw | Zkopíruje všechny zašifrované soubory v režimu nezpracovaného systému souborů EFS. Tuto možnost použijte pouze u zašifrovaných souborů.         |
    |protokol +:\<soubor_protokolu >| Připojí výstup k existujícímu souboru protokolu.|
    
 
    Následující příklad ukazuje výstup příkaz robocopy pro kopírování souborů do Data Box náročné.

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

2. Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy. (Níže uvedených čísel představují doporučené scénáře.)

    | Platforma    | Vetšinou malé soubory (< 512 kB)    | Většinou střední soubory 512 KB - 1 MB  | Většinou velké soubory (> 1 MB)                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 relací Robocopy <br> 24 vláken na relaci | 6 relací Robocopy <br> 16 vláken na relaci | 6 relací Robocopy <br> 16 vláken na relaci |


    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

3. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je.

    ![Zobrazit zkopírované soubory](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Jak se kopírují data:

    - Názvy souborů, formáty a formát jsou ověřena k zajištění, že ty splňovat omezení objektu a úložiště v Azure stejně jako Azure file a container zásady vytváření názvů.
    - K zajištění integrity dat, je kontrolního součtu také počítaný vložená.

    Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Vyberte ikonu šipky stáhnout chybovým souborům.

    ![Stáhněte si chybovým souborům](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Další informace najdete v tématu [zobrazit protokoly chyb při kopírování dat do Data Box náročné](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat, naleznete v tématu [Poradce při potížích s Data Box na těžké problémy](data-box-troubleshoot.md).

5. Otevřete chybový soubor v poznámkovém bloku. Následující soubor chyba označuje, že není správně zarovnaná data.

    ![Chyba při otevírání souboru](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Pro objekt blob stránky data musí být 512 bajtů zarovnána. Po odebrání těchto dat chyba řeší, jak je znázorněno na následujícím snímku obrazovky.

    ![Vyřešené chyby](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Až se kopírování dokončí, přejděte do **zobrazit řídicí panel** stránky. Zkontrolujte využité místo a volné místo na vašem zařízení.
    
    ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Opakujte předchozí postup pro kopírování dat do druhý uzel zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box náročné témata, jako:

> [!div class="checklist"]
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy


Přejděte k dalšímu kurzu, kde se naučíte, jak dodávat vaše Data Box náročné zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Dodávejte vaše Azure Data pole těžkých společnosti Microsoft](./data-box-heavy-deploy-picked-up.md)

