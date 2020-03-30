---
title: Správa sdílené složky Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Popisuje, jak používat portál Azure ke správě sdílených složek na Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: b25409c63806e203bd841b0373543b7cc2b96d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79212944"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Správa sdílených složek na Azure Data Box Edge pomocí portálu Azure Portal

Tento článek popisuje, jak spravovat sdílené složky na Azure Data Box Edge. Azure Data Box Edge můžete spravovat prostřednictvím portálu Azure nebo prostřednictvím místního webového uživatelského prostředí. Na webu Azure Portal můžete přidat, odstranit, aktualizovat sdílené složky nebo synchronizovat klíč úložiště pro účet úložiště přidružený ke sdíleným položkám.

## <a name="about-shares"></a>Informace o sdílených složkách

Chcete-li přenášet data do Azure, musíte vytvořit sdílené složky na Azure Data Box Edge. Sdílené složky, které přidáte do zařízení Data Box Edge, mohou být místní sdílené složky nebo sdílené složky, které vyčlí data do cloudu.

 - **Místní sdílené složky**: Tyto sdílené složky použijte, pokud chcete, aby byla data v zařízení zpracována místně.
 - **Sdílené složky**: Tyto sdílené složky použijte, pokud chcete, aby se data zařízení automaticky odesílala do vašeho účtu úložiště v cloudu. Všechny cloudové funkce, jako je **aktualizace** a **synchronizace klíčů úložiště** platí pro sdílené složky.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Odstranění sdílené složky
> * Aktualizace sdílených složek
> * Synchronizace klíče úložiště


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete sdílenou složku vytvořit, proveďte na webu Azure Portal následující kroky.

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom přejděte na **Gateway > Shares**. Vyberte **+ Přidat sdílenou položku** na panelu příkazů.

    ![Vybrat přidat sdílenou složku](media/data-box-edge-manage-shares/add-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **Typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

4. Je nutné zadat **účet úložiště**, ve kterém se bude sdílená složka nacházet. V účtu úložiště se vytvoří kontejner s názvem sdílené složky, pokud ještě neexistuje. Pokud už kontejner existuje, použije se existující kontejner.

5. V rozevíracím seznamu zvolte **službu Úložiště** z objektu blob bloku, objektu blob stránky nebo souborů. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme, aby data umístěna jako objekty BLOB bloku v Azure, proto vybereme **blok objekt blob**. Pokud zvolíte **objekt blob stránky**, musíte zajistit, aby vaše data byla zarovnána o 512 bajtů. **Objekt blob stránky** použijte pro virtuální pevné disky nebo vhdx, které jsou vždy zarovnané s 512 bajty.

   > [!IMPORTANT]
   > Ujistěte se, že účet Azure Storage, který používáte, nemá zásady neměnnosti, pokud ho používáte se zařízením Azure Stack Edge nebo Data Box Gateway. Další informace najdete v tématu [Nastavení a správa zásad neměnnosti pro úložiště objektů blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
    - **Pokud vytváříte sdílenou složku SMB** – v poli **Místní uživatel se všemi oprávněními** zvolte **Vytvořit nový** nebo **Použít existující**. Pokud vytváříte nového místního uživatele, zadejte **uživatelské jméno**, **heslo** a pak potvrďte heslo. Tím se místnímu uživateli přiřadí oprávnění. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.

        ![Přidání sdílené složky SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Pokud pro data této sdílené složky zaškrtnete možnost Povolit jen operace čtení, můžete určit uživatele jen pro čtení.
    - **Pokud vytváříte sdílenou složku systému souborů NFS** – je nutné zadat **IP adresy klientů s povoleným přístupem** ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Chcete-li snadno přistupovat ke sdíleným položkám z výpočetních modulů Edge, použijte místní přípojný bod. Vyberte **Použít sdílenou složku s výpočty Edge** tak, aby se sdílená složky po vytvoření automaticky připevnila. Je-li vybrána tato možnost, modul Edge může také použít výpočetní prostředky s místním přípojným bodem.

8. Kliknutím na **Vytvořit** vytvořte sdílenou složku. Zobrazí se oznámení o probíhajícím vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka.

## <a name="add-a-local-share"></a>Přidání místní sdílené složky

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom přejděte na **Gateway > Shares**. Vyberte **+ Přidat sdílenou položku** na panelu příkazů.

    ![Vybrat přidat sdílenou složku](media/data-box-edge-manage-shares/add-local-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **Typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

4. Chcete-li snadno přistupovat ke sdíleným položkám z výpočetních modulů Edge, použijte místní přípojný bod. Vyberte **Použít sdílenou složku s výpočetními prostředky Edge,** aby modul Edge mohl výpočetní prostředky používat s místním přípojným bodem.

5. Vyberte **Konfigurovat jako místní sdílené složky Edge**. Data v místních sdílených složek zůstanou místně v zařízení. Tato data můžete zpracovat místně.

6. V poli **Všechny oprávnění místního uživatele** zvolte možnost Vytvořit **nový** nebo **Použít existující**.

7. Vyberte **Vytvořit**. 

    ![Vytvořit místní sdílenou složku](media/data-box-edge-manage-shares/add-local-share-2.png)

    Zobrazí se oznámení, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka.

    ![Zobrazit okno Sdílení aktualizací](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Vyberte sdílenou složku, chcete-li zobrazit místní přípojný bod pro výpočetní moduly Edge pro tuto sdílenou složku.

    ![Zobrazit podrobnosti místní sdílené složky](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Připojení sdílené složky

Pokud jste sdílenou složku vytvořili před konfigurací výpočetních prostředků na zařízení Data Box Edge, budete muset sdílenou složku připojit. Chcete-li sdílenou složku připojit, postupujte takto.


1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom přejděte na **Gateway > Shares**. Ze seznamu sdílených složek vyberte sdílenou složku, kterou chcete připojit. Ve sloupci **Použít pro výpočetní výkon** se zobrazí stav **Zakázáno** pro vybranou sdílenou složku.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/select-share-mount.png)

2. Vyberte **možnost Připojit**.

    ![Vybrat připojit](media/data-box-edge-manage-shares/select-mount.png)

3. Po zobrazení výzvy k potvrzení vyberte **možnost Ano**. Tím se připojí podíl.

    ![Potvrdit připojení](media/data-box-edge-manage-shares/confirm-mount.png)

4. Po montáži sdílené složky přejděte do seznamu sdílených složek. Uvidíte, že sloupec **Použít pro výpočetní prostředky** zobrazuje stav sdílené složky jako **Povolený**.

    ![Sdílení připojeno](media/data-box-edge-manage-shares/share-mounted.png)

5. Vyberte sdílenou složku, chcete-li zobrazit místní přípojné pole pro sdílenou složku. Hraniční výpočetní modul používá tento místní přípojný bod pro sdílenou složku.

    ![Místní přípojkový bod pro sdílenou složku](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Odpojení sdílené položky

Chcete-li zrušit připojení sdílené položky, postupujte na webu Azure Portal následujícím postupem.

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom přejděte na **Gateway > Shares**.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Ze seznamu sdílených složek vyberte sdílenou složku, kterou chcete odpojit. Chcete se ujistit, že sdílená část, kterou odpojíte, není používána žádnými moduly. Pokud je sdílená sdílená sdílená část používána modulem, zobrazí se problémy s odpovídajícím modulem. Vyberte **Odpojit**.

    ![Vybrat odpojit](media/data-box-edge-manage-shares/select-unmount.png)

3. Po zobrazení výzvy k potvrzení vyberte **možnost Ano**. Tím se rozbalí podíl.

    ![Potvrzení odpojení](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Po odpojení sdílené složky přejděte do seznamu sdílených složek. Uvidíte, že **sloupec Použít pro výpočetní výkon** zobrazuje stav sdílené složky jako **Zakázaný**.

    ![Sdílení odpojeno](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Odstranění sdílené složky

Pokud chcete sdílenou složku odstranit, proveďte na webu Azure Portal následující kroky.

1. V seznamu sdílených složek vyberte a klikněte na sdílenou složku, kterou chcete odstranit.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/delete-share-1.png)

2. Klepněte na **tlačítko Odstranit**.

    ![Kliknutím odstraňte](media/data-box-edge-manage-shares/delete-share-2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/data-box-edge-manage-shares/delete-share-3.png)

Seznam sdílených složek se aktualizuje tak, aby odrážel odstranění.


## <a name="refresh-shares"></a>Aktualizace sdílených složek

Funkce aktualizace umožňuje aktualizovat obsah sdílené složky. Když aktualizujete sdílenou složku, spustí se vyhledávání, které najde všechny objekty Azure včetně objektů blob a souborů, které byly do cloudu přidány od poslední aktualizace. Tyto další soubory jsou pak staženy aktualizovat obsah sdílené složky v zařízení.

> [!IMPORTANT]
> - Místní sdílené složky nelze aktualizovat.
> - Oprávnění a seznamy řízení přístupu (ACL) nejsou zachovány v rámci operace aktualizace. 

Pokud chcete sdílenou složku aktualizovat, proveďte na webu Azure Portal následující kroky.

1.   Na webu Azure Portal přejděte na **Sdílené složky**. Vyberte a klikněte na sdílenou složku, kterou chcete aktualizovat.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/refresh-share-1.png)

2.   Klikněte na **Aktualizovat**. 

    ![Kliknutím aktualizujte](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.   Po zobrazení výzvy k potvrzení klikněte na **Ano**. Spustí se úloha, která zaktualizuje obsah místní sdílené složky.

    ![Potvrďte aktualizaci](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.   Když aktualizace probíhá, bude možnost aktualizace v místní nabídce neaktivní. Kliknutím na oznámení úlohy zobrazte stav úlohy aktualizace.

5.   Doba aktualizace závisí na počtu souborů v kontejneru Azure a také na souborech v zařízení. Po úspěšném dokončení aktualizace se zaktualizuje časové razítko sdílené složky. Operace se považuje za úspěšnou i v případě částečně neúspěšné aktualizace a časové razítko zaktualizuje. Protokoly chyb aktualizace jsou také aktualizovány.

    ![Aktualizované časové razítko](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Pokud dojde k selhání, bude vydána výstraha. Výstraha uvede podrobnosti příčiny a doporučení k vyřešení problému. Výstraha obsahuje také odkazy na soubor s úplným souhrnem chyb, včetně souborů, které se nepovedlo aktualizovat nebo odstranit.


## <a name="sync-storage-keys"></a>Synchronizace klíčů úložiště

Pokud se klíče k vašemu účtu úložiště obměňovaly, bude potřeba provést synchronizaci přístupových klíčů k úložišti. Synchronizace pomáhá zařízení se získáním nejnovějších klíčů k účtu úložiště.

Pokud chcete synchronizovat přístupový klíč úložiště, proveďte na webu Azure Portal následující kroky.

1. Ve svém prostředku přejděte na **Přehled**. V seznamu sdílených složek zvolte a klikněte na sdílenou složku, která je přidružená k účtu úložiště, které potřebujete synchronizovat.

    ![Vybrat sdílení s příslušným účtem úložiště](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Klikněte na **Synchronizovat klíč úložiště**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Vybrat klíč úložiště synchronizace](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

>[!NOTE]
> U každého účtu úložiště to stačí provést jednou. Není nutné tento postup opakovat u každé sdílené složky, která je přidružená ke stejnému účtu úložiště.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
