---
title: Definování nového typu Azure IoT Edge zařízení v Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte jako tvůrce, jak vytvořit nové zařízení Azure IoT Edge v aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 814b88c4632551ff455eae848c8e2d50b1806dc5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955974"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Kurz: definování nového typu Azure IoT Edge zařízení v aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte jako tvůrce, jak použít šablonu zařízení k definování nového typu Azure IoT Edge zařízení v aplikaci Azure IoT Central. 

Pokud chcete získat přehled o Azure IoT Edge, [Přečtěte si tento článek](overview-iot-central-pnp.md). 

Azure IoT Edge se skládá ze tří komponent:
* **Moduly IoT Edge** jsou kontejnery, ve kterých běží služby Azure, služby třetích stran nebo váš vlastní kód. Moduly se nasazují do IoT Edge zařízení a lokálně se spouštějí na těchto zařízeních.
* **IoT Edge runtime** běží na každém zařízení IoT Edge a spravuje moduly nasazené na každé zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat IoT Edge zařízení. IoT Central bude cloudové rozhraní.

Zařízení **Azure IoT Edge** může být zařízení brány s připojenými zařízeními, která se připojují k Azure IoT Edgemu zařízení. V tomto kurzu budou popsány vzory připojení k navazujícím zařízením.

**Šablona zařízení** definuje možnosti vašeho zařízení & IoT Edge moduly. Mezi možnosti patří telemetrie, kterou modul odesílá, vlastnosti modulu a příkazy, na které modul reaguje.

V tomto kurzu vytvoříte šablonu zařízení **snímače prostředí** . Zařízení snímače prostředí:

* Odesílá telemetrii jako teplotu.
* Reaguje na zapisovatelné vlastnosti při aktualizaci v cloudu, jako je například interval odesílání telemetrie.
* Reaguje na příkazy, jako je resetování teploty.

V tomto kurzu taky vytvoříte šablonu zařízení **brány prostředí** . Zařízení brány pro prostředí:

* Odesílá telemetrii jako teplotu.
* Reaguje na zapisovatelné vlastnosti při aktualizaci v cloudu, jako je například interval odesílání telemetrie.
* Reaguje na příkazy, jako je resetování teploty.
* Umožňuje vztahy k jiným modelům schopností zařízení.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte novou šablonu zařízení Azure IoT Edge zařízení.
> * Nahrajte manifest nasazení.
> * Vytváření schopností, včetně telemetrie, vlastností a příkazů pro každý modul
> * Definujte vizualizaci pro telemetrii modulu.
> * Přidání vztahů k šablonám zařízení pro příjem dat
> * Publikujte šablonu zařízení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. V tomto rychlém startu [vytvoříte aplikaci Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Vztahy navazujících zařízení s moduly & brány

Podřízená zařízení se mohou připojit k zařízení Azure IoT Edge brány prostřednictvím modulu $edgeHub. Toto zařízení Azure IoT Edge se v tomto scénáři stávají transparentní bránou.

![Stránka centrální aplikace](./media/tutorial-define-edge-device-type-pnp/gateway-transparent.png)

Podřízená zařízení se můžou připojit k zařízení Azure IoT Edge brány prostřednictvím vlastního modulu. V níže uvedeném scénáři se zařízení se připojují prostřednictvím vlastního modulu Modbus a podřízená zařízení se mohou připojit k zařízení Azure IoT Edge brány prostřednictvím modulu $edgeHub.

![Stránka centrální aplikace](./media/tutorial-define-edge-device-type-pnp/gateway-module.png)

Podřízená zařízení se můžou připojit k zařízení Azure IoT Edge brány prostřednictvím vlastního modulu. V níže uvedeném scénáři se zařízení připojují prostřednictvím vlastního modulu Modbus. 

![Stránka centrální aplikace](./media/tutorial-define-edge-device-type-pnp/gateway-module-transparent.png)

Podřízená zařízení se můžou připojit k zařízení Azure IoT Edge brány prostřednictvím několika vlastních modulů. V níže uvedeném scénáři se zařízení se připojují prostřednictvím vlastního modulu Modbus, vlastní modul a podřízená zařízení v tabulce se můžou připojit k zařízení Azure IoT Edge brány prostřednictvím modulu $edgeHub. 

![Stránka centrální aplikace](./media/tutorial-define-edge-device-type-pnp/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete v aplikaci vytvořit a upravit Azure IoT Edge šablony zařízení. Po publikování šablony zařízení můžete propojit skutečná zařízení, která implementují šablonu zařízení.

### <a name="select-device-template-type"></a>Vybrat typ šablony zařízení 

Chcete-li do aplikace přidat novou šablonu zařízení, otevřete stránku **šablony zařízení** . To provedete tak, že v levém podokně vyberete kartu **šablony zařízení** .

![Stránka centrální aplikace](./media/tutorial-define-edge-device-type-pnp/edgedevicetemplate.png)

Kliknutím na **+ Nový** Začněte vytvářet novou šablonu zařízení.

![Šablony zařízení – nové](./media/tutorial-define-edge-device-type-pnp/edgedevicetemplatenew.png)

Na stránce Výběr typu šablony zařízení se dostanete. Vyberte **Azure IoT Edge** dlaždici a klikněte na **Další: tlačítko Přizpůsobit** v dolní části.

![Výběr šablon zařízení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/selectiotedge.png)

### <a name="customize-device-template"></a>Přizpůsobení šablony zařízení

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku ve formě modulů. **Azure IoT Edge moduly** jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Abyste pochopili, jak se moduly vyvíjí, nasazují a udržují moduly pro čtení [IoT Edge](../../iot-edge/iot-edge-modules.md).

Manifest nasazení je na vysoké úrovni seznamem vláken modulu, které jsou nakonfigurovány s požadovanými vlastnostmi. Manifest nasazení oznamuje IoT Edge zařízení (nebo skupině zařízení), které moduly se mají nainstalovat a jak je nakonfigurovat. Manifesty nasazení obsahují požadované vlastnosti pro každý modul s dvojitou vlastností. IoT Edge zařízení hlásí zpět ohlášené vlastnosti pro každý modul.

Pomocí Visual Studio Code vytvořit manifest nasazení. Postup vytvoření [manifestu nasazení](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)najdete v dokumentaci.

Tady je základní manifest nasazení s jedním modulem jako příkladem, který se má použít pro tento kurz. Zkopírujte níže JSON a uložte ho jako soubor. JSON. 

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

**Nahrání manifestu nasazení Azure IoT Edge**

Klikněte na tlačítko **Procházet** . 

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgedevicetemplateuploadmanifest.png)

Pokud máte v plánu vytvořit šablonu zařízení Azure IoT Edge brány, ujistěte se, že jste zaškrtli políčko **zařízení brány s nenavazujícími zařízeními** .

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-upload.png)

Zobrazí se dialogové okno pro výběr souboru. Vyberte soubor manifestu nasazení a klikněte na tlačítko **otevřít** .

Soubor manifestu nasazení bude ověřen proti schématu. Po úspěšném ověření klikněte na tlačítko pro **kontrolu** .

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/deploymentmanifestvalidate.png)

Níže je tok životního cyklu manifestu nasazení v IoT Central.

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/dmflow.png)

Zobrazí se stránka s podrobnostmi o manifestu nasazení. Seznam modulů z manifestu nasazení se zobrazí na stránce Kontrola. V tomto kurzu se zobrazí seznam SimulatedTemperatureSensor modul. Klikněte na tlačítko **vytvořit** .

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgedevicetemplatereview.png)

Pokud jste vybrali zařízení brány, zobrazí se tato stránka s touto kontrolou

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-review.png)


Vytvoří se nová číselník pro šablonu zařízení, ve kterém se vytvoří šablona zařízení v IoT Central.

Šablona zařízení se vytváří s modely schopností modulu. V tomto kurzu uvidíte model funkce modulu SimulatedTemperatureSensor vytvořit. 

Změňte název šablony zařízení na šablonu zařízení snímače prostředí.

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgedevicetemplatelanding.png)

Modelování Azure IoT Edge zařízení Plug n Play se provádí takto:
* Každá šablona zařízení Azure IoT Edge bude mít **model schopností zařízení** .
* Pro každý vlastní modul uvedený v manifestu nasazení se vygeneruje **model schopností modulu** .
* Mezi jednotlivými modely schopností modulu a modelem schopností zařízení bude vytvořen **vztah** .
* Model schopnosti modulu implementuje **rozhraní modulů** .
* Každé rozhraní modulu obsahuje
   - Telemetrie
   - Vlastnosti
   - Příkazy

![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgemodelling.png)

**Přidání funkcí do modelu schopností modulu**

Tady je ukázkový výstup z modulu SimulatedTemperatureSensor.
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

Přidejte do modulu SimulatedTemperatureSensor možnosti, které budou odpovídat výše uvedenému formátu JSON. 

* Klikněte na **Spravovat** rozhraní modelu schopností modulu SimulatedTemperatureSensor. Klikněte na tlačítko **Přidat možnost**. 

    ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplateaddcapability.png)
  
* Přidat počítač jako typ objektu, protože se jedná o komplexní typ
  
    ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplatemachineobject.png)

    Klikněte na **definovat**. V překryvném okně modální Změna název objektu na počítač a vytváření vlastností teplota, tlak a klikněte na **použít** .
  
    ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplatemachineattributes.png)
  
* Přidání okolí jako typu objektu, protože se jedná o komplexní typ

    Klikněte na **definovat**. V překryvném okně modální Změna název objektu změňte na Ambient a vytvořte vlastnosti teploty, vlhkosti a klikněte na **použít** .
  
    ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplateambientattributes.png)

  
* Přidejte timeCreated jako typ DateTime a klikněte na **Uložit** .
  
    ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Přidat relace

Pokud jste vybrali Azure IoT Edge zařízení jako zařízení brány, můžete přidat podřízené vztahy k modelům schopností zařízení pro zařízení, která se připojíte k zařízení brány.
  
  ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-add-relationship.png)

Relace se dá přidat na zařízení nebo v modulu.
  
  ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-relationship-types.png)


Můžete vybrat model schopností pro příjem dat nebo vybrat hvězdičky. 
  
  ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-downstream-rel.png)

  Pro tento kurz vybereme hvězdičky, což znamená, že bude povolený jakýkoli vztah podřízenosti. Klikněte na **Uložit**.

  ![Šablona zařízení-Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. Pomocí informací v následující tabulce můžete přidat vlastnost cloudu do šablony zařízení.

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Datum   |
    | Jméno zákazníka     | Žádné          | Řetězec |

2. Kliknutím na **Uložit** uložte změny:

  
    ![Vlastnosti cloudu – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Přidat přizpůsobení

Vlastní nastavení použijte v případě, že potřebujete změnit rozhraní nebo přidat funkce specifické pro IoT Central, které nevyžadují, abyste si využívali verzi modelu schopností zařízení. Můžete přizpůsobit pole, když je model schopností v konceptu nebo publikovaném stavu. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti. Klikněte na **Uložit**.
  
![Přizpůsobení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/edgetemplatecustomize.png)


### <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurace zobrazení pro vizualizaci zařízení

Řídicí panel zařízení umožňuje, aby operátor vizualizuje zařízení pomocí grafů a metrik. Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. Pro zařízení můžete definovat několik řídicích panelů. Pokud chcete vytvořit řídicí panel pro vizualizaci telemetrie senzorů pro životní prostředí, vyberte **zobrazení** a pak **Vizualizujte zařízení**:

  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/visualizingthedevice.png)


Ambientní telemetrie a telemetrie počítačů jsou komplexní objekty, které slouží k vytváření grafů:

Přetáhněte ambientní telemetrii a vyberte spojnicový graf. 
  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/sensorambientchart.png)

Klikněte na ikonu konfigurovat a vyberte teplotu a vlhkost pro vizualizaci dat a klikněte na tlačítko **aktualizovat konfiguraci** . 
  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/sensorambienttelemetrychart.png)

Kliknutím na **Uložit** uložte zobrazení:

Můžete přidat další dlaždice, které zobrazují další vlastnosti nebo hodnoty telemetrie. Můžete také přidat statický text, odkazy a obrázky. Chcete-li přesunout nebo změnit velikost dlaždice na řídicím panelu, přesuňte ukazatel myši na dlaždici a přetáhněte dlaždici na nové umístění nebo změňte jeho velikost.
  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/viewsdashboard.png)

### <a name="add-a-device-form"></a>Přidat formulář zařízení

Formulář zařízení umožňuje operátorovi upravovat vlastnosti zapisovatelného zařízení a cloudové vlastnosti. Jako tvůrce můžete definovat několik forem a vybrat, které vlastnosti zařízení a cloudu se mají na každém formuláři zobrazit. Ve formuláři můžete také zobrazit vlastnosti zařízení jen pro čtení.

Vytvoření formuláře pro zobrazení a úpravy vlastností senzoru životního prostředí:

Přejděte k **zobrazením** v šabloně **environmentálního senzoru** . Kliknutím na dlaždici pro **úpravu zařízení a cloudových dat** přidejte nové zobrazení.
  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/editingdeviceandclouddata.png)

Zadejte název formuláře **vlastnosti senzoru prostředí**.

Přetáhněte **název zákazníka** a poslední vlastnosti cloudového **data služby** do existující části formuláře.
  
![Zobrazení – Azure IoT Edge](./media/tutorial-define-edge-device-type-pnp/views-properties.png)

Kliknutím na **Uložit** uložte zobrazení.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Funkce generování výchozích zobrazení není pro šablony Azure IoT Edge podporována. 

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moct vytvořit simulovaný senzor pro životní prostředí nebo připojit reálný senzor pro životní prostředí, budete muset publikovat šablonu zařízení.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** můžete přejít na šablonu zařízení.

2. Vyberte **Publikovat**.
  
    ![Zobrazení – publikování](./media/tutorial-define-edge-device-type-pnp/edgetemplatepublish.png)

1. V dialogovém okně **publikovat šablonu zařízení** vyberte **publikovat**:
  
    ![Zobrazení – publikování](./media/tutorial-define-edge-device-type-pnp/edgepublishtemplate.png)

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. Můžete ale dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení změn vyberte **publikovat** , aby se tyto změny převedly do vašeho operátoru.
  
![Zobrazení – publikování](./media/tutorial-define-edge-device-type-pnp/publishedtemplate.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvořit novou hranici jako šablonu na listovém zařízení
* Generovat moduly z nahraného manifestu nasazení
* Přidání telemetrie a vlastností komplexního typu
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Publikujte šablonu hraničního zařízení.

Teď, když jste vytvořili šablonu zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Přidat hraniční zařízení](tutorial-add-edge-as-leaf-device.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
