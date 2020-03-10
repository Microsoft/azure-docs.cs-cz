---
title: Kurz přenosu dat do sdílených složek pomocí Azure Data Box Edge | Microsoft Docs
description: Zjistěte, jak do zařízení Data Box Edge přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7a15db6bbbcd9dfd43b025b780fda5a8b1d79da2
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78946147"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Kurz: přenos dat pomocí Azure Data Box Edge

V tomto kurzu se dozvíte, jak přidat sdílené složky do zařízení Data Box Edge a připojit se k nim. Po přidání sdílených složek můžou Data Box Edge přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

 
## <a name="prerequisites"></a>Předpoklady

Než přidáte sdílené složky do Data Box Edge, ujistěte se, že:

- Nainstalovali jste fyzické zařízení, jak je popsáno v tématu [Install Azure Data box Edge](data-box-edge-deploy-install.md).

- Aktivovali jste fyzické zařízení, jak je popsáno v tématu [připojení, nastavení a aktivace Azure Data box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku, proveďte následující postup:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj prostředek data box Edge a pak přejít na **Přehled**. Vaše zařízení by mělo být online.

   ![Online zařízení](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Na panelu příkazů zařízení vyberte **+ Přidat sdílenou složku** .

   ![Přidání sdílené složky](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. V podokně **Přidat sdílenou složku** proveďte následující postup:

    a. Do pole **název** zadejte jedinečný název pro sdílenou složku.  
    Název sdílené složky může obsahovat jenom malá písmena, číslice a spojovníky. Musí mít 3 až 63 znaků a nesmí začínat písmenem nebo číslicí. Spojovníky musí předcházet znak a následovaný písmenem nebo číslicí.
    
    b. Vyberte **Typ** sdílené složky.  
    Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu.  
    V závislosti na tom, jestli zvolíte sdílené složky SMB nebo NFS, se ostatní možnosti mírně liší. 

    c. Zadejte účet úložiště, ve kterém se sdílená složka bude nacházet. 

    
    d. V rozevíracím seznamu **služba úložiště** vyberte objekty **blob bloku**, objekty **blob stránky**nebo **soubory**.  
    Typ služby, kterou zvolíte, závisí na tom, který formát má data používat v Azure. Protože v tomto příkladu chceme ukládat data jako objekty blob bloku v Azure, vybíráme **objekt blob bloku**. Pokud vyberete **objekt blob stránky**, ujistěte se, že jsou data v 512 bajtů. Formát VHDX je například vždy zarovnaný na 512 bajtů.

    e. Vytvořte nový kontejner objektů BLOB nebo použijte existující objekt v rozevíracím seznamu. Pokud vytváříte kontejner objektů blob, zadejte název kontejneru. Pokud kontejner ještě neexistuje, vytvoří se v účtu úložiště s nově vytvořeným názvem sdílené složky.
   
    f. V závislosti na tom, jestli jste vytvořili sdílenou složku SMB nebo sdílenou složku NFS, proveďte jeden z následujících kroků: 
     
    - **Sdílená složka SMB**: v části **všechny oprávnění místního uživatele**vyberte **vytvořit novou** nebo **použít existující**. Pokud vytvoříte nového místního uživatele, zadejte uživatelské jméno a heslo a potvrďte heslo. Tato akce přiřadí oprávnění místnímu uživateli. Úprava oprávnění na úrovni sdílené složky se v tuto chvíli nepodporuje.

        Pokud pro tato sdílená data zaškrtnete políčko **povoleno pouze čtení operací** , můžete zadat uživatele jen pro čtení.

        ![Přidání sdílené složky SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Sdílená složka NFS**: zadejte IP adresy povolených klientů, kteří mají přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Vyberte **vytvořit** a vytvořte sdílenou složku.
    
    Zobrazí se oznámení o tom, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se **sdílené** složky budou aktualizovat, aby odrážely novou sdílenou složku.
    

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Nyní se můžete připojit k jedné nebo více sdíleným složkám, které jste vytvořili v posledním kroku. Postup se může lišit v závislosti na tom, jestli máte sdílenou složku SMB nebo NFS.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientovi Windows serveru připojeném k vašemu zařízení Data Box Edge se připojte ke sdílené složce SMB zadáním příkazů:


1. V příkazovém okně zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Po zobrazení výzvy zadejte heslo pro sdílenou složku.  
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

4. V okně **Spustit** zadejte `\\<device IP address>`a pak vyberte **OK**.  
   Otevře se Průzkumník souborů. Nyní byste měli mít přehled o sdílených složkách, které jste vytvořili jako složky. V Průzkumníku souborů poklikejte na sdílenou složku (složku) a zobrazte obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

V klientském počítači se systémem Linux připojeném k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má nainstalovaného klienta názvů NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po instalaci klienta NFS připojte sdílenou složku NFS, kterou jste vytvořili na zařízení Data Box Edge, pomocí následujícího příkazu:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Použití možnosti `sync` při připojování sdílených složek vylepšuje přenosové rychlosti velkých souborů.
    > Než sdílenou složku připojíte, ujistěte se, že jsou již vytvořeny adresáře, které budou fungovat jako mountpoints na místním počítači. Tyto adresáře by neměly obsahovat žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Data Box Edge přes systém souborů NFS. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> V této verzi se vztahují následující omezení:
> - Po vytvoření souboru ve sdílené složce se přejmenování souboru nepodporuje. 
> - Odstranění souboru ze sdílené složky neodstraní položku v účtu úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o následujících tématech Data Box Edge:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

Pokud se chcete dozvědět, jak transformovat data pomocí Data Box Edge, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Data Boxu Edge](./data-box-edge-deploy-configure-compute.md)


