---
title: Kurz kopírování dat do Azure Data Box přes systém souborů NFS | Microsoft Docs
description: V tomto kurzu se dozvíte, jak se připojit a kopírovat data z hostitelského počítače do Azure Data Box pomocí systému souborů NFS s místním webovým uživatelským rozhraním.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678878"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Kurz: kopírování dat do Azure Data Box přes systém souborů NFS

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
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není připojení k dispozici, je možné použít datové propojení s 1 GbEí, ale bude to mít vliv na rychlost kopírování. 

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

V závislosti na vybraném účtu úložiště vytvoří Data Box až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jednu sdílenou složku pro každou službu Storage úrovně Premium. 
- Jednu sdílenou složku pro každý účet úložiště objektů blob. 

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
| Typ Azure Storage| Sdílené složky Data Boxů                                       |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

    ![Konfigurace přístupu klienta NFS](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace IP adresy klienta NFS](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu. 

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `10.161.23.130`, sdílená složka `Mystoracct_Blob` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    U klientů se systémem Mac budete muset přidat další možnost následujícím způsobem: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat. Než začnete s kopírováním dat, projděte si následující důležité informace:

* Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Kopírovat virtuální pevné disky do objektů blob stránky. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
*  Při kopírování dat se ujistěte, že velikost dat odpovídá omezení velikosti popsané v [omezeních velikosti účtu úložiště Azure](data-box-limits.md#azure-storage-account-size-limits).
* Pokud data nahrávaná Data Boxem zároveň nahrávají jiné aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
* Doporučujeme, abyste nepoužívali protokol SMB a systém souborů NFS současně a abyste nekopírovali stejná data do stejného cíle v Azure. V takových případech není možné určit konečný výsledek.
* **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.
* Pokud se potýkají ingestování názvů adresářů a souborů ze sdílené složky NFS do systému souborů NFS v Data Box:
  * V názvu se zachová velká a malá písmena.
  * V souborech se nerozlišují malá a velká písmena.

    Například při kopírování `SampleFile.txt` a `Samplefile.Txt` zachová se v názvu při zkopírování do data box, ale druhý soubor přepíše první soubor, který se považuje za stejný soubor.

> [!IMPORTANT]
> Než budete moct potvrdit, že zařízení Data Box převedlo data do Azure Storage, ujistěte se, že si uchováváte kopii zdrojových dat.

Pokud používáte hostitelský počítač s Linuxem, použijte podobný nástroj pro kopírování jako Robocopy. Některé alternativy dostupné v systému Linux jsou [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [úlohách](https://www.cis.upenn.edu/~bcpierce/unison/)nebo [Ultracopier](https://ultracopier.first-world.info/).  

Jednou z nejlepších možností, jak zkopírovat adresář, je příkaz `cp`. Další informace o jeho použití najdete na [manuálových stránkách pro příkaz cp](http://man7.org/linux/man-pages/man1/cp.1.html).

Pokud použijete `rsync` možnost pro kopírování s více vlákny, postupujte podle těchto pokynů:

* V závislosti na systému souborů, který používá váš klient Linuxu, nainstalujte balíček **CIFS Utils** nebo **NFS Utils**.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Instalace `rsync` a **paralelní** (liší se v závislosti na distribuované verzi systému Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Vytvořte přípojný bod.

    `sudo mkdir /mnt/databox`

* Připojte svazek.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Zrcadlete adresářovou strukturu složky.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Zkopírujte soubory.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     V tomto příkazu parametr j určuje úroveň paralelizace a hodnota X počet paralelních kopií.

     Doporučujeme začít s 16 paralelními kopiemi a zvyšovat počet vláken v závislosti na dostupnosti prostředků.

> [!IMPORTANT]
> Následující typy souborů systému Linux nejsou podporovány: symbolické odkazy, soubory znaků, blokovat soubory, sokety a kanály. Tyto typy souborů budou mít za následek chyby během **Příprava k odeslání** kroku.

Pokud během procesu kopírování dojde k nějakým chybám, zobrazí se oznámení.

![Stažení a zobrazení chyb pro operaci Připojit a kopírovat](media/data-box-deploy-copy-data/view-errors-1.png)

Vyberte **Stáhnout seznam problémů**.

![Stažení seznamu problémů pro chybu kopírování](media/data-box-deploy-copy-data/view-errors-2.png)

Otevřete tento seznam, projděte si podrobnosti o chybě a vyberte adresu URL pro zobrazení doporučeného řešení.

![Problémy v seznamu problémů s chybou kopírování](media/data-box-deploy-copy-data/view-errors-3.png)

Další informace najdete v tématu věnovaném [zobrazení protokolů chyb při kopírování dat do Data Boxu](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat najdete v tématu [Řešení potíží s Data Boxem](data-box-troubleshoot.md).

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.

   ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

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
