---
title: Správa sdílených GPU pro Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak použít Azure Portal ke správě sdílených složek v GPU pro procesor Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 413a93a145ae063a3aab4066ed62365e154d744a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454113"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Použití Azure Portal ke správě sdílených složek na Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat sdílené složky na Azure Stack Edge pro. Azure Stack Edge pro můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. Pomocí Azure Portal můžete přidat, odstranit, aktualizovat sdílené složky nebo synchronizovat klíč úložiště pro účet úložiště, který je přidružený ke sdíleným složkám.

## <a name="about-shares"></a>Informace o sdílených složkách

Pokud chcete přenést data do Azure, musíte vytvořit sdílené složky na Azure Stack Edge pro. Sdílené složky, které přidáte na zařízení Azure Stack Edge pro, můžou být místní sdílené složky nebo sdílené složky, které zadávají data do cloudu.

 - **Místní sdílené složky**: tyto sdílené složky použijte, pokud chcete, aby se data zpracovala místně na zařízení.
 - **Sdílené složky**: tyto sdílené složky použijte, pokud chcete, aby se data zařízení automaticky odeslala do vašeho účtu úložiště v cloudu. Všechny cloudové funkce, jako jsou například **aktualizace** a **synchronizace klíčů úložiště** , se vztahují ke sdíleným složkám.


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete sdílenou složku vytvořit, proveďte na webu Azure Portal následující kroky.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **brána > sdílené složky**. Na panelu příkazů vyberte **+ Přidat sdílenou složku** .

    ![Vyberte přidat sdílenou složku](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí hodnota je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

4. Je nutné zadat **účet úložiště**, ve kterém se bude sdílená složka nacházet. V účtu úložiště se vytvoří kontejner s názvem sdílené složky, pokud ještě neexistuje. Pokud už kontejner existuje, použije se existující kontejner.

5. V rozevíracím seznamu vyberte **službu úložiště** z objektu blob bloku, objektu blob stránky nebo souborů. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v této instanci chceme, aby se data nacházela jako objekty blob bloku v Azure, a proto vybereme možnost **objekt blob bloku**. Pokud zvolíte **objekt blob stránky**, musíte zajistit, aby vaše data byla 512 bajtů. Použijte **objekt blob stránky** pro VHD nebo VHDX, který je vždycky zarovnaný 512 bajtů.

6. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
    - **Pokud vytváříte sdílenou složku SMB** – v poli **Místní uživatel se všemi oprávněními** zvolte **Vytvořit nový** nebo **Použít existující**. Pokud vytváříte nového místního uživatele, zadejte **uživatelské jméno**, **heslo** a pak potvrďte heslo. Tím se místnímu uživateli přiřadí oprávnění. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.

        ![Přidání sdílené složky SMB](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        Pokud pro data této sdílené složky zaškrtnete možnost Povolit jen operace čtení, můžete určit uživatele jen pro čtení.
    - **Pokud vytváříte sdílenou složku systému souborů NFS** – je nutné zadat **IP adresy klientů s povoleným přístupem** ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. K usnadnění přístupu ke sdíleným složkám z hraničních výpočetních modulů použijte místní přípojný bod. Vyberte možnost **použít sdílenou položku s hraničním** výpočtem, aby se sdílená složka po vytvoření automaticky připojila. Je-li vybrána tato možnost, může modul Edge použít také výpočty s místním přípojným bodem.

8. Kliknutím na **vytvořit** vytvořte sdílenou složku. Zobrazí se oznámení o probíhajícím vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **sdílené složky** aktualizuje a projeví se nová sdílená složka.

## <a name="add-a-local-share"></a>Přidat místní sdílenou složku

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **brána > sdílené složky**. Na panelu příkazů vyberte **+ Přidat sdílenou složku** .

    ![Vyberte přidat sdílenou složku 2.](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat jenom čísla, malá a velká písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí hodnota je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

   > [!IMPORTANT]
   > Ujistěte se, že účet Azure Storage, který používáte, nemá nastavené zásady neměnnosti, pokud ho používáte se zařízením Azure Stack Edge pro nebo Data Box Gateway. Další informace najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. K usnadnění přístupu ke sdíleným složkám z hraničních výpočetních modulů použijte místní přípojný bod. Vyberte možnost **použít sdílenou položku s** výpočtem hran, aby modul Edge mohl používat výpočty s místním přípojným bodem.

5. Vyberte **Konfigurovat jako hraniční místní sdílení**. Data v místních sdílených složkách zůstanou v zařízení místně. Tato data můžete zpracovat místně.

6. V poli **místní uživatel všechna oprávnění** vyberte možnost **vytvořit novou** nebo **použít existující**.

7. Vyberte **Vytvořit**. 

    ![Vytvořit místní sdílenou složku](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    Zobrazí se oznámení o tom, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **sdílené složky** aktualizuje a projeví se nová sdílená složka.

    ![Okno zobrazit aktualizace pro sdílené složky](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    Vyberte sdílenou složku pro zobrazení místních přípojný bod pro výpočetní moduly hraničních zařízení pro tuto sdílenou složku.

    ![Zobrazit podrobnosti o místní sdílené složce](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Připojení sdílené složky

Pokud jste vytvořili sdílenou složku před tím, než jste nakonfigurovali výpočetní výkon na zařízení Azure Stack Edge pro, budete muset sdílenou složku připojit. Pro připojení sdílené složky proveďte následující kroky.


1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **brána > sdílené složky**. V seznamu sdílených složek vyberte sdílenou složku, kterou chcete připojit. Ve sloupci **použito pro výpočty** se zobrazí stav **zakázáno** pro vybranou sdílenou složku.

    ![Vyberte sdílenou složku](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. Vyberte **připojit**.

    ![Vybrat připojení](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. Po zobrazení výzvy k potvrzení vyberte **Ano**. Tím se sdílená složka připojí.

    ![Potvrdit připojení](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. Po připojení sdílené složky přejdete do seznamu sdílených složek. Uvidíte, že se ve sloupci **použito pro výpočetní** prostředky zobrazuje stav sdílení jako **povolený**.

    ![Sdílet připojené](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. Pokud chcete zobrazit místní přípojný bod pro sdílenou složku, vyberte ji znovu. Modul COMPUTE pro Edge používá pro sdílenou složku Tento místní přípojný bod.

    ![Místní přípojný bod pro sdílenou složku](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Odpojení sdílené složky

Chcete-li odpojit sdílenou složku, proveďte následující kroky v Azure Portal.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **brána > sdílené složky**.

    ![Vybrat sdílenou složku 2](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2. V seznamu sdílených složek vyberte sdílenou složku, kterou chcete odpojit. Chcete se ujistit, že se sdílená složka nepoužívá v žádných modulech. Pokud je sdílená složka používána modulem, zobrazí se problémy s odpovídajícím modulem. Vyberte **Odpojit**.

    ![Vybrat odpojit](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. Po zobrazení výzvy k potvrzení vyberte **Ano**. Tím se sdílená složka odpojí.

    ![Potvrdit odpojení](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. Až bude sdílená složka odpojená, otevřete seznam sdílených složek. Uvidíte, že se **používá pro výpočetní** sloupec zobrazuje stav sdílení jako **zakázaný**.

    ![Sdílet nepřipojené](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Odstranění sdílené složky

Pokud chcete sdílenou složku odstranit, proveďte na webu Azure Portal následující kroky.

1. V seznamu sdílených složek vyberte a klikněte na sdílenou složku, kterou chcete odstranit.

    ![Vybrat sdílenou složku 3](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. Klikněte na **Odstranit**.

    ![Kliknutím odstraňte](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

Seznam sdílených složek aktualizuje, aby odrážel odstranění.

## <a name="refresh-shares"></a>Aktualizace sdílených složek

Funkce aktualizovat umožňuje aktualizovat obsah sdílené složky. Když aktualizujete sdílenou složku, spustí se vyhledávání, které najde všechny objekty Azure včetně objektů blob a souborů, které byly do cloudu přidány od poslední aktualizace. Po stažení těchto dalších souborů dojde k aktualizaci obsahu sdílené složky na zařízení.

> [!IMPORTANT]
> - Nemůžete aktualizovat místní sdílené složky.
> - Oprávnění a seznamy řízení přístupu (ACL) nejsou zachovány v rámci operace aktualizace. 

Pokud chcete sdílenou složku aktualizovat, proveďte na webu Azure Portal následující kroky.

1.  Na webu Azure Portal přejděte na **Sdílené složky**. Vyberte a klikněte na sdílenou složku, kterou chcete aktualizovat.

    ![Vybrat sdílenou složku 4](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  Klikněte na **Aktualizovat**. 

    ![Kliknutím aktualizujte](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  Po zobrazení výzvy k potvrzení klikněte na **Ano**. Spustí se úloha, která zaktualizuje obsah místní sdílené složky.

    ![Potvrďte aktualizaci](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   Když aktualizace probíhá, bude možnost aktualizace v místní nabídce neaktivní. Kliknutím na oznámení úlohy zobrazte stav úlohy aktualizace.

5.   Doba aktualizace závisí na počtu souborů v kontejneru Azure a také na souborech v zařízení. Po úspěšném dokončení aktualizace se zaktualizuje časové razítko sdílené složky. Operace se považuje za úspěšnou i v případě částečně neúspěšné aktualizace a časové razítko zaktualizuje. Aktualizují se také protokoly chyb při aktualizaci.

![Aktualizované časové razítko](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
Pokud dojde k selhání, bude vydána výstraha. Výstraha uvede podrobnosti příčiny a doporučení k vyřešení problému. Výstraha obsahuje také odkazy na soubor s úplným souhrnem chyb, včetně souborů, které se nepovedlo aktualizovat nebo odstranit.

## <a name="sync-pinned-files"></a>Synchronizovat připojené soubory 

K automatické synchronizaci připnutých souborů proveďte následující kroky v Azure Portal:
 
1. Vyberte existující účet úložiště Azure. 

2. Pokud chcete vytvořit kontejner, klikněte na **kontejnery** a vyberte **+ kontejner** . Pojmenujte tento kontejner jako *newcontainer*. Nastavte **úroveň veřejného přístupu** na Container.

    ![Automatizovaná synchronizace pro připnuté soubory 1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. Vyberte název kontejneru a nastavte následující metadata:  

    - Name = "připnuté" 
    - Value = "true" 

    ![Automatizovaná synchronizace pro připnutý soubor 2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. Vytvořte na svém zařízení novou sdílenou složku. Namapujte ho k připnutému kontejneru výběrem možnosti existující kontejner. Označí sdílenou složku jako jen pro čtení. Vytvořte nového uživatele a zadejte uživatelské jméno a odpovídající heslo pro tuto sdílenou složku.  

    ![Automatizovaná synchronizace připnutých souborů 3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. Z Azure Portal přejděte do kontejneru, který jste vytvořili. Nahrajte soubor, který chcete připnout do newcontaineru s metadaty nastavenými na připnutý. 

6. Vyberte možnost **aktualizovat data** v Azure Portal, aby zařízení stahoval zásady připnutí pro daný kontejner Azure Storage.  

    ![Automatizovaná synchronizace pro připnuté soubory 4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. Přihlaste se k nové sdílené složce, která byla vytvořena v zařízení. Soubor, který se nahrál do účtu úložiště, se teď stáhne do místní sdílené složky. 

    Kdykoli je zařízení odpojené nebo znovu připojeno, aktivuje se aktualizace. Aktualizace zobrazí pouze soubory, které se změnily. 


## <a name="sync-storage-keys"></a>Synchronizace klíčů úložiště

Pokud se klíče k vašemu účtu úložiště obměňovaly, bude potřeba provést synchronizaci přístupových klíčů k úložišti. Synchronizace pomáhá zařízení se získáním nejnovějších klíčů k účtu úložiště.

Pokud chcete synchronizovat přístupový klíč úložiště, proveďte na webu Azure Portal následující kroky.

1. Ve svém prostředku přejděte na **Přehled**. V seznamu sdílených složek zvolte a klikněte na sdílenou složku, která je přidružená k účtu úložiště, které potřebujete synchronizovat.

    ![Vyberte sdílet s relevantním účtem úložiště.](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. Klikněte na **Synchronizovat klíč úložiště**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Vybrat synchronizační klíč úložiště](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

>[!NOTE]
> U každého účtu úložiště to stačí provést jednou. Není nutné tento postup opakovat u každé sdílené složky, která je přidružená ke stejnému účtu úložiště.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](azure-stack-edge-j-series-manage-users.md).