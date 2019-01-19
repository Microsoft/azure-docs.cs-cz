---
title: Transformace dat pomocí Azure Data Boxu Edge | Microsoft Docs
description: Zjistěte, jak na Data Boxu Edge nakonfigurovat výpočetní roli a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402111"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Kurz: Transformace dat pomocí Azure Data Box Edge (preview)

Tento kurz popisuje, jak nakonfigurovat výpočetní roli na zařízení s Azure Data Box Edge. Po dokončení konfigurace výpočetní roli Edge dat pole můžete transformovat data před jejich odesláním do Azure.

Tento postup může trvat přibližně 30 až 45 minut na dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoří prostředek služby Azure IoT Hub
> * Nastavení výpočetní role
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Než pořadí a nasazení tohohle řešení, projděte si [podmínkami pro verzi preview služby Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní roli na vašem zařízení Data Box Edge, ujistěte se, že:

* Po dokončení aktivace zařízení Data Box Edge, jak je popsáno v [připojení, nastavení a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Vytvoření prostředku služby IoT Hub

Před nastavením výpočetní roli na okraji pole dat, je nutné vytvořit prostředek služby IoT Hub.

Podrobné pokyny najdete v tématu [Vytvoření služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Použijte stejném předplatném a skupině prostředků, který jste použili pro váš prostředek okraj pole Data.

![Vytvoření prostředku služby IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Pokud nebyl nastaven hraniční výpočetní roli, platí následující upozornění:

- Prostředek služby IoT Hub neobsahuje žádné zařízení Azure IoT nebo zařízení Azure IoT Edge.
- Nemůžete vytvářet místní sdílené složky Edge. Při přidávání sdílené složky nebude povolená možnost vytvořit místní sdílenou složku pro výpočetní funkci Edge.


## <a name="set-up-compute-role"></a>Nastavení výpočetní role

Když výpočetní roli okraj nastaven na hraniční zařízení, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení lze zobrazit v prostředku služby IoT Hub.

Pokud chcete nastavit výpočetní roli na zařízení, postupujte takto:

1. Přejděte k prostředku okraj pole dat, vyberte **přehled**a pak vyberte **nastavení role výpočetní**. 

    ![Přehled odkaz v levém podokně](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Volitelně můžete přejít na **moduly** a vyberte **konfigurace výpočtů**.

    !["Modules" a "Konfigurace výpočetní" odkazy](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. V rozevíracím seznamu vyberte **prostředků služby IoT Hub** jste vytvořili v předchozím kroku.  
    V tomto okamžiku je pouze platformě Linux k dispozici pro zařízení IoT Edge. 
    
1. Klikněte na možnost **Vytvořit**.

    ![Tlačítka pro vytvoření](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    Vytvoření výpočetní role trvá několik minut. Chyba v této vydané verzi způsobuje, že se ani po vytvoření výpočetní role obrazovka neaktualizuje. Pokud chcete potvrdit, že hraniční výpočetní roli nakonfigurovaný, přejděte na **moduly**.  

    ![V seznamu zařízení "Konfigurovat hraniční výpočetní"](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Přejděte na **přehled** znovu.  
    Na obrazovce se aktualizuje a označuje, že je nakonfigurovaný výpočetní roli.

    ![Nastavte výpočetní roli](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. Ve službě IoT Hub, který jste použili při vytváření hraniční výpočetní roli, přejděte na **zařízení IoT**.  
    Zařízení IoT je nyní zapnuta. 

    ![Na stránce "Zařízení IoT"](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. V levém podokně vyberte **IoT Edge**.  
    Zařízení IoT Edge je také povolena.

    ![Nastavte výpočetní roli](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Vyberte a klikněte na zařízení IoT Edge.  
    Na tomto zařízení IoT Edge je spuštěný agent Edge. 

    ![Stránce s podrobnostmi o zařízení](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Na hraničním zařízení nejsou žádné vlastní moduly, ale teď můžete přidávat vlastní modul. Zjistěte, jak vytvořit vlastní modul, přejděte na [vývoje C# modulu pro vaše zařízení Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Přidání vlastního modulu

V této části přidáte do zařízení IoT Edge, které jste vytvořili vlastní modul [vývoje C# modulu pro vaše Data Box Edge](data-box-edge-create-iot-edge-module.md). 

Následující postup používá příklad, kdy vlastní modul přijímá soubory z místní sdílené složky na hraniční zařízení a přesune je do sdílené složky cloudu na zařízení. Cloudové sdílené složky souborů předá do účtu úložiště Azure, který je spojen s cloudové sdílené složky. 

1. Přidáte místní sdílené složky na hraniční zařízení následujícím způsobem:

    a. V prostředku Data Box Edge přejděte do části **Sdílené složky**. 
    
    b. Vyberte **přidat sdílenou složku**a pak zadejte název sdílené složky a vyberte typ sdílené složky. 
    
    c. Chcete-li vytvořit místní sdílené složky, vyberte **konfigurovat jako místní sdílený adresář Edge** zaškrtávací políčko. 
    
    d. Vyberte **vytvořit nový** nebo **použít existující**a pak vyberte **vytvořit**.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Pokud jste vytvořili místní sdílené složky systému souborů NFS, použijte možnost vzdáleného synchronizace (rsync) příkaz pro kopírování souborů do sdílené složky:

    `rsync --inplace <source file path> < destination file path>`

    Další informace o příkaz rsync, přejděte na [Rsync dokumentaci](https://www.computerhope.com/unix/rsync.htm). 

    Místní sdílenou složku vytvoříte a budete dostávat oznámení o úspěšném vytvoření. Seznam sdílená složka může být aktualizován, ale musíte počkat, pro vytvoření sdílené složky na dokončení.
    
1. Přejdete na seznam sdílených složek. 

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Chcete-li zobrazit vlastnosti nově vytvořeného místní sdílené složky, vyberte ji. 

1. V **bod místního připojení pro hraniční výpočetní moduly** pole, zkopírovat hodnotu odpovídající této sdílené složce.  
    Tento místní přípojný bod budete používat při nasazení v modulu.

    ![Do pole "bod místního připojení pro hraniční výpočetní moduly"](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. Na stávající sdílené složce cloudu, který byl vytvořen v zařízení Data Box Edge v **bod místního připojení pro hraniční výpočetní moduly** pole, zkopírovat místní přípojný bod pro hraniční výpočetní moduly pro tuto sdílenou složku v cloudu.  
    Tento místní přípojný bod budete používat při nasazení v modulu.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Přidání vlastního modulu do zařízení IoT Edge, přejděte k prostředku služby IoT Hub a potom přejděte ke **zařízení IoT Edge**. 

1. Vyberte zařízení a pak v části **podrobnosti o zařízení**vyberte **nastavit moduly**. 

    ![Odkaz na nastavit moduly](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. V části **přidat moduly**, postupujte takto:

    a. Zadejte jméno, adresu, uživatelské jméno a heslo pro nastavení registru kontejneru pro vlastní modul.  
    Jméno, adresu a uvedené přihlašovací údaje se používají k načtení modulů s odpovídající adresy URL. Pokud chcete tento modul nasadit, v části **Moduly nasazení** vyberte **Modul IoT Edge**. Tento modul IoT Edge je kontejner dockeru, který nasadíte do zařízení IoT Edge, který je spojen s vaším zařízením Data Box Edge.

    ![Na stránce nastavit moduly](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Zadejte nastavení pro vlastní modul IoT Edge tak, že zadáte název modulu a identifikátor URI image pro odpovídající image kontejneru. 
    
    ![Moduly IoT Edge vlastní stránku](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. V **možnosti vytvoření kontejneru** zadejte místní přípojné body pro hraniční moduly, které jste zkopírovali v předchozích krocích pro cloudové a místní sdílené složky.
    > [!IMPORTANT]
    > Používat zkopírovaný cesty; Nevytvářejte nové cesty. Místní přípojné body se mapují na odpovídající **InputFolderPath** a **OutputFolderPath** , který jste zadali v modulu při vám [aktualizuje modul s vlastním kódem](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    V **možnosti vytvoření kontejneru** pole, můžete vložit následující ukázce: 
    
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

    Také zadejte sem proměnných prostředí pro modul.

    ![Do pole možnosti vytvoření kontejneru](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. V případě potřeby nakonfigurujte upřesňující nastavení modulu runtime Edge a potom klikněte na tlačítko **Další**.

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  V části **určení tras**, nastavit trasy mezi moduly.  
    V tomto příkladu zadejte název místní sdílené složky, která zařadí data do cloudové sdílené složky.

    Můžete nahradit *trasy* řetězcem následující trasy:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![V části Určení tras](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Vyberte **Další**. 

1.  V části **zkontrolujte nasazení**zkontrolujte všechna nastavení a pak vyberte **odeslat** odeslat modul pro nasazení.

    ![Na stránce nastavit moduly](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Tato akce spustí nasazení modulu, jak je znázorněno na následujícím obrázku:

    ![Přidání vlastního modulu](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

V posledním kroku je zajistit, že modul je připojené a běží podle očekávání. Stav běhu modulu by měl být spuštěn pro vaše zařízení IoT Edge v prostředku služby IoT Hub.

![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Pokud chcete ověřit, zda je spuštěna v modulu, postupujte takto:

1. Vyberte modul a pak zobrazte Identity Dvojčete modulu.  
    Stav klienta pro modul a hraniční zařízení má být zobrazen jako *připojeno*.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    Po spuštění modulu se také zobrazí v seznamu moduly Edge ve svém prostředku okraj pole Data. Stav modulu runtime modul, který jste přidali, se *systémem*.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  V Průzkumníku souborů, připojit k místní a cloudové sdílené složky, jste vytvořili dříve.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Přidejte data do místní sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Data se přesunou do cloudové sdílené složky.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Data a odešle na účet úložiště z cloudové sdílené složky. K zobrazení dat, přejděte Průzkumníka služby Storage.

    ![Ověření transformace dat](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Dokončení procesu ověření.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření prostředku služby IoT Hub
> * Nastavení výpočetní role
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Další informace o správě zařízení Data Box Edge najdete v tématu:

> [!div class="nextstepaction"]
> [Správa Data Boxu Edge pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


