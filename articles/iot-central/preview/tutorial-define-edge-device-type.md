---
title: Kurz – definování nového typu Azure IoT Edge zařízení v Azure IoT Central
description: V tomto kurzu se dozvíte jako tvůrce, jak vytvořit nové zařízení Azure IoT Edge v aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702794"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Kurz: definování nového typu Azure IoT Edge zařízení v aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte jako tvůrce, jak použít šablonu zařízení k definování nového typu Azure IoT Edge zařízení v aplikaci Azure IoT Central. 

Přehled najdete v tématu [co je Azure IoT Central (funkce verze Preview)?](overview-iot-central.md). 

IoT Edge se skládá ze tří součástí:
* **Moduly IoT Edge** jsou kontejnery, ve kterých běží služby Azure, partnerské služby nebo vlastní kód. Moduly se nasazují na zařízení IoT Edge a spustí se místně na těchto zařízeních.
* Modul **runtime IoT Edge** běží na každém zařízení IoT Edge a spravuje moduly nasazené na každé zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat IoT Edge zařízení. IoT Central je cloudové rozhraní.

Zařízení **Azure IoT Edge** může být zařízení brány se zařízeními, která se připojují k IoT Edgemu zařízení. Tento kurz sdílí Další informace o vzorech připojení k navazujícím zařízením.

**Šablona zařízení** definuje možnosti zařízení a IoT Edge moduly. Mezi možnosti patří telemetrie, kterou modul odesílá, vlastnosti modulu a příkazy, na které modul reaguje.

V tomto kurzu vytvoříte šablonu zařízení snímače prostředí. Zařízení snímače prostředí:

* Odesílá telemetrii, například teplotu.
* Reaguje na zapisovatelné vlastnosti při aktualizaci v cloudu, jako je například interval odesílání telemetrie.
* Reaguje na příkazy, jako je resetování teploty.

V tomto kurzu taky vytvoříte šablonu zařízení brány prostředí. Zařízení brány pro prostředí:

* Odesílá telemetrii, například teplotu.
* Reaguje na zapisovatelné vlastnosti při aktualizaci v cloudu, jako je například interval odesílání telemetrie.
* Reaguje na příkazy, jako je resetování teploty.
* Umožňuje vztahy k jiným modelům schopností zařízení.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte novou šablonu zařízení Azure IoT Edge zařízení.
> * Nahrajte manifest nasazení.
> * Vytvářejte možnosti, včetně telemetrie, vlastností a příkazů pro každý modul.
> * Definujte vizualizaci pro telemetrii modulu.
> * Přidejte relace do šablon zařízení pro příjem dat.
> * Publikujte šablonu zařízení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete [vytvořit aplikaci Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Vztahy navazujících zařízení s bránou a moduly

Podřízená zařízení se mohou připojit k zařízení IoT Edge brány prostřednictvím modulu `$edgeHub`. Toto zařízení IoT Edge se v tomto scénáři stávají transparentní bránou.

![Diagram transparentní brány](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Podřízená zařízení se taky můžou připojit k zařízení IoT Edge brány prostřednictvím vlastního modulu. V následujícím scénáři se pro zařízení se zařízením připojí vlastní modul Modbus.

![Diagram připojení vlastního modulu](./media/tutorial-define-edge-device-type/gateway-module.png)

Následující diagram znázorňuje připojení k zařízení IoT Edge brány prostřednictvím obou typů modulů (vlastní a `$edgeHub`).  

![Diagram připojení prostřednictvím obou připojovacích modulů](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

V konečném případě se mohou zařízení pro příjem dat připojit k zařízení IoT Edge brány prostřednictvím několika vlastních modulů. Následující diagram zobrazuje zařízení, která se připojují prostřednictvím vlastního modulu Modbus, vlastního modulu v police a modulu `$edgeHub`. 

![Diagram připojení přes více vlastních modulů](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete v aplikaci vytvořit a upravit IoT Edge šablony zařízení. Po publikování šablony zařízení můžete propojit skutečná zařízení, která implementují šablonu zařízení.

### <a name="select-device-template-type"></a>Vybrat typ šablony zařízení 

Chcete-li do aplikace přidat novou šablonu zařízení, v levém podokně vyberte **šablony zařízení** .

![Snímek obrazovky aplikace ve verzi Preview se zvýrazněnými šablonami zařízení](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Vyberte **+ Nová** a začněte vytvářet novou šablonu zařízení.

![Obrazovka stránky šablony zařízení s novým zvýrazněným](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Na stránce **Vybrat typ šablony** vyberte **Azure IoT Edge**a vyberte **Další: přizpůsobit**.

![Snímek obrazovky s výběrem stránky typu šablony](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Přizpůsobení šablony zařízení

V IoT Edge můžete nasadit a spravovat obchodní logiku ve formě modulů. IoT Edge moduly jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Pokud chcete pochopit, jak se moduly vyvíjí, nasazují a udržují, přečtěte si téma [IoT Edge moduly](../../iot-edge/iot-edge-modules.md).

Manifest nasazení je na vysoké úrovni seznamem vláken modulu, které jsou nakonfigurovány s požadovanými vlastnostmi. Manifest nasazení oznamuje IoT Edge zařízení (nebo skupině zařízení), které moduly se mají nainstalovat, a jak je nakonfigurovat. Manifesty nasazení obsahují požadované vlastnosti pro každý modul s dvojitou vlastností. IoT Edge zařízení hlásí zpět ohlášené vlastnosti pro každý modul.

Pomocí Visual Studio Code vytvořit manifest nasazení. Další informace najdete v tématu [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Tady je základní manifest nasazení s jedním modulem jako příkladem, který se má použít pro tento kurz. Zkopírujte následující kód JSON a uložte ho jako soubor. JSON. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Nahrání manifestu nasazení IoT Edge

Na stránce **přizpůsobit zařízení** v části **nahrát manifest nasazení Azure IoT Edge**vyberte **Procházet**. 

![Snímek obrazovky s přizpůsobením stránky zařízení se zvýrazněnou možností Procházet](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Pokud plánujete vytvořit šablonu zařízení IoT Edge Gateway, ujistěte se, že jste vybrali **zařízení brány se zařízeními pro příjem dat**.

![Snímek obrazovky s přizpůsobením stránky zařízení se zařízením brány se zvýrazněnými zařízeními pro příjem dat](./media/tutorial-define-edge-device-type/gateway-upload.png)

V dialogovém okně Výběr souboru vyberte soubor manifestu nasazení a vyberte **otevřít**.

IoT Edge ověří soubor manifestu nasazení proti schématu. Pokud je ověření úspěšné, vyberte **zkontrolovat**.

![Snímek obrazovky s přizpůsobením stránky zařízení s manifestem nasazení a zvýrazněnou revizí](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Následující vývojový diagram znázorňuje životní cyklus manifestu nasazení v IoT Central.

![Vývojový diagram životního cyklu manifestu nasazení](./media/tutorial-define-edge-device-type/dmflow.png)

V dalším kroku se zobrazí stránka pro kontrolu s podrobnostmi o manifestu nasazení. Tato stránka zobrazuje seznam modulů z manifestu nasazení. V tomto kurzu si všimněte, že je uvedený modul `SimulatedTemperatureSensor`. Vyberte **Create** (Vytvořit).

![Snímek obrazovky se stránkou pro revize s modulem a zvýrazněnou možností vytvořit](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Pokud jste vybrali zařízení brány, zobrazí se následující stránka pro kontrolu.

![Snímek obrazovky se zvýrazněnou Azure IoT Edge bránou](./media/tutorial-define-edge-device-type/gateway-review.png)


Vytvoříte šablonu zařízení s modely schopností modulu. V tomto kurzu vytvoříte šablonu zařízení s modelem schopností modulu `SimulatedTemperatureSensor`. 

Změňte název šablony zařízení na **šablonu zařízení snímače prostředí**.

![Snímek šablony zařízení s zvýrazněným aktualizovaným nadpisem](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

V IoT Edge zařízení technologie Plug and Play model IoT následujícím způsobem:
* Každá šablona zařízení IoT Edge má model schopností zařízení.
* Pro každý vlastní modul uvedený v manifestu nasazení je vygenerován model schopností modulu.
* Mezi jednotlivými modely schopností modulu a modelem schopností zařízení je vytvořen vztah.
* Model schopnosti modulu implementuje rozhraní modulů.
* Každé rozhraní modulů obsahuje telemetrii, vlastnosti a příkazy.

![Diagram modelování IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Přidání schopností do modelu schopností modulu

Tady je ukázkový výstup z modulu `SimulatedTemperatureSensor`:
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Do modulu `SimulatedTemperatureSensor` můžete přidat funkce, které budou odrážet předchozí výstup. 

1. Pokud chcete spravovat rozhraní modelu schopností modulu `SimulatedTemperatureSensor`, vyberte **spravovat** > **Přidat možnost**. 

    ![Snímek šablony senzoru prostředí se zvýrazněnou funkcí přidat](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Přidejte počítač jako typ objektu.
  
    ![Snímek obrazovky se stránkou možností šablon senzorů prostředí se zvýrazněným schématem](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Vyberte **definovat**. V dialogovém okně, které se zobrazí, změňte název objektu na **Machine**. Vytvořte vlastnosti teploty a tlaku a vyberte **použít**.
  
    ![Snímek obrazovky dialogového okna atributů s různými možnostmi zvýraznění](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Přidejte **okolí** jako typ objektu.

1. Vyberte **definovat**. V dialogovém okně, které se zobrazí, změňte název objektu na **Ambient**. Vytvořte vlastnosti teploty a vlhkosti a vyberte **použít**.
  
    ![Snímek obrazovky dialogového okna atributů s různými možnostmi zvýraznění](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Přidejte `timeCreated` jako typ `DateTime` a vyberte **Uložit**.
  
    ![Snímek šablony senzoru prostředí se zvýrazněnou možností Uložit](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Přidat relace

Pokud jste jako zařízení brány vybrali IoT Edge zařízení, můžete pro zařízení, která chcete připojit k zařízení brány, přidat podřízené vztahy k modelům schopností zařízení.
  
  ![Snímek šablony brány prostředí se zvýrazněnou možností přidat relaci](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Můžete přidat relaci na zařízení nebo v modulu.
  
  ![Snímek obrazovky s šablonou brány prostředí se zvýrazněnými vztahy na úrovni zařízení a modulu](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Můžete vybrat model schopností pro příjem dat, nebo můžete vybrat symbol hvězdičky. 
  
  ![Snímek šablony brány prostředí se zvýrazněnou možností cíl](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  V tomto kurzu vyberte hvězdičku. Tato možnost povolí jakýkoli vztah podřízenosti. Potom vyberte **Uložit**.

  ![Snímek šablony brány prostředí se zvýrazněnou možností cíl](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** >  **+ přidat cloudovou vlastnost**. Pomocí informací v následující tabulce můžete přidat vlastnost cloudu do šablony zařízení.

    | Zobrazované jméno      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Datum   |
    | Jméno zákazníka     | Žádné          | Řetězec |

2. Vyberte **Save** (Uložit).

  
    ![Snímek šablony senzoru prostředí se zvýrazněnou možností Uložit](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Přidat přizpůsobení

Pomocí přizpůsobení můžete změnit rozhraní nebo přidat funkce specifické pro IoT Central, které nevyžadují, abyste používali model schopností zařízení. Můžete přizpůsobit pole, když je model schopností v konceptu nebo publikovaném stavu. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti.

Až budete hotovi s přizpůsobením, vyberte **Uložit**.
  
![Snímek obrazovky se stránkou pro přizpůsobení šablony senzoru prostředí](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurace zobrazení pro vizualizaci zařízení

Řídicí panel zařízení umožňuje, aby operátor vizualizuje zařízení pomocí grafů a metrik. Jako tvůrce můžete definovat, jaké informace se zobrazí na řídicím panelu zařízení. Pro zařízení můžete definovat několik řídicích panelů. Pokud chcete vytvořit řídicí panel pro vizualizaci telemetrie senzorů pro životní prostředí, vyberte **zobrazení** > **vizualizaci zařízení**:

  
![Obrazovka stránky zobrazení šablon senzoru prostředí s vizualizací zvýrazněné zařízení](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Ambientní telemetrie a telemetrie počítačů jsou komplexní objekty. Vytváření grafů:

1. Přetáhněte **ambientní telemetrii**a vyberte **Spojnicový graf**. 
  
   ![Snímek šablony senzoru prostředí se zvýrazněnou ambientní telemetrie a spojnicovým grafem](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Vyberte ikonu konfigurovat. Vyberte možnost **teplota** a **vlhkost** pro vizualizaci dat a vyberte **aktualizovat konfiguraci**. 
  
   ![Snímek šablony senzoru prostředí s vybranými různými možnostmi](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Vyberte **Save** (Uložit).

Můžete přidat další dlaždice, které zobrazují další vlastnosti nebo hodnoty telemetrie. Můžete také přidat statický text, odkazy a obrázky. Chcete-li přesunout nebo změnit velikost dlaždice na řídicím panelu, přesuňte ukazatel myši na dlaždici a přetáhněte dlaždici na nové umístění nebo změňte jeho velikost.
  
![Snímek obrazovky s zobrazením řídicího panelu šablony senzoru prostředí](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Přidat formulář zařízení

Formulář zařízení umožňuje operátorovi upravovat vlastnosti zapisovatelného zařízení a cloudové vlastnosti. Jako tvůrce můžete definovat několik forem a vybrat, které vlastnosti zařízení a cloudu se mají na každém formuláři zobrazit. Ve formuláři můžete také zobrazit vlastnosti zařízení jen pro čtení.

Vytvoření formuláře pro zobrazení a úpravy vlastností senzoru životního prostředí:

1. V **šabloně snímače životního prostředí**, přejít na **zobrazení**. Kliknutím na dlaždici pro **úpravu zařízení a cloudových dat** přidejte nové zobrazení.
  
   ![Snímek obrazovky se stránkou zobrazení šablony senzoru prostředí se zvýrazněnými úpravami zařízení a cloudových dat](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Zadejte název formuláře **vlastnosti senzoru prostředí**.

1. Přetáhněte **název zákazníka** a poslední vlastnosti cloudového **data služby** do existující části formuláře.
  
   ![Obrazovka stránky zobrazení šablon senzoru v prostředí s vybranými různými možnostmi](./media/tutorial-define-edge-device-type/views-properties.png)

1. Vyberte **Save** (Uložit).

## <a name="publish-a-device-template"></a>Publikování šablony zařízení

Než budete moct vytvořit simulovaný senzor pro životní prostředí nebo připojit reálný senzor pro životní prostředí, budete muset publikovat šablonu zařízení.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** můžete přejít na šablonu zařízení.

2. Vyberte **Publikovat**.
  
    ![Snímek šablony environmentálního senzoru se zvýrazněnou možností publikovat](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. V dialogovém okně **publikovat šablonu zařízení** klikněte na tlačítko **publikovat**.
  
    ![Snímek obrazovky dialogového okna publikování šablony zařízení s zvýrazněným publikováním](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. V publikované šabloně zařízení ale můžete dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení. Tyto aktualizace nezpůsobí vytvoření nové verze. Až provedete nějaké změny, vyberte **publikovat** a přehrajte tyto změny do svého operátoru.
  
![Snímek obrazovky s seznamem publikovaných šablon zařízení](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoří novou hranu jako šablonu na listovém zařízení.
* Generuje moduly z nahraného manifestu nasazení.
* Přidejte telemetrii a vlastnosti pro komplexní typy.
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Publikujte šablonu hraničního zařízení.

Teď, když jste v aplikaci Azure IoT Central vytvořili šablonu zařízení, můžete to udělat dál:

> [!div class="nextstepaction"]
> [Připojit zařízení](./tutorial-connect-pnp-device.md)
