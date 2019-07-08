---
title: Kurz ke zkopírování dat do Azure Data Box náročné prostřednictvím systému souborů NFS | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data na váš Azure Data Box náročné prostřednictvím systému souborů NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595746"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Kurz: Kopírování dat do služby Azure Data Box náročné prostřednictvím systému souborů NFS

Tento kurz popisuje, jak se připojit k a kopírování dat z hostitelského počítače pomocí místního webového uživatelského rozhraní pro váš Azure Data Box těžká.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Azure Data Box náročné nastavit](data-box-heavy-deploy-set-up.md).
2. Jste přijali vaše náročné pole Data a stav objednávky na portálu je **dodáno**.
3. Máte hostitelský počítač, který obsahuje data, který chcete zkopírovat do Data Box náročné. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Pro nejrychlejší kopírování rychlosti můžete využít dvě připojení 10GbE 40 (jeden do každého uzlu) paralelně. Pokud nemáte k dispozici připojení 10GbE 40, doporučujeme, že máte alespoň dva 10 GbE připojení (jeden do každého uzlu). 

## <a name="connect-to-data-box-heavy"></a>Připojte se k datové pole náročná na výkon

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
| Objekty BLOB bloku Azure | <li>Cesta UNC ke sdílené složky: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty BLOB stránky Azure  | <li>Cesta UNC ke sdílené složky: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdílené složky: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Pokud používáte Linux hostitelský počítač, proveďte následující kroky pro konfiguraci zařízení chcete povolit přístup k systému souborů NFS klientů.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu. 

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje, jak se připojit prostřednictvím systému souborů NFS ke sdílené datové pole v případě velkého. Je těžké IP pole Data `10.161.23.130`, sdílené složky `Mystoracct_Blob` je připojena v ubuntuVM, připojení se bod `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Pro klienty systému Mac. budete muset přidat další možnost následujícím způsobem: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do služby Data Box Heavy

Po připojení ke sdíleným složkám náročné pole dat, dalším krokem je zkopírovat data. Než začnete kopírování dat, projděte si následující aspekty:

- Ujistěte se, že data kopírujete do sdílených složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do sdílené složky určené pro objekty blob bloku. Zkopírujte virtuální pevné disky pro objekty BLOB stránky. Pokud formát dat neodpovídá příslušnému typu sdílené složky, nahrávání dat do Azure v pozdějším kroku selže.
-  Při kopírování dat, ujistěte se, že velikost dat odpovídá omezení velikosti podle [služby Azure storage a Data Box náročné omezení](data-box-heavy-limits.md). 
- Pokud se data, která se nahrává podle těžké pole Data, současně odešle jiné aplikace mimo náročné pole dat, pak to může způsobit poškození nahrávání úlohy selhání a data.
- Doporučujeme, abyste nepoužívali protokol SMB a systém souborů NFS současně a abyste nekopírovali stejná data do stejného cíle v Azure. V takových případech není možné určit konečný výsledek.
- **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené v rámci objektů blob bloku a objektů blob stránky sdílené složky představuje kontejner, do kterého se data odesílají jako objekty BLOB. Nelze kopírovat soubory přímo do *kořenové* složky v účtu úložiště.
- Pokud ingestovat malá a velká písmena adresáře a názvy souborů ze sdílené složky NFS systému souborů NFS na Data Box náročné: 
    - Tento případ je zachováno v názvu.
    - Soubory jsou malá a velká písmena.
    
    Například při kopírování souborů `SampleFile.txt` a `Samplefile.Txt`, tak se zachovají v názvu po zkopírování do zařízení, ale druhý soubor přepíše první z nich, jak tyto adresy jsou považované za stejný soubor.


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
> Nejsou podporovány následující typy souborů Linux: symbolické odkazy, soubory, bloku souborů, soketů a kanálů. Tyto typy souborů bude mít za následek selhání během **přípravu k odeslání** kroku.

Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování dojde k nějakým chybám, stáhněte si soubory s chybami, abyste mohli vyřešit případné potíže. Další informace najdete v tématu [zobrazit protokoly chyb při kopírování dat do Data Box náročné](data-box-logs.md#view-error-log-during-data-copy). Podrobný seznam chyb při kopírování dat, naleznete v tématu [Poradce při potížích s Data Box na těžké problémy](data-box-troubleshoot.md).

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
   ![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box náročné témata, jako:

> [!div class="checklist"]
> * Požadavky
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy


Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Dodávejte vaše Azure Data pole těžkých společnosti Microsoft](./data-box-heavy-deploy-picked-up.md)

