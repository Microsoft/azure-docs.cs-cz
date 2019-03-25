---
title: Přenos dat pomocí služby Azure Data Box Gateway | Microsoft Docs
description: Zjistěte, jak na zařízení Data Box Gateway přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: d930b1db48e3a5c4bda96f0b7d80a9c9f24d53d9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400640"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Kurz: Přenos dat pomocí Azure Data Box brány


## <a name="introduction"></a>Úvod

Tento článek popisuje, jak přidat a připojte se ke sdíleným složkám na bráně Data Box. Po přidání sdílených složek na zařízení Data Box brány může přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do služby Data Box Gateway se ujistěte, že:

- Jsme zřídili virtuální zařízení a jsou k němu připojená podle popisu v [bránu Data Gateway pole v Hyper-V zřídit](data-box-gateway-deploy-provision-hyperv.md) nebo [zřízení bránu Data Gateway pole v prostředí VMware](data-box-gateway-deploy-provision-vmware.md).

- Po dokončení aktivace virtuální zařízení je popsáno v [připojit a aktivovat Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Zařízení je připravené pro vytvoření sdílených složek a přenos dat.

## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku proveďte následující postup:

1. V [webu Azure portal](https://portal.azure.com/), vyberte prostředek brány dat pole a potom přejděte ke **přehled**. Vaše zařízení by měl být online. Vyberte **+ přidat sdílenou složku** na panelu příkazů zařízení.
   
   ![Přidání sdílené složky](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. V **přidat sdílenou složku**, proveďte následující postup:

    1. Zadejte jedinečný název sdílené složky. Názvy sdílených složek obsahovat pouze malá písmena, číslice a pomlčky. Název sdílené složky musí mít délku 3 až 63 znaků a musí začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.
    
    2. Vyberte **Typ** sdílené složky. Typ může být SMB nebo NFS, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

    3. Zadejte účet úložiště, kde se bude nacházet sdílenou složku. Pokud kontejner neexistuje, vytvoří se v účtu úložiště s názvem nově vytvořené sdílené složky. Pokud kontejner již existuje, použije se tento kontejner.
    
    4. V části **Služba úložiště** zvolte Objekt blob bloku, Objekt blob stránky nebo Soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme data v Azure ukládat jako objekty blob bloku, proto vybereme Objekt blob bloku. Pokud zvolíte Objekt blob stránky, musíte zajistit, aby vaše data byla zarovnaná na 512 bajtů. Například formát VHDX je vždy zarovnaný na 512 bajtů.
   
    5. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
     
    - **Sdílená složka SMB** – v části **všechny místní uživatel s oprávněním**vyberte **vytvořit nový** nebo **použít existující**. Pokud vytváříte novou místní uživatele, zadejte **uživatelské jméno** a **heslo**a potom **potvrzení hesla**. Tato akce se přiřadí oprávnění na místního uživatele. Po přiřazení oprávnění tady, můžete upravit tato oprávnění Průzkumníka souborů.
    
        ![Přidání sdílené složky SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Pokud vyberete **Povolit jenom operace čtení** zaškrtávací políčko pro tato data sdílené složky můžete zadat jen pro čtení uživatelů.
        
    - **Sdílených složek NFS** – zadejte IP adresy povolené klientů, které můžete přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Vyberte **vytvořit** sdílenou složku vytvořit.
    
    Zobrazí upozornění, že vytvoření sdílené složky se v průběhu. Po vytvoření sdílené složky se zadaným nastavením **sdílené složky** dlaždici aktualizace tak, aby odrážely novou sdílenou složku.
    
    ![Aktualizované dlaždice sdílené složky](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Teď můžete připojit k jednomu nebo více sdílených složek, které jste vytvořili v předchozím kroku. V závislosti na tom, jestli máte SMB nebo sdílené složky NFS, postup se může lišit.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientu Windows Server připojený k vaší bráně dat pole připojení k serveru SMB pro sdílení zadáním příkazů:


1. V příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po zobrazení výzvy zadejte heslo ke sdílené složce. Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Na klávesnici stiskněte Windows + r 
3. V **spustit** okno, zadejte `\\<device IP address>` a pak vyberte **OK**. Otevře se Průzkumník souborů. Nyní by se zobrazit sdílené složky, které jste vytvořili jako složky. V Průzkumníku souborů dvakrát klikněte na sdílenou složku (složku) a zobrazit obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

U svého klienta systému Linux, připojení k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má názvů NFSv4 nainstalovaného klienta. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS, kterou jste vytvořili, ke svému zařízení Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Před nastavením připojení se ujistěte, že adresáře, které budou fungovat jako přípojné body na vašem místním počítači, už jsou vytvořené a neobsahují žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Gateway přes systém souborů NFS. IP adresa virtuálního zařízení je `10.10.10.60`, sdílená složka `mylinuxshare2` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Na tuto verzi platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce se nepodporuje přejmenování souboru.
> - Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
> - Pokud používáte `rsync` ke kopírování dat, pak `rsync -a` možnost není podporována.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


V dalším kurzu se dozvíte, jak službu Data Box Gateway spravovat.

> [!div class="nextstepaction"]
> [Správa služby Data Box Gateway pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


