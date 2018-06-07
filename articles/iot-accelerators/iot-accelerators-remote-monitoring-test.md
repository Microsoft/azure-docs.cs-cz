---
title: Simulace zařízení v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak používat v simulátoru zařízení s vzdálené monitorování akcelerátoru řešení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: d8a528265acc3e0bee24da6c1b6130082815b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628255"
---
# <a name="create-a-new-simulated-device"></a>Vytvoření nového simulovaného zařízení

V tomto kurzu se dozvíte, jak přizpůsobit mikroslužbu simulátoru zařízení v akcelerátoru řešení vzdáleného monitorování. Chcete-li zobrazit možnosti v simulátoru zařízení, tento kurz používá dva scénáře v aplikaci Contoso IoT.

Následující video obsahuje přehled možností pro přizpůsobení mikroslužbu simulátoru zařízení:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

V prvního scénáře Contoso chce otestovat nového inteligentní žárovek zařízení. Pokud chcete provést testy, vytvořte nového simulovaného zařízení s následujícími charakteristikami:

*Vlastnosti*

| Název                     | Hodnoty                      |
| ------------------------ | --------------------------- |
| Barva                    | Prázdné, červená, modrá            |
| Také průraznost               | 0 až 100                    |
| Odhadovaný zbývající dobu životnosti | Odpočítávání 10 000 hodiny |

*Telemetrie*

Následující tabulka obsahuje data že žárovek sestav v cloudu jako datový proud:

| Název   | Hodnoty      |
| ------ | ----------- |
| Status | "na" "off" |
| Teplota | Stupňů F |
| online | Hodnota TRUE, false |

> [!NOTE]
> **Online** hodnota telemetrie je povinná pro všechny simulované typy.

*Metody*

V následující tabulce jsou uvedeny akce, které podporuje nové zařízení:

| Název        |
| ----------- |
| Přepnout   |
| Vypnout  |

*Počáteční stav*

Následující tabulka uvádí počáteční stav zařízení:

| Název                     | Hodnoty |
| ------------------------ | -------|
| Počáteční barvu            | Bílá  |
| Počáteční také průraznost       | 75     |
| Počáteční zbývající dobu životnosti   | 10 000 |
| Stav počáteční telemetrie | "na"   |
| Teplotní telemetrie počáteční | 200   |

Druhý scénář, přidáte nový typ telemetrie a Contoso existující **chladič** zařízení.

V tomto kurzu se dozvíte, jak používat v simulátoru zařízení s akcelerátoru řešení vzdáleného monitorování:

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vytvořte nový typ zařízení
> * Simulovat chování vlastní zařízení
> * Přidejte nový typ zařízení na řídicí panel
> * Odeslat vlastní telemetrii z existující typ zařízení

Následující video ukazuje návod simulované a skutečné zařízení připojovat k řešení vzdáleného monitorování:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Požadavky

Chcete-li v tomto kurzu, je třeba:

* Instance nasazené řešení vzdáleného monitorování ve vašem předplatném Azure. Pokud jste nenasadili řešení vzdáleného monitorování ještě by se měla Dokončit [nasazení akcelerátoru řešení vzdáleného monitorování](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) kurzu.

* Sada Visual Studio 2017. Pokud nemáte nainstalované Visual Studio 2017, si můžete stáhnout bezplatnou [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Cloudové aplikace Explorer pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) rozšíření sady Visual Studio.

* Účet na [úložiště Docker Hub](https://hub.docker.com/). Můžete si zaregistrovat bezplatné začít pracovat.

* [Git](https://git-scm.com/downloads) nainstalovaný na počítači klientů.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Proveďte následující úkoly Příprava vývojového prostředí pro přidání nového simulovaného zařízení do řešení pro vzdálené monitorování:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Konfigurace SSH přístup k virtuálnímu počítači řešení v Azure

Při vytváření řešení vzdáleného monitorování v [www.azureiotsolutions.com](https://www.azureiotsolutions.com), vyberete název řešení. Název řešení se změní na název skupiny prostředků Azure, která obsahuje různé nasazené prostředky, které používá řešení. Následující příkazy použijte skupinu prostředků s názvem **Contoso-01**, měli byste nahradit **Contoso-01** s názvem vaší skupiny prostředků.

Následující příkazy použijte `az` příkaz [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Můžete nainstalovat Azure CLI 2.0 na počítači pro vývoj nebo pomocí [cloudové prostředí](https://docs.microsoft.com/azure/cloud-shell/overview) v [portál Azure](http://portal.azure.com). Azure CLI 2.0 je předinstalován v prostředí cloudu.

1. Pokud chcete ověřit název skupiny prostředků, která obsahuje vaše prostředky vzdálené monitorování, spusťte následující příkaz:

    ```sh
    az group list | grep "name"
    ```

    Tento příkaz vypíše všechny skupiny prostředků v rámci vašeho předplatného. V seznamu by měla obsahovat skupinu prostředků se stejným názvem jako řešení vzdáleného monitorování.

1. Chcete-li skupinu jako výchozí pro následné příkazy prostředku, spusťte následující příkaz, pomocí vaší název skupiny prostředků v místě **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Seznam prostředků ve vaší skupině prostředků, spusťte následující příkaz:

    ```sh
    az resource list -o table
    ```

    Poznamenejte si názvy virtuální počítač a vaše skupina zabezpečení sítě. Použít tyto hodnoty v dalších krocích.

1. Pokud chcete povolit přístup SSH virtuálního počítače, spusťte následující příkaz pomocí názvu skupiny zabezpečení sítě v předchozím kroku:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Chcete-li zobrazit seznam příchozích pravidel pro vaši síť, spusťte následující příkaz:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Chcete-li změnit heslo pro virtuální počítače na heslo, které znáte, spusťte následující příkaz. Použijte název virtuálního počítače, které jste si poznamenali dříve a heslo podle vašeho výběru:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Chcete-li najít IP adresu virtuálního počítače, použijte následující příkaz a poznamenejte si veřejnou IP adresu:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. SSH tu teď můžete použít pro připojení k virtuálnímu počítači. `ssh` Příkaz je předinstalován v prostředí cloudu. Použijte veřejnou IP adresu z předchozího kroku, a po zobrazení výzvy heslo, můžete nakonfigurovat pro virtuální počítač:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Nyní máte přístup k prostředí virtuální počítač, který spustí kontejnery Docker v řešení vzdáleného monitorování. Chcete-li zobrazit spuštěné kontejnery, použijte následující příkaz:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Vyhledání služby připojovací řetězce

V tomto kurzu pracovat s řešení sady Visual Studio, který se připojuje ke službám Cosmos DB a služby IoT Hub na řešení. Následující kroky vám ukážou jeden způsob, jak najít připojení řetězcové hodnoty, které potřebujete:

1. Najít připojovací řetězec databáze Cosmos, spusťte následující příkaz v relaci SSH připojen k virtuálnímu počítači:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Poznamenejte si připojovací řetězec. Tuto hodnotu použijete později v tomto kurzu.

1. Pokud chcete vyhledat připojovací řetězec služby IoT Hub, spusťte následující příkaz v relaci SSH připojen k virtuálnímu počítači:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Poznamenejte si připojovací řetězec. Tuto hodnotu použijete později v tomto kurzu.

> [!NOTE]
> Můžete také vyhledat tyto řetězce připojení na portálu Azure nebo pomocí `az` příkaz.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Zastavte službu simulace zařízení ve virtuálním počítači.

Když upravíte službu simulace zařízení, můžete spustit místně a otestovat změny. Než spustíte službu simulace zařízení místně, je nutné zastavit instance běží na virtuálním počítači následujícím způsobem:

1. Najít **ID KONTEJNERU** z **zařízení simulace** služby, spusťte následující příkaz v relaci SSH připojen k virtuálnímu počítači:

    ```sh
    docker ps
    ```

    Poznamenejte si ID kontejneru **zařízení simulace** služby.

1. Chcete-li zastavit **zařízení simulace** kontejneru, spusťte následující příkaz:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Naklonujte úložiště GitHub

V tomto kurzu pracujete s **zařízení simulace** a **adaptéru úložiště** projektů sady Visual Studio. Může klonovat úložišť zdrojového kódu z Githubu. Tento krok proveďte na místním vývojovém počítači kde máte nainstalovanou sadu Visual Studio:

1. Otevřete příkazový řádek a přejděte do složky, kam chcete uložit kopii **zařízení simulace** a **adaptéru úložiště** úložišť GitHub.

1. Klonování verze .NET **zařízení simulace** úložiště, spusťte následující příkaz:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Služba simulace zařízení v řešení vzdáleného monitorování umožňuje změnit typy předdefinované simulované zařízení a pro vytváření nových simulated typy zařízení. Typy vlastní zařízení můžete použít k testování chování řešení vzdáleného monitorování, než připojíte fyzických zařízení.

1. Klonování verze .NET **adaptéru úložiště** úložiště, spusťte následující příkaz:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Služba simulace zařízení používá službu úložiště adaptér pro připojení ke službě Cosmos DB v Azure. Řešení vzdáleného monitorování ukládá simulované zařízení konfigurační data do databáze Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Místní spuštění adaptér služby úložiště

Služba simulace zařízení používá službu úložiště adaptér pro připojení k databázi na řešení Cosmos DB. Pokud spustíte službu simulace zařízení místně, je nutné spustit také službu úložiště adaptér místně. Následující kroky vám ukážou, jak spustit službu adaptér úložiště ze sady Visual Studio:

1. V sadě Visual Studio, otevřete **počítačů storage-adapter.sln** soubor řešení ve vaší místní klon **adaptéru úložiště** úložiště.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **WebService** projektu, zvolte **vlastnosti**a potom vyberte **ladění**.

1. V **proměnné prostředí** část, upravte hodnotu **počítače\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** proměnná Cosmos DB připojení řetězec, který jste si poznamenali dříve. Potom změny uložte.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **webovou službu** projektu, zvolte **ladění**a potom zvolte **spustit novou instanci**.

1. Služba spuštěn místně a otevře `http://localhost:9022/v1/status` ve výchozím prohlížeči. Ověřte, zda **stav** hodnota je "OK: zachování připojení a dobře."

1. Ponechte službu adaptéru úložiště, který je spuštěn místně, dokud jste dokončili kurz.

Nyní máte všechno, co na místě a jste připraveni začít přidávat nový typ simulované zařízení do řešení vzdáleného monitorování.

## <a name="create-a-simulated-device-type"></a>Vytvoření simulovaného zařízení typu

Nejjednodušší způsob, jak vytvořit nový typ zařízení ve službě simulace zařízení je zkopírovat a upravit existující typ. Následující kroky vám ukážou, jak zkopírovat integrované **chladič** zařízení pro vytvoření nového **žárovek** zařízení:

1. V sadě Visual Studio, otevřete **zařízení simulation.sln** soubor řešení ve vaší místní klon **zařízení simulace** úložiště.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulationAgent** projektu, zvolte **vlastnosti**a potom zvolte **ladění**.

1. V **proměnné prostředí** část, upravte hodnotu **počítače\_IOTHUB\_CONNSTRING** proměnné jako připojovací řetězec služby IoT Hub jste si poznamenali dříve. Potom změny uložte.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **WebService** projektu, zvolte **vlastnosti**a potom vyberte **ladění**.

1. V **proměnné prostředí** část, upravte hodnotu **počítače\_IOTHUB\_CONNSTRING** proměnné jako připojovací řetězec služby IoT Hub jste si poznamenali dříve. Potom změny uložte.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **zařízení simulace** řešení a zvolte **nastavit projekty po spuštění**. Zvolte **jeden projekt po spuštění** a vyberte **WebService**. Pak klikněte na **OK**.

1. Každý typ zařízení má soubor modelu JSON a související skripty v **služby/data/devicemodels** složky. V Průzkumníku řešení, zkopírujte **chladič** soubory a vytvořte **žárovek** souborů, jak je znázorněno v následující tabulce:

    | Zdroj                      | Cíl                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | skripty nebo chladič-01-state.js | skripty nebo žárovek-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definovat vlastnosti nového typu zařízení

**Žárovek 01.json** souboru definuje vlastnosti typu, jako je například telemetrii generuje a metody podporuje. Následující postup aktualizace **žárovek 01.json** souboru definujte **žárovek** zařízení:

1. V **žárovek 01.json** souboru, jak je znázorněno v následujícím fragmentu kódu k aktualizaci metadat zařízení:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. V **žárovek 01.json** souboru, aktualizujte definici simulaci, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. V **žárovek 01.json** souboru, aktualizovat vlastnosti typu zařízení, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. V **žárovek 01.json** souboru, aktualizovat definice telemetrie typ zařízení, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. V **žárovek 01.json** souboru, jak je znázorněno v následujícím fragmentu kódu k aktualizaci metody typu zařízení:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Uložit **žárovek 01.json** souboru.

### <a name="simulate-custom-device-behavior"></a>Simulovat chování vlastní zařízení

**Skripty nebo žárovek-01-state.js** soubor definuje chování simulace **žárovek** typu. Následující postup aktualizace **skripty nebo žárovek-01-state.js** souboru k definování chování **žárovek** zařízení:

1. Upravit definici stavu v **skripty nebo žárovek-01-state.js** souboru, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Přidat **překlopit** po fungovat **lišit** funkce s následující definice:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Upravit **hlavní** funkce pro implementaci chování, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Uložit **skripty nebo žárovek-01-state.js** souboru.

**Skripty nebo SwitchOn-method.js** souboru implementuje **přepínač na** metoda v **žárovek** zařízení. Následující postup aktualizace **skripty nebo SwitchOn-method.js** souboru:

1. Upravit definici stavu v **skripty nebo SwitchOn-method.js** souboru, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Chcete-li přepnout na skutečnost, upravte **hlavní** funkce následujícím způsobem:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Uložit **skripty nebo SwitchOn-method.js** souboru.

1. Zkopírujte **skripty nebo SwitchOn-method.js** souboru s názvem **skripty nebo SwitchOff-method.js**.

1. Chcete-li vypnout skutečnost, upravte **hlavní** fungovat v **skripty nebo SwitchOff-method.js** následujícím způsobem:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Uložit **skripty nebo SwitchOff-method.js** souboru.

1. V Průzkumníku řešení vyberte všechny čtyři nové soubory naopak. V **vlastnosti** okna pro každý soubor, ověřte, že **kopírovat do výstupního adresáře** je nastaven na **kopírovat, pokud je novější**.

### <a name="configure-the-device-simulation-service"></a>Konfigurace služby simulace zařízení

Chcete-li omezit počet Simulovaná zařízení, která se připojují k řešení během testování, konfigurovat službu ke spuštění jedné chladič a jeden žárovek zařízení. Konfigurační data jsou uložena v instanci Cosmos databáze ve skupině prostředků řešení. Chcete-li upravit konfigurační data, použijte **Průzkumník cloudu** zobrazení v sadě Visual Studio:

1. Chcete-li otevřít **Průzkumník cloudu** zobrazit v sadě Visual Studio, vyberte **zobrazení** a potom **Průzkumník cloudu**.

1. Najít v dokumentu konfigurace simulace, **hledat prostředky** zadejte **simualtions.1**.

1. Dvakrát klikněte **simulations.1** dokumentu ji otevřete pro úpravy.

1. V hodnotě pro **Data**, vyhledejte **DeviceModels** pole, které vypadá jako následující fragment kódu:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Chcete-li definovat jeden chladič a jeden žárovek simulovaného zařízení, nahraďte **DeviceModels** pole s následujícím kódem:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Uložte změnu **simulations.1** dokumentu.

> [!NOTE]
> Můžete taky Průzkumníku dat DB Cosmos na portálu Azure můžete upravit **simulations.1** dokumentu.

### <a name="test-the-lightbulb-device-type-locally"></a>Typ zařízení žárovek místně testu

Nyní jste připraveni k testování nového typu simulované žárovek spuštěním projektu simulace zařízení místně.

1. V Průzkumníku řešení klikněte pravým tlačítkem na **WebService**, zvolte **ladění** a potom zvolte **spustit novou instanci**.

1. Pokud chcete zkontrolovat, že jsou připojené dva Simulovaná zařízení do služby IoT Hub, otevřete v prohlížeči na portálu Azure.

1. Přejděte do služby IoT hub ve skupině prostředků, která obsahuje vaše řešení vzdáleného monitorování.

1. V **monitorování** zvolte **metriky**. Ověřte, že počet **připojená zařízení** dvě:

    ![Počet připojených zařízení](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. V prohlížeči přejděte na **řídicí panel** pro vaše řešení vzdáleného monitorování. V panelu telemetrii na **řídicí panel**, vyberte **teploty**. Teplotní všechna Simulovaná zařízení zobrazí v grafu:

    ![Teplotní telemetrie](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Nyní máte simulaci zařízení žárovek spuštěn místně. Dalším krokem je nasazení aktualizované simulátoru kódu do virtuálního počítače, který spouští mikroslužeb vzdálené monitorování v Azure.

Než budete pokračovat, můžete zastavit ladění simulace zařízení i úložiště adaptér projekty v sadě Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Nasazení aktualizované simulátor do cloudu

V kontejnerech docker spustit mikroslužeb v řešení vzdáleného monitorování. Kontejnery hostovaným ve virtuálním počítači na řešení v Azure. V této části:

* Vytvořte novou bitovou kopii docker simulace zařízení.
* Odešlete bitovou kopii do úložiště docker hub.
* Importujte bitovou kopii do vašeho řešení virtuálního počítače.

Následující kroky předpokládají, že byl úložiště volána **žárovek** ve vašem účtu úložiště Docker Hub.

1. V sadě Visual Studio v **zařízení simulace** projektu, otevřete soubor **solution\scripts\docker\build.cmd**.

1. Upravíte řádek, který nastaví **DOCKER_IMAGE** proměnnou prostředí na název úložiště Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Uložte změnu.

1. V sadě Visual Studio v **zařízení simulace** projektu, otevřete soubor **solution\scripts\docker\publish.cmd**.

1. Upravíte řádek, který nastaví **DOCKER_IMAGE** proměnnou prostředí na název úložiště Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Uložte změnu.

1. Otevřete příkazový řádek jako správce. Pak přejděte do složky **scripts\docker** ve vašem klon **zařízení simulace** úložiště GitHub.

1. Pokud chcete vytvořit bitovou kopii docker, spusťte následující příkaz:

    ```cmd
    build.cmd
    ```

1. Pro přihlášení k účtu úložiště Docker Hub, spusťte následující příkaz:

    ```cmd
    docker login
    ```

1. Chcete-li odeslat vaší novou bitovou kopii do svého účtu úložiště Docker Hub, spusťte následující příkaz:

    ```cmd
    publish.cmd
    ```

1. Chcete-li ověřit nahrávání, přejděte na [ https://hub.docker.com/ ](https://hub.docker.com/). Vyhledání vašeho **žárovek** úložiště a zvolte **podrobnosti**. Zvolte **značky**:

    ![Úložiště docker hub](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    Skripty vložené **testování** značky na bitovou kopii.

1. Použití SSH se připojit k virtuálnímu počítači na řešení v Azure. Pak přejděte do **aplikace** složku a upravit **docker compose.yaml** souboru:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Upravte položku služby simulace zařízení používat docker image:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Uložte provedené změny.

1. Pokud chcete restartovat všechny služby s novým nastavením, spusťte následující příkaz:

    ```sh
    sudo ./start.sh
    ```

1. Pokud chcete zkontrolovat soubor protokolu z vašeho nový kontejner simulace zařízení, spusťte následující příkaz k vyhledání ID kontejneru:

    ```sh
    docker ps
    ```

    Spusťte následující příkaz pomocí ID kontejneru:

    ```sh
    docker logs {container ID}
    ```

Nyní jste dokončili postup nasazení aktualizované verze služby simulace zařízení do řešení vzdáleného monitorování.

V prohlížeči přejděte na **řídicí panel** pro vaše řešení vzdáleného monitorování. V panelu telemetrii na **řídicí panel**, vyberte **teploty**. Teplotní dvě Simulovaná zařízení zobrazí v grafu:

![Teplotní telemetrie](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Na **zařízení** stránky, můžete zřídit instance nového typu:

![Zobrazit seznam dostupných simulace](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

Můžete zobrazit telemetrii ze simulovaného zařízení:

![Zobrazení telemetrie žárovek](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Můžete volat **SwitchOn** a **SwitchOff** metody na vašem zařízení:

![Volání metody žárovek](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Přidejte nový typ telemetrie

Tato část popisuje postup úpravy existujícího typu simulované zařízení pro podporu nového typu telemetrie.

### <a name="locate-the-chiller-device-type-files"></a>Vyhledejte chladič soubory typ zařízení

Následující kroky vám ukážou, jak najít soubory, které definují integrované **chladič** zařízení:

1. Pokud jste tak již neučinili, použijte následující příkaz klonovat **zařízení simulace** úložiště GitHub do místního počítače:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Každý typ zařízení má soubor modelu JSON a související skripty v `data/devicemodels` složky. Soubory, které definují simulovaném **chladič** typ zařízení:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Zadejte nový typ telemetrie

Následující kroky vám ukážou, jak přidat nové **interní teploty** typ, který má **chladič** typ zařízení:

1. Otevřete **chladič 01.json** souboru.

1. Aktualizace **SchemaVersion** hodnotu následujícím způsobem:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. V **InitialState** přidejte toto dvě definice:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. V **Telemetrie** pole, přidejte následující definice:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Uložit **chladič 01.json** souboru.

1. Otevřete **skripty nebo chladič-01-state.js** souboru.

1. Přidejte následující pole do **stavu** proměnné:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Přidejte následující řádek na **hlavní** funkce:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Uložit **skripty nebo chladič-01-state.js** souboru.

### <a name="test-the-chiller-device-type"></a>Typ zařízení chladič testu

K testování aktualizovaný **chladič** typ zařízení, nejprve spusťte místní kopii **zařízení simulace** služby k testování typu vašeho zařízení chová podle očekávání. Když máte testovat a ladit typu vašeho zařízení aktualizované místně, můžete znovu vytvořit kontejner a znovu nasaďte **zařízení simulace** službu Azure.

Při spuštění **zařízení simulace** místně služby, odešle telemetrie do řešení vzdáleného monitorování. Na **zařízení** stránky, můžete zřídit instance vaše aktualizované typu.

K testování a ladění změny místně, najdete v předchozí části [testování typ zařízení žárovek místně](#test-the-lightbulb-device-type-locally).

Nasazení služby simulace aktualizované zařízení k virtuálnímu počítači na řešení v Azure najdete v tématu v předchozí části [simulátoru aktualizované nasadit do cloudu](#deploy-the-updated-simulator-to-the-cloud).

Na **zařízení** stránky, můžete zřídit instance vaše aktualizované typu:

![Přidejte aktualizovanou chladič](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Můžete zobrazit nové **interní teploty** telemetrie ze simulovaného zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu ukázal, jak na:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Vytvořte nový typ zařízení
> * Simulovat chování vlastní zařízení
> * Přidejte nový typ zařízení na řídicí panel
> * Odeslat vlastní telemetrii z existující typ zařízení

Nyní jste se naučili postup přizpůsobení službu simulace zařízení. Navrhované dalším krokem je další postup [připojení fyzického zařízení k řešení vzdáleného monitorování](iot-accelerators-connecting-devices-node.md).

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v části:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
