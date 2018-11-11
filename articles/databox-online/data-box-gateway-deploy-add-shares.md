---
title: Přenos dat pomocí služby Azure Data Box Gateway | Microsoft Docs
description: Zjistěte, jak na zařízení Data Box Gateway přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: fa31397e0ecffbd245557a824bdd770724bbc91c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249876"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Kurz: Přenos dat pomocí služby Azure Data Box Gateway (Preview)


## <a name="introduction"></a>Úvod

Tento článek popisuje, jak do služby Data Box Gateway přidat sdílené složky a připojit se k nim. Po přidání sdílených složek může zařízení Data Box Gateway přenést data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

> [!IMPORTANT]
> - Data Box Gateway je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do služby Data Box Gateway se ujistěte, že:

* Máte zřízené virtuální zařízení a jste k němu připojeni, jak je podrobně popsáno v tématu [Zřízení služby Data Box Gateway v prostředí Hyper-V](data-box-gateway-deploy-provision-hyperv.md) nebo [Zřízení služby Data Box Gateway v prostředí VMware](data-box-gateway-deploy-provision-vmware.md). 

    Virtuální zařízení je aktivované, jak je podrobně popsáno v tématu [Připojení a aktivace služby Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md), a připravené k vytvoření sdílených složek a přenosu dat.


## <a name="add-a-share"></a>Přidání sdílené složky

Provedením následujících kroků na webu [Azure Portal](https://portal.azure.com/) vytvořte sdílenou složku.

1. Vraťte se na Azure Portal. Přejděte do části **Všechny prostředky** a vyhledejte prostředek vaší služby Data Box Gateway.
    
2. Ve vyfiltrovaném seznamu prostředků vyberte prostředek Data Box Gateway a pak přejděte na **Přehled**. Na panelu příkazů zařízení klikněte na **+ Přidat sdílenou složku**.
   
   ![Přidání sdílené složky](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky. 

   Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.
    
5. Vyberte **Typ** sdílené složky. Typ může být SMB nebo NFS, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti. 

6. Musíte zadat účet úložiště, ve kterém se sdílená složka bude nacházet. V účtu úložiště se vytvoří kontejner s názvem sdílené složky, pokud ještě neexistuje. Pokud už kontejner existuje, použije se existující kontejner. 
    
7. V části **Služba úložiště** zvolte Objekt blob bloku, Objekt blob stránky nebo Soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme data v Azure ukládat jako objekty blob bloku, proto vybereme Objekt blob bloku. Pokud zvolíte Objekt blob stránky, musíte zajistit, aby vaše data byla zarovnaná na 512 bajtů. Poznámka: Formát VHDX je vždy zarovnaný na 512 bajtů.
   
8. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS. 
     
    - **Pokud vytváříte sdílenou složku SMB** – V poli Místní uživatel se všemi oprávněními zvolte **Vytvořit nový** nebo **Použít existující**. Pokud vytváříte nového místního uživatele, zadejte **uživatelské jméno**, **heslo** a pak **potvrďte heslo**. Tím se místnímu uživateli přiřadí oprávnění. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
    
        ![Přidání sdílené složky SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Pokud pro data této sdílené složky zaškrtnete možnost **Povolit jen operace čtení**, budete mít možnost určit uživatele jen pro čtení.
        
    - **Pokud vytváříte sdílenou složku systému souborů NFS** – Musíte zadat IP adresy klientů s povoleným přístupem ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Kliknutím na **Vytvořit** vytvořte sdílenou složku. 
    
    Zobrazí se oznámení o probíhajícím vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka. 
    
    ![Aktualizovaný seznam sdílených složek](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Pokud se chcete připojit ke sdíleným složkám, na svém klientovi Windows Serveru připojeném k vaší službě Data Box Gateway proveďte následující kroky.


1. Otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po zobrazení výzvy zadejte heslo ke sdílené složce. Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Klikněte na **OK**. Tím se otevře Průzkumník souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte příslušnou složku a dvakrát na ni klikněte.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

Na svém klientovi Linuxu připojeném k vašemu Data Boxu Edge proveďte následující kroky.

1. Ujistěte se, že je na klientovi nainstalovaný klient NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS, kterou jste vytvořili, ke svému zařízení Data Box Gateway:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Před nastavením připojení se ujistěte, že adresáře, které budou fungovat jako přípojné body na vašem místním počítači, už jsou vytvořené a neobsahují žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Gateway přes systém souborů NFS. IP adresa virtuálního zařízení je `10.10.10.60`, sdílená složka `mylinuxshare2` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Pro verzi Preview platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce se nepodporuje přejmenování souboru. 
> - Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


V dalším kurzu se dozvíte, jak službu Data Box Gateway spravovat.

> [!div class="nextstepaction"]
> [Správa služby Data Box Gateway pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


