---
title: 'Kurz: Kopírování z virtuálních pevných disků na spravované disky'
titleSuffix: Azure Data Box Heavy
description: Zjistěte, jak zkopírovat data z virtuálních disponiál z místních úloh virtuálních zařízení do vaší Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77471325"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Kurz: Import dat jako spravovaných disků v Azure pomocí funkce Data Box Heavy

Tento kurz popisuje, jak použít Azure Data Box Heavy k migraci místních virtuálních pevných disků na spravované disky v Azure. Virtuální pevné disky z místních virtuálních počítačů se zkopírují do data boxu Heavy jako objekty BLOB stránky a nahrají se do Azure jako spravované disky. Tyto spravované disky pak lze připojit k virtuálním počítačům Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurz: Nastavení Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Obdrželi jste Data Box Heavy a stav objednávky na portálu je **Doručeno**.
3. Jste připojeni k vysokorychlostní síti. Největší rychlosti kopírování je možné dosáhnout použitím dvou paralelních připojení 40 GbE (jedno na uzel). Pokud nemáte k dispozici připojení 40 GbE, doporučujeme použít alespoň dvě připojení 10 GbE (jedno na uzel). 
4. Zhodnotili jste:

    - Podporované [velikosti spravovaných disků v omezení velikosti objektů Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Úvod do spravovaných disků Azure](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Připojení k Data Boxu Heavy

Na základě zadaných skupin prostředků vytvoří data box heavy jednu sdílenou složku pro každou přidruženou skupinu prostředků na uzel. Pokud `mydbmdrg1` například `mydbmdrg2` byly vytvořeny a byly vytvořeny při zadávání objednávky, jsou vytvořeny následující sdílené složky:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

V rámci každé sdílené složky jsou vytvořeny následující tři složky, které odpovídají kontejnery v účtu úložiště.

- SSD úrovně Premium
- HDD úrovně Standard
- SSD úrovně Standard

V následující tabulce jsou uvedeny cesty UNC ke sdíleným položkám v datové schránce Heavy.
 
|        Protokol připojení           |             Cesta UNC ke sdílené položce                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Na základě toho, zda používáte SMB nebo NFS pro připojení ke sdíleným položkám Data Box Heavy, jsou kroky pro připojení odlišné.

> [!NOTE]
> - Připojení přes REST není pro tuto funkci podporováno.
> - Opakujte pokyny pro připojení pro připojení k druhému uzlu Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Připojení k datové schránce Těžké přes SMB

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu Heavy pomocí následujícího postupu.

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
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>\<ShareName>`. Kliknutím na **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Nyní byste měli vidět následující předvytvořené složky v rámci každé sdílené složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Připojení k datové schránce Heavy přes NFS

Pokud používáte hostitelský počítač s Linuxem, proveďte následující kroky a nakonfigurujte zařízení tak, aby umožňovalo přístup ke klientům systému automatického připojení k systému zabezpečení sítě.

1. Zadejte IP adresy klientů s povoleným přístupem ke sdílené složce. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**.

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Zadejte IP adresu klienta systému souborů NFS a klikněte na **Přidat**. Opakováním tohoto kroku můžete nakonfigurovat přístup pro více klientů systému souborů NFS. Klikněte na tlačítko **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Ujistěte se, že je na hostitelském počítači s Linuxem nainstalovaná [podporovaná verze](data-box-system-requirements.md) klienta systému souborů NFS. Použijte konkrétní verzi pro vaši distribuci Linuxu.

3. Po instalaci klienta systému sdílení systému sdílení systému sdílení systému sdílení systému sdílení systému sdílení na vašem zařízení:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Následující příklad ukazuje, jak se připojit přes NFS k datové schránce nebo sdílené schránce Data Box Heavy. Data Box nebo Data Box `169.254.250.200`Heavy device `mydbmdrg1_MDisk` IP je , sdílená složky jsou namontovány na ubuntuVM, mount point je `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do Data Boxu Heavy

Po připojení k datovému serveru je dalším krokem kopírování dat. Soubor VHD se zkopíruje do účtu pracovního úložiště jako objekt blob stránky. Objekt blob stránky se pak převede na spravovaný disk a přesune se do skupiny prostředků.

Než začnete kopírovat data, přečtěte si následující aspekty:

- Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud zkopírujete virtuální disky mimo tyto složky nebo ve složce, kterou jste vytvořili, virtuální pevné disky se nahrají do účtu Služby Azure Storage jako objekty BLOB stránky a ne spravované disky.
- K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. VHDX soubory nebo dynamické a differencing VHDnejsou podporovány.
- Ve všech předem vytvořených složkách můžete mít ve skupině prostředků pouze jeden spravovaný disk s daným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků.
- Zkontrolujte limity spravovaných disků v [limitech velikosti objektů Azure](data-box-heavy-limits.md#azure-object-size-limits).

V závislosti na tom, zda se připojujete přes SMB nebo NFS, můžete použít:

- [Kopírování dat pomocí protokolu SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopírování dat přes systém souborů NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

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

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Boxem Heavy, mezi která patří:

> [!div class="checklist"]
> * Kontrola požadavků
> * Připojení k Data Boxu Heavy
> * Kopírování dat do Data Boxu Heavy


V dalším kurzu se dozvíte, jak Data Box Heavy odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu heavy do Microsoftu](./data-box-heavy-deploy-picked-up.md)

