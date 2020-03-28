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
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79213560"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Kurz: Přenos dat pomocí brány datové schránky Azure


## <a name="introduction"></a>Úvod

Tento článek popisuje, jak přidat sdílené složky a připojit se k ní v bráně datové schránky. Po přidání sdílených složek může zařízení Data Box Gateway přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přidání sdílené složky
> * Připojení ke sdílené složce

## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do služby Data Box Gateway se ujistěte, že:

- Zřídíte virtuální zařízení a připojíte k němu, jak je podrobně uvedeno v [části Zřídit bránu datové schránky v technologii Hyper-V](data-box-gateway-deploy-provision-hyperv.md) nebo [Zřídit bránu datové schránky ve společnosti VMware](data-box-gateway-deploy-provision-vmware.md).

- Aktivovali jste virtuální zařízení popsané v [části Připojit a aktivovali jste bránu Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Zařízení je připravené pro vytvoření sdílených složek a přenos dat.

## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku, proveďte následující postup:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte prostředek brány datové schránky a přejděte na **Přehled**. Vaše zařízení by mělo být online. Na panelu příkazů zařízení vyberte **+ Přidat sdílenou** složku.
   
   ![Přidání sdílené složky](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. V **části Přidat sdílenou**složku proveďte následující postup:

    1. Zadejte jedinečný název sdílené složky. Názvy sdílení mohou mít pouze malá písmena, čísla a pomlčky. Název sdílené položky musí mít dlouhé 3 až 63 znaků a musí začínat písmenem nebo číslem. Před i za každým spojovníkem musí být jiný znak než spojovník.
    
    2. Vyberte **Typ** sdílené složky. Typ může být SMB nebo NFS, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

    3. Zadejte účet úložiště, ve kterém bude sdílená stránka umístěna. Pokud kontejner ještě neexistuje, vytvoří se v účtu úložiště s nově vytvořeným názvem sdílené položky. Pokud kontejner již existuje, tento kontejner se používá.
       > [!IMPORTANT]
       > Ujistěte se, že účet Azure Storage, který používáte, nemá zásady neměnnosti, pokud ho používáte se zařízením Azure Stack Edge nebo Data Box Gateway. Další informace najdete v tématu [Nastavení a správa zásad neměnnosti pro úložiště objektů blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. V části **Služba úložiště** zvolte Objekt blob bloku, Objekt blob stránky nebo Soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme data v Azure ukládat jako objekty blob bloku, proto vybereme Objekt blob bloku. Pokud zvolíte Objekt blob stránky, musíte zajistit, aby vaše data byla zarovnaná na 512 bajtů. Například formát VHDX je vždy zarovnaný na 512 bajtů.
   
    5. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
     
    - **Sdílená složky SMB** – v části **Všechna oprávnění místního uživatele**vyberte vytvořit **nový** nebo **použít existující**. Pokud vytvoříte nového místního uživatele, zadejte **uživatelské jméno** a **heslo**a **potvrďte heslo**. Tato akce přiřadí oprávnění místnímu uživateli. Úprava oprávnění na úrovni sdílené složky není aktuálně podporována.
    
        ![Přidání sdílené složky SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Pokud pro tato data sdílení vyberete políčko **Povolit pouze operace čtení,** můžete určit uživatele jen pro čtení.
        
    - **Sdílená složky služby NFS** – Zadejte IP adresy povolených klientů, kteří mají ke sdílené položce přístup.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Chcete-li vytvořit sdílenou složku, vyberte **Vytvořit.**
    
    Budete upozorněni, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se se zadaným nastavením dlaždice **Sdílené složky** aktualizuje tak, aby odrážela novou sdílenou složku.
    
    ![Dlaždice Aktualizované sdílené složky](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Nyní se můžete připojit k jedné nebo více sdílených složek, které jste vytvořili v posledním kroku. V závislosti na tom, zda máte sdílenou složku SMB nebo nfs, se kroky mohou lišit.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientovi Windows Serveru připojeném k bráně datové schránky se připojte ke sdílené složce SMB zadáním příkazů:


1. V příkazovém okně zadejte:

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


2. Na klávesnici vyberte Windows + R. 
3. V okně **Spustit** zadejte `\\<device IP address>` a pak vyberte **OK**. Otevře se Průzkumník souborů. Nyní byste měli být schopni zobrazit sdílené složky, které jste vytvořili jako složky. V Průzkumníkovi souborů poklikejte na sdílenou složku (složku) a zobrazte obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

Na linuxovém klientovi připojeném k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má nainstalovaný klient NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS, kterou jste vytvořili, ke svému zařízení Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Před nastavením připojení se ujistěte, že adresáře, které budou fungovat jako přípojné body na vašem místním počítači, už jsou vytvořené a neobsahují žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Gateway přes systém souborů NFS. IP adresa virtuálního zařízení je `10.10.10.60`, sdílená složka `mylinuxshare2` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Pro tuto verzi platí následující upozornění:
> - Po vytvoření souboru ve sdílených složek není přejmenování souboru podporováno.
> - Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
> - Pokud `rsync` používáte kopírování `rsync -a` dat, pak možnost není podporována.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


V dalším kurzu se dozvíte, jak službu Data Box Gateway spravovat.

> [!div class="nextstepaction"]
> [Správa služby Data Box Gateway pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


