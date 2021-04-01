---
title: Kurz kopírování dat z Azure Data Box přes systém souborů NFS | Microsoft Docs
description: Informace o tom, jak kopírovat data z Azure Data Box přes systém souborů NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680628"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>Kurz: kopírování dat z Azure Data Box přes systém souborů NFS

V tomto kurzu se dozvíte, jak se připojit ke svému místnímu datovému serveru pomocí systému souborů NFS a kopírovat z něj data z místního webového uživatelského rozhraní Data Box. Data ve vašem Data Box jsou exportována z účtu Azure Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat z Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Zadali jste objednávku Azure Data Boxu.
    - V případě objednávky importu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-ordered.md).
    - V případě objednávky exportu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-export-ordered.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, do kterého chcete zkopírovat data z Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
   * Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
   * Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte k dispozici připojení 10 GbE, použijte datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS. Data Box se může připojit až po dobu pěti klientů systému souborů NFS.

1. Zadejte IP adresy povolených klientů, kteří mají přístup ke sdílené složce:

    1.  V místním webovém uživatelském rozhraní přejdete na stránku **připojit a kopírovat** . V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

        ![Otevření přístupu klienta NFS](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Chcete-li přidat klienta systému souborů NFS, zadejte IP adresu klienta a klikněte na tlačítko **Přidat**. Data Box se může připojit až po dobu pěti klientů systému souborů NFS. Po dokončení klikněte na tlačítko **OK**.

         ![Přidání klienta NFS](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu. 

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `10.161.23.130`, sdílená složka `Mystoracct_Blob` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    U klientů se systémem Mac budete muset přidat další možnost následujícím způsobem: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.

## <a name="copy-data-from-data-box"></a>Kopírování dat z Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Teď můžete začít kopírovat data. Pokud používáte hostitelský počítač s Linuxem, použijte podobný nástroj pro kopírování jako Robocopy. Některé alternativy dostupné v systému Linux jsou [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [úlohách](https://www.cis.upenn.edu/~bcpierce/unison/)nebo [Ultracopier](https://ultracopier.first-world.info/).  

Jednou z nejlepších možností, jak zkopírovat adresář, je příkaz `cp`. Další informace o jeho použití najdete na [manuálových stránkách pro příkaz cp](http://man7.org/linux/man-pages/man1/cp.1.html).

Pokud používáte `rsync` možnost kopírování s více vlákny, postupujte podle těchto pokynů:

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

Po dokončení kopírování přejděte k **řídicímu panelu** a zkontrolujte využité a volné místo na zařízení.

Teď můžete přistoupit k odeslání Data Boxu do Microsoftu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat z Data Boxu

V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-export-picked-up.md)
