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
ms.openlocfilehash: 6c6553ace250aa9cbc06dfdfea77fc5e1637cd41
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384815"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Kurz: Přenos dat pomocí Azure Data Box Edge (preview)

Tento kurz popisuje, jak přidat a připojte se ke sdíleným složkám na vašem zařízení Data Box Edge. Po přidání sdílených složek na okraji pole Data můžou přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Než pořadí a nasazení tohohle řešení, projděte si [podmínkami pro verzi preview služby Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Požadavky

Abyste mohli přidat sdílené složky do hraničních zařízení Data Box, ujistěte se, že:
* Fyzické zařízení jste nainstalovali, jak je popsáno v [nainstalovat Azure Data Box Edge](data-box-edge-deploy-install.md). 

* Po dokončení aktivace fyzického zařízení, jak je popsáno v [připojení, nastavení a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). 

* Zařízení je připravené pro vytvoření sdílených složek a přenos dat.


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete vytvořit sdílenou složku, proveďte následující postup:

1. V [webu Azure portal](https://portal.azure.com/), přejděte na stránku **všechny prostředky**a poté vyhledejte okraj pole datového prostředku.
    
1. Ve filtrovaném seznamu prostředků vyberte prostředek Data Box Edge.

1. V levém podokně vyberte **přehled**a pak vyberte **přidat sdílenou složku**.
   
   ![Přidání sdílené složky](./media/data-box-edge-deploy-add-shares/click-add-share.png)

1. V **přidat sdílenou složku** podokno, proveďte následující postup:

    a. V **název** zadejte jedinečný název pro svou sdílenou složku.  
    Název sdílené složky může mít jenom malá písmena, číslice a pomlčky. Musí mít délku 3 až 63 znaků a začínat písmenem nebo číslo. Pomlčky musí být před a následované písmenem nebo číslo.
    
    b. Vyberte **Typ** sdílené složky.  
    Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu.  
    V závislosti na tom, zda zvolíte, že sdílené složky SMB nebo NFS, zbývající možnosti mírně lišit. 

    c. Zadejte účet úložiště, kam se budou ukládat sdílenou složku.  
    Pokud kontejner neexistuje, vytvoří se v účtu úložiště s názvem nově vytvořené sdílené složky. Pokud kontejner již existuje, použije se tento kontejner. 
    
    d. V **službu Storage** rozevíracího seznamu vyberte **objektů Blob bloku**, **objektů Blob stránky**, nebo **soubory**.  
    Typ služby, kterou jste vybrali závisí na to, jaký formát chtějí dat pro použití v Azure. V tomto příkladu, protože chceme, aby k ukládání dat jako objektů blob bloků v Azure, vybereme **objektů Blob bloku**. Pokud jste vybrali objekty Blob stránky, ujistěte se, že vaše data jsou 512 bajtů zarovnána. Například formát VHDX je vždy zarovnaný na 512 bajtů.
   
    e. V závislosti na tom, zda jste vytvořili sdílenou složku protokolu SMB nebo sdílené složky NFS proveďte jednu z následujících kroků: 
     
    - **Sdílená složka SMB**: V části **všechny místní uživatel s oprávněním**vyberte **vytvořit nový** nebo **použít existující**. Pokud vytváříte novou místní uživatele, zadejte uživatelské jméno a heslo a potvrďte heslo. Tato akce přiřadí oprávnění na místního uživatele. Po přiřazení oprávnění tady, můžete je upravit Průzkumníka souborů.

        Pokud vyberete **Povolit jenom operace čtení** zaškrtávací políčko pro tato data sdílené složky můžete zadat jen pro čtení uživatelů.

        ![Přidání sdílené složky SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Sdílených složek NFS**: Zadejte IP adresy povolené klientů, které můžete přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
1. Vyberte **vytvořit** sdílenou složku vytvořit. 
    
    Zobrazí upozornění, že vytvoření sdílené složky se v průběhu. Po vytvoření sdílené složky se zadaným nastavením **sdílené složky** oddílu se aktualizuje novými informacemi sdílené složky. 
    
    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Teď můžete připojit k jednomu nebo více sdílených složek, které jste vytvořili v předchozím kroku. V závislosti na tom, jestli máte SMB nebo sdílené složky NFS, postup se může lišit. 

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientu Windows Server připojený do vašeho zařízení Data Box Edge připojení k serveru SMB pro sdílení zadáním příkazů:


1. V příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

1. Po zobrazení výzvy k tomu, zadejte heslo pro sdílenou složku.  
   Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 Microsoft Corporation. All rights reserved. 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


1. Na klávesnici stiskněte Windows + r 

1. V **spustit** okno, zadejte `\\<device IP address>`a pak vyberte **OK**.  
   Otevře se Průzkumník souborů. Nyní by se zobrazit sdílené složky, které jste vytvořili jako složky. V Průzkumníku souborů dvakrát klikněte na sdílenou složku (složku) a zobrazit obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

U svého klienta systému Linux, připojení k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má názvů NFSv4 nainstalovaného klienta. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

1. Po instalaci klienta systému souborů NFS, připojte sdílenou složku systému souborů NFS, který jste vytvořili v zařízení Data Box Edge pomocí následujícího příkazu:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Než připojíte sdílenou složku, ujistěte se, že budou vytvořeny již adresáře, které bude fungovat jako přípojné body na místním počítači. Tyto adresáře by neměly obsahovat jakékoli soubory nebo podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Data Box Edge přes systém souborů NFS. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Pro verzi Preview platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce přejmenování souboru se nepodporuje. 
> - Při odstranění souboru ze sdílené složky nedojde k odstranění položky v účtu úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o následujících tématech okraj pole dat:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

Informace o transformaci dat pomocí okraj pole dat, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Data Boxu Edge](./data-box-edge-deploy-configure-compute.md)


