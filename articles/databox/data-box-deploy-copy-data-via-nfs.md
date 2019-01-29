---
title: Kopírování dat do vaší aplikace Microsoft Azure Data Box prostřednictvím systému souborů NFS | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do Azure Data Box prostřednictvím systému souborů NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: cec96871d379f9484bf8e08a9d511146a80d45c6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095595"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Kurz: Kopírování dat do služby Azure Data Box prostřednictvím systému souborů NFS

Tento kurz popisuje, jak se připojit k a kopírování dat z hostitelského počítače pomocí místního webového uživatelského rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, který obsahuje data, která chcete zkopírovat do Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, je možné použít 1 GbE dat propojení ale rychlosti kopírování bude mít vliv. 

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

Založené na vybraný účet úložiště, zařízení Data Box vytvoří až:
- Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
- Jednu sdílenou složku pro účet úložiště Premium nebo účet úložiště objektů blob. 

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

V následující tabulce jsou uvedeny cesty UNC ke sdílené složky na vaše zařízení Data Box a Azure Storage cestu URL, kterého se nahrávají data. Konečná adresa URL služby Azure Storage cesta může být odvozena z cesty UNC sdílené složky.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty BLOB bloku Azure | <li>Cesta UNC ke sdílené složky: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty BLOB stránky Azure  | <li>Cesta UNC ke sdílené složky: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdílené složky: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

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

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat. Než začnete kopírování dat, projděte si následující aspekty:

- Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky pro objekty BLOB stránky. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení úložiště Azure a Data Boxu](data-box-limits.md). 
- Pokud data nahrávaná Data Boxem zároveň nahrávají jiné aplikace mimo Data Box, může to způsobit selhání úlohy nahrávání a poškození dat.
- Doporučujeme, abyste nepoužívali protokol SMB a systém souborů NFS současně a abyste nekopírovali stejná data do stejného cíle v Azure. V takových případech není možné určit konečný výsledek.
- **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.

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

- Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
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

