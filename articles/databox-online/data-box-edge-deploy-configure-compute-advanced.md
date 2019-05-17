---
title: Kurz k filtrování, analýza dat pro pokročilé nasazení s výpočetními prostředky na hraničních zařízeních Azure Data Box | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat výpočetní roli na okraji pole Data a použít ho k transformaci dat pro tok pokročilého nasazení před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/13/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 836a3f3c17c4cf11ac972b7cc129fb3f83093024
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794032"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Kurz: Transformace dat pomocí Azure Data Box Edge pro pokročilé nasazení toku

Tento kurz popisuje, jak nakonfigurovat výpočetní roli pro tok, který pokročilého nasazení na zařízení s Azure Data Box Edge. Po dokončení konfigurace výpočetní roli Edge dat pole můžete transformovat data před jejich odesláním do Azure.

Výpočetní prostředky lze nakonfigurovat pro nasazení jednoduchými nebo pokročilými toku ve vašem zařízení.

|                  | Snadné nasazení                                | Pokročilé nasazení                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Určený pro     | Správci IT                                | Vývojáři                            |
| Type             | Použití služby Data Box Edge můžete nasadit moduly      | Použití služby Azure IoT Hub můžete nasadit moduly |
| Nasazené moduly | Single                                           | Zřetězené nebo více modulů           |


Tento postup může trvat přibližně 20 až 30 minut na dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výpočtů
> * Přidat sdílené složky
> * Přidat trigger
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní roli na vašem zařízení Data Box Edge, ujistěte se, že:

- Po dokončení aktivace zařízení Data Box Edge, jak je popsáno v [připojení, nastavení a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace výpočtů

Konfigurace výpočtů na hranici vaší datové pole, vytvoříte prostředek služby IoT Hub.

1. Přejděte na portálu Azure Data Box Edge prostředku **přehled**. V pravém podokně na **Compute** dlaždice, vyberte **Začínáme**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na **nakonfigurovat hraniční výpočetní** dlaždice, vyberte **konfigurace výpočtů**.

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Na **nakonfigurovat hraniční výpočetní** okně zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte si z **nové** nebo **existující**. <br> Ve výchozím nastavení slouží úrovně Standard (S1) a vytvoří prostředek IoT. Použít prostředek na úrovni free IoT, vytvořte si ho a potom vyberte existující prostředek. <br> V obou případech prostředek služby IoT Hub používá stejné předplatné a skupinu prostředků, která používá okraj pole Data prostředků.     |
    |Název     |Zadejte název pro prostředek služby IoT Hub.         |

    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Vyberte **Vytvořit**. Vytvoření služby IoT Hub prostředků trvá několik minut. Po vytvoření prostředku služby IoT Hub **nakonfigurovat hraniční výpočetní** dlaždice aktualizuje a zobrazí konfiguraci výpočtů. Potvrďte, že byl nakonfigurován hraniční výpočetní roli vyberte **zobrazení config** na **konfigurace výpočtů** dlaždici.
    
    ![Začínáme s výpočetními prostředky](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Když výpočetní roli okraj nastaven na hraniční zařízení, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení lze zobrazit v prostředku služby IoT Hub. Modul Runtime IoT Edge běží na tomto zařízení IoT Edge.

    V tomto okamžiku je pouze platformě Linux k dispozici pro zařízení IoT Edge.


## <a name="add-shares"></a>Přidat sdílené složky

Pro pokročilé nasazení v tomto kurzu budete potřebovat dvě složky: jedna sdílená složka Edge a jiné místní sdílenou složkou Edge.

1. Přidáte do sdílené složky Edge na zařízení podle následujících kroků:

    1. V prostředku okraj pole dat, přejděte na **hrany výpočetní > Začínáme**.
    2. Na **přidání sdílených složek** dlaždice, vyberte **přidat**.
    3. Na **přidat sdílenou složku** okně zadejte název sdílené složky a vyberte typ sdílené složky.
    4. Chcete-li připojit sdílenou složku Edge, zaškrtněte políčko pro **použít sdílenou složku s výpočetními prostředky na Edge**.
    5. Vyberte **účtu úložiště**, **službu Storage**, existující uživatele a pak vyberte **vytvořit**.

        ![Přidat do sdílené složky Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po vytvoření sdílené složky Edge, obdržíte oznámení o úspěšném vytvoření. Sdílet seznam se aktualizuje tak, aby odrážely novou sdílenou složku.

2. Přidat do sdílené složky místní Edge na hraniční zařízení tak, že zopakujete všech kroků v předchozím kroku a zaškrtněte políčko pro **konfigurovat jako místní sdílený adresář Edge**. Data v místní sdílené složky zůstanou v zařízení.

    ![Přidat do sdílené složky místní Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na **sdílené složky** okně zobrazit aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Chcete-li zobrazit vlastnosti nově vytvořeného místní sdílené složky, vyberte sdílenou složku ze seznamu. V **bod místního připojení pro hraniční výpočetní moduly** pole, zkopírovat hodnotu odpovídající této sdílené složce.

    Tento místní přípojný bod budete používat při nasazení v modulu.

    ![Do pole "bod místního připojení pro hraniční výpočetní moduly"](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Chcete-li zobrazit vlastnosti okraj sdílenou složku, kterou jste vytvořili, vyberte sdílenou složku ze seznamu. V **bod místního připojení pro hraniční výpočetní moduly** pole, zkopírovat hodnotu odpovídající této sdílené složce.

    Tento místní přípojný bod budete používat při nasazení v modulu.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Přidat trigger

1. Přejděte na **hrany výpočetní > triggery**. Vyberte **+ přidat trigger**.

    ![Přidat trigger](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. V **aktivační událost přidat** okně zadejte následující hodnoty.

    |Pole  |Hodnota  |
    |---------|---------|
    |Název triggeru     | Jedinečný název pro trigger.         |
    |Typ aktivační události     | Vyberte **souboru** aktivační události. Soubor aktivační událost aktivuje se při každém výskytu události souborů, jako je soubor zapsán do vstupní sdílené složky. Naplánovanou aktivační událost na druhé straně aktivuje nahoru, podle plánu, které jste definovali. V tomto příkladu budeme potřebovat soubor aktivační události.    |
    |Vstupní sdílené složky     | Vyberte vstupní sdílené složky. Edge místní sdílené složky v tomto případě je vstupní sdílené složky. Modul se tady použít přesune soubory ze sdílené složky místní Edge do sdílené složky Edge ve kterém se nahrají do cloudu.        |

    ![Přidat trigger](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Po vytvoření aktivační události, se zobrazí oznámení. Seznam aktivačních událostí se aktualizuje a zobrazí se nově vytvořeného aktivační událost. Vyberte trigger, který jste právě vytvořili.

    ![Přidat trigger](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Zkopírujte a uložte ukázkový trasy. Je třeba upravit tato trasa vzorku a použít později ve službě IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Přidat trigger](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Přidat modul

Neexistují žádné vlastní moduly na hraniční zařízení. Můžete přidat vlastní nebo předem sestavených modulu. Zjistěte, jak vytvořit vlastní modul, přejděte na [vývoje C# modulu pro vaše zařízení Data Box Edge](data-box-edge-create-iot-edge-module.md).

V této části přidáte do zařízení IoT Edge, které jste vytvořili vlastní modul [vývoje C# modulu pro vaše Data Box Edge](data-box-edge-create-iot-edge-module.md). Tento vlastní modul přijímá soubory z hraničních místní sdílenou složkou na hraniční zařízení a přesune je do sdílené složky Edge (cloud) na zařízení. Cloudové sdílené složky souborů předá do účtu úložiště Azure, který je spojen s cloudové sdílené složky.

1. Přejděte na **hrany výpočetní > Začínáme**. Na **přidat moduly** dlaždice, vyberte typ scénáře jako **pokročilé**. Vyberte **přejděte do služby IoT Hub**.

    ![Vyberte rozšířené nasazení](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. V prostředku služby IoT Hub, přejděte na **zařízení IoT Edge** a pak vyberte zařízení IoT Edge.

    ![Přejděte do zařízení IoT Edge ve službě IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Na **podrobnosti o zařízení**vyberte **nastavit moduly**.

    ![Odkaz na nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. V části **přidat moduly**, postupujte takto:

    1. Zadejte jméno, adresu, uživatelské jméno a heslo pro nastavení registru kontejneru pro vlastní modul.
    Jméno, adresu a uvedené přihlašovací údaje se používají k načtení modulů s odpovídající adresy URL. Pokud chcete tento modul nasadit, v části **Moduly nasazení** vyberte **Modul IoT Edge**. Tento modul IoT Edge je kontejner dockeru, který nasadíte do zařízení IoT Edge, který je spojen s vaším zařízením Data Box Edge.

        ![Na stránce nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Zadejte nastavení vlastního modulu IoT Edge. Zadejte následující hodnoty.
     
        |Pole  |Hodnota  |
        |---------|---------|
        |Název     | Jedinečný název modulu. Tento modul je kontejner dockeru, který nasadíte do zařízení IoT Edge, které jsou přidružené k hranici Data Box.        |
        |Identifikátor URI image     | Obrázek, identifikátor URI pro odpovídající image kontejneru pro modul.        |
        |Vyžadují se přihlašovací údaje.     | Pokud je zaškrtnuto, uživatelské jméno a heslo slouží k načtení modulů s odpovídající adresy URL.        |
    
        V **možnosti vytvoření kontejneru** zadejte místní přípojné body pro hraniční moduly, které jste zkopírovali v předchozích krocích pro sdílenou složku okraj a okraj místní sdílené složky.

        > [!IMPORTANT]
        > Cesty se tady použít jsou připojené do kontejneru, proto musí odpovídat hodnotám, očekává funkci ve vašem kontejneru. Pokud jste postupovali [vytvořit vlastní modul](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), kód uvedený v tomto modulu očekává, že zkopírovaný cesty. Neprovádějte žádné změny těchto cest.
    
        V **možnosti vytvoření kontejneru** pole, můžete vložit následující ukázce:
    
        ```
        {
            "HostConfig": {
            "Binds": [
            "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
            "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
            }
        }
        ```

        Zadejte libovolné proměnné prostředí použít pro modul. Proměnné prostředí poskytují volitelných informací, které pomáhají definují prostředí, ve kterém se modul spouští.

        ![Do pole možnosti vytvoření kontejneru](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. V případě potřeby nakonfigurujte upřesňující nastavení modulu runtime Edge a potom klikněte na tlačítko **Další**.

        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  V části **určení tras**, nastavit trasy mezi moduly.  
    
    ![Zadejte trasy](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Můžete nahradit *trasy* řetězcem následující trasy, který jste si zkopírovali dříve. V tomto příkladu zadejte název místní sdílené složky, která zařadí data do cloudové sdílené složky. Nahraďte `modulename` s názvem modulu. Vyberte **Další**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![V části Určení tras](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  V části **zkontrolujte nasazení**zkontrolujte všechna nastavení a pak vyberte **odeslat** odeslat modul pro nasazení.

    ![Na stránce nastavit moduly](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Tato akce spustí nasazení modulu. Po dokončení nasazení **stav Runtime** modulu je **systémem**.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Ověření dat transformace, přenos

V posledním kroku je zajistit, že modul je připojené a běží podle očekávání. Stav běhu modulu by měl být spuštěn pro vaše zařízení IoT Edge v prostředku služby IoT Hub.

Pomocí následujících kroků ověřte transformace dat a přenos do Azure.
 
1.  V Průzkumníku souborů připojte místní hraniční a sdílených složek Edge, který jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Data a odešle na účet úložiště z cloudové sdílené složky. Chcete-li zobrazit data, přejděte na svůj účet úložiště a pak vyberte **Průzkumníka služby Storage**. Zobrazit nahraných dat v účtu úložiště.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Dokončení procesu ověření.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace výpočtů
> * Přidat sdílené složky
> * Přidat trigger
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Další informace o správě zařízení Data Box Edge najdete v tématu:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](data-box-edge-manage-access-power-connectivity-mode.md)
