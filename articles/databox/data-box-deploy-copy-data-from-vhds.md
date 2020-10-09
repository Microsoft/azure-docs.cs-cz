---
title: 'Kurz: kopírování z VHD na spravované disky'
titleSuffix: Azure Data Box
description: Přečtěte si, jak kopírovat data z virtuálních pevných disků z místních úloh virtuálních počítačů do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 9af4de51e94a1be118211e2bb4404926656fedf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739863"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Kurz: použití Data Box k importu dat jako spravovaných disků v Azure

V tomto kurzu se dozvíte, jak pomocí Azure Data Box migrovat místní virtuální pevné disky na spravované disky v Azure. Virtuální pevné disky z místních virtuálních počítačů se zkopírují do Data Box jako objekty blob stránky a nahrají se do Azure jako spravované disky. Tyto spravované disky pak můžete připojit k virtuálním počítačům Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Nastavení Azure Data Boxu](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Jste připojení k síti s vysokou rychlostí. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není připojení k dispozici, použijte linku s 1 GbE, ale rychlost kopírování ovlivní.
4. Zkontrolovali jste:

    - Podporované [velikosti spravovaných disků v omezeních velikosti objektů Azure](data-box-limits.md#azure-object-size-limits).
    - [Seznámení se službou Azure Managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

5. Kopii zdrojových dat jste zachovali, dokud nepotvrdíte, že Data Box přenesli vaše data do Azure Storage.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

Na základě zadaných skupin prostředků vytvoří Data Box pro každou přidruženou skupinu prostředků jednu sdílenou složku. Například pokud `mydbmdrg1` a `mydbmdrg2` byly vytvořeny při umístění objednávky, jsou vytvořeny následující sdílené složky:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

V rámci každé sdílené složky jsou vytvořeny následující tři složky, které odpovídají kontejnerům v účtu úložiště.

- SSD úrovně Premium
- HDD úrovně Standard
- SSD úrovně Standard

V následující tabulce jsou uvedeny cesty UNC ke sdíleným složkám v Data Box.
 
|        Protokol připojení           |             Cesta UNC ke sdílené složce                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

V závislosti na tom, jestli k připojení Data Box ke sdíleným složkám používáte SMB nebo NFS, se postup pro připojení liší.

> [!NOTE]
> Připojení přes REST není pro tuto funkci podporováno.

### <a name="connect-to-data-box-via-smb"></a>Připojení k Data Box přes protokol SMB

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Kliknutím na **získat přihlašovací údaje** získáte Přístupová pověření ke sdíleným složkám přidruženým k vaší skupině prostředků. Přihlašovací údaje pro přístup můžete získat taky z **podrobností o zařízení** v Azure Portal.

    > [!NOTE]
    > Přihlašovací údaje pro všechny sdílené složky pro spravované disky jsou identické.

    ![Získání přihlašovacích údajů sdílené složky](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. V dialogovém okně sdílená složka pro přístup a kopírovat data zkopírujte **uživatelské jméno** a **heslo** sdílené složky. Klikněte na **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky 2](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Pro přístup ke sdíleným složkám přidruženým k vašemu prostředku (*mydbmdrg1* v následujícím příkladu) z hostitelského počítače otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Cesty ke sdílené složce UNC v tomto příkladu jsou následující:

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
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    V každé sdílené složce by se teď měly zobrazit následující předem vytvořené složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Připojení k Data Box přes systém souborů NFS

Pokud používáte hostitelský počítač s Linuxem, pomocí následujícího postupu nakonfigurujte Data Box tak, aby povoloval přístup klientům systému souborů NFS.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**.

    ![Konfigurace přístupu klienta NFS](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu.

3. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS ke svému zařízení Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje připojení ke sdílené složce Data Boxu přes systém souborů NFS. IP adresa zařízení Data Box je `169.254.250.200`, sdílená složka `mydbmdrg1_MDisk` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Až budete připojeni k datovému serveru, je dalším krokem kopírování dat. Soubor VHD se zkopíruje do pracovního účtu úložiště jako objekt blob stránky. Objekt blob stránky se pak převede na spravovaný disk a přesune se do skupiny prostředků.

Než začnete s kopírováním dat, přečtěte si následující skutečnosti:

- Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud zkopírujete virtuální pevné disky mimo tyto složky nebo do složky, kterou jste vytvořili, budou se virtuální pevné disky nahrály do Azure Storage účtu jako objekty blob stránky a ne spravované disky.
- K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. Soubory VHDX nebo dynamické a rozdílové virtuální pevné disky nejsou podporovány.
- Ve skupině prostředků ve všech předdefinovaných složkách můžete mít jenom jeden spravovaný disk se zadaným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků.
- Zkontrolujte omezení na spravovaných discích v [omezeních velikosti objektů Azure](data-box-limits.md#azure-object-size-limits).

V závislosti na tom, jestli se připojujete přes protokol SMB nebo NFS, můžete použít:

- [Kopírování dat pomocí protokolu SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Kopírování dat přes systém souborů NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Počkejte, než se úlohy kopírování dokončí. Než přejdete k dalšímu kroku, ujistěte se, že se úlohy kopírování dokončily bez chyb.

![Žádné chyby na stránce * * připojit a kopírovat * *](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Pokud během kopírování dojde k chybám, Stáhněte si protokoly ze stránky **připojit a kopírovat** .

- Pokud jste zkopírovali soubor, který není zarovnaný 512 bajtů, soubor se nahraje jako objekt blob stránky do pracovního účtu úložiště. V protokolech se zobrazí chyba. Odeberte soubor a zkopírujte soubor, který je 512 bajtů zarovnaných.

- Pokud jste zkopírovali soubor VHDX (tyto soubory nejsou podporované) s dlouhým názvem, zobrazí se v protokolech chyba.

    ![Chyba na stránce protokoly z * * připojit a kopírovat * *](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Než budete pokračovat k dalšímu kroku, vyřešte tyto chyby.

Aby se zajistila integrita dat, při kopírování dat se počítá kontrolní součet. Po dokončení kopírování zkontrolujte využité a volné místo na zařízení.
    
![Kontrola volného a využitého místa na řídicím panelu](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Jakmile je úloha kopírování dokončená, můžete přejít na **Příprava k odeslání**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu
> * Kopírování dat do Data Boxu


V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)

