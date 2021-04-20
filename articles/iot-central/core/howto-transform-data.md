---
title: Transformují data pro Azure IoT Central | Microsoft Docs
description: Zařízení IoT odesílají data v různých formátech, které možná budete potřebovat transformovat. Tento článek popisuje, jak transformovat data jak jak na to IoT Central, tak i na cestě. Popsané scénáře používají IoT Edge a Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751683"
---
# <a name="transform-data-for-iot-central"></a>Transformovat data pro IoT Central

*Toto téma se týká tvůrců řešení.*

Zařízení IoT odesílají data v různých formátech. Pokud chcete používat data zařízení s aplikací IoT Central, možná budete muset použít transformaci na:

- Nastavte formát dat kompatibilních s vaší aplikací IoT Central.
- Převede jednotky.
- Výpočetní nové metriky.
- Obohacení dat z jiných zdrojů.

V tomto článku se dozvíte, jak transformovat data zařízení mimo IoT Central v příchozím nebo odchozím přenosu.

Následující diagram znázorňuje tři trasy pro data, která zahrnují transformace:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Souhrn transformačních dat tras pro příchozí i odchozí přenosy" border="false":::

Následující tabulka uvádí tři příklad typů transformace:

| Transformace | Popis | Příklad  | Poznámky |
|------------------------|-------------|----------|-------|
| Formát zprávy         | Převod na zprávy JSON nebo manipulace s nimi. | CSV do formátu JSON  | Při příchozím přenosu dat. IoT Central akceptují jenom zprávy JSON s hodnotou. Další informace najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md). |
| Výpočty           | Matematické funkce, které mohou [Azure Functions](../../azure-functions/index.yml) provádět. | Konverze jednotek z Fahrenheita na Celsia.  | Transformujte pomocí vzoru výstupních dat, abyste mohli využít výhod škálovatelného zařízení prostřednictvím přímého připojení k IoT Central. Transformace dat umožňuje použití IoT Centralch funkcí, jako jsou vizualizace a úlohy. |
| Rozšíření zprávy     | Rozšíření z externích zdrojů dat nenalezena ve vlastnostech zařízení nebo telemetrie. Další informace o vnitřních rozšířeních najdete v tématu [Export dat IoT do cloudových cílů pomocí exportu dat](howto-export-data.md) . | Přidejte do zpráv informace o počasí pomocí dat umístění ze zařízení. | Transformujte pomocí vzoru výstupních dat, abyste mohli využít výhod škálovatelného zařízení prostřednictvím přímého připojení k IoT Central. |

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

K nastavení řešení potřebujete aplikaci IoT Central. Informace o tom, jak vytvořit aplikaci IoT Central, najdete v tématu [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).

## <a name="data-transformation-at-ingress"></a>Transformace dat při příchozím přenosu dat

K transformaci dat zařízení v příchozím přenosu jsou k dispozici dvě možnosti:

- **IoT Edge**: před odesláním dat do aplikace IoT Central použijte modul IoT Edge k transformaci dat ze všech podřízených zařízení.

- **IoT Central mostu zařízení**: [most IoT Central zařízení](howto-build-iotc-device-bridge.md) spojuje ostatní cloudy zařízení IoT, jako je Sigfox, částice a síť, do IoT Central. Most zařízení používá funkci Azure k přeposílání dat a můžete přizpůsobit funkci pro transformaci dat zařízení.

### <a name="use-iot-edge-to-transform-device-data"></a>Použití IoT Edge k transformaci dat zařízení

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Transformace dat na příchozí přenosy pomocí IoT Edge" border="false":::

V tomto scénáři modul IoT Edge transformuje data ze zařízení, která se budou předávat do aplikace IoT Central. Na nejvyšší úrovni je postup pro konfiguraci tohoto scénáře následující:

1. **Nastavení zařízení IoT Edge**: Nainstalujte a zajistěte IoT Edge zařízení jako bránu a připojte bránu k vaší IoT Central aplikaci.

1. **Připojit zařízení pro IoT Edge k zařízení:** Připojte zařízení pro příjem dat k IoT Edge a zřiďte je do aplikace IoT Central.

1. **Transformovat data zařízení v IoT Edge:** Vytvořte modul IoT Edge pro transformaci dat. Nasaďte modul do zařízení IoT Edge brány, které přepošle převedená data zařízení do aplikace IoT Central.

1. **Ověření**: odešlete data ze zařízení pro příjem dat do brány a ověřte, že data přetransformovaných zařízení dosáhnou vaší aplikace IoT Central.

V příkladu popsaném v následujících částech odesílá zařízení pro IoT Edge brány data CSV v následujícím formátu:

```csv
"<temperature >, <pressure>, <humidity>"
```

Chcete použít modul IoT Edge k transformaci dat do následujícího formátu JSON před odesláním do IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

Následující kroky ukazují, jak nastavit a nakonfigurovat tento scénář:

### <a name="build-the-custom-module"></a>Sestavení vlastního modulu

V tomto scénáři IoT Edge zařízení spustí vlastní modul, který transformuje data ze zařízení pro příjem dat. Než nasadíte a nakonfigurujete IoT Edge zařízení, budete potřebovat:

- Sestavte vlastní modul.
- Přidejte vlastní modul do registru kontejneru.

Modul runtime IoT Edge stahuje vlastní moduly z registru kontejnerů, jako je Azure Container registry nebo Docker Hub. [Azure Cloud Shell](../../cloud-shell/overview.md) obsahuje všechny nástroje, které potřebujete k vytvoření registru kontejnerů, sestavení modulu a nahrání modulu do registru:

Postup vytvoření registru kontejneru:

1. Otevřete [Azure Cloud Shell](https://shell.azure.com/) a přihlaste se ke svému předplatnému Azure.

1. Spuštěním následujících příkazů vytvořte službu Azure Container Registry:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Poznamenejte si `username` `password` hodnoty a, později je použijete.

Chcete-li vytvořit vlastní modul v [Azure Cloud Shell](https://shell.azure.com/):

1. V [Azure Cloud Shell](https://shell.azure.com/)přejděte do vhodné složky.
1. Pokud chcete klonovat úložiště GitHub, které obsahuje zdrojový kód modulu, spusťte následující příkaz:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Pokud chcete vytvořit vlastní modul, spusťte v Azure Cloud Shell následující příkazy:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Spuštění předchozích příkazů může trvat několik minut.

### <a name="set-up-an-iot-edge-device"></a>Nastavení zařízení IoT Edge

Tento scénář používá zařízení IoT Edge brány k transformaci dat z libovolného podřízeného zařízení. Tato část popisuje, jak vytvořit IoT Central šablony zařízení pro bránu a zařízení pro příjem dat ve vaší aplikaci IoT Central. IoT Edge zařízení používají ke konfiguraci svých modulů manifest nasazení.

Chcete-li vytvořit šablonu zařízení pro zařízení pro příjem dat, tento scénář používá jednoduchý model zařízení termostat:

1. Stáhněte si [model zařízení pro zařízení termostata](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) do svého místního počítače.

1. Přihlaste se k aplikaci IoT Central a přejděte na stránku **šablony zařízení** .

1. Vyberte **+ Nový**, vyberte **zařízení IoT** a vyberte **Další: přizpůsobit**.

1. Jako název šablony zadejte *termostat* a vyberte **Další: zkontrolovat**. Potom vyberte **Vytvořit**.

1. Vyberte **importovat model** a importujte *thermostat-2.jsdo* souboru, který jste předtím stáhli.

1. Vyberte **publikovat** a publikujte novou šablonu zařízení.

Vytvoření šablony zařízení pro zařízení IoT Edge brány:

1. Uložte kopii manifestu nasazení do místního vývojového počítače: [moduledeployment.jsna](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Otevřete místní kopii *moduledeployment.jsv* souboru manifestu v textovém editoru.

1. Najděte `registryCredentials` oddíl a nahraďte zástupné symboly hodnotami, které jste si poznamenali při vytváření služby Azure Container Registry. `address`Hodnota vypadá jako `<username>.azurecr.io` .

1. Vyhledejte `settings` část pro `transformmodule` . Nahraďte `<acr or docker repo>` stejnou `address` hodnotou, jakou jste použili v předchozím kroku. Uložte změny.

1. V aplikaci IoT Central přejděte na stránku **šablony zařízení** .

1. Vyberte **+ Nový**, vyberte **Azure IoT Edge** a pak vyberte **Další: přizpůsobit**.

1. Jako název šablony zařízení zadejte *IoT Edge zařízení brány* . Vyberte **Toto zařízení brány**. Vyberte **Procházet** a nahrajte *moduledeployment.jsv* souboru manifestu nasazení, který jste upravovali dříve.

1. Po ověření manifestu nasazení vyberte **Další: Zkontrolujte** a pak vyberte **vytvořit**.

1. V části **model** vyberte **vztahy**. Vyberte **+ Přidat relaci**. Jako zobrazovaný název zadejte *zařízení pro příjem dat* a jako cíl vyberte **termostat** . Vyberte **Uložit**.

1. Vyberte **publikovat** a publikujte šablonu zařízení.

Ve vaší aplikaci IoT Central nyní máte dvě šablony zařízení. Šablonu **zařízení IoT Edge brány** a šablonu **termostata** jako zařízení pro příjem dat.

Postup při registraci zařízení brány v IoT Central:

1. V aplikaci IoT Central přejděte na stránku **zařízení** .

1. Vyberte **zařízení IoT Edge brány** a vyberte **vytvořit zařízení**. Jako název zařízení zadejte *IoT Edge brány* , jako ID zařízení zadejte *Gateway-01* , ujistěte se, že je jako šablona zařízení vybraná možnost **IoT Edge zařízení brány** . Vyberte **Vytvořit**.

1. V seznamu zařízení klikněte na **zařízení IoT Edge brány** a pak vyberte **připojit**.

1. Poznamenejte si **Rozsah ID**, **ID zařízení** a hodnoty **primárního klíče** pro **zařízení IoT Edge brány**. Později je můžete použít.

Postup při registraci zařízení pro příjem dat v IoT Central:

1. V aplikaci IoT Central přejděte na stránku **zařízení** .

1. Vyberte **termostat** a vyberte **vytvořit zařízení**. Jako název zařízení zadejte *termostat* , jako ID zařízení zadejte *1 – 01* a ujistěte se, že je jako šablona zařízení vybraný **termostat** . Vyberte **Vytvořit**.

1. V seznamu zařízení vyberte **termostat** a pak vyberte **připojit k bráně**. Vyberte šablonu **zařízení IoT Edge brány** a instanci **zařízení IoT Edge brány** . Vyberte **připojit**.

1. V seznamu zařízení klikněte na **termostat** a pak vyberte **připojit**.

1. Poznamenejte si **Rozsah ID**, **ID zařízení** a hodnoty **primárního klíče** pro zařízení **termostatu** . Později je můžete použít.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Nasazení brány a zařízení pro příjem dat

Pro usnadnění práce Tento článek používá virtuální počítače Azure ke spuštění brány a zařízení pro příjem dat. Pokud chcete vytvořit dva virtuální počítače Azure, klikněte na tlačítko **nasadit do Azure** níže a použijte informace v následující tabulce k dokončení formuláře **vlastního nasazení** :

| Pole | Hodnota |
| ----- | ----- |
| Skupina prostředků | `ingress-scenario` |
| Brána předpony popisku DNS | Jedinečný název DNS pro tento počítač, například `<your name>edgegateway` |
| Předpona popisku DNS pro podřízené služby | Jedinečný název DNS pro tento počítač, například `<your name>downstream` |
| ID oboru | Rozsah ID, na který jste si poznamenali dříve |
| ID zařízení IoT Edge bránu | `gateway-01` |
| Klíč zařízení IoT Edge bránu | Hodnota primárního klíče, na kterou jste si poznamenali předchozí poznámku |
| Typ ověřování | Heslo |
| Heslo nebo klíč správce | Vaše volba hesla pro účet **AzureUser** na obou virtuálních počítačích. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**. Vytvoření virtuálních počítačů ve skupině prostředků příchozího přenosu dat ve **scénáři** trvá několik minut.

Chcete-li ověřit, zda zařízení IoT Edge správně funguje:

1. Otevřete aplikaci IoT Central. Pak přejděte na **zařízení IoT Edge brány** na seznamu zařízení na stránce **zařízení** .

1. Vyberte kartu **moduly** a ověřte stav tří modulů. Spuštění modulu runtime IoT Edge na virtuálním počítači trvá několik minut. Po spuštění se **spustí** stav tří modulů. Pokud se modul runtime IoT Edge nespustí, přečtěte si téma [řešení potíží se zařízením IoT Edge](../../iot-edge/troubleshoot.md).

Aby zařízení IoT Edge fungovalo jako brána, potřebuje některé certifikáty k prokázání své identity pro všechna podřízená zařízení. Tento článek používá ukázkové certifikáty. V produkčním prostředí použijte certifikáty od certifikační autority.

Pro vygenerování ukázkových certifikátů a jejich instalaci na zařízení brány:

1. Pomocí SSH se připojte k virtuálnímu počítači zařízení brány a přihlaste se k němu. Název DNS pro tento virtuální počítač najdete v Azure Portal. Přejděte k virtuálnímu počítači **edgegateway** ve skupině **prostředků** příchozího přenosu dat.

    > [!TIP]
    > Než budete moct použít SSH k připojení z místního počítače nebo Azure Cloud Shell, možná budete muset otevřít port 22 pro přístup SSH na obou virtuálních počítačích.

1. Spuštěním následujících příkazů naklonujte úložiště IoT Edge a vygenerujte ukázkové certifikáty:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Po spuštění předchozích příkazů jsou následující soubory připravené k použití v následujících krocích:

    - *~/certs/certs/Azure-IoT-test-Only.root.ca.CERT.pem* – certifikát kořenové certifikační autority, který se používá k vytvoření všech ostatních ukázkových certifikátů pro testování scénáře IoT Edge.
    - *~/certs/certs/IoT-Edge-Device-mycacert-Full-Chain.CERT.pem* – certifikát certifikační autority zařízení, na který se odkazuje v souboru *config. yaml* . Tento certifikát certifikační autority ve scénáři brány je způsob, jakým IoT Edge zařízení ověřuje svoji identitu pro zařízení se stavem pro příjem dat.
    - *~/certs/Private/IoT-Edge-Device-mycacert.Key.pem* – privátní klíč přidružený k certifikátu certifikační autority zařízení.

    Další informace o těchto ukázkových certifikátech najdete v tématu [Vytvoření ukázkových certifikátů pro otestování IoT Edgech funkcí zařízení](../../iot-edge/how-to-create-test-certificates.md).

1. Otevřete soubor *config. yaml* v textovém editoru. Například:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Vyhledejte `Certificate settings` nastavení. Odkomentujte a upravte nastavení certifikátu následujícím způsobem:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Výše uvedený příklad předpokládá, že jste se přihlásili jako **AzureUser** a vytvořili jste certifikát certifikační autority pro zařízení s názvem "mycacert".

1. Uložte změny a restartujte IoT Edge Runtime:

    ```bash
    sudo systemctl restart iotedge
    ```

Pokud se modul runtime IoT Edge po změnách úspěšně spustí, stav modulů **$edgeAgent** a **$edgeHub** se změní na **spuštěno**. Tyto hodnoty stavu můžete zobrazit na stránce **moduly** pro zařízení brány v IoT Central.

Pokud se modul runtime nespustí, zkontrolujte změny, které jste provedli v *souboru config. yaml* , a podívejte se na téma [řešení potíží s IoT Edge](../../iot-edge/troubleshoot.md).

### <a name="connect-downstream-device-to-iot-edge-device"></a>Připojit zařízení pro IoT Edge zařízení

Připojení zařízení pro příjem dat k zařízení IoT Edge brány:

1. Pomocí SSH se připojte k virtuálnímu počítači pro dané zařízení a přihlaste se k němu. Název DNS pro tento virtuální počítač najdete v Azure Portal. Přejděte k virtuálnímu počítači **leafdevice** ve skupině **prostředků** příchozího přenosu dat.

    > [!TIP]
    > Než budete moct použít SSH k připojení z místního počítače nebo Azure Cloud Shell, možná budete muset otevřít port 22 pro přístup SSH na obou virtuálních počítačích.

1. Pokud chcete klonovat úložiště GitHub se zdrojovým kódem pro ukázkové zařízení pro příjem dat, spusťte následující příkaz:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Chcete-li zkopírovat požadovaný certifikát ze zařízení brány, spusťte následující `scp` příkazy. Tento `scp` příkaz používá název hostitele `edgegateway` k identifikaci virtuálního počítače brány. Zobrazí se výzva k zadání hesla:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Přejděte do složky *leafdevice* a nainstalujte požadované balíčky. Pak spusťte `build` skripty a, `start` abyste zařízení zřídili a připojili k bráně:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Zadejte ID zařízení, ID oboru a klíč SAS pro zařízení, které jste předtím vytvořili. Jako název hostitele zadejte `edgegateway` . Výstup příkazu vypadá takto:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Ověření

Pokud chcete ověřit, že je scénář spuštěný, přejděte do **zařízení IoT Edge brány** v IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Snímek obrazovky, který ukazuje stránku transformovaná data na zařízení.":::

- Vyberte **moduly**. Ověřte, zda jsou spuštěny tři IoT Edge moduly **$edgeAgent**, **$edgeHub** a **transformmodule** .
- Vyberte **zařízení pro příjem dat** a ověřte zřízení zařízení pro příjem dat.
- Vyberte **nezpracovaná data**. Data telemetrie v **nemodelovaná data** sloupce vypadají jako:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Vzhledem k tomu, že zařízení IoT Edge transformují data ze zařízení pro příjem dat, telemetrie je přidružená k zařízení brány v IoT Central. K vizualizaci telemetrie vytvořte novou verzi šablony **zařízení IoT Edge brány** s definicemi pro typy telemetrie.

## <a name="data-transformation-at-egress"></a>Transformace dat při odchozím přenosu

Zařízení můžete připojit k IoT Central, exportovat data zařízení do výpočetního modulu, který je transformuje, a poté přenést transformovaná data zpět do IoT Central pro správu a správu zařízení. Například:

- Vaše zařízení odesílají data o umístění do IoT Central.
- IoT Central exportuje data do výpočetního modulu, který vylepšuje data o poloze s informacemi o počasí.
- Výpočetní modul odesílá rozšířená data zpět do IoT Central.

K transformaci dat exportovaných z IoT Central můžete použít nástroj [IoT Central Device most](https://github.com/Azure/iotc-device-bridge) jako výpočetní modul.

Výhodou transformace dat při odchozím přenosu je to, že vaše zařízení se připojují přímo k IoT Central, což usnadňuje posílání příkazů do zařízení nebo aktualizace vlastností zařízení. Pomocí této metody ale můžete použít více zpráv, než je měsíční plnění, a zvýšit náklady na používání Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Použití mostu zařízení IoT Central k transformaci dat zařízení

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Transformace dat při odchozím přenosu pomocí IoT Edge" border="false":::

V tomto scénáři výpočetní modul transformuje data zařízení exportovaná z IoT Central před odesláním zpět do aplikace IoT Central. Na nejvyšší úrovni je postup pro konfiguraci tohoto scénáře následující:

1. **Nastavení modulu Compute:** Vytvořte most IoT Central zařízení, který bude sloužit jako výpočetní modul pro transformaci dat.

1. **Transformovat data zařízení v mostu zařízení:** Transformujte data v mostu zařízení úpravou kódu funkce mostu zařízení pro případ použití transformace dat.

1. **Povolit tok dat z IoT Central do mostu zařízení:** Exportujte data z IoT Central do mostu zařízení pro transformaci. Pak předejte transformovaná data zpátky do IoT Central. Při vytváření exportu dat použijte filtry vlastností zprávy a exportujte pouze netransformovaná data.

1. **Ověření**: Připojte zařízení k aplikaci IoT Central a zkontrolujte data nezpracovaných zařízení a transformovaná data v IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
V příkladu popsaném v následujících částech zařízení odesílají do zařízení IoT Edge brány data sdíleného svazku clusteru v následujícím formátu:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

K transformaci dat zařízení můžete použít most zařízení:

- Změna jednotky teploty z centigrade na Fahrenheita.
- Rozšíření dat zařízení pomocí dat o počasí získaných z otevřené služby [počasí](https://openweathermap.org/) pro hodnoty zeměpisné šířky a délky.

Most zařízení pak pošle transformovaná data do IoT Central v následujícím formátu:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Následující kroky ukazují, jak nastavit a nakonfigurovat tento scénář:

### <a name="retrieve-your-iot-central-connection-settings"></a>Načtení nastavení připojení IoT Central

Před nastavením tohoto scénáře je potřeba získat některá nastavení připojení z aplikace IoT Central:

1. Přihlaste se k aplikaci IoT Central.

1. Přejděte do **administrace > připojení zařízení**.

1. Poznamenejte si **Rozsah ID**. Tuto hodnotu použijete později.

1. Vyberte skupinu pro zápis **SAS-IoT-Device** . Poznamenejte si primární klíč sdíleného přístupového podpisu. Tuto hodnotu použijete později.

### <a name="set-up-a-compute-engine"></a>Nastavení výpočetní modul

Tento scénář používá stejné nasazení Azure Functions jako most IoT Central zařízení. Chcete-li nasadit most zařízení, klikněte na tlačítko **nasadit do Azure** níže a použijte informace v následující tabulce k dokončení formuláře **vlastního nasazení** :

| Pole | Hodnota |
| ----- | ----- |
| Skupina prostředků | Vytvořte novou skupinu prostředků s názvem. `egress-scenario` |
| Oblast | Vyberte oblast, která je k vám nejblíže. |
| ID oboru | Použijte **Rozsah ID** , který jste si poznamenali dříve. |
| IoT Central klíč SAS | Použijte primární klíč sdíleného přístupového podpisu pro skupinu pro zápis **SAS-IoT-Device** . Tuto hodnotu jste si poznamenali dříve. |

[ ![ Nasaďte do Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**. Vytvoření funkce Azure a souvisejících prostředků ve skupině prostředků **výstupních scénářů** trvá několik minut.

### <a name="transform-device-data-in-the-device-bridge"></a>Transformace dat zařízení v mostu zařízení

Konfigurace mostu zařízení pro transformaci exportovaných dat zařízení:

1. Získejte klíč rozhraní API aplikace z otevřené služby počasí. Účet je zdarma s omezeným využitím služby. Pokud chcete vytvořit klíč rozhraní API aplikace, vytvořte účet na [otevřeném portálu služby na počasí](https://openweathermap.org/) a postupujte podle pokynů. Později použijete otevřený klíč rozhraní API počasí.

1. V Azure Portal přejděte na Function App ve skupině prostředků **výstup-scénář** .

1. V levém navigačním panelu vyberte ve **vývojových nástrojích** možnost **Editor služby App Service (Preview)**.

1. Vyberte **Přejít &rarr;** a otevřete stránku **Editor služby App Service** . Proveďte následující změny:

    1. Otevřete soubor *wwwroot/IoTCIntegration/index.js* . Nahraďte veškerý kód v tomto souboru kódem v [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. V novém *index.js* aktualizujte `openWeatherAppId` soubor proměnných pomocí otevřeného klíče rozhraní API pro počasí, který jste získali dříve.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Přidejte vlastnost Message do dat odesílaných funkcí do IoT Central. IoT Central používá tuto vlastnost k zabránění exportu transformovaných dat. Chcete-li tuto změnu provést, otevřete soubor *wwwroot/IoTCIntegration/lib/engine.js* . Vyhledejte následující kód:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Přidejte následující kód hned za kód v předchozím fragmentu kódu:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Pro referenci můžete zobrazit dokončený příklad souboru [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) .

1. V **Editor služby App Service** v levém navigačním panelu vyberte **Konzola** . Spuštěním následujících příkazů nainstalujte požadované balíčky:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Spuštění tohoto příkazu může trvat několik minut.

1. Vraťte se na stránku **Přehled funkce Azure** a restartujte funkci:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Restartujte funkci":::

1. V levém navigačním panelu vyberte **funkce** . Pak vyberte **IoTCIntegration**. Vyberte **kód + test**.

1. Poznamenejte si adresu URL funkce. tuto hodnotu budete potřebovat později:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Získat adresu URL funkce":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Povolit tok dat z IoT Central do mostu zařízení

Tato část popisuje, jak nastavit aplikaci Azure IoT Central.

Nejdřív uložte soubor [modelu zařízení](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) do místního počítače.

Chcete-li do aplikace IoT Central přidat šablonu zařízení, přejděte do IoT Central aplikace a pak:

1. Přihlaste se k aplikaci IoT Central a přejděte na stránku **šablony zařízení** .

1. Vyberte **+ Nový**, vyberte **zařízení IoT**, vyberte **Další: přizpůsobit**, jako název šablony zadejte *výpočetní model* . Vyberte **Další: Kontrola**. Potom vyberte **Vytvořit**.

1. Vyberte **importovat model** a v souboru, který jste předtím stáhli, vyhledejte *model.js* .

1. Po importu modelu vyberte **publikovat** a publikujte šablonu zařízení **COMPUTE model** .

Nastavení exportu dat pro odeslání dat do mostu zařízení:

1. V aplikaci IoT Central vyberte **exportovat data**.

1. Pokud chcete vytvořit cíl pro použití s mostem zařízení, vyberte **+ Nový cíl** . Pro **cílový typ** vyberte **Webhook** a zavolejte cílovou *výpočetní funkci*. V poli Adresa URL zpětného volání vyberte Vložit do adresy URL funkce, na kterou jste si poznamenali dříve. Ponechte **autorizaci** **bez ověření**.

1. Uložte změny.

1. Vyberte **+ Nový export** a vytvořte export dat s názvem *Export COMPUTE*.

1. Přidejte filtr pro export dat zařízení jenom pro šablonu zařízení, kterou používáte. Vyberte **+ filtrovat**, vyberte položku **Šablona zařízení**, vyberte operátor **rovná** se a vyberte šablonu **výpočetního modelu** zařízení, kterou jste právě vytvořili.

1. Přidejte filtr zpráv, který bude rozlišovat mezi transformovanými a netransformovanými daty. Tento filtr zabraňuje odeslání transformovaných hodnot zpátky do mostu zařízení. Vyberte **+ filtr vlastnosti zprávy** a zadejte hodnotu název *vypočítané* a pak vyberte operátor **neexistuje**. Řetězec `computed` se používá jako klíčové slovo v ukázkovém kódu mostu zařízení.

1. V části cíl vyberte cíl **výpočetní funkce** , který jste vytvořili dříve.

1. Uložte změny. Po minutě se **stav exportu** zobrazí jako **v pořádku**.

### <a name="verify"></a>Ověření

Ukázkové zařízení, které používáte k otestování tohoto scénáře, je napsané v Node.js. Ujistěte se, že máte na svém místním počítači nainstalované Node.js a NPM. Pokud tyto požadavky nechcete instalovat, použijte[Azure Cloud Shell](https://shell.azure.com/) , která je předinstalována.

Spuštění ukázkového zařízení, které testuje scénář:

1. Naklonujte úložiště GitHub, které obsahuje vzorový kód, a spusťte následující příkaz:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Pokud chcete k aplikaci IoT Central připojit ukázkové zařízení, upravte nastavení připojení v souboru *IoT-Central-COMPUTE/Device/device.js* . Nahraďte klíč ID oboru a klíč SAS skupiny hodnotami, které jste si poznamenali dříve:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Uložte změny.

1. Pomocí následujících příkazů nainstalujte požadované balíčky a spusťte zařízení:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Výsledek tohoto příkazu vypadá jako na následujícím výstupu:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. V aplikaci IoT Central přejděte do zařízení s názvem **computeDevice**. V zobrazení **nezpracovaná data** existují dva různé proudy telemetrie, které se přibližně každých pět sekund zobrazují. Datový proud s nemodelovaná data je původní telemetrie. datový proud s modelem dat je data, která funkce transformují:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Snímek obrazovky zobrazující původní a transformovaná nezpracovaná data":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky Azure, které jste vytvořili, a postupovat podle kroků v tomto průvodci, odstraňte [skupiny prostředků v Azure Portal](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

Mezi dvě skupiny prostředků, které jste použili v této příručce, patří **scénář pro** příchozí a **odchozí** komunikaci.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s různými možnostmi pro transformaci dat zařízení IoT Central, a to v příchozím i odchozím přenosu. Tento článek obsahuje návody pro dva konkrétní scénáře:

- Použijte modul IoT Edge k transformaci dat ze všech podřízených zařízení předtím, než se data odešlou do aplikace IoT Central.
- Použijte Azure Functions k transformaci dat mimo IoT Central. V tomto scénáři IoT Central používá export dat k odesílání příchozích dat do funkce Azure, která se má transformovat. Funkce pošle transformovaná data zpátky do vaší aplikace IoT Central.

Teď, když jste se naučili, jak transformovat data zařízení mimo vaši aplikaci IoT Central Azure, se můžete dozvědět, [jak pomocí analýzy analyzovat data zařízení v IoT Central](howto-create-analytics.md).
