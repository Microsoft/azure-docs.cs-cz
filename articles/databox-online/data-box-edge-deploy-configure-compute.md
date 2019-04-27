---
title: Transformace dat pomocí Azure Data Boxu Edge | Microsoft Docs
description: Zjistěte, jak na Data Boxu Edge nakonfigurovat výpočetní roli a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 31911c124aeafecb8ee37d14e58d3a0bdc0d4955
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112601"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Kurz: Transformace dat pomocí Azure Data Box Edge

Tento kurz popisuje, jak nakonfigurovat výpočetní roli na zařízení s Azure Data Box Edge. Po dokončení konfigurace výpočetní roli Edge dat pole můžete transformovat data před jejich odesláním do Azure.

Tento postup může trvat asi 10 až 15 minut na dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výpočtů
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní roli na vašem zařízení Data Box Edge, ujistěte se, že:

- Po dokončení aktivace zařízení Data Box Edge, jak je popsáno v [připojení, nastavení a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace výpočtů

Konfigurace výpočtů na hranici vaší datové pole, vytvoříte prostředek služby IoT Hub.

1. Na portálu Azure Data Box Edge prostředku přejdete na Přehled. V pravém podokně na **Compute** dlaždice, vyberte **Začínáme**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na **nakonfigurovat hraniční výpočetní** dlaždice, vyberte **konfigurace výpočtů**.
3. Na **nakonfigurovat hraniční výpočetní** okně zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte si z **nové** nebo **existující**. <br> Ve výchozím nastavení slouží úrovně Standard (S1) a vytvoří prostředek IoT. Použít prostředek na úrovni free IoT, vytvořte si ho a potom vyberte existující prostředek. <br> V obou případech prostředek služby IoT Hub používá stejné předplatné a skupinu prostředků, která používá okraj pole Data prostředků.     |
    |Název     |Zadejte název pro prostředek služby IoT Hub.         |

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Vyberte **Vytvořit**. Vytvoření služby IoT Hub prostředků trvá několik minut. Po vytvoření prostředku služby IoT Hub **konfigurace výpočtů** dlaždice aktualizuje a zobrazí konfiguraci výpočtů. Potvrďte, že byl nakonfigurován hraniční výpočetní roli vyberte **zobrazení Compute** na **konfigurace výpočtů** dlaždici.
    
    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Když výpočetní roli okraj nastaven na hraniční zařízení, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení lze zobrazit v prostředku služby IoT Hub. Modul Runtime IoT Edge běží na tomto zařízení IoT Edge. V tomto okamžiku je pouze platformě Linux k dispozici pro zařízení IoT Edge.


## <a name="add-shares"></a>Přidat sdílené složky

Pro jednoduché nasazení v tomto kurzu budete potřebovat dvě složky: jedna sdílená složka Edge a jiné místní sdílenou složkou Edge.

1. Přidáte do sdílené složky Edge na zařízení podle následujících kroků:

    1. V prostředku okraj pole dat, přejděte na **hrany výpočetní > Začínáme**.
    2. Na **přidání sdílených složek** dlaždice, vyberte **přidat**.
    3. Na **přidat sdílenou složku** okně zadejte název sdílené složky a vyberte typ sdílené složky.
    4. Chcete-li připojit sdílenou složku Edge, zaškrtněte políčko pro **použít sdílenou složku s výpočetními prostředky na Edge**.
    5. Vyberte **účtu úložiště**, **službu Storage**, existující uživatele a pak vyberte **vytvořit**.

        ![Přidat do sdílené složky Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Pokud jste vytvořili místní sdílené složky systému souborů NFS, použijte možnost vzdáleného synchronizace (rsync) příkaz pro kopírování souborů do sdílené složky:

    `rsync <source file path> < destination file path>`

    Další informace o příkaz rsync, přejděte na [Rsync dokumentaci](https://www.computerhope.com/unix/rsync.htm).

    Vytvoření sdílené složky Edge a obdržíte oznámení o úspěšném vytvoření. Seznam sdílená složka může být aktualizován, ale musíte počkat, pro vytvoření sdílené složky na dokončení.

2. Přidat do sdílené složky místní Edge na hraniční zařízení tak, že zopakujete všech kroků v předchozím kroku a zaškrtněte políčko pro **konfigurovat jako místní sdílený adresář Edge**. Data v místní sdílené složky zůstanou v zařízení.

    ![Přidat do sdílené složky místní Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Vyberte **přidání sdílených složek** chcete zobrazit aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Přidat modul

Můžete přidat vlastní nebo předem sestavených modulu. Neexistují žádné vlastní moduly na hraniční zařízení. Zjistěte, jak vytvořit vlastní modul, přejděte na [vývoje C# modulu pro vaše zařízení Data Box Edge](data-box-edge-create-iot-edge-module.md).

V této části přidáte do zařízení IoT Edge, které jste vytvořili vlastní modul [vývoje C# modulu pro vaše Data Box Edge](data-box-edge-create-iot-edge-module.md). Tento vlastní modul přijímá soubory z hraničních místní sdílenou složkou na hraniční zařízení a přesune je do sdílené složky Edge (cloud) na zařízení. Cloudové sdílené složky souborů předá do účtu úložiště Azure, který je spojen s cloudové sdílené složky.

1. Přejděte na **hrany výpočetní > Začínáme**. Na **přidat moduly** dlaždice, vyberte typ scénáře jako **jednoduché**. Vyberte **Přidat**.
2. V **konfigurace a přidání modulu** okně zadejte následující hodnoty:

    
    |Pole  |Hodnota  |
    |---------|---------|
    |Název     | Jedinečný název modulu. Tento modul je kontejner dockeru, který nasadíte do zařízení IoT Edge, přiřazené k hranici Data Box.        |
    |Identifikátor URI image     | Obrázek, identifikátor URI pro odpovídající image kontejneru pro modul.        |
    |Vyžadují se přihlašovací údaje.     | Pokud je zaškrtnuto, uživatelské jméno a heslo slouží k načtení modulů s odpovídající adresy URL.        |
    |Vstupní sdílené složky     | Vyberte vstupní sdílené složky. Edge místní sdílené složky v tomto případě je vstupní sdílené složky. Modul se tady použít přesune soubory ze sdílené složky místní Edge do sdílené složky Edge ve kterém se nahrají do cloudu.        |
    |Sdílená složka pro výstup     | Vyberte sdílenou složku výstupu. Výstup sdílená složka je sdílená složka Edge v tomto případě.        |
    |Typ aktivační události     | Vyberte z **souboru** nebo **plán**. Soubor aktivační událost aktivuje se při každém výskytu události souborů, jako je soubor zapsán do vstupní sdílené složky. Naplánovanou aktivační událost se aktivuje, podle plánu, které jste definovali.         |
    |Název triggeru     | Jedinečný název pro trigger.         |
    |Proměnné prostředí| Volitelné informace, které vám pomohou definují prostředí, ve kterém se spustí modul.   |

    ![Přidat a nakonfigurovat modul](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Vyberte **Přidat**. Přidá modul. **Přidat modul** dlaždice aktualizuje a indikuje, že v modulu nasazení. 

    ![Modul nasazení](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

V posledním kroku je zajistit, že modul je připojené a běží podle očekávání. Stav běhu modulu by měl být spuštěn pro vaše zařízení IoT Edge v prostředku služby IoT Hub.

Pokud chcete ověřit, zda je spuštěna v modulu, postupujte takto:

1. Vyberte **přidat modul** dlaždici. Tím přejdete **moduly** okno. V seznamu modulů Identifikujte modul, který jste nasadili. Stav modulu runtime modulu, který jste přidali by měl být *systémem*.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  V Průzkumníku souborů připojte místní hraniční a sdílených složek Edge, který jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Data a odešle na účet úložiště z cloudové sdílené složky. K zobrazení dat, přejděte Průzkumníka služby Storage.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Dokončení procesu ověření.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace výpočtů
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Další informace o správě zařízení Data Box Edge najdete v tématu:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](data-box-edge-manage-access-power-connectivity-mode.md)
