---
title: Kurz, ve kterém můžete filtrovat, analyzovat data pro pokročilé nasazení s výpočetními prostředky na Azure Stack Edge pro | Microsoft Docs
description: Naučte se konfigurovat výpočetní roli na Azure Stack Edge pro a použít ji k transformaci dat pro pokročilý tok nasazení před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: f62eec29aebdcc98569134e0c3b75457467bc014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903696"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Kurz: transformace dat pomocí Azure Stack Edge pro pro pokročilý tok nasazení

V tomto kurzu se dozvíte, jak nakonfigurovat výpočetní roli pro pokročilý tok nasazení na zařízení Azure Stack Edge pro. Po nakonfigurování výpočetní role může Azure Stack Edge pro transformovat data před jejich odesláním do Azure.

Výpočetní prostředí je možné nakonfigurovat pro jednoduchý nebo pokročilý tok nasazení na zařízení.

| Kritéria | Jednoduché nasazení                                | Pokročilé nasazení                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Určeno pro     | Správci IT                                | Vývojáři                            |
| Typ             | Použití služby Azure Stack Edge k nasazení modulů      | Použití služby IoT Hub k nasazení modulů |
| Nasazené moduly | Jednoduché                                           | Zřetězené nebo vícenásobné moduly           |


Dokončení této procedury může trvat přibližně 20 až 30 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání triggeru
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní role na zařízení Azure Stack Edge pro se ujistěte, že:

- Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [připojení, nastavení a aktivace Azure Stack Edge pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurace COMPUTE

Pokud chcete nakonfigurovat výpočetní výkon na Azure Stack Edge pro, vytvoříte prostředek IoT Hub.

1. V Azure Portal prostředku Azure Stack Edge si Projděte **Přehled**. V pravém podokně na dlaždici **výpočty** **vyberte Začínáme**.

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Na dlaždici **Konfigurace hraničních výpočtů** vyberte **Konfigurovat výpočetní**prostředky.

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. V okně **Konfigurace hraničních výpočtů** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte z **nových** nebo **existujících**. <br> Ve výchozím nastavení se k vytváření prostředků IoT používá úroveň Standard (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek. <br> V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, kterou používá prostředek Azure Stack Edge.     |
    |Name     |Zadejte název prostředku IoT Hub.         |

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Vyberte **Vytvořit**. Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku IoT Hub se aktualizují dlaždice **konfigurace hraničního** navýšení, aby se zobrazila konfigurace výpočtů. Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte **Zobrazit** na dlaždici **Konfigurovat výpočty** .
    
    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V tomto zařízení IoT Edge je spuštěn také modul runtime IoT Edge.

    V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.


## <a name="add-shares"></a>Přidat sdílené složky

V rámci pokročilého nasazení v tomto kurzu budete potřebovat dvě sdílené složky: jednu hraniční sdílenou složku a další okrajovou místní sdílenou složku.

1. Pomocí následujících kroků přidejte na zařízení sdílenou složku Edge:

    1. V prostředcích Azure Stack Edge můžete začít tím, že přejdete na **Edge compute >**.
    2. Na dlaždici **Přidat sdílené složky** vyberte **Přidat**.
    3. V okně **Přidat sdílenou složku** zadejte název sdílené složky a vyberte typ sdílené složky.
    4. Pokud chcete připojit hraniční sdílenou složku, zaškrtněte políčko pro **použití sdílené složky s hraničními výpočty**.
    5. Vyberte **účet úložiště**, **službu úložiště**, stávající uživatel a pak vyberte **vytvořit**.

        ![Přidat hraniční sdílenou složku](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Po vytvoření sdílené složky Edge obdržíte oznámení o úspěšném vytvoření. Seznam sdílení se aktualizuje tak, aby odrážel novou sdílenou složku.

2. Přidejte do hraničního zařízení místní sdílenou složku tak, že zopakujete všechny kroky v předchozím kroku a zaškrtnete políčko **Konfigurovat jako hraniční místní sdílení**. Data v místní sdílené složce zůstanou v zařízení.

    ![Přidat místní sdílenou položku Edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. V okně **akcie** se zobrazí aktualizovaný seznam sdílených složek.

    ![Aktualizovaný seznam sdílených složek](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Chcete-li zobrazit vlastnosti nově vytvořené místní sdílené složky, vyberte sdílení ze seznamu. V poli **místní přípojný bod pro výpočetní moduly na hraničních** zařízeních Zkopírujte hodnotu odpovídající této sdílené složce.

    Tento místní přípojný bod použijete při nasazení modulu.

    ![Pole místní přípojný bod pro výpočetní moduly hraničních zařízení](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Chcete-li zobrazit vlastnosti hraniční sdílené složky, kterou jste vytvořili, vyberte v seznamu sdílenou složku. V poli **místní přípojný bod pro výpočetní moduly na hraničních** zařízeních Zkopírujte hodnotu odpovídající této sdílené složce.

    Tento místní přípojný bod použijete při nasazení modulu.

    ![Přidání vlastního modulu](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Přidání triggeru

1. Přejít na **> triggery na hraniční výpočty** Vyberte **+ Přidat Trigger**.

    ![Přidání triggeru](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. V okně **Přidat aktivační událost** zadejte následující hodnoty.

    |Pole  |Hodnota  |
    |---------|---------|
    |Název aktivační události     | Jedinečný název aktivační události.         |
    |Typ aktivační události     | Vyberte Trigger **souboru** . Triggery souborů se aktivují vždy, když dojde k události souboru, jako je zápis souboru do vstupní sdílené složky. Naplánovaná aktivační událost je na druhé straně aktivovaná na základě vámi definovaného plánu. V tomto příkladu potřebujeme Trigger souboru.    |
    |Vstupní sdílená složka     | Vyberte vstupní sdílenou složku. V tomto případě je v tomto případě vstupní sdílená složka Edge v místním prostředí. Pomocí tohoto modulu se přesunuly soubory z místní sdílené složky Edge do hraniční sdílené složky, kam se nahrají do cloudu.        |

    ![Přidání triggeru](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Po vytvoření triggeru se zobrazí oznámení. Seznam aktivačních událostí se aktualizuje tak, aby zobrazoval nově vytvořenou aktivační událost. Vyberte aktivační událost, kterou jste právě vytvořili.

    ![Přidání triggeru](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Zkopírujte a uložte ukázkovou trasu. Tuto ukázkovou trasu upravíte a použijete ji později v IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Přidání triggeru](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Přidat modul

Na tomto hraničním zařízení nejsou žádné vlastní moduly. Můžete přidat vlastní nebo předem sestavený modul. Další informace o tom, jak vytvořit vlastní modul, najdete v tématu [vývoj modulu C# pro zařízení Azure Stack Edge pro](azure-stack-edge-create-iot-edge-module.md).

V této části přidáte vlastní modul do zařízení IoT Edge, které jste vytvořili v tématu [vývoj modulu C# pro vaši Azure Stack Edge pro](azure-stack-edge-create-iot-edge-module.md). Tento vlastní modul přebírá soubory z hraniční místní sdílené složky na hraničním zařízení a přesouvá je do hraniční sdílené složky (Cloud) na zařízení. Sdílená složka cloudu pak tyto soubory vloží do účtu služby Azure Storage, který je přidružený ke sdílené složce cloudu.

1. Začněte tím, že přejdete na **Edge compute >**. Na dlaždici **přidat moduly** vyberte typ scénáře **Upřesnit**. Vyberte **Přejít na IoT Hub**.

    ![Vybrat rozšířené nasazení](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. V IoT Hub prostředku, klikněte na **IoT Edge zařízení** a vyberte zařízení IoT Edge.

    ![Přejít na IoT Edge zařízení v IoT Hub](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. V **podrobnostech o zařízení**vyberte **nastavit moduly**.

    ![Odkaz set modules](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. V části **přidat moduly**udělejte toto:

    1. Zadejte jméno, adresu, uživatelské jméno a heslo pro nastavení registru kontejneru pro vlastní modul.
    Název, adresa a uvedené přihlašovací údaje se používají k načtení modulů s porovnávací adresou URL. Pokud chcete tento modul nasadit, v části **Moduly nasazení** vyberte **Modul IoT Edge**. Tento modul IoT Edge je kontejner Docker, který můžete nasadit do IoT Edge zařízení, které je přidružené k vašemu zařízení Azure Stack Edge pro.

        ![Stránka set modules](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Zadejte nastavení vlastního modulu IoT Edge. Zadejte následující hodnoty.
     
        |Pole  |Hodnota  |
        |---------|---------|
        |Název     | Jedinečný název modulu. Tento modul je kontejner Docker, který můžete nasadit do zařízení IoT Edge přidruženého k vašemu Azure Stack Edge pro.        |
        |Identifikátor URI image     | Identifikátor URI image pro odpovídající image kontejneru pro modul        |
        |Požadovány přihlašovací údaje     | Pokud je zaškrtnuto, uživatelské jméno a heslo slouží k načtení modulů s porovnávací adresou URL.        |
    
        V poli **kontejner vytvořit možnosti** zadejte místní přípojné body pro moduly Edge, které jste zkopírovali v předchozích krocích pro sdílenou složku Edge a místní sdílenou složku Edge.

        > [!IMPORTANT]
        > Cesty, které tady použijete, jsou připojené do kontejneru, takže se musí shodovat s funkcemi, které váš kontejner očekává. Pokud se vám podaří [vytvořit vlastní modul](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code), kód zadaný v tomto modulu očekává zkopírované cesty. Tyto cesty neupravujte.
    
        V poli **kontejner vytvořit možnosti** můžete vložit následující ukázku:
    
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

        Zadejte všechny proměnné prostředí používané pro váš modul. Proměnné prostředí poskytují volitelné informace, které vám pomůžou definovat prostředí, ve kterém se modul spouští.

        ![Okno Možnosti vytvoření kontejneru](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. V případě potřeby nakonfigurujte nastavení pokročilého prostředí Edge a pak klikněte na **Další**.

        ![Přidání vlastního modulu](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. V části **zadat trasy**nastavte trasy mezi moduly.  
   
   ![Zadat trasy](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    *Trasu* můžete nahradit následujícím řetězcem trasy, který jste zkopírovali dříve. V tomto příkladu zadejte název místní sdílené složky, která bude zaručovat data do sdílené složky cloudu. Nahraďte `modulename` názvem modulu. Vyberte **Další**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Oddíl zadat trasy](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. V části **zkontrolovat nasazení**Zkontrolujte všechna nastavení a pak vyberte **Odeslat** a odešlete modul pro nasazení.

   ![Stránka set modules](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Tato akce spustí nasazení modulu. Po dokončení nasazení se **běhový stav** modulu **spouští**.

    ![Přidání vlastního modulu](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Ověřit transformaci dat, přenos

Posledním krokem je ujistit se, že je modul připojený a spuštěný podle očekávání. Běhový stav modulu by měl být spuštěný pro vaše IoT Edge zařízení v prostředku IoT Hub.

Provedením následujících kroků ověříte transformaci dat a přenos do Azure.
 
1. V Průzkumníku souborů se připojte ke sdíleným složkám Edge i k okrajům, které jste vytvořili dříve.

   ![Ověření transformace dat](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Přidejte data do místní sdílené složky.

   ![Ověření transformace dat](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Data se pak odešlou ze sdílené složky cloudu do účtu úložiště. Pokud si chcete data zobrazit, klikněte na svůj účet úložiště a vyberte **Průzkumník služby Storage**. Nahraná data můžete zobrazit ve svém účtu úložiště.

    ![Ověření transformace dat](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Dokončili jste proces ověření.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání triggeru
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Informace o tom, jak spravovat zařízení Azure Stack Edge pro, najdete tady:

> [!div class="nextstepaction"]
> [Použití místního webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
