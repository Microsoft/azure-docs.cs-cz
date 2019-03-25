---
title: Správu sdílené složky Azure Data Box Edge | Dokumentace Microsoftu
description: Popisuje, jak pomocí webu Azure portal ke správě sdílených složek na hranici vaší Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: ec5fbffdf7df5ef3a952e21b79ab02f355fb8e29
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403642"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Pomocí webu Azure portal ke správě sdílených složek na hranici vaší Azure Data Box

Tento článek popisuje, jak spravovat sdílené složky na hranici vaší Azure Data Box. Můžete spravovat Azure Data Box Edge prostřednictvím webu Azure portal nebo přes místní webové uživatelské rozhraní. Pomocí webu Azure portal k přidání, odstranění, aktualizace sdílené složky nebo synchronizovat klíč úložiště pro účet úložiště přidružený k sdíleným složkám.

## <a name="about-shares"></a>Informace o sdílených složkách

Přenos dat do Azure, je potřeba vytvořit sdílené složky na hranici vaší Azure Data Box. Sdílené složky, které přidáte na zařízení Data Box Edge může být místní sdílené složky nebo sdílené složky, které zápis dat do cloudu.

 - **Místní sdílené složky**: Pokud chcete data, která mají být zpracovány místně na zařízení, použijte tyto sdílené složky.
 - **Sdílené složky**: Použijte tyto sdílené složky, pokud chcete data zařízení automaticky doručí do účtu úložiště v cloudu. Všechny cloudové funkce, jako například **aktualizovat** a **synchronizaci klíčů k úložišti** použít ke sdíleným složkám.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Odstranění sdílené složky
> * Aktualizace sdílených složek
> * Synchronizace klíče úložiště


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete sdílenou složku vytvořit, proveďte na webu Azure Portal následující kroky.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **brána > sdílených složek**. Vyberte **+ přidat sdílenou složku** na panelu příkazů.

    ![Výběr možnosti Přidat sdílené složky](media/data-box-edge-manage-shares/add-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **Typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

4. Je nutné zadat **účet úložiště**, ve kterém se bude sdílená složka nacházet. V účtu úložiště se vytvoří kontejner s názvem sdílené složky, pokud ještě neexistuje. Pokud už kontejner existuje, použije se existující kontejner.

5. Z rozevíracího seznamu, zvolte **službu Storage** z objektů blob bloku, objekty blob stránky nebo soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme, aby data uložená jako objekty BLOB bloku v Azure, takže vybereme **objektů Blob bloku**. Pokud zvolíte **objektů Blob stránky**, ujistěte se, že vaše data jsou 512 bajtů zarovnána. Použití **objektů blob stránky** pro několik disků VHD nebo VHDX, které jsou vždy 512 bajtů zarovnána.

6. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
    - **Pokud vytváříte sdílenou složku SMB** – v poli **Místní uživatel se všemi oprávněními** zvolte **Vytvořit nový** nebo **Použít existující**. Pokud vytváříte nového místního uživatele, zadejte **uživatelské jméno**, **heslo** a pak potvrďte heslo. Tím se místnímu uživateli přiřadí oprávnění. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.

        ![Přidání sdílené složky SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Pokud pro data této sdílené složky zaškrtnete možnost Povolit jen operace čtení, můžete určit uživatele jen pro čtení.
    - **Pokud vytváříte sdílenou složku systému souborů NFS** – je nutné zadat **IP adresy klientů s povoleným přístupem** ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Pro snadný přístup ke sdíleným složkám z výpočetní moduly Edge, použijte místní přípojného bodu. Vyberte **použít sdílenou složku s výpočetními prostředky na Edge** tak, aby sdílenou složku automaticky připojí po něm jeho vytvořený. Pokud je vybraná tato možnost, modul Edge můžete také použít tak výpočetní prostředky s místní přípojného bodu.

8. Kliknutím na **Vytvořit** vytvořte sdílenou složku. Zobrazí se oznámení o probíhajícím vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka.

## <a name="add-a-local-share"></a>Přidat místní sdílené složky

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **brána > sdílených složek**. Vyberte **+ přidat sdílenou složku** na panelu příkazů.

    ![Výběr možnosti Přidat sdílené složky](media/data-box-edge-manage-shares/add-local-share-1.png)

2. V části **Přidat sdílenou složku** zadejte nastavení sdílené složky. Zadejte jedinečný název sdílené složky.
    
    Názvy sdílených složek můžou obsahovat pouze číslice, malá písmena a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.

3. Vyberte **Typ** sdílené složky. Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

4. Pro snadný přístup ke sdíleným složkám z výpočetní moduly Edge, použijte místní přípojného bodu. Vyberte **použít sdílenou složku s výpočetními prostředky na Edge** tak, aby modul Edge může používat výpočetní s místní přípojného bodu.

5. Vyberte **konfigurovat jako místní sdílené složky Edge**. Data v místních sdílených položek místně zůstanou v zařízení. Můžete zpracovávat data místně.

6. V **všechny místní uživatel s oprávněním** zvolte z **vytvořit nový** nebo **použít existující**.

7. Vyberte **Vytvořit**. 

    ![Vytvoření místní sdílené složky](media/data-box-edge-manage-shares/add-local-share-2.png)

    Zobrazí se oznámení, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se okno **Sdílené složky** aktualizuje a zobrazí se v něm nová sdílená složka.

    ![Zobrazit aktualizace sdílené složky okno](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Vyberte sdílenou složku, chcete-li zobrazit místní přípojný bod pro výpočetní moduly Edge pro tuto sdílenou složku.

    ![Zobrazit podrobnosti o místní sdílené složky](media/data-box-edge-manage-shares/add-local-share-4.png)


## <a name="unmount-a-share"></a>Odpojte sdílenou složku

Proveďte následující kroky na webu Azure Portal odpojit sdílenou složku.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **brána > sdílených složek**.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Ze seznamu sdílené složky vyberte sdílenou složku, kterou chcete odpojit. Chcete, aby se zajistilo, že všechny moduly, které nepoužívají sdílenou složku, kterou můžete odpojit. Pokud sdílená složka používá modul, se zobrazí problémy s odpovídající modulu. Vyberte **odpojte**.

    ![Vyberte odpojení](media/data-box-edge-manage-shares/select-unmount.png)

3. Po zobrazení výzvy k potvrzení, vyberte **Ano**. To se odpojte sdílenou složku.

    ![Potvrdit odpojení](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Poté, co je sdílená složka odpojeny, přejděte na seznam sdílených složek. Uvidíte, že **použít pro službu compute** sloupci se zobrazuje stav sdílenou složku jako **zakázané**.

    ![Odpojit sdílenou složku](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Odstranění sdílené složky

Pokud chcete sdílenou složku odstranit, proveďte na webu Azure Portal následující kroky.

1. V seznamu sdílených složek vyberte a klikněte na sdílenou složku, kterou chcete odstranit.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/delete-share-1.png)

2. Klikněte na **Odstranit**.

    ![Kliknutím odstraňte](media/data-box-edge-manage-shares/delete-share-2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/data-box-edge-manage-shares/delete-share-3.png)

Seznam se aktualizuje tak, aby odrážely odstranění sdílené složky.


## <a name="refresh-shares"></a>Aktualizace sdílených složek

Funkce aktualizace umožňuje aktualizovat obsah sdílené složky. Když aktualizujete sdílenou složku, spustí se vyhledávání, které najde všechny objekty Azure včetně objektů blob a souborů, které byly do cloudu přidány od poslední aktualizace. Tyto další soubory se potom stáhnou do aktualizovat obsah sdílené složky v zařízení.

> [!IMPORTANT]
> Nelze aktualizovat místní sdílené složky.

Pokud chcete sdílenou složku aktualizovat, proveďte na webu Azure Portal následující kroky.

1.  Na webu Azure Portal přejděte na **Sdílené složky**. Vyberte a klikněte na sdílenou složku, kterou chcete aktualizovat.

    ![Vyberte sdílenou složku](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Klikněte na **Aktualizovat**. 

    ![Kliknutím aktualizujte](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Po zobrazení výzvy k potvrzení klikněte na **Ano**. Spustí se úloha, která zaktualizuje obsah místní sdílené složky.

    ![Potvrďte aktualizaci](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  Když aktualizace probíhá, bude možnost aktualizace v místní nabídce neaktivní. Kliknutím na oznámení úlohy zobrazte stav úlohy aktualizace.

5.  Doba aktualizace závisí na počtu souborů v kontejneru Azure a také na souborech v zařízení. Po úspěšném dokončení aktualizace se zaktualizuje časové razítko sdílené složky. Operace se považuje za úspěšnou i v případě částečně neúspěšné aktualizace a časové razítko zaktualizuje. Protokoly chyb aktualizace jsou také aktualizovány.

    ![Aktualizované časové razítko](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Pokud dojde k selhání, bude vydána výstraha. Výstraha uvede podrobnosti příčiny a doporučení k vyřešení problému. Výstraha obsahuje také odkazy na soubor s úplným souhrnem chyb, včetně souborů, které se nepovedlo aktualizovat nebo odstranit.


## <a name="sync-storage-keys"></a>Synchronizace klíčů úložiště

Pokud se klíče k vašemu účtu úložiště obměňovaly, bude potřeba provést synchronizaci přístupových klíčů k úložišti. Synchronizace pomáhá zařízení se získáním nejnovějších klíčů k účtu úložiště.

Pokud chcete synchronizovat přístupový klíč úložiště, proveďte na webu Azure Portal následující kroky.

1. Ve svém prostředku přejděte na **Přehled**. V seznamu sdílených složek zvolte a klikněte na sdílenou složku, která je přidružená k účtu úložiště, které potřebujete synchronizovat.

    ![Vyberte sdílenou složku s účtem příslušné úložiště](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Klikněte na **Synchronizovat klíč úložiště**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Vyberte klíč synchronizace úložiště](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

>[!NOTE]
> U každého účtu úložiště to stačí provést jednou. Není nutné tento postup opakovat u každé sdílené složky, která je přidružená ke stejnému účtu úložiště.


## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
