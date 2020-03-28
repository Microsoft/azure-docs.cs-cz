---
title: Kurz filtrování, analýzy dat pro pokročilé nasazení pomocí výpočetních prostředků na Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat výpočetní roli na Okraji datové schránky a použít ji k transformaci dat pro pokročilý tok nasazení před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239039"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Kurz: Transformace dat pomocí Azure Data Box Edge pro pokročilý tok nasazení

Tento kurz popisuje, jak nakonfigurovat výpočetní roli pro pokročilý tok nasazení na zařízení Azure Data Box Edge. Po konfiguraci výpočetní role data box edge můžete transformovat data před odesláním do Azure.

Výpočetní prostředky lze nakonfigurovat pro jednoduché nebo pokročilé tok nasazení na vašem zařízení.

|                  | Jednoduché nasazení                                | Pokročilé nasazení                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Určeno pro     | Správci IT                                | Vývojáři                            |
| Typ             | Nasazení modulů pomocí služby Data Box Edge      | Nasazení modulů pomocí služby IoT Hub |
| Nasazené moduly | Single                                           | Zřetězené nebo více modulů           |


Tento postup může trvat přibližně 20 až 30 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výpočetních prostředků
> * Přidání sdílených složek
> * Přidání triggeru
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Než nastavíte výpočetní roli na zařízení Data Box Edge, ujistěte se, že:

- Zařízení Data Box Edge jste aktivovali, jak je popsáno v [části Připojit, nastavit a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace výpočetních prostředků

Chcete-li nakonfigurovat výpočetní prostředky na okraji datové schránky, vytvoříte prostředek služby IoT Hub.

1. Na portálu Azure vašeho prostředku Data Box Edge přejděte na **Přehled**. V pravém podokně vyberte na dlaždici **Výpočetní výkon** **možnost Začínáme**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na **výpočetní** dlaždici Konfigurovat edge vyberte **Konfigurovat výpočetní výkon**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na výpočetním okně **Konfigurovat edge** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte si z **nové nebo** **existující**. <br> Ve výchozím nastavení se úroveň Standard (S1) používá k vytvoření prostředku IoT. Chcete-li použít prostředek IoT na volné úrovni, vytvořte ho a vyberte existující prostředek. <br> V každém případě prostředek služby IoT Hub používá stejné předplatné a skupinu prostředků, které používá prostředek Data Box Edge.     |
    |Name (Název)     |Zadejte název prostředku centra IoT Hub.         |

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Vyberte **Vytvořit**. Vytvoření prostředků centra IoT Hub trvá několik minut. Po vytvoření prostředku služby IoT Hub **konfigurace výpočetní** dlaždice Edge aktualizace pro zobrazení konfigurace výpočetních prostředků. Pokud chcete ověřit, že byla nakonfigurovaná výpočetní role Edge, vyberte **Zobrazit konfiguraci** na dlaždici **Konfigurovat výpočetní výkon.**
    
    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Když je výpočetní role Edge nastavena na zařízení Edge, vytvoří dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení lze zobrazit v prostředku ioT hubu. Na tomto zařízení IoT Edge běží taky runtime IoT Edge.

    V tomto okamžiku je k dispozici pouze platforma Linux pro vaše zařízení IoT Edge.


## <a name="add-shares"></a>Přidání sdílených složek

Pro pokročilé nasazení v tomto kurzu budete potřebovat dvě sdílené složky: jednu sdílenou složku Edge a další místní sdílenou složku Edge.

1. Přidejte do zařízení sdílenou složku Edge takto:

    1. V prostředku Data Box Edge přejděte na **Edge compute > Začínáme**.
    2. Na dlaždici **Přidat sdílenou složku** vyberte **Přidat**.
    3. V okně **Přidat sdílenou** položku zadejte název sdílené položky a vyberte typ sdílené složky.
    4. Chcete-li připojit sdílenou složku Edge, zaškrtněte políčko **Použít sdílenou složku s výpočty Edge**.
    5. Vyberte **účet úložiště**, **službu Úložiště**, existujícího uživatele a pak vyberte **Vytvořit**.

        ![Přidání sdílené položky edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po vytvoření sdílené složky Edge obdržíte oznámení o úspěšném vytvoření. Seznam sdílení je aktualizován tak, aby odrážel novou sdílenou složku.

2. Přidejte místní sdílenou složku Edge na zařízení Edge opakováním všech kroků v předchozím kroku a zaškrtnutím políčka **Konfigurovat jako místní sdílenou složku Edge**. Data v místní sdílené složce zůstanou v zařízení.

    ![Přidání místní sdílené složky Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. V okně **Sdílené složky** se zobrazí aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Chcete-li zobrazit vlastnosti nově vytvořené místní sdílené složky, vyberte sdílenou složku ze seznamu. V poli **Místní přípojný bod pro výpočetní moduly Edge** zkopírujte hodnotu odpovídající této sdílené položce.

    Tento místní přípojný bod použijete při nasazování modulu.

    ![Pole "Místní přípojný bod pro výpočetní moduly Edge"](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Chcete-li zobrazit vlastnosti sdílené složky Edge, kterou jste vytvořili, vyberte sdílenou složku ze seznamu. V poli **Místní přípojný bod pro výpočetní moduly Edge** zkopírujte hodnotu odpovídající této sdílené položce.

    Tento místní přípojný bod použijete při nasazování modulu.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Přidání triggeru

1. Přejděte na **edge výpočetní > aktivační události**. Vyberte **+ Přidat aktivační událost**.

    ![Přidání triggeru](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. V okně **Přidat aktivační událost** zadejte následující hodnoty.

    |Pole  |Hodnota  |
    |---------|---------|
    |Název aktivační události     | Jedinečný název aktivační události.         |
    |Typ aktivační události     | Vyberte **aktivační událost Soubor.** Aktivační událost souboru se spustí vždy, když dojde k události souboru, jako je například zápis souboru do vstupní sdílené složky. Naplánovaná aktivační událost se naopak spustí na základě vámi definovaného plánu. V tomto příkladu potřebujeme aktivační událost souboru.    |
    |Vstupní sdílená složky     | Vyberte vstupní sdílenou složku. Místní sdílená složky Edge je vstupní sdílené složky v tomto případě. Modul, který je zde použit, přesune soubory z místní sdílené složky Edge do sdílené složky Edge, kde jsou nahrány do cloudu.        |

    ![Přidání triggeru](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Budete upozorněni po vytvoření aktivační události. Seznam aktivačních událostí je aktualizován tak, aby zobrazoval nově vytvořenou aktivační událost. Vyberte aktivační událost, kterou jste právě vytvořili.

    ![Přidání triggeru](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Zkopírujte a uložte ukázkovou trasu. Upravíte tento ukázkový postup a použijete ho později v centru IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Přidání triggeru](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Přidání modulu

Na tomto zařízení Edge nejsou žádné vlastní moduly. Můžete přidat vlastní nebo předem sestavený modul. Chcete-li se dozvědět, jak vytvořit vlastní modul, přejděte na [vývoj modulu C# pro zařízení Data Box Edge](data-box-edge-create-iot-edge-module.md).

V této části přidáte vlastní modul do zařízení IoT Edge, které jste vytvořili v [modulu Vývoj c# pro váš datový schránka Edge](data-box-edge-create-iot-edge-module.md). Tento vlastní modul přebírá soubory z místní sdílené složky Edge na zařízení Edge a přesune je do sdílené složky Edge (cloud) v zařízení. Sdílená složka cloudu pak odešle soubory do účtu úložiště Azure, který je přidružený ke sdílené složce cloudu.

1. Přejděte na **Edge compute > Začínáme**. Na dlaždici **Přidat moduly** vyberte typ scénáře jako **upřesnil**. Vyberte **Přejít na IoT Hub**.

    ![Výběr rozšířeného nasazení](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Ve vašem prostředku IoT Hub přejděte na **zařízení IoT Edge** a vyberte zařízení IoT Edge.

    ![Přejděte na zařízení IoT Edge v centru IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. V **části Podrobnosti o zařízení**vyberte Nastavit **moduly**.

    ![Odkaz Nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. V části **Přidat moduly**postupujte takto:

    1. Zadejte název, adresu, uživatelské jméno a heslo pro nastavení registru kontejneru pro vlastní modul.
    Název, adresa a uvedená pověření se používají k načtení modulů s odpovídající adresou URL. Pokud chcete tento modul nasadit, v části **Moduly nasazení** vyberte **Modul IoT Edge**. Tento modul IoT Edge je kontejner dockeru, který můžete nasadit do zařízení IoT Edge, které je přidruženo k vašemu zařízení Data Box Edge.

        ![Stránka Nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Zadejte nastavení vlastního modulu IoT Edge. Zadejte následující hodnoty.
     
        |Pole  |Hodnota  |
        |---------|---------|
        |Name (Název)     | Jedinečný název modulu. Tento modul je kontejner dockeru, který můžete nasadit do zařízení IoT Edge přidruženého k vašemu okraji datové schránky.        |
        |Identifikátor URI obrázku     | Identifikátor URI obrázku pro odpovídající bitovou kopii kontejneru pro modul.        |
        |Je vyžadována pověření.     | Pokud je zaškrtnuto, uživatelské jméno a heslo se používají k načtení modulů s odpovídající adresou URL.        |
    
        Do pole **Možnosti vytvoření kontejneru** zadejte místní přípojné body pro moduly Edge, které jste zkopírovali v předchozích krocích pro sdílenou složku Edge a místní sdílenou složku Edge.

        > [!IMPORTANT]
        > Cesty zde použité jsou připojeny do kontejneru, takže musí odpovídat co funkce v kontejneru očekává. Pokud jste následující [Vytvořit vlastní modul](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), kód zadaný v tomto modulu očekává zkopírované cesty. Tyto cesty neupravujte.
    
        V poli **Možnosti vytvoření kontejneru** můžete vložit následující ukázku:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Zadejte všechny proměnné prostředí použité pro váš modul. Proměnné prostředí poskytují volitelné informace, které pomáhají definovat prostředí, ve kterém je modul spuštěn.

        ![Pole Možnosti vytvoření kontejneru](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. V případě potřeby nakonfigurujte upřesňující nastavení běhu Edge a klepněte na tlačítko **Další**.

        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  V části **Zadat trasy**nastavte trasy mezi moduly.  
    
    ![Určit trasy](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    *Trasu* můžete nahradit následujícím řetězcem postupu, který jste zkopírovali dříve. V tomto příkladu zadejte název místní sdílené složky, která bude nabízena data do sdílené složky cloudu. Nahraďte `modulename` název modulu. Vyberte **další**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Oddíl Určit trasy](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  V **části Kontrola nasazení**zkontrolujte všechna nastavení a pak vyberte **Odeslat,** chcete-li odeslat modul k nasazení.

    ![Stránka Nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Tato akce spustí nasazení modulu. Po dokončení nasazení je **spuštěn** **stav modulu runtime** .

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Ověření transformace dat, přenos

Posledním krokem je zajistit, že modul je připojen a spuštěn podle očekávání. Stav běhu modulu by měl být spuštěn pro vaše zařízení IoT Edge v prostředku IoT Hub.

Postupujte podle následujících kroků k ověření transformace dat a přenosu do Azure.
 
1.  V Průzkumníkovi souborů se připojte k místním akciím Edge i k sdíleným položkám Edge, které jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Data se pak posune ze sdílené složky cloudu do účtu úložiště. Chcete-li data zobrazit, přejděte do účtu úložiště a vyberte **Průzkumník a vyvolení .** Nahraná data si můžete zobrazit v účtu úložiště.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Dokončili jste proces ověření.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace výpočetních prostředků
> * Přidání sdílených složek
> * Přidání triggeru
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Informace o tom, jak spravovat zařízení Data Box Edge, najdete v tématu:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](data-box-edge-manage-access-power-connectivity-mode.md)
