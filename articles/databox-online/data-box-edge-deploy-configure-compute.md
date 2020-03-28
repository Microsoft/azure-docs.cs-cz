---
title: Kurz filtrování, analýzy dat pomocí výpočetních prostředků na Azure Data Box Edge | Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239018"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Kurz: Transformace dat pomocí Azure Data Box Edge

Tento kurz popisuje, jak nakonfigurovat výpočetní roli na zařízení Azure Data Box Edge. Po konfiguraci výpočetní role data box edge můžete transformovat data před odesláním do Azure.

Tento postup může trvat přibližně 10 až 15 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výpočetních prostředků
> * Přidání sdílených složek
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Než nastavíte výpočetní roli na zařízení Data Box Edge, ujistěte se, že:

- Zařízení Data Box Edge jste aktivovali, jak je popsáno v [části Připojit, nastavit a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace výpočetních prostředků

Chcete-li nakonfigurovat výpočetní prostředky na okraji datové schránky, vytvoříte prostředek služby IoT Hub.

1. Na portálu Azure vašeho prostředku Data Box Edge přejděte na Přehled. V pravém podokně vyberte na dlaždici **Výpočetní výkon** **možnost Začínáme**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Na **výpočetní** dlaždici Konfigurovat edge vyberte **Konfigurovat výpočetní výkon**.
3. Na výpočetním okně **Konfigurovat edge** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte si z **nové nebo** **existující**. <br> Ve výchozím nastavení se úroveň Standard (S1) používá k vytvoření prostředku IoT. Chcete-li použít prostředek IoT na volné úrovni, vytvořte ho a vyberte existující prostředek. <br> V každém případě prostředek služby IoT Hub používá stejné předplatné a skupinu prostředků, které používá prostředek Data Box Edge.     |
    |Name (Název)     |Zadejte název prostředku centra IoT Hub.         |

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Vyberte **Vytvořit**. Vytvoření prostředků centra IoT Hub trvá několik minut. Po vytvoření prostředku služby IoT Hub **konfigurace aktualizací výpočetní** dlaždice tak, aby zobrazovaly konfiguraci výpočetních prostředků. Pokud chcete ověřit, že byla nakonfigurovaná výpočetní role Edge, vyberte **Zobrazit výpočetní výkon** na dlaždici Konfigurovat výpočetní **výkon.**
    
    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Pokud je dialogové okno **Konfigurovat výpočetní výkon** uzavřeno před tím, než je služba IoT Hub přidružena k zařízení Data Box Edge, služba IoT Hub se vytvoří, ale nezobrazí se v konfiguraci výpočetních prostředků. 
    
    Když je výpočetní role Edge nastavena na zařízení Edge, vytvoří dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení lze zobrazit v prostředku ioT hubu. Na tomto zařízení IoT Edge běží taky runtime IoT Edge. V tomto okamžiku je k dispozici pouze platforma Linux pro vaše zařízení IoT Edge.


## <a name="add-shares"></a>Přidání sdílených složek

Pro jednoduché nasazení v tomto kurzu budete potřebovat dvě sdílené složky: jednu sdílenou složku Edge a další místní sdílenou složku Edge.

1. Přidejte do zařízení sdílenou složku Edge takto:

    1. V prostředku Data Box Edge přejděte na **Edge compute > Začínáme**.
    2. Na dlaždici **Přidat sdílenou složku** vyberte **Přidat**.
    3. V okně **Přidat sdílenou** položku zadejte název sdílené položky a vyberte typ sdílené složky.
    4. Chcete-li připojit sdílenou složku Edge, zaškrtněte políčko **Použít sdílenou složku s výpočty Edge**.
    5. Vyberte **účet úložiště**, **službu Úložiště**, existujícího uživatele a pak vyberte **Vytvořit**.

        ![Přidání sdílené položky edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Pokud jste vytvořili místní sdílenou složku systému souborů NFS, zkopírujte do sdílené složky následující možnost příkazu vzdálené synchronizace (rsync):

    `rsync <source file path> < destination file path>`

    Další informace o příkazu rsync naleznete v [dokumentaci k rsync](https://www.computerhope.com/unix/rsync.htm).

    Vytvoří se sdílená hrana a obdržíte oznámení o úspěšném vytvoření. Seznam sdílení může být aktualizován, ale musíte počkat na dokončení vytvoření sdílené složky.

2. Přidejte místní sdílenou složku Edge na zařízení Edge opakováním všech kroků v předchozím kroku a zaškrtnutím políčka **Konfigurovat jako místní sdílenou složku Edge**. Data v místní sdílené složce zůstanou v zařízení.

    ![Přidání místní sdílené složky Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Výběrem **možnosti Přidat sdílené složky** zobrazíte aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Přidání modulu

Můžete přidat vlastní nebo předem sestavený modul. Na tomto zařízení Edge nejsou žádné vlastní moduly. Chcete-li se dozvědět, jak vytvořit vlastní modul, přejděte na [vývoj modulu C# pro zařízení Data Box Edge](data-box-edge-create-iot-edge-module.md).

V této části přidáte vlastní modul do zařízení IoT Edge, které jste vytvořili v [modulu Vývoj c# pro váš datový schránka Edge](data-box-edge-create-iot-edge-module.md). Tento vlastní modul přebírá soubory z místní sdílené složky Edge na zařízení Edge a přesune je do sdílené složky Edge (cloud) v zařízení. Sdílená složka cloudu pak odešle soubory do účtu úložiště Azure, který je přidružený ke sdílené složce cloudu.

1. Přejděte na **Edge compute > Začínáme**. Na dlaždici **Přidat moduly** vyberte typ scénáře jako **jednoduchý**. Vyberte **Přidat**.
2. V okně **Konfigurovat a přidejte modul,** zadejte následující hodnoty:

    
    |Pole  |Hodnota  |
    |---------|---------|
    |Name (Název)     | Jedinečný název modulu. Tento modul je kontejner dockeru, který můžete nasadit do zařízení IoT Edge, které je přidruženo k vašemu okraji datové schránky.        |
    |Identifikátor URI obrázku     | Identifikátor URI obrázku pro odpovídající bitovou kopii kontejneru pro modul.        |
    |Je vyžadována pověření.     | Pokud je zaškrtnuto, uživatelské jméno a heslo se používají k načtení modulů s odpovídající adresou URL.        |
    |Vstupní sdílená složky     | Vyberte vstupní sdílenou složku. Místní sdílená složky Edge je vstupní sdílené složky v tomto případě. Modul, který je zde použit, přesune soubory z místní sdílené složky Edge do sdílené složky Edge, kde jsou nahrány do cloudu.        |
    |Výstupní podíl     | Vyberte výstupní sdílenou složku. Podíl Edge je výstupní sdílená v tomto případě.        |
    |Typ aktivační události     | Vyberte z **souboru** nebo **plánu**. Aktivační událost souboru se spustí vždy, když dojde k události souboru, jako je například zápis souboru do vstupní sdílené složky. Naplánovaná aktivační událost se spustí na základě vámi definovaného plánu.         |
    |Název aktivační události     | Jedinečný název aktivační události.         |
    |Proměnné prostředí| Volitelné informace, které vám pomohou definovat prostředí, ve kterém bude modul spuštěn.   |

    ![Přidání a konfigurace modulu](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Vyberte **Přidat**. Modul je přidán. Aktualizace dlaždice **Přidat modul** označující, že modul je nasazen. 

    ![Nasadit modul](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

Posledním krokem je zajistit, že modul je připojen a spuštěn podle očekávání. Stav běhu modulu by měl být spuštěn pro vaše zařízení IoT Edge v prostředku IoT Hub.

Chcete-li ověřit, zda je modul spuštěn, postupujte takto:

1. Vyberte dlaždici **Přidat modul.** Tím přejdete k čepeli **Modules.** V seznamu modulů identifikujte modul, který jste nasadili. Stav modulu, který jste přidali, by měl být *spuštěn*.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  V Průzkumníkovi souborů se připojte k místním akciím Edge i k sdíleným položkám Edge, které jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Data se pak posune ze sdílené složky cloudu do účtu úložiště. Chcete-li zobrazit data, přejděte do Průzkumníka úložiště.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Dokončili jste proces ověření.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace výpočetních prostředků
> * Přidání sdílených složek
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Informace o tom, jak spravovat zařízení Data Box Edge, najdete v tématu:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](data-box-edge-manage-access-power-connectivity-mode.md)
