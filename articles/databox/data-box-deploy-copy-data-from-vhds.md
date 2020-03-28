---
title: 'Kurz: Kopírování z virtuálních pevných disků na spravované disky'
titleSuffix: Azure Data Box
description: Zjistěte, jak zkopírovat data z virtuálních disponiál z místních úloh virtuálních zařízení do datové schránky Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 965c768df9138d850c2ac9f88e3797dcc54fa3fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501853"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Kurz: Import dat jako spravovaných disků v Azure pomocí datové schránky

Tento kurz popisuje, jak použít Azure Data Box k migraci místních virtuálních pevných disků na spravované disky v Azure. Virtuální pevné disky z místních virtuálních počítačů se zkopírují do datové schránky jako objekty BLOB stránky a nahrají se do Azure jako spravované disky. Tyto spravované disky pak lze připojit k virtuálním počítačům Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurz: Nastavení datové schránky Azure](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Jste připojeni k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud připojení 10 GbE není k dispozici, použijte datové spojení 1 GbE, ale rychlost kopírování je ovlivněna.
4. Zhodnotili jste:

    - Podporované [velikosti spravovaných disků v omezení velikosti objektů Azure](data-box-limits.md#azure-object-size-limits).
    - [Úvod do spravovaných disků Azure](/azure/virtual-machines/windows/managed-disks-overview). 

5. Uchováváte kopii zdrojových dat, dokud nepotvrdíte, že datová schránka převedla vaše data do Azure Storage.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

Na základě zadaných skupin prostředků vytvoří data box pro každou přidruženou skupinu prostředků jednu sdílenou složku. Pokud `mydbmdrg1` například `mydbmdrg2` byly vytvořeny a byly vytvořeny při zadávání objednávky, jsou vytvořeny následující sdílené složky:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

V rámci každé sdílené složky jsou vytvořeny následující tři složky, které odpovídají kontejnery v účtu úložiště.

- SSD úrovně Premium
- HDD úrovně Standard
- SSD úrovně Standard

V následující tabulce jsou uvedeny cesty UNC ke sdíleným položkám v datové schránce.
 
|        Protokol připojení           |             Cesta UNC ke sdílené položce                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Na základě toho, zda používáte SMB nebo NFS pro připojení ke sdíleným položkám Datové schránky, jsou kroky pro připojení odlišné.

> [!NOTE]
> Připojení přes REST není pro tuto funkci podporováno.

### <a name="connect-to-data-box-via-smb"></a>Připojení k datové schránce přes SMB

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **Získat přihlašovací údaje** získáte přístupová pověření pro sdílené složky přidružené ke skupině prostředků. Přístupová pověření můžete taky získat z **podrobností o zařízení** na webu Azure Portal.

    > [!NOTE]
    > Pověření pro všechny sdílené složky pro spravované disky jsou identické.

    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. V dialogovém okně Access share and copy data zkopírujte **uživatelské jméno** a **heslo** pro sdílenou složku. Klikněte na tlačítko **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Chcete-li získat přístup ke sdíleným položkám přidruženým k prostředku *(mydbmdrg1* v následujícím příkladu) z hostitelského počítače, otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Vaše cesty sdílení UNC v tomto příkladu jsou následující:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>\<ShareName>`. Kliknutím na **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Nyní byste měli vidět následující předvytvořené složky v rámci každé sdílené složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Připojení k datové schránce přes NFS

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**.

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na tlačítko **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu.

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `169.254.250.200`, sdílená složka `mydbmdrg1_MDisk` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k datovému serveru je dalším krokem kopírování dat. Soubor VHD se zkopíruje do účtu pracovního úložiště jako objekt blob stránky. Objekt blob stránky se pak převede na spravovaný disk a přesune se do skupiny prostředků.

Než začnete kopírovat data, přečtěte si následující aspekty:

- Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud zkopírujete virtuální disky mimo tyto složky nebo ve složce, kterou jste vytvořili, virtuální pevné disky se nahrají do účtu Služby Azure Storage jako objekty BLOB stránky a ne spravované disky.
- K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. VHDX soubory nebo dynamické a differencing VHDnejsou podporovány.
- Ve všech předem vytvořených složkách můžete mít ve skupině prostředků pouze jeden spravovaný disk s daným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků.
- Zkontrolujte limity spravovaných disků v [limitech velikosti objektů Azure](data-box-limits.md#azure-object-size-limits).

V závislosti na tom, zda se připojujete přes SMB nebo NFS, můžete použít:

- [Kopírování dat pomocí protokolu SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Kopírování dat přes systém souborů NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Počkejte na dokončení úloh kopírování. Před provedením dalšího kroku se ujistěte, že úlohy kopírování byly dokončeny bez chyb.

![Žádné chyby na stránce **Připojit a kopírovat**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Pokud během kopírování dojde k chybám, stáhněte protokoly ze stránky **Připojit a zkopírovat.**

- Pokud jste zkopírovali soubor, který není zarovnaný o 512 bajtů, nebude soubor odeslán jako objekt blob stránky do vašeho účtu pracovního úložiště. Zobrazí se chyba v protokolech. Odeberte soubor a zkopírujte soubor, který je zarovnaný o velikosti 512 bajtů.

- Pokud jste zkopírovali VHDX (tyto soubory nejsou podporovány) s dlouhým názvem, zobrazí se chyba v protokolech.

    ![Chyba v protokolech ze stránky **Connect and copy**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Před dalším krokem vyřešte chyby.

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Po dokončení úlohy kopírování můžete přejít na **připravit k odeslání**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

