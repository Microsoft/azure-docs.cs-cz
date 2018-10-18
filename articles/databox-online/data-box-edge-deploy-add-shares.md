---
title: Přenos dat pomocí Azure Data Boxu Edge | Microsoft Docs
description: Zjistěte, jak do zařízení Data Box Edge přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 61d427c22f2ac57627ac04a91748e4e6cf8c4e55
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165352"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Kurz: Přenos dat pomocí Azure Data Boxu Edge (Preview)

Tento kurz popisuje, jak do Data Boxu Edge přidat sdílené složky a připojit se k nim. Po přidání sdílených složek může zařízení Data Box Edge přenést data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do Data Boxu Edge se ujistěte, že platí následující:

* Nainstalovali jste fyzické zařízení, jak je podrobně popsáno v tématu [Instalace Data Boxu Edge](data-box-edge-deploy-install.md). 

    Fyzické zařízení je aktivované, jak je popsáno v tématu [Připojení a aktivace Azure Data Boxu Edge](data-box-edge-deploy-connect-setup-activate.md). Zařízení je připravené pro vytvoření sdílených složek a přenos dat.


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete vytvořit sdílenou složku, proveďte na webu [Azure Portal](https://portal.azure.com/) následující kroky.

1. Vraťte se na Azure Portal. Přejděte do části **Všechny prostředky** a vyhledejte váš prostředek Data Boxu Edge.
    
2. Ve vyfiltrovaném seznamu prostředků vyberte prostředek Data Boxu Edge a přejděte do části **Přehled**. Na panelu příkazů zařízení klikněte na **+ Přidat sdílenou složku**.
   
   ![Přidání sdílené složky](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky. 

   Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.
    
    1. Vyberte **Typ** sdílené složky. Typ může být SMB nebo NFS, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. 

    2. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti. 

    3. Musíte zadat účet úložiště, ve kterém se sdílená složka bude nacházet. V účtu úložiště se vytvoří kontejner s názvem sdílené složky, pokud ještě neexistuje. Pokud už kontejner existuje, použije se existující kontejner. 
    
    4. V části **Služba úložiště** zvolte Objekt blob bloku, Objekt blob stránky nebo Soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. V tomto případě chceme, aby se ukládala jako objekty blob bloku v Azure, proto vybereme Objekt blob bloku. Pokud zvolíte Objekt blob stránky, musíte zajistit, aby vaše data byla zarovnaná na 512 bajtů. Například formát VHDX je vždy zarovnaný na 512 bajtů.
   
    5. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS. 
     
        - **Pokud vytváříte sdílenou složku SMB** – V poli Místní uživatel se všemi oprávněními zvolte **Vytvořit nový** nebo **Použít existující**. Pokud vytváříte nového místního uživatele, zadejte **uživatelské jméno**, **heslo** a pak **potvrďte heslo**. Tím se místnímu uživateli přiřadí oprávnění. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.

            Pokud pro data této sdílené složky zaškrtnete možnost **Povolit jen operace čtení**, budete mít možnost určit uživatele jen pro čtení.

            ![Přidání sdílené složky SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **Pokud vytváříte sdílenou složku systému souborů NFS** – Musíte zadat IP adresy klientů s povoleným přístupem ke sdílené složce.

            ![Přidání sdílené složky systému souborů NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Kliknutím na **Vytvořit** vytvořte sdílenou složku. 
    
    Zobrazí se oznámení o probíhajícím vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka. 
    
    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Teď se můžete připojit k jedné nebo několik sdíleným složkám, které jste vytvořili v předchozím kroku. Postup se může lišit v závislosti na tom, jestli máte sdílenou složku SMB nebo sdílenou složku systému souborů NFS. 

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

Pokud se chcete připojit ke sdíleným složkám, na klientovi Windows Serveru připojeném k vašemu Data Boxu Edge proveďte následující kroky.


1. Otevřete příkazové okno. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po zobrazení výzvy zadejte heslo ke sdílené složce. Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Klikněte na **OK**. Tím se otevře Průzkumník souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte příslušnou složku a dvakrát na ni klikněte.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

Na klientovi Linuxu připojeném k vašemu Data Boxu Edge proveďte následující kroky.

1. Ujistěte se, že je na klientovi nainstalovaný klient NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS, kterou jste vytvořili, ke svému zařízení Data Box Edge:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Před připojením sdílených složek se ujistěte, že už jsou vytvořené adresáře, které budou představovat přípojné body na místním počítači. Tyto adresáře nesmí obsahovat žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Data Box Edge přes systém souborů NFS. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Pro verzi Preview platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce se nepodporuje přejmenování souboru. 
> - Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


V dalším kurzu se dozvíte, jak pomocí Data Boxu Edge transformovat data.

> [!div class="nextstepaction"]
> [Transformace dat pomocí Data Boxu Edge](./data-box-edge-deploy-configure-compute.md)


