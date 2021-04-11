---
title: Kurz přenosu dat do sdílených složek s grafickým procesorem Azure Stack Edge pro | Microsoft Docs
description: Přečtěte si, jak přidat sdílené složky a připojit se k nim na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 26a2be6d2752393362b6b4f9a666dd0b74e64321
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102663250"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Kurz: přenos dat prostřednictvím sdílených složek s grafickým procesorem Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

V tomto kurzu se dozvíte, jak přidat sdílené složky a připojit se k nim na zařízení Azure Stack Edge pro. Po přidání sdílených složek může Azure Stack Edge pro přenést data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přidání sdílené složky
> * Připojení ke sdílené složce

## <a name="prerequisites"></a>Požadavky

Než přidáte sdílené složky do Azure Stack Edge pro, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v tématu [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).

* Aktivovali jste fyzické zařízení, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).

## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku, proveďte následující postup:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj prostředek Azure Stack Edge a pak přejít na **Přehled**. Vaše zařízení by mělo být online. Vyberte možnost **Brána cloudového úložiště**.

   ![Online zařízení](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. Na panelu příkazů zařízení vyberte **+ Přidat sdílenou složku** .

   ![Přidání sdílené složky](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. V podokně **Přidat sdílenou složku** proveďte následující kroky:

    a. Do pole **název** zadejte jedinečný název pro sdílenou složku.  
    Název sdílené složky může obsahovat jenom písmena, číslice a spojovníky. Musí mít 3 až 63 znaků a nesmí začínat písmenem nebo číslicí. Spojovníky musí předcházet znak a následovaný písmenem nebo číslicí.
    
    b. Vyberte **typ** sdílené složky.  
    Typ může být **SMB** nebo **NFS**, přičemž výchozí hodnota je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu.  
    V závislosti na tom, jestli zvolíte sdílené složky SMB nebo NFS, se ostatní možnosti mírně liší. 

    c. Zadejte účet úložiště, ve kterém se sdílená složka bude nacházet.

    d. V rozevíracím seznamu **služba úložiště** vyberte objekty **blob bloku**, objekty **blob stránky** nebo **soubory**.  
    Typ služby, kterou zvolíte, závisí na tom, který formát má data používat v Azure. Protože v tomto příkladu chceme ukládat data jako objekty blob bloku v Azure, vybíráme **objekt blob bloku**. Pokud vyberete **objekt blob stránky**, ujistěte se, že jsou data v 512 bajtů. Například formát VHDX je vždy zarovnaný na 512 bajtů.

   > [!IMPORTANT]
   > Ujistěte se, že účet Azure Storage, který používáte, nemá nastavené zásady neměnnosti, pokud ho používáte se zařízením Azure Stack Edge pro nebo Data Box Gateway. Další informace najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](../storage/blobs/storage-blob-immutability-policies-manage.md).

    e. Vytvořte nový kontejner objektů BLOB nebo použijte existující objekt v rozevíracím seznamu. Pokud vytváříte kontejner objektů blob, zadejte název kontejneru. Pokud ještě neexistuje, v účtu úložiště se vytvoří kontejner s názvem nové sdílené složky.
   
    f. V závislosti na tom, jestli jste vytvořili sdílenou složku SMB nebo sdílenou složku NFS, proveďte jeden z následujících kroků: 
     
    - **Sdílená složka SMB**: v části **všechny oprávnění místního uživatele** vyberte **vytvořit novou** nebo **použít existující**. Pokud vytváříte nového místního uživatele, zadejte uživatelské jméno a heslo a pak heslo potvrďte. Touto akcí se místnímu uživateli přiřadí oprávnění. Úprava oprávnění na úrovni sdílené složky se v tuto chvíli nepodporuje. Pokud pro tato sdílená data zaškrtnete políčko **povoleno pouze čtení operací** , můžete zadat uživatele jen pro čtení.
    
        ![Přidání sdílené složky SMB](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **Sdílená složka NFS**: zadejte IP adresy povolených klientů, kteří mají přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. Vyberte **vytvořit** a vytvořte sdílenou složku.
    
    Zobrazí se oznámení o tom, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se **sdílené** složky budou aktualizovat, aby odrážely novou sdílenou složku.
    

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Nyní se můžete připojit k jedné nebo více sdíleným složkám, které jste vytvořili v posledním kroku. Postup se může lišit v závislosti na tom, jestli máte sdílenou složku SMB nebo NFS.

Prvním krokem je zajistit, aby se název zařízení mohl vyřešit při použití sdílené složky SMB nebo NFS.

### <a name="modify-host-file-for-name-resolution"></a>Upravit soubor hostitele pro překlad názvů

Nyní přidáte IP adresu zařízení a popisný název zařízení, který jste definovali v místním webovém uživatelském rozhraní zařízení:

- Hostitelský soubor na klientovi, nebo,
- Hostitelský soubor na serveru DNS

> [!IMPORTANT]
> Doporučujeme, abyste na serveru DNS pro překlad názvů zařízení změnili soubor hostitele.

V klientovi se systémem Windows, který používáte pro připojení k zařízení, proveďte následující kroky:

1. Spusťte **Poznámkový blok** jako správce a pak otevřete soubor **hosts** v umístění `C:\Windows\System32\Drivers\etc` .

    ![Soubor hostitelů Průzkumníka Windows](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. Přidejte následující položku do souboru **hostitelů** nahrazením odpovídajícími hodnotami pro vaše zařízení: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    IP adresu zařízení ze **sítě** a popisný název zařízení můžete získat ze stránky **zařízení** v místním webovém uživatelském rozhraní. Následující snímek obrazovky se souborem hostitelů zobrazuje položku:

    ![Průzkumník Windows hostuje soubor 2](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientovi Windows serveru připojeném k vašemu zařízení Azure Stack Edge pro se připojte ke sdílené složce SMB zadáním příkazů:


1. V příkazovém okně zadejte: .

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > Ke sdílené složce SMB se můžete připojit pouze s názvem zařízení a nikoli prostřednictvím IP adresy zařízení.

2. Po zobrazení výzvy zadejte heslo ke sdílené složce.  
   Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. Na klávesnici vyberte Windows + R.

4. V okně **Spustit** zadejte a `\\<device name>` pak vyberte **OK**.  

    ![Dialogové okno pro spuštění systému Windows](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   Otevře se Průzkumník souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte ji v Průzkumníku souborů a dvakrát na ni klikněte.
 
    ![Připojení ke sdílené složce SMB](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

V klientském počítači se systémem Linux připojeném k zařízení Azure Stack Edge pro proveďte následující postup:

1. Ujistěte se, že je na klientovi nainstalovaný klient NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete na webu [instalace klienta názvů NFSv4](https://help.ubuntu.com/community/NFSv4Howto).

2. Po instalaci klienta NFS připojte sdílenou složku NFS, kterou jste vytvořili na zařízení Azure Stack Edge pro, pomocí následujícího příkazu:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    IP adresu zařízení můžete získat ze stránky **síť** místního webového uživatelského rozhraní.

    > [!IMPORTANT]
    > Použití `sync` Možnosti při připojování sdílených složek vylepšuje přenosové rychlosti velkých souborů.
    > Než sdílenou složku připojíte, ujistěte se, že jsou již vytvořeny adresáře, které budou fungovat jako mountpoints na místním počítači. Tyto adresáře by neměly obsahovat žádné soubory ani podsložky.

    Následující příklad ukazuje, jak se připojit přes systém souborů NFS ke sdílené složce na zařízení Azure Stack Edge pro. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> V této verzi se vztahují následující omezení:
> - Po vytvoření souboru ve sdílené složce se přejmenování souboru nepodporuje. 
> - Odstranění souboru ze sdílené složky neodstraní položku v účtu Azure Storage.
> - Při použití `rsync` ke kopírování přes systém souborů NFS použijte `--inplace` příznak. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s následujícími tématy Azure Stack Edge pro:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

Pokud se chcete dozvědět, jak transformovat data pomocí Azure Stack Edge pro, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Azure Stack Edge pro](./azure-stack-edge-j-series-deploy-configure-compute.md)