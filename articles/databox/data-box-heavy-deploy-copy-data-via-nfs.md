---
title: Kurz kopírování dat do Azure Data Box Heavy přes systém souborů NFS | Microsoft Docs
description: V tomto kurzu se dozvíte, jak se připojit a kopírovat data z hostitelského počítače do Azure Data Box Heavy pomocí systému souborů NFS s místním webovým uživatelským rozhraním.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a8199039ea803d5919e168edccaaa03c18985725
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87921023"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Kurz: kopírování dat do Azure Data Box Heavy přes systém souborů NFS

V tomto kurzu se dozvíte, jak připojit a zkopírovat data z hostitelského počítače pomocí místního webového uživatelského rozhraní k vašemu Azure Data Box Heavy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurz: nastavte Azure Data box Heavy](data-box-heavy-deploy-set-up.md).
2. Dostali jste Data Box Heavy se **doručí** stav objednávky na portálu.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu Heavy. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Největší rychlosti kopírování je možné dosáhnout použitím dvou paralelních připojení 40 GbE (jedno na uzel). Pokud nemáte k dispozici připojení 40 GbE, doporučujeme použít alespoň dvě připojení 10 GbE (jedno na uzel). 

## <a name="connect-to-data-box-heavy"></a>Připojení k Data Boxu Heavy

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
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Pokud používáte hostitelský počítač se systémem Linux, proveďte následující kroky, abyste mohli nakonfigurovat zařízení tak, aby umožňovalo přístup k klientům NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu. 

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje, jak se připojit přes systém souborů NFS ke sdílené Data Box Heavy. Data Box Heavy IP adresa je `10.161.23.130` sdílená složka `Mystoracct_Blob` připojená k ubuntuVM, přípojnému bodu `/home/databoxheavyubuntuhost/databoxheavy` .

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    U klientů se systémem Mac budete muset přidat další možnost následujícím způsobem: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do Data Boxu Heavy

Až budete připojeni ke sdíleným složkám Data Box Heavy, je dalším krokem kopírování dat. Než začnete s kopírováním dat, projděte si následující důležité informace:

- Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Kopírovat virtuální pevné disky do objektů blob stránky. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v [úložišti Azure a omezeních data box Heavy](data-box-heavy-limits.md). 
- Pokud data nahrávaná Data Boxem Heavy zároveň nahrávají jiné aplikace mimo Data Box Heavy, může to způsobit selhání úlohy nahrávání a poškození dat.
- Doporučujeme, abyste nepoužívali protokol SMB a systém souborů NFS současně a abyste nekopírovali stejná data do stejného cíle v Azure. V takových případech není možné určit konečný výsledek.
- **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.
- Pokud se potýkají ingestování názvů adresářů a souborů ze sdílené složky NFS do systému souborů NFS v Data Box Heavy: 
    - V názvu se zachová velká a malá písmena.
    - V souborech se nerozlišují malá a velká písmena.
    
    Například při kopírování `SampleFile.txt` a `Samplefile.Txt` zachová se v názvu při kopírování do zařízení, ale druhý soubor přepíše první soubor, který se považuje za stejný soubor.


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

    `sudo mkdir /mnt/databoxheavy`

 - Připojte svazek.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Zrcadlete adresářovou strukturu složky.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Zkopírujte soubory. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     V tomto příkazu parametr j určuje úroveň paralelizace a hodnota X počet paralelních kopií.

     Doporučujeme začít s 16 paralelními kopiemi a zvyšovat počet vláken v závislosti na dostupnosti prostředků.

> [!IMPORTANT]
> Následující typy souborů systému Linux nejsou podporovány: symbolické odkazy, soubory znaků, blokovat soubory, sokety a kanály. Tyto typy souborů budou mít za následek chyby během **Příprava k odeslání** kroku.

Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Další informace najdete v tématu věnovaném [zobrazení protokolů chyb při kopírování dat do Data Boxu Heavy](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [Řešení potíží s Data Boxem Heavy](data-box-troubleshoot.md).

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
   ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Boxem Heavy, mezi která patří:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy


V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu heavy do Microsoftu](./data-box-heavy-deploy-picked-up.md)

