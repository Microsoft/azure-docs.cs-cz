---
title: Kurz ke zkopírování dat z virtuálních pevných disků na spravované disky s Azure Data Box náročné | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z virtuálních pevných disků z místních úloh virtuálních počítačů pro váš Azure Data Box těžká
services: databox
author: alkohli
ms.service: databox
ms.subservice: Heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8065d29c0cb984244178d49fe8c8c5aa853ee682
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595760"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Kurz: Použijte Data Box náročné k importu dat jako spravovaných disků v Azure

Tento kurz popisuje, jak migrace místních virtuálních pevných disků na managed disks v Azure pomocí Azure Data Box náročné. Virtuální pevné disky z místních virtuálních počítačů se zkopírují do datového pole náročné jako objekty BLOB stránky a nahrají do Azure jako spravované disky. Tyto spravované disky může pak být připojené k virtuálním počítačům Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Azure Data Box náročné nastavit](data-box-heavy-deploy-set-up.md).
2. Jste přijali vaše náročné pole Data a stav objednávky na portálu je **dodáno**.
3. Jste připojeni k vysokorychlostní sítí. Pro nejrychlejší kopírování rychlosti můžete využít dvě připojení 10GbE 40 (jeden do každého uzlu) paralelně. Pokud nemáte k dispozici připojení 10GbE 40, doporučujeme, že máte alespoň dva 10 GbE připojení (jeden do každého uzlu). 
4. Můžete si:

    - Podporované [spravované velikosti disků v omezení velikosti objektu Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Úvod do služby Azure managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Připojte se k datové pole náročná na výkon

Podle skupiny prostředků, který je zadán, Data Box náročné vytvoří jedna sdílená složka pro každou skupinu přidružený prostředek podle počtu uzlů. Například pokud `mydbmdrg1` a `mydbmdrg2` při uvádění pořadí, se vytvoří následující složky:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

V rámci každé sdílené složky se vytvoří následující tři složky, které odpovídají kontejnery v účtu úložiště.

- Premium SSD
- Standard HDD
- SSD úrovně Standard

V následující tabulce jsou uvedeny cesty UNC ke sdíleným složkám na vaše Data Box náročné.
 
|        Protokol připojení           |             Cesta UNC ke sdílené složce                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Podle toho, jestli používáte SMB nebo NFS se připojit ke sdíleným složkám Data Box náročné, kroky pro připojení se liší.

> [!NOTE]
> - Tato funkce nepodporuje připojování přes REST.
> - Opakujte připojit pokyny pro připojení k druhého uzlu Data Box náročné.

### <a name="connect-to-data-box-heavy-via-smb"></a>Připojte se k velmi náročné pole dat prostřednictvím protokolu SMB

Pokud používáte počítač s Windows serverem hostitele, postupujte podle těchto kroků se připojíte k velkým pole Data.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Klikněte na tlačítko **získání přihlašovacích údajů** k získání přihlašovacích údajů pro přístup pro sdílené složky přidružené tomuto vaší skupiny prostředků. Můžete také získat přístup k přihlašovací údaje z **podrobnosti o zařízení** na webu Azure Portal.

    > [!NOTE]
    > Přihlašovací údaje pro všechny sdílené složky za spravované disky jsou identické.

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Sdílené složce přístup a dialogové okno Kopírovat data, kopie **uživatelské jméno** a **heslo** pro sdílenou složku. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Pro přístup ke složkám spojené s vaším prostředkem (*mydbmdrg1* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Vaše cesty UNC sdílené složky v tomto příkladu jsou následující:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>\<ShareName>`. Klikněte na tlačítko **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Teď byste měli vidět následující vytvořených složky v rámci každé sdílené složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Připojte se k velmi náročné pole dat prostřednictvím systému souborů NFS

Pokud používáte Linux hostitelský počítač, proveďte následující kroky pro konfiguraci zařízení chcete povolit přístup k systému souborů NFS klientů.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**.

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu.

3. Po instalaci klienta systému souborů NFS, použijte následující příkaz pro připojení sdílené složky systému souborů NFS na vašem zařízení:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje, jak se připojit prostřednictvím systému souborů NFS na zařízení Data Box nebo velkým pole Data sdílet. Zařízení Data Box nebo Data Box náročné IP adresa patří `169.254.250.200`, sdílené složky `mydbmdrg1_MDisk` je připojena v ubuntuVM, připojení se bod `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do služby Data Box Heavy

Po připojení k serveru data, dalším krokem je zkopírovat data. Soubor virtuálního pevného disku je zkopírován do přípravného účtu úložiště jako objekt blob stránky. Objekty blob stránky je poté převeden na spravovaný disk a přesunout do skupiny prostředků.

Než začnete kopírování dat, přečtěte si následující aspekty:

- Vždy kopírovat virtuální pevné disky k jednomu z vytvořených složky. Pokud kopírujete virtuální pevné disky mimo tyto složky nebo do složky, kterou jste vytvořili, virtuální pevné disky se nahraje do účtu Azure Storage jako objekty BLOB stránky a nespravovaných disků.
- Pouze pevné virtuální pevné disky můžete nahrát do vytvoření spravovaných disků. Soubory VHDX a dynamických a rozdílových virtuálních pevných disků nejsou podporovány.
- Ve všech složkách, vytvořených můžete mít pouze jeden spravovaný disk se zadaným názvem ve skupině prostředků. Z toho vyplývá, že virtuální pevné disky nahráli do složek vytvořených by měly mít jedinečné názvy. Ujistěte se, že zadaný název neodpovídá již existujícího spravovaného disku do skupiny prostředků.
- Zkontrolujte limity spravovaného disku v [omezení velikosti objektu Azure](data-box-heavy-limits.md#azure-object-size-limits).

V závislosti na tom, jestli se připojují prostřednictvím protokolu SMB nebo NFS můžete použít:

- [Kopírování dat prostřednictvím protokolu SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopírování dat pomocí systému souborů NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Počkejte na dokončení úloh kopírování. Ujistěte se, že úlohy kopírování dokončili bez chyb, než přejdete k dalšímu kroku.

![Žádné chyby na ** připojit a kopírovat ** stránky](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Pokud nejsou chyby během kopírování, stáhněte si protokoly z **připojit a Kopírovat** stránky.

- Pokud jste zkopírovali soubor, který ne 512 bajtů zarovnána, není soubor nahrát jako objekt blob stránky do přípravného účtu úložiště. Zobrazí se chybu v protokolech. Odeberte tento soubor a zkopírujte soubor, který je 512 bajtů zarovnána.

- Pokud jste si zkopírovali VHDX (tyto soubory nejsou podporovány) s dlouhý název, zobrazí se chybu v protokolech.

    ![Chyby v protokolech z ** stránka připojit a kopírovat **](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Chyby vyřešte, než budete pokračovat k dalšímu kroku.

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Po dokončení kopírování úlohy můžete přejít na **přípravu k odeslání**.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box náročné témata, jako:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojte se k datové pole náročná na výkon
> * Kopírování dat do služby Data Box Heavy


Přejděte k dalšímu kurzu, kde se naučíte, jak dodávat vaše Data Box náročné zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Dodávejte vaše Azure Data pole těžkých společnosti Microsoft](./data-box-heavy-deploy-picked-up.md)

