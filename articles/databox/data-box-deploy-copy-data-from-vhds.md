---
title: Umožňuje importovat data z virtuálních pevných disků a zkopírujte na spravované disky s Microsoft Azure Data Box | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z virtuálních pevných disků z místních úloh virtuálních počítačů pro vaše zařízení Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
ms.openlocfilehash: 3b218aa7b99ad23789f1f88ef8ea56332d4f13a5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411476"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Kurz: Použijte Data Box pro import dat jako spravovaných disků v Azure

Tento kurz popisuje, jak migrovat místní virtuální pevné disky na managed disks v Azure pomocí Azure Data Box. Virtuální pevné disky z místních virtuálních počítačů se zkopírují do zařízení Data Box jako objekty BLOB stránky a nahrají do Azure jako spravované disky. Tyto spravované disky může pak být připojené k virtuálním počítačům Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **dodáno**.
3. Jste připojeni k vysokorychlostní sítí. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, použijte odkaz 1 GbE dat ale rychlosti kopírování se to týká.
4. Můžete si:

    - Podporované [spravované velikosti disků v omezení velikosti objektu Azure](data-box-limits.md#azure-object-size-limits).
    - [Úvod do služby Azure managed disks](/azure/virtual-machines/windows/managed-disks-overview.md). 

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

Data Box podle skupin prostředků, který je zadán, vytvoří jedna sdílená složka pro každou skupinu přidružený prostředek. Například pokud `mydbmdrg1` a `mydbmdrg2` při uvádění pořadí, se vytvoří následující složky:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

V rámci každé sdílené složky se vytvoří následující čtyři složky, které odpovídají kontejnery v účtu úložiště.

- Premium SSD
- Standard HDD
- SSD úrovně Standard

V následující tabulce jsou uvedeny cesty UNC ke sdíleným složkám na vaše zařízení Data Box.
 
|        Protokol připojení           |             Cesta UNC ke sdílené složce                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Podle toho, jestli používáte SMB nebo NFS pro připojení ke sdíleným složkám zařízení Data Box, kroky pro připojení se liší.

> [!NOTE]
> Tato funkce nepodporuje připojování přes REST.

### <a name="connect-to-data-box-via-smb"></a>Připojte se k zařízení Data Box prostřednictvím protokolu SMB

Pokud používáte počítač s Windows serverem hostitele, postupujte podle těchto kroků se připojíte k zařízení Data Box.

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


### <a name="connect-to-data-box-via-nfs"></a>Připojte se k zařízení Data Box prostřednictvím systému souborů NFS

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**.

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu.

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `169.254.250.200`, sdílená složka `mydbmdrg1_MDisk` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k serveru data, dalším krokem je zkopírovat data. Soubor virtuálního pevného disku je zkopírován do přípravného účtu úložiště jako objekt blob stránky. Objekty blob stránky je poté převeden na spravovaný disk a přesunout do skupiny prostředků.

Než začnete kopírování dat, přečtěte si následující aspekty:

- Vždy kopírovat virtuální pevné disky k jednomu z vytvořených složky. Pokud kopírujete virtuální pevné disky mimo tyto složky nebo do složky, kterou jste vytvořili, virtuální pevné disky se nahraje do účtu Azure Storage jako objekty BLOB stránky a nespravovaných disků.
- Pouze pevné virtuální pevné disky můžete nahrát do vytvoření spravovaných disků. Soubory VHDX a dynamických a rozdílových virtuálních pevných disků nejsou podporovány.
- Ve všech složkách, vytvořených můžete mít pouze jeden spravovaný disk se zadaným názvem ve skupině prostředků. Z toho vyplývá, že virtuální pevné disky nahráli do složek vytvořených by měly mít jedinečné názvy. Ujistěte se, že zadaný název neodpovídá již existujícího spravovaného disku do skupiny prostředků.
- Zkontrolujte limity spravovaného disku v [omezení velikosti objektu Azure](data-box-limits.md#azure-object-size-limits).

V závislosti na tom, jestli se připojují prostřednictvím protokolu SMB nebo NFS můžete použít:

- [Kopírování dat prostřednictvím protokolu SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Kopírování dat pomocí systému souborů NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

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

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

