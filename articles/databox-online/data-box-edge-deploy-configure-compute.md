---
title: Kurz, ve kterém můžete filtrovat, analyzovat data s využitím výpočtů Azure Data Box Edge | Microsoft Docs
description: Zjistěte, jak na Data Boxu Edge nakonfigurovat výpočetní roli a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397321"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Kurz: transformace dat pomocí Azure Data Box Edge

V tomto kurzu se dozvíte, jak na zařízení Azure Data Box Edge nakonfigurovat výpočetní roli. Po nakonfigurování výpočetní role Data Box Edge může data před odesláním do Azure transformovat.

Dokončení této procedury může trvat přibližně 10 až 15 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Předpoklady

Před nastavením výpočetní role na zařízení Data Box Edge se ujistěte, že:

- Aktivovali jste zařízení Data Box Edge, jak je popsáno v tématu [připojení, nastavení a aktivace Azure Data box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace COMPUTE

Pokud chcete na svém Data Box Edge nakonfigurovat výpočetní prostředky, vytvoříte prostředek IoT Hub.

1. V Azure Portal prostředku Data Box Edge si přečtěte přehled. V pravém podokně na dlaždici **výpočty** **vyberte Začínáme**.

    ![Začínáme se službou COMPUTE](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na dlaždici **Konfigurace hraničních výpočtů** vyberte **Konfigurovat výpočetní**prostředky.
3. V okně **Konfigurace hraničních výpočtů** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte z **nových** nebo **existujících**. <br> Ve výchozím nastavení se k vytvoření prostředku IoT používá standardní vrstva (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek. <br> V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, kterou používá prostředek Data Box Edge.     |
    |Název     |Zadejte název prostředku IoT Hub.         |

    ![Začínáme se službou COMPUTE](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Vyberte **Vytvořit**. Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku IoT Hub se aktualizuje a zobrazí **se konfigurace výpočtů na dlaždici** . Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte na dlaždici **Konfigurovat výpočty** možnost **Zobrazit výpočetní** prostředky.
    
    ![Začínáme se službou COMPUTE](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Pokud je dialogové okno **Konfigurovat výpočetní** prostředí ukončeno, než je IoT Hub přidruženo k zařízení data box Edge, bude IoT Hub vytvořeno, ale není zobrazeno v konfiguraci výpočtů. 
    
    Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V tomto zařízení IoT Edge je spuštěn také modul runtime IoT Edge. V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.


## <a name="add-shares"></a>Přidat sdílené složky

Pro jednoduché nasazení v tomto kurzu budete potřebovat dvě sdílené složky: jednu hranovou sdílenou složku a další hranovou místní sdílenou složku.

1. Pomocí následujících kroků přidejte na zařízení sdílenou složku Edge:

    1. Ve svém prostředku Data Box Edge jděte na **Edge compute > Začínáme**.
    2. Na dlaždici **Přidat sdílené složky** vyberte **Přidat**.
    3. V okně **Přidat sdílenou složku** zadejte název sdílené složky a vyberte typ sdílené složky.
    4. Pokud chcete připojit hraniční sdílenou složku, zaškrtněte políčko pro **použití sdílené složky s hraničními výpočty**.
    5. Vyberte **účet úložiště**, **službu úložiště**, stávající uživatel a pak vyberte **vytvořit**.

        ![Přidat hraniční sdílenou složku](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Pokud jste vytvořili místní sdílenou složku NFS, ke zkopírování souborů do sdílené složky použijte následující příkaz vzdálené synchronizace (rsync):

    `rsync <source file path> < destination file path>`

    Další informace o příkazu rsync najdete v [dokumentaci k rsync](https://www.computerhope.com/unix/rsync.htm).

    Vytvoří se sdílená složka Edge a obdržíte oznámení o úspěšném vytvoření. Seznam sdílených složek se může aktualizovat, ale musíte počkat na dokončení vytváření sdílené složky.

2. Přidejte do hraničního zařízení místní sdílenou složku tak, že zopakujete všechny kroky v předchozím kroku a zaškrtnete políčko **Konfigurovat jako hraniční místní sdílení**. Data v místní sdílené složce zůstanou v zařízení.

    ![Přidat místní sdílenou položku Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Vyberte **Přidat sdílené složky** a zobrazte aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Přidat modul

Můžete přidat vlastní nebo předem sestavený modul. Na tomto hraničním zařízení nejsou žádné vlastní moduly. Další informace o tom, jak vytvořit vlastní modul, najdete v tématu [vývoj C# modulu pro zařízení data box Edge](data-box-edge-create-iot-edge-module.md).

V této části přidáte vlastní modul do zařízení IoT Edge, které jste vytvořili v tématu [vývoj C# modulu pro data box Edge](data-box-edge-create-iot-edge-module.md). Tento vlastní modul přebírá soubory z hraniční místní sdílené složky na hraničním zařízení a přesouvá je do hraniční sdílené složky (Cloud) na zařízení. Sdílená složka cloudu pak tyto soubory vloží do účtu služby Azure Storage, který je přidružený ke sdílené složce cloudu.

1. Začněte tím, že přejdete na **Edge compute >** . Na dlaždici **přidat moduly** vyberte typ scénáře **jednoduché**. Vyberte **Přidat**.
2. V okně **Konfigurovat a přidat modul** zadejte následující hodnoty:

    
    |Pole  |Hodnota  |
    |---------|---------|
    |Název     | Jedinečný název modulu. Tento modul je kontejner Docker, který můžete nasadit do zařízení IoT Edge, které je přidružené k vašemu Data Box Edge.        |
    |Identifikátor URI image     | Identifikátor URI image pro odpovídající image kontejneru pro modul        |
    |Požadovány přihlašovací údaje     | Pokud je zaškrtnuto, uživatelské jméno a heslo slouží k načtení modulů s porovnávací adresou URL.        |
    |Vstupní sdílená složka     | Vyberte vstupní sdílenou složku. V tomto případě je v tomto případě vstupní sdílená složka Edge v místním prostředí. Pomocí tohoto modulu se přesunuly soubory z místní sdílené složky Edge do hraniční sdílené složky, kam se nahrají do cloudu.        |
    |Výstupní sdílená složka     | Vyberte výstupní sdílenou složku. Sdílená složka Edge je v tomto případě výstupní sdílenou složkou.        |
    |Typ triggeru     | Vyberte ze **souboru** nebo **plánu**. Aktivační událost se aktivuje vždy, když dojde k události souboru, jako je například soubor, který je zapsán do vstupní sdílené složky. Naplánovaná aktivační událost se aktivuje na základě vámi definovaného plánu.         |
    |Název aktivační události     | Jedinečný název aktivační události.         |
    |Proměnné prostředí| Volitelné informace, které vám pomůžou definovat prostředí, ve kterém se modul spustí   |

    ![Přidat a nakonfigurovat modul](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Vyberte **Přidat**. Modul se přidá. Aktualizace dlaždice **Přidat modul** , která označuje, že je modul nasazený 

    ![Nasazený modul](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

Posledním krokem je ujistit se, že je modul připojený a spuštěný podle očekávání. Běhový stav modulu by měl být spuštěný pro vaše IoT Edge zařízení v prostředku IoT Hub.

Pokud chcete ověřit, že je modul spuštěný, udělejte toto:

1. Vyberte dlaždici **Přidat modul** . Tím přejdete do okna **moduly** . V seznamu modulů Identifikujte modul, který jste nasadili. Běhový stav modulu, který jste přidali, by měl být *spuštěný*.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  V Průzkumníku souborů se připojte ke sdíleným složkám Edge i k okrajům, které jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Data se pak odešlou ze sdílené složky cloudu do účtu úložiště. Data zobrazíte tak, že přejdete na Průzkumník služby Storage.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Dokončili jste proces ověření.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Informace o tom, jak spravovat zařízení Data Box Edge, najdete tady:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](data-box-edge-manage-access-power-connectivity-mode.md)
