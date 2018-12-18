---
title: Kopírování dat do vaší aplikace Microsoft Azure Data Box prostřednictvím systému souborů NFS | Dokumentace Microsoftu
description: Zjistěte, jak zkopírovat data do Azure Data Boxu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/20/2018
ms.author: alkohli
ms.openlocfilehash: 7ba6bc2cf3cf5286719bc6da519aabb364302af3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550526"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Kurz: Kopírování dat do služby Azure Data Box prostřednictvím systému souborů NFS 

Tento kurz popisuje, jak se pomocí místního webového uživatelského rozhraní připojit z hostitelského počítače k Data Boxu, zkopírovat do něj data z hostitelského počítače a pak připravit Data Box k odeslání.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu
> * Příprava k odeslání Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
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
- Sdílené složky pro objekt blob bloku: *Mystoracct_BlockBlob/my kontejneru/objektů blob*
- Sdílené složky pro objekt blob stránky: *Mystoracct_PageBlob/my kontejneru/objektů blob*
- Sdílené složky pro soubor: *Mystoracct_AzFile/my-share*

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

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu
> * Příprava k odeslání Data Boxu

Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

