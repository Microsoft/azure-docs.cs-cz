---
title: Transformace dat pomocí Azure Data Boxu Edge | Microsoft Docs
description: Zjistěte, jak na Data Boxu Edge nakonfigurovat výpočetní roli a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: e970f7cc0d4c4620f2da69286be36d0c22e0d747
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260359"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Kurz: Transformace dat pomocí Azure Data Boxu Edge (Preview)

Tento kurz popisuje, jak nakonfigurovat výpočetní roli na Data Boxu Edge. Jakmile bude výpočetní role nakonfigurovaná, může Data Box Edge transformovat data před odesláním do Azure.

Dokončení tohoto postupu může trvat přibližně 30 až 45 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředku služby IoT Hub
> * Nastavení výpočetní role
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetních funkcí na Data Boxu Edge se ujistěte, že platí následující:

* Zařízení Data Box Edge je aktivované, jak je podrobně popsáno v tématu [Připojení a aktivace Azure Data Boxu Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Vytvoření prostředku služby IoT Hub

Před nastavením výpočetní role na Data Boxu Edge je potřeba vytvořit prostředek služby IoT Hub.

Podrobné pokyny najdete v tématu [Vytvoření služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Použijte stejné předplatné a skupinu prostředků jako pro prostředek Data Box Edge.

![Vytvoření prostředku služby IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Pokud není nastavená výpočetní role Edge, mějte na paměti následující:

- Prostředek služby IoT Hub neobsahuje žádná zařízení IoT ani IoT Edge.
- Nemůžete vytvářet místní sdílené složky Edge. Při přidávání sdílené složky nebude povolená možnost vytvořit místní sdílenou složku pro výpočetní funkci Edge.


## <a name="set-up-compute-role"></a>Nastavení výpočetní role

Při nastavování výpočetní role Edge na zařízení Edge se vytvoří dvě zařízení – jedno zařízení IoT a jedno zařízení IoT Edge. Obě tato zařízení je možné zobrazit v prostředku služby IoT Hub.

Pokud chcete na zařízení nastavit výpočetní roli, postupujte následovně.

1. Přejděte k prostředku Data Box Edge, pak přejděte do části **Přehled** a klikněte na **Nastavit výpočetní roli**. 

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Můžete také přejít do části **Moduly** a kliknout na **Konfigurovat výpočty**.

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. V rozevíracím seznamu vyberte **prostředek služby IoT Hub**, který jste vytvořili v předchozím kroku. V tuto chvíli je pro zařízení IoT Edge k dispozici pouze platforma Linux. Klikněte na možnost **Vytvořit**.

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. Vytvoření výpočetní role trvá několik minut. Chyba v této vydané verzi způsobuje, že se ani po vytvoření výpočetní role obrazovka neaktualizuje. Přejděte do části **Moduly**, kde uvidíte, že je výpočetní funkce Edge nakonfigurovaná.  

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Znovu přejděte do části **Přehled**. Obrazovka se teď aktualizovala a značí, že je výpočetní role nakonfigurovaná.

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Přejděte ke službě IoT Hub, kterou jste použili při vytváření výpočetní role Edge. Přejděte do části **Zařízení IoT**. Uvidíte, že je teď povolené zařízení IoT. 

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Přejděte ke službě **IoT Edge** a uvidíte, že je povolené také zařízení IoT Edge.

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Vyberte a klikněte na zařízení IoT Edge. Na tomto zařízení IoT Edge je spuštěný agent Edge. 

    ![Nastavení výpočetní role](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Toto zařízení Edge však neobsahuje žádné vlastní moduly. Teď můžete na toto zařízení přidat vlastní modul. Informace o vytvoření vlastního modulu najdete v tématu [Vývoj modulu jazyka C# pro Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Přidání vlastního modulu

V této části přidáte do zařízení IoT Edge vlastní modul, který jste vytvořili v tématu [Vývoj modulu jazyka C# pro Data Box Edge](data-box-edge-create-iot-edge-module.md). 

V tomto postupu se používá příklad, kdy vlastní modul přesouvá soubory z místní sdílené složky na zařízení Edge do cloudové sdílené složky na zařízení. Tato cloudová sdílená složka pak tyto soubory odesílá do přidruženého účtu úložiště Azure. 

1. Prvním krokem je přidat na zařízení Edge místní sdílenou složku. V prostředku Data Box Edge přejděte do části **Sdílené složky**. Klikněte na **+ Přidat sdílenou složku**. Zadejte název sdílené složky a vyberte její typ. Pokud chcete vytvořit místní sdílenou složku, zaškrtněte možnost **Nakonfigurovat jako místní sdílenou složku Edge**. Vyberte **existujícího uživatele** nebo **vytvořte nového**. Klikněte na možnost **Vytvořit**.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Pokud jste vytvořili místní sdílenou složku systému souborů NFS, použijte ke kopírování souborů do sdílené složky následující možnost příkazu rsync:

    `rsync --inplace <source file path> < destination file path>`

     Další informace o příkazu rsync najdete v [dokumentaci k příkazu rsync](https://www.computerhope.com/unix/rsync.htm). 

 
2. Po vytvoření místní sdílené složky a zobrazení oznámení o úspěšném vytvoření (seznam sdílených složek se může aktualizovat dřív, ale musíte počkat na dokončení vytváření sdílené složky) přejděte k seznamu sdílených složek. 

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Vyberte a klikněte na nově vytvořenou místní sdílenou složku a zobrazte její vlastnosti. Zkopírujte a uložte **místní přípojný bod pro moduly Edge** této sdílené složky.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Přejděte k existující cloudové sdílené složce vytvořené na zařízení Data Box Edge. Opět zkopírujte a uložte místní přípojný bod této sdílené složky pro výpočetní moduly Edge. Tyto místní přípojné body se použijí při nasazování modulu.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Pokud chcete na zařízení IoT Edge přidat vlastní modul, přejděte k prostředku služby IoT Hub a pak do části **Zařízení IoT Edge**. Vyberte a klikněte na zařízení. V části **Podrobnosti o zařízení** na horním panelu příkazů klikněte na **Nastavit moduly**. 

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. V části **Přidat moduly** proveďte následující kroky:

    1. V části **Nastavení registru kontejneru** zadejte **název**, **adresu**, **uživatelské jméno** a **heslo** vlastního modulu. Název, adresa a uvedené přihlašovací údaje slouží k načtení modulu s odpovídající adresou URL. Pokud chcete tento modul nasadit, v části **Moduly nasazení** vyberte **Modul IoT Edge**. Modul IoT Edge je kontejner Dockeru, který můžete nasadit na zařízení IoT Edge přidružené k vašemu zařízení Data Box Edge.

        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Zadejte nastavení vlastního modulu IoT Edge. Zadejte **název** vašeho modulu a **identifikátor URI image** odpovídající image kontejneru. 
    
        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. V části **Možnosti vytvoření kontejneru** zadejte místní přípojné body cloudové a místní sdílené složky pro moduly Edge, které jste zkopírovali v předchozích krocích (je důležité použít tyto cesty, a nevytvářet nové). Místní přípojné body se mapují na odpovídající cesty **InputFolderPath** a **OutputFolderPath**, které jste zadali v modulu při [aktualizaci modulu s použitím vlastního kódu](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
        Následující ukázku můžete zkopírovat a vložit do **možností vytvoření kontejneru**: 
        
        ```
        {
         "HostConfig": {
          "Binds": [
           "/home/hcsshares/mysmblocalshare:/home/LocalShare",
           "/home/hcsshares/mysmbshare1:/home/CloudShare"
           ]
         }
        }
        ```

        Sem zadejte také všechny proměnné prostředí pro váš modul.

        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. V případě potřeby vyberte **Konfigurovat upřesňující nastavení modulu runtime Edge** a pak klikněte na **Další**.

        ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  V části **Zadejte trasy** nastavte trasy mezi moduly. V tomto případě zadejte název místní sdílené složky, která bude odesílat data do cloudové sdílené složky. Klikněte na **Další**.

    Trasu můžete nahradit následujícím řetězcem trasy: "route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  V části **Kontrola nasazení** zkontrolujte všechna nastavení, a pokud budete spokojeni, **odešlete** modul k nasazení.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Tím se spustí nasazování modulu, jak je vidět ve **vlastním modulu IoT Edge** v části **Moduly**.

![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

Posledním krokem je ověření, že je modul připojený a funguje podle očekávání. Proveďte následující kroky a ověřte, že je modul spuštěný.

1. Modul runtime modulu pro vaše zařízení IoT Edge v prostředku služby IoT Hub by měl být ve spuštěném stavu.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Vyberte a klikněte na modul a prohlédněte si **Dvojče identity modulu**. Stav klienta pro modul a zařízení Edge by teď měl být **Připojeno**.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Jakmile bude modul spuštěný, zobrazí se také v seznamu modulů Edge v prostředku Data Box Edge. **Stav modulu runtime** přidaného modulu je **spuštěno**.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Prostřednictvím Průzkumníka souborů se připojte k místní i cloudové sdílené složce, které jste vytvořili.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Pak se data odešlou z cloudové sdílené složky do účtu úložiště. Přejděte do Průzkumníka služby Storage a zobrazte data.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Tím proces ověření končí.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření prostředku služby IoT Hub
> * Nastavení výpočetní role
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

V dalším kurzu se dozvíte, jak Data Box Edge spravovat.

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


