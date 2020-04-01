---
title: Kurz přenosu dat do sdílených složek s Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Zjistěte, jak do zařízení Data Box Edge přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b1988656e2c15515e121df3ee71e31ce7edd750
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79212948"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Kurz: Přenos dat pomocí Azure Data Box Edge

Tento kurz popisuje, jak přidat sdílené složky a připojit se k němu na zařízení Data Box Edge. Po přidání sdílených složek může Data Box Edge přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

 
## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do funkce Data Box Edge se ujistěte, že:

- Fyzické zařízení jste nainstalovali, jak je popsáno v [části Instalace Azure Data Box Edge](data-box-edge-deploy-install.md).

- Fyzické zařízení jste aktivovali, jak je popsáno v [části Připojit, nastavit a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku, proveďte následující postup:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte prostředek Data Box Edge a přejděte na **přehled**. Vaše zařízení by mělo být online.

   ![Zařízení online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Na panelu příkazů zařízení vyberte **+ Přidat sdílenou** složku.

   ![Přidání sdílené složky](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. V podokně **Přidat sdílenou** složku proveďte následující postup:

    a. Do pole **Název** zadejte jedinečný název sdílené položky.  
    Název sdílené položky může mít pouze malá písmena, číslice a pomlčky. Musí mít 3 až 63 znaků a začínat písmenem nebo číslicí. Spojovníkům musí předcházet písmeno nebo číslice.
    
    b. Vyberte **Typ** sdílené složky.  
    Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu.  
    V závislosti na tom, zda zvolíte sdílené položky SMB nebo NFS, se zbytek opcí mírně liší. 

    c. Zadejte účet úložiště, ve kterém bude sdílená stránka umístěna.

      > [!IMPORTANT]
      > Ujistěte se, že účet Azure Storage, který používáte, nemá zásady neměnnosti, pokud ho používáte se zařízením Azure Stack Edge nebo Data Box Gateway. Další informace najdete v tématu [Nastavení a správa zásad neměnnosti pro úložiště objektů blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

    d. V rozevíracím seznamu **Služba úložiště** vyberte blokovat **objekt blob**, **objekt blob stránky**nebo **soubory**.  
    Typ služby, kterou vyberete, závisí na formátu, který chcete data použít v Azure. V tomto příkladu, protože chceme ukládat data jako objekty BLOB bloku v Azure, vybereme **objekt blob bloku**. Pokud vyberete **objekt blob stránky**, ujistěte se, že vaše data jsou zarovnaná o 512 bajtů. Například formát VHDX je vždy zarovnaný na 512 bajtů.

    e. Vytvořte nový kontejner objektů blob nebo použijte existující z rozevíracího seznamu. Pokud vytváříte kontejner objektů blob, zadejte název kontejneru. Pokud kontejner ještě neexistuje, vytvoří se v účtu úložiště s nově vytvořeným názvem sdílené položky.

    f. V závislosti na tom, zda jste vytvořili sdílenou složku SMB nebo sdílenou položku služby NFS, proveďte jeden z následujících kroků:

    * **SMB share**: V části **Všechna oprávnění místního uživatele**vyberte Vytvořit **nový** nebo **Použít existující**. Pokud vytvoříte nového místního uživatele, zadejte uživatelské jméno a heslo a potvrďte heslo. Tato akce přiřazuje oprávnění místnímu uživateli. Úprava oprávnění na úrovni sdílené složky není aktuálně podporována.

        Pokud pro tato data sdílení zaškrtnete políčko **Povolit pouze operace čtení,** můžete určit uživatele jen pro čtení.

        ![Přidání sdílené složky SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)

    * **Sdílená složky služby NFS**: Zadejte adresy IP povolených klientů, kteří mají ke sdílené položce přístup.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)

4. Chcete-li vytvořit sdílenou složku, vyberte **Vytvořit.**
    
    Budete upozorněni, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se se zadaným nastavením dlaždice **Sdílené složky** aktualizuje tak, aby odrážela novou sdílenou složku.
    

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Nyní se můžete připojit k jedné nebo více sdílených složek, které jste vytvořili v posledním kroku. V závislosti na tom, zda máte sdílenou složku SMB nebo nfs, se kroky mohou lišit.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientovi Windows Serveru připojeném k zařízení Data Box Edge se připojte ke sdílené složce SMB zadáním příkazů:


1. V příkazovém okně zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Až k tomu budete vyzváni, zadejte heslo ke sdílené položce.  
   Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Na klávesnici vyberte Windows + R.

4. V okně **Spustit** zadejte položku `\\<device IP address>`a pak vyberte **OK**.  
   Otevře se Průzkumník souborů. Nyní byste měli být schopni zobrazit sdílené složky, které jste vytvořili jako složky. V Průzkumníkovi souborů poklikejte na sdílenou složku (složku) a zobrazte obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

Na linuxovém klientovi připojeném k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má nainstalovaný klient NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po instalaci klienta systému sdílení systému sdílení systému sdílení systému sdílení, které jste vytvořili na zařízení Data Box Edge, pomocí následujícího příkazu:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Použití `sync` možnosti při montáži sdílených složek zlepšuje přenosové rychlosti velkých souborů.
    > Před připojením sdílené složky se ujistěte, že adresáře, které budou fungovat jako přípojné body v místním počítači, jsou již vytvořeny. Tyto adresáře by neměly obsahovat žádné soubory nebo podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Data Box Edge přes systém souborů NFS. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Pro tuto verzi platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce není přejmenování souboru podporováno. 
> - Odstraněním souboru ze sdílené složky se položka v účtu úložiště neodstraní.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o následujících tématech Data Box Edge:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

Chcete-li se dozvědět, jak transformovat data pomocí data box edge, přejdete k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Data Boxu Edge](./data-box-edge-deploy-configure-compute.md)


