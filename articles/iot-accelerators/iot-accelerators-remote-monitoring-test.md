---
title: Simulace zařízení v řešení vzdáleného monitorování – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak použít simulátor zařízení s akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: 9c0c1ba9dd343baa453f10ad82c0cc8b8e69da7b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596150"
---
# <a name="create-a-new-simulated-device"></a>Vytvoření nového simulovaného zařízení

V tomto kurzu se dozvíte, jak přizpůsobit mikroslužeb simulátor zařízení v akcelerátoru řešení vzdáleného monitorování. Chcete-li zobrazit možnosti simulátor zařízení, tento kurz používá dva scénáře v aplikaci Contoso IoT.

Následující video představuje přehled možností pro přizpůsobení mikroslužeb simulátor zařízení:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

Do prvního scénáře Contoso chce testovat nové zařízení inteligentní žárovky. Provádění testů, můžete vytvořit nového simulovaného zařízení s následujícími charakteristikami:

*Vlastnosti*

| Název                     | Hodnoty                      |
| ------------------------ | --------------------------- |
| Barva                    | Prázdné, červená, modrá            |
| Jas               | 0 až 100                    |
| Odhadovaný zbývající životnost | Odpočítávání za 10 000 hodin |

*Telemetrie*

V následující tabulce jsou uvedeny data na žárovku zprávy do cloudu jako datový proud:

| Název   | Hodnoty      |
| ------ | ----------- |
| Status | "na" "off" |
| Teplota | Stupně F |
| Online | Hodnota TRUE, false |

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
| Počáteční barva            | Bílá  |
| Počáteční jas       | 75     |
| Počáteční zbývající životnost   | 10 000 |
| Telemetrie počáteční stav | "na"   |
| Počáteční telemetrie teploty | 200   |

V druhém scénáři, přidáte nový typ telemetrických dat na Contoso existující **chladič** zařízení.

V tomto kurzu se dozvíte, jak použít simulátor zařízení s akcelerátor řešení vzdálené monitorování:

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vytvořte nový typ zařízení
> * Simulace zařízení vlastní chování
> * Přidat nový typ zařízení na řídicí panel
> * Odesílání vlastní telemetrie z existujícího typu zařízení

Následující video ukazuje názorný postup připojení k řešení vzdáleného monitorování skutečných a simulovaných zařízení:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Požadavky

V tomto kurzu, budete potřebovat:

* Nasazená instance řešení vzdálené monitorování ve vašem předplatném Azure. Pokud nemáte ještě nasazené řešení vzdáleného monitorování, by se měl Dokončit [nasazení akcelerátoru řešení vzdáleného monitorování](../iot-accelerators/quickstart-remote-monitoring-deploy.md) kurzu.

* Sada Visual Studio 2017. Pokud nemáte nainstalovanou sadu Visual Studio 2017, si můžete stáhnout bezplatnou [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Cloud Explorer for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) rozšíření sady Visual Studio.

* Účet na [Docker Hubu](https://hub.docker.com/). Abyste mohli začít, můžete se ZDARMA zaregistrovat.

* [Git](https://git-scm.com/downloads) nainstalované na desktopovém počítači.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Proveďte následující úkoly Příprava vývojového prostředí pro přidání nového simulovaného zařízení do řešení vzdáleného monitorování:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Konfigurovat přístup přes SSH k virtuálnímu počítači řešení v Azure

Při vytváření řešení vzdáleného monitorování v [www.azureiotsolutions.com](https://www.azureiotsolutions.com), zvolíte název řešení. Název řešení bude název skupiny prostředků Azure, který obsahuje různé nasazené prostředky, které toto řešení využívá. Následující příkazy použijte skupinu prostředků s názvem **Contoso-01**, byste měli vyměnit **Contoso-01** s názvem vaší skupiny prostředků.

Následující příkazy použijte `az` příkaz [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Můžete nainstalovat rozhraní příkazového řádku Azure CLI 2.0 na vývojovém počítači, nebo použít [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) v [webu Azure portal](http://portal.azure.com). Rozhraní příkazového řádku Azure CLI 2.0 je předem nainstalovaných ve službě Cloud Shell.

1. Pokud chcete ověřit název skupiny prostředků obsahující prostředky vzdáleného monitorování, spusťte následující příkaz:

    ```sh
    az group list | grep "name"
    ```

    Tento příkaz zobrazí seznam všech skupin prostředků ve vašem předplatném. V seznamu by měly zahrnovat skupinu prostředků se stejným názvem jako řešení vzdáleného monitorování.

1. Chcete-li váš prostředek skupinu jako výchozí pro následné příkazy, spusťte následující příkaz pomocí název skupiny prostředků místo **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Seznam prostředků ve vaší skupině prostředků, spusťte následující příkaz:

    ```sh
    az resource list -o table
    ```

    Poznamenejte si názvy vašeho virtuálního počítače a skupiny zabezpečení sítě. Tyto hodnoty použijete v dalších krocích.

1. Pokud chcete povolit přístup přes SSH vašeho virtuálního počítače, spusťte následující příkaz pomocí názvu skupiny zabezpečení sítě z předchozího kroku:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Chcete-li zobrazit seznam příchozích pravidel pro vaši síť, spusťte následující příkaz:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

    Přístup přes SSH byste měli povolit jenom během vývoj a testování. Pokud povolíte SSH, [byste měli zakázat znovu co nejdříve](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-azure-virtual-machines)

1. Chcete-li změnit heslo virtuálního počítače na heslo, které už znáte, spusťte následující příkaz. Použijte název virtuálního počítače, které jste si poznamenali dříve a hesla podle svého výběru:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Chcete-li zjistit IP adresu vašeho virtuálního počítače, použijte následující příkaz a poznamenejte si veřejnou IP adresu:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Můžete teď použít SSH pro připojení k virtuálnímu počítači. `ssh` Příkaz je předem nainstalovaných ve službě Cloud Shell. Použijte veřejnou IP adresu z předchozího kroku, a po výzvě heslo, jste nakonfigurovali pro virtuální počítač:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Teď máte přístup k prostředí ve virtuálním počítači, na kterém běží kontejnerů Dockeru v řešení vzdáleného monitorování. Chcete-li zobrazit spuštěné kontejnery, použijte následující příkaz:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Vyhledání služby připojovací řetězce

V tomto kurzu pracujete s řešení sady Visual Studio, který se připojí ke službě Cosmos DB a služby IoT Hub řešení. Následující kroky ukazují, jeden způsob, jak najít připojení řetězcové hodnoty, které potřebujete:

1. K vyhledání připojovacího řetězce služby Cosmos DB, spusťte následující příkaz v relaci SSH, který je připojený k virtuálnímu počítači:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Poznamenejte si připojovací řetězec. Tuto hodnotu budete později v kurzu potřebovat.

1. Pokud chcete najít připojovací řetězec služby IoT Hub, spusťte následující příkaz v relaci SSH, který je připojený k virtuálnímu počítači:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Poznamenejte si připojovací řetězec. Tuto hodnotu budete později v kurzu potřebovat.

> [!NOTE]
> Tyto řetězce připojení můžete také vyhledat na webu Azure Portal nebo pomocí `az` příkazu.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Zastavit službu simulace zařízení na virtuálním počítači

Při úpravě službu simulace zařízení můžete jej můžete spustit místně a otestujte provedené změny. Před spuštěním služby simulace zařízení místně, je nutné zastavit instanci spuštěné na virtuálním počítači následujícím způsobem:

1. Najít **ID KONTEJNERU** z **zařízení – simulace dotnet** service, spusťte následující příkaz v relaci SSH, který je připojený k virtuálnímu počítači:

    ```sh
    docker ps
    ```

    Poznamenejte si ID kontejneru **zařízení – simulace dotnet** služby.

1. Chcete-li zastavit **zařízení – simulace dotnet** kontejneru, spusťte následující příkaz:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Naklonujte úložiště GitHub

V tomto kurzu jste pracovat **simulace zařízení** a **adaptér úložiště** projektů sady Visual Studio. Můžete naklonovat úložišť zdrojového kódu z Githubu. Tento krok proveďte na svém místním vývojovém počítači, kde máte nainstalovanou sadu Visual Studio:

1. Otevřete příkazový řádek a přejděte do složky, kam chcete uložit kopii **simulace zařízení** a **adaptér úložiště** úložištích GitHub.

1. Naklonujte verzi .NET **simulace zařízení** úložiště, spusťte následující příkaz:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Služba simulace zařízení v řešení vzdáleného monitorování umožňuje provádět změny typů předdefinovaných simulované zařízení a pro vytváření nových simulované typy zařízení. Typy vlastních zařízení můžete použít k testování chování řešení vzdáleného monitorování před připojení fyzických zařízení.

1. Naklonujte verzi .NET **adaptér úložiště** úložiště, spusťte následující příkaz:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Službu simulace zařízení pomocí služby adaptér úložiště připojit ke službě Cosmos DB v Azure. Řešení vzdáleného monitorování jsou uložena konfigurační data simulovaného zařízení v databázi Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Místní spuštění adaptér služby storage

Služba simulace zařízení používá službu úložiště adaptér pro připojení k řešení databázi Cosmos DB. Pokud službu simulace zařízení spouštíte místně, musíte také spustit službu adaptér úložiště místně. Následující kroky ukazují, jak spustit adaptér služby úložiště ze sady Visual Studio:

1. V sadě Visual Studio, otevřete **počítače storage-adapter.sln** soubor řešení v místním klonu z **adaptér úložiště** úložiště.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **webová služba** projektu, zvolte **vlastnosti**a klikněte na tlačítko **ladění**.

1. V **proměnné prostředí** části, upravte hodnotu **počítače\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** proměnnou deklarovanou připojení Cosmos DB řetězec, který jste si poznamenali dříve. Uložte změny.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **webová služba** projektu, zvolte **ladění**a klikněte na tlačítko **zahájit novou instanci**.

1. Služba se spustí místně a otevře `http://localhost:9022/v1/status` ve vašem výchozím prohlížeči. Ověřte, že **stav** hodnota je "OK: aktivní a dobře."

1. Nechte službu adaptér úložiště, místně spuštěná před dokončením tohoto kurzu.

Teď máte všechno nastavené a jste připraveni začít přidávat nového typu simulovaného zařízení k řešení vzdáleného monitorování.

## <a name="create-a-simulated-device-type"></a>Vytvoření simulovaného zařízení typu

Zkopírovat a upravit existující typ je nejjednodušší způsob, jak vytvořit nový typ zařízení ve službě simulaci zařízení. Následující kroky ukazují, jak zkopírovat předdefinované **chladič** zařízení k vytvoření nového **žárovky** zařízení:

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **webová služba** projektu, zvolte **vlastnosti**a klikněte na tlačítko **ladění**.

1. V **proměnné prostředí** části, upravte hodnotu **počítače\_IOTHUB\_CONNSTRING** proměnné se připojovací řetězec služby IoT Hub jste si poznamenali dříve. Uložte změny.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **simulace zařízení** řešení a zvolte **nastavit projekty po spuštění**. Zvolte **jeden spouštěný projekt** a vyberte **webová služba**. Pak klikněte na **OK**.

1. Každý typ zařízení má soubor modelu JSON a přidružené skripty v **služby/data/devicemodels** složky. V Průzkumníku řešení, zkopírujte **chladič** soubory a vytvořte **žárovky** soubory, jak je znázorněno v následující tabulce:

    | Zdroj                      | Cíl                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | skripty/chladič-01-state.js | skripty/žárovky-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definovat vlastnosti nového typu zařízení

**Žárovky 01.json** soubor definuje vlastnosti typu, jako je například telemetrických dat generuje a metody podporuje. Následující postup aktualizace **žárovky 01.json** souboru k definování **žárovky** zařízení:

1. V **žárovky 01.json** souboru, aktualizujte metadata zařízení, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. V **žárovky 01.json** souboru, aktualizujte definici simulaci, jak je znázorněno v následujícím fragmentu kódu:

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

1. V **žárovky 01.json** souboru, aktualizace vlastností zařízení zadejte, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. V **žárovky 01.json** souboru, aktualizujte definice telemetrická data typu zařízení, jak je znázorněno v následujícím fragmentu kódu:

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

1. V **žárovky 01.json** soubor, aktualizovat metody typu zařízení, jak je znázorněno v následujícím fragmentu kódu:

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

1. Uložit **žárovky 01.json** souboru.

### <a name="simulate-custom-device-behavior"></a>Simulace zařízení vlastní chování

**Skripty/žárovky-01-state.js** soubor definuje chování simulace **žárovky** typu. Následující postup aktualizace **skripty/žárovky-01-state.js** souboru k definování chování **žárovky** zařízení:

1. Úprava definice stavu v **skripty/žárovky-01-state.js** sdílené, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Přidat **překlopit** po fungovat **lišit** funkce s následující definicí:

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

1. Upravit **hlavní** funkci, která implementuje chování, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global device properties and the global state before
        // generating the new telemetry, so that the telemetry can apply changes
        // using the previous function state.
        restoreSimulation(previousState, previousProperties);

        state.temperature = vary(200, 5, 150, 250);

        // Make this flip every so often
        state.status = flip(state.status);

        updateState(state);

        return state;
    }
    ```

1. Uložit **skripty/žárovky-01-state.js** souboru.

**Skripty/SwitchOn-method.js** implementuje **přepínač na** metoda ve **žárovky** zařízení. Následující postup aktualizace **skripty/SwitchOn-method.js** souboru:

1. Úprava definice stavu v **skripty/SwitchOn-method.js** sdílené, jak je znázorněno v následujícím fragmentu kódu:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Chcete-li zapnout na žárovku, upravte **hlavní** funkce takto:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Uložit **skripty/SwitchOn-method.js** souboru.

1. Vytvořit kopii **skripty/SwitchOn-method.js** soubor s názvem **skripty/SwitchOff-method.js**.

1. Chcete-li vypnout na žárovku, upravte **hlavní** fungovat v **skripty/SwitchOff-method.js** to následujícím způsobem:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Uložit **skripty/SwitchOff-method.js** souboru.

1. V Průzkumníku řešení vyberte každé z čtyři nové soubory pak. V **vlastnosti** okna pro každý soubor, ověřte, že **kopírovat do výstupního adresáře** je nastavena na **kopírovat, pokud je novější**.

### <a name="configure-the-device-simulation-service"></a>Konfigurace služby simulace zařízení

Pokud chcete omezit počet simulovaných zařízení, která se připojují k řešení během testování, konfigurace služby pro spuštění jedné chladič a jeden žárovky zařízení. Konfigurační data jsou uložena v instanci databáze Cosmos DB ve skupině prostředků řešení. Chcete-li upravit konfigurační data, použijte **Průzkumníka cloudu** zobrazení v sadě Visual Studio:

1. Chcete-li otevřít **Průzkumníka cloudu** zobrazení v sadě Visual Studio, zvolte **zobrazení** a potom **Průzkumníka cloudu**.

1. Najít v dokumentu konfigurace simulace, **hledat prostředky** zadejte **simualtions.1**.

1. Dvakrát klikněte **simulations.1** dokument otevřít pro úpravy.

1. V hodnotě **Data**, vyhledejte **DeviceModels** pole, která vypadá jako následující fragment kódu:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Chcete-li definovat jeden chladič a jeden žárovky simulované zařízení, nahradit **DeviceModels** pole s následujícím kódem:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Uložit změny **simulations.1** dokumentu.

> [!NOTE]
> Můžete také použít Průzkumník dat Cosmos DB na webu Azure Portal upravit **simulations.1** dokumentu.

### <a name="test-the-lightbulb-device-type-locally"></a>Typ zařízení žárovky místně testu

Nyní jste připraveni k testování nového typu žárovka s Simulovaná spuštěním projektu simulace zařízení místně.

1. V Průzkumníku řešení klikněte pravým tlačítkem na **webová služba**, zvolte **ladění** a klikněte na tlačítko **zahájit novou instanci**.

1. Pokud chcete zkontrolovat, že dvě Simulovaná zařízení jsou připojené ke službě IoT Hub, otevřete v prohlížeči na webu Azure portal.

1. Přejděte do služby IoT hub ve skupině prostředků, která obsahuje vaše řešení vzdálené monitorování.

1. V **monitorování** zvolte **metriky**. Ověřte, že počet **připojená zařízení** dvě:

    ![Počet připojených zařízení](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. V prohlížeči přejděte **řídicí panel** pro vaše řešení vzdáleného monitorování. Na panelu telemetrických dat **řídicí panel**vyberte **teploty**. Teplota pro všechny vaše simulované zařízení zobrazí v grafu:

    ![Teplotní telemetrie](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Teď máte simulace zařízení žárovky spuštěná místně. Dalším krokem je nasazení aktualizované simulátor kódu k virtuálnímu počítači, na kterém běží vzdálené monitorování mikroslužeb v Azure.

Než budete pokračovat, můžete zastavit ladění simulace zařízení i úložiště adaptér projekty v sadě Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Nasazení aktualizované simulátor do cloudu

Mikroslužby v řešení vzdáleného monitorování běží v kontejnerech dockeru. Kontejnery jsou hostované v řešení virtuálního počítače v Azure. V této části:

* Vytvoření nové image dockeru simulaci zařízení.
* Odešlete image do úložiště docker hub.
* Importujte bitovou kopii k virtuálnímu počítači, vaše řešení.

Následující postup předpokládá, že máte úložiště volána **žárovky** ve vašem účtu Docker Hubu.

1. V sadě Visual Studio v **simulace zařízení** projektu, otevřete soubor **solution\scripts\docker\build.cmd**.

1. Upravit řádek, který nastaví **DOCKER_IMAGE** proměnné prostředí na název úložiště Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Uložte změny.

1. V sadě Visual Studio v **simulace zařízení** projektu, otevřete soubor **solution\scripts\docker\publish.cmd**.

1. Upravit řádek, který nastaví **DOCKER_IMAGE** proměnné prostředí na název úložiště Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Uložte změny.

1. Otevřete příkazový řádek jako správce. Pak přejděte do složky **scripts\docker** ve vašem klonu z **simulace zařízení** úložiště GitHub.

1. Pokud chcete sestavit image dockeru, spusťte následující příkaz:

    ```cmd
    build.cmd
    ```

1. Přihlásit se ke svému účtu Docker Hubu, spusťte následující příkaz:

    ```cmd
    docker login
    ```

1. Pokud chcete nahrát novou bitovou kopii do svého účtu Docker Hubu, spusťte následující příkaz:

    ```cmd
    publish.cmd
    ```

1. Pokud chcete ověřit nahrání, přejděte na [ https://hub.docker.com/ ](https://hub.docker.com/). Vyhledejte vaši **žárovky** úložiště a zvolte **podrobnosti**. Klikněte na tlačítko **značky**:

    ![Docker hubu](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    Skripty přidané **testování** značky do bitové kopie.

1. Pomocí SSH se připojte k virtuálnímu počítači vaše řešení v Azure. Přejděte na **aplikace** složky a úpravy **docker-compose.yml** souboru:

    ```sh
    cd /app
    sudo nano docker-compose.yml
    ```

1. Upravte položku pro službu simulace zařízení pomocí image dockeru:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Uložte provedené změny.

1. Pokud chcete restartovat všechny služby s novým nastavením, spusťte následující příkaz:

    ```sh
    sudo ./start.sh
    ```

1. Můžete zkontrolovat soubor protokolu z vašeho nového kontejneru simulace zařízení, spuštěním následujícího příkazu najděte ID kontejneru:

    ```sh
    docker ps
    ```

    Potom spusťte následující příkaz s použitím ID kontejneru:

    ```sh
    docker logs {container ID}
    ```

Teď jste dokončili postup nasazení aktualizovanou verzi služby simulace zařízení do řešení vzdáleného monitorování.

V prohlížeči přejděte **řídicí panel** pro vaše řešení vzdáleného monitorování. Na panelu telemetrických dat **řídicí panel**vyberte **teploty**. Teplota dvě Simulovaná zařízení zobrazí v grafu:

![Teplotní telemetrie](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Na **zařízení** stránky, můžete zřídit instance stejného nového typu:

![Zobrazí se seznam dostupných simulace](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

Můžete zobrazit telemetrická data ze simulovaného zařízení:

![Zobrazení telemetrie žárovky](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Můžete volat **SwitchOn** a **SwitchOff** metody v zařízení:

![Volání metody žárovky](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Přidat nový typ telemetrie

Tato část popisuje, jak upravit existující typ simulovaného zařízení pro podporu nového typu telemetrická data.

### <a name="locate-the-chiller-device-type-files"></a>Vyhledejte soubory typu chladič zařízení

Následující kroky ukazují, jak k vyhledání souborů, které definují předdefinované **chladič** zařízení:

1. Pokud jste tak již neučinili, použijte následující příkaz, naklonujte **zařízení – simulace dotnet** úložiště GitHub do svého místního počítače:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Každý typ zařízení má soubor modelu JSON a přidružené skripty v `data/devicemodels` složky. Soubory, které definují simulované **chladič** typ zařízení:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Zadejte nový typ telemetrie

Následující kroky ukazují, jak přidat nové **vnitřní teplota** typ, který **chladič** typ zařízení:

1. Otevřít **chladič 01.json** souboru.

1. Aktualizace **SchemaVersion** hodnota následujícím způsobem:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. V **InitialState** části, přidejte naleznete dvě definice:

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

1. Otevřít **skripty/chladič-01-state.js** souboru.

1. Přidejte následující pole na **stavu** proměnné:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Přidejte následující řádek, který **hlavní** funkce:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Uložit **skripty/chladič-01-state.js** souboru.

### <a name="test-the-chiller-device-type"></a>Typ zařízení chladič testu

K otestování aktualizované **chladič** typ zařízení, nejdříve spusťte místní kopii **zařízení – simulace dotnet** služby k otestování typu vašeho zařízení se chová podle očekávání. Když jste otestovali a ladit místně typu vašeho zařízení aktualizované, můžete znovu sestavovat kontejner a opětovné nasazení **zařízení – simulace dotnet** služby do Azure.

Při spuštění **zařízení – simulace dotnet** služby místně, se odesílá telemetrická data do vašeho řešení vzdáleného monitorování. Na **zařízení** stránky, můžete zřídit instance stejného typu aktualizované.

Testovat a ladit své změny místně, viz předchozí oddíl [typ zařízení žárovky místně testu](#test-the-lightbulb-device-type-locally).

Nasazení služby simulace aktualizované zařízení do řešení virtuálního počítače v Azure, viz předchozí oddíl [aktualizované simulátor nasadit do cloudu](#deploy-the-updated-simulator-to-the-cloud).

Na **zařízení** stránky, můžete zřídit instance stejného typu aktualizace:

![Přidejte aktualizované chladič](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Můžete zobrazit nové **vnitřní teplota** telemetrie ze simulovaných zařízení.

## <a name="next-steps"></a>Další postup

Tento kurz vám ukázali, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Vytvořte nový typ zařízení
> * Simulace zařízení vlastní chování
> * Přidat nový typ zařízení na řídicí panel
> * Odesílání vlastní telemetrie z existujícího typu zařízení

Nyní jste se naučili, jak přizpůsobit službu simulace zařízení. Navrhované dalším krokem je další způsob [připojení fyzických zařízení k řešení vzdáleného monitorování](iot-accelerators-connecting-devices-node.md).

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v tématu:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
