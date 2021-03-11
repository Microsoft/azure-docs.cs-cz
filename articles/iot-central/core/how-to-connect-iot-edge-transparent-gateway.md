---
title: Připojení IoT Edge transparentní brány k aplikaci Azure IoT Central
description: Postup připojení zařízení přes IoT Edge transparentní bránu k aplikaci IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bdfb5f65106f3f8843b4aa52b752f5e563ab03f0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620005"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Postup připojení zařízení přes IoT Edge transparentní bránu

Zařízení IoT Edge může fungovat jako brána, která poskytuje připojení mezi ostatními zařízeními v místní síti a vaší IoT Central aplikace. Bránu použijete, když zařízení nemá přímý přístup k aplikaci IoT Central.

IoT Edge podporuje vzory [ *transparentního* a *Překladatelského* brány](../../iot-edge/iot-edge-as-gateway.md). Tento článek shrnuje, jak implementovat model transparentní brány. V tomto modelu brána předá do IoT Hubho koncového bodu ve vaší aplikaci IoT Central zprávy ze zařízení pro příjem dat.

Tento článek používá virtuální počítače pro hostování zařízení a brány pro příjem dat. V reálném scénáři by se pro zařízení a bránu spouštěla na fyzických zařízeních v místní síti.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto kurzu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

Pokud chcete postupovat podle kroků v tomto článku, Stáhněte si do počítače následující soubory:

- [Model zařízení termostatu](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Transparentní manifest brány](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Přidat šablony zařízení

Zařízení pro příjem dat i zařízení brány vyžadují šablony zařízení v IoT Central. IoT Central umožňuje modelovat vztah mezi vašimi zařízeními pro příjem dat a bránou, abyste je mohli zobrazit a spravovat po jejich připojení.

Chcete-li vytvořit šablonu zařízení pro zařízení se systémem pro příjem dat, vytvořte standardní šablonu zařízení, která bude modelovat schopnosti vašeho zařízení. Příklad uvedený v tomto článku používá model zařízení termostatu.

Vytvoření šablony zařízení pro zařízení se systémem pro příjem dat:

1. Vytvořte šablonu zařízení a jako typ šablony vyberte **zařízení IoT** .

1. Na stránce **přizpůsobit** průvodce zadejte pro šablonu zařízení název, třeba *termostat* .

1. Po vytvoření šablony zařízení vyberte **importovat model**. Vyberte model, například *thermostat-1.jspro* soubor, který jste předtím stáhli.

1. Chcete-li pro termostat generovat některá výchozí zobrazení, vyberte možnost zobrazení a pak zvolte možnost **Generovat výchozí zobrazení**.

1. Publikujte šablonu zařízení.

Vytvoření šablony zařízení pro transparentní bránu IoT Edge:

1. Vytvořte šablonu zařízení a jako typ šablony vyberte **Azure IoT Edge** .

1. Na stránce **vlastní nastavení** v průvodci zadejte název, jako je například *Brána Edge* pro šablonu zařízení.

1. Na stránce **vlastní nastavení** v průvodci ověřte **zařízení brány se zařízeními pro příjem dat**.

1. Na stránce **přizpůsobit** v průvodci vyberte **Procházet**. Nahrajte *EdgeTransparentGatewayManifest.jsdo* souboru, který jste předtím stáhli.

1. Přidejte položku v **relaci** k šabloně zařízení pro příjem dat.

Na následujícím snímku obrazovky vidíte stránku **relace** pro zařízení IoT Edge brány, které má zařízení s dodanými zařízeními, která používají šablonu pro zařízení **termostata** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Snímek obrazovky se vztahem šablony zařízení IoT Edge brány se šablonou zařízení s termostatem pro příjem dat.":::

Předchozí snímek obrazovky zobrazuje šablonu zařízení IoT Edge brány bez definovaných modulů. Transparentní brána nevyžaduje žádné moduly, protože modul runtime IoT Edge předává zprávy ze zařízení pro příjem dat do IoT Central. Pokud brána potřebuje odeslat telemetrii, synchronizovat vlastnosti nebo zpracovat příkazy, můžete tyto možnosti definovat ve výchozí komponentě nebo v modulu.

Před publikováním šablon brány a zařízení pro příjem dat přidejte všechny požadované vlastnosti cloudu a zobrazení.

## <a name="add-the-devices"></a>Přidat zařízení

Když přidáte zařízení do aplikace IoT Central, můžete definovat vztah mezi zařízeními pro příjem dat a transparentní bránou.

Postup přidání zařízení:

1. V aplikaci IoT Central přejděte na stránku zařízení.

1. Přidejte instanci transparentní brány IoT Edge zařízení. V tomto článku je ID zařízení brány `edgegateway` .

1. Přidejte jednu nebo více instancí zařízení pro příjem dat. V tomto článku jsou v zařízeních pro příjem dat termostaty s ID `thermostat1` a `thermostat2` .

1. V seznamu zařízení vyberte všechna zařízení pro příjem dat a vyberte **připojit k bráně**.

Na následujícím snímku obrazovky vidíte seznam zařízení připojených k bráně na stránce **zařízení pro příjem dat** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Snímek obrazovky zobrazující seznam zařízení, která jsou připojená k transparentní bráně.":::

V transparentní bráně se zařízení pro příjem dat připojují k samotné bráně, nikoli k vlastnímu modulu hostovanému bránou.

Před nasazením zařízení budete potřebovat:

- **Rozsah ID** vaší aplikace IoT Central
- Hodnoty **ID zařízení** pro bránu a zařízení pro příjem dat.
- Hodnoty **primárního klíče** pro bránu a zařízení pro příjem dat.

Pokud chcete tyto hodnoty najít, přejděte na každé zařízení v seznamu zařízení a vyberte **připojit**. Než budete pokračovat, zaznamenejte si tyto hodnoty.

## <a name="deploy-the-gateway-and-devices"></a>Nasazení brány a zařízení

Následující kroky vám ukážou, jak nasadit bránu a zařízení pro příjem dat do virtuálních počítačů Azure, abyste si mohli vyzkoušet tento scénář. V reálném scénáři se pro zařízení a bránu spouští na fyzických zařízeních v místní síti.

Pokud chcete vyzkoušet scénář transparentní brány, vyberte následující tlačítko a nasaďte dva virtuální počítače se systémem Linux. Jeden virtuální počítač je transparentní IoT Edge bránou, druhá je zařízení, které simuluje termostat:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Po nasazení a spuštění těchto dvou virtuálních počítačů ověřte, že na virtuálním počítači běží zařízení IoT Edge brány `edgegateway` :

1. Přejít na stránku **zařízení** v aplikaci IoT Central. Pokud je zařízení IoT Edge brány připojené k IoT Central, stav se **zřídí**.

1. Otevřete zařízení IoT Edge brány a ověřte stav modulů na stránce **moduly** . Pokud se modul runtime IoT Edge úspěšně spustil, je **spuštěný** stav modulů **$edgeAgent** a **$edgeHub** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Snímek obrazovky znázorňující moduly $edgeAgent a $edgeHub běžící na IoT Edge bráně":::

> [!TIP]
> Může se stát, že budete muset několik minut počkat, než se virtuální počítač spustí, a zařízení se zřídí ve vaší aplikaci IoT Central.

## <a name="configure-the-gateway"></a>Konfigurace brány

Aby vaše zařízení IoT Edge fungovalo jako transparentní brána, potřebuje některé certifikáty k prokázání své identity pro všechna zařízení, která se budou podřízená. Tento článek používá ukázkové certifikáty. V produkčním prostředí použijte certifikáty od certifikační autority.

Pro vygenerování ukázkových certifikátů a jejich instalaci na zařízení brány:

1. Pomocí SSH se připojte k virtuálnímu počítači zařízení brány a přihlaste se k němu.

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

Pokud se modul runtime IoT Edge po změnách úspěšně spustí, změní se stav modulů **$edgeAgent** a **$edgeHub** na **spouštění** na stránce **moduly** pro vaše zařízení brány v IoT Central.

Pokud se modul runtime nespustí, zkontrolujte změny, které jste provedli v *souboru config. yaml* , a podívejte se na téma [řešení potíží s IoT Edge](../../iot-edge/troubleshoot.md).

Vaše transparentní brána je teď nakonfigurovaná a připravená na zahájení předávání telemetrie ze zařízení pro příjem dat.

## <a name="provision-a-downstream-device"></a>Zřízení zařízení pro příjem dat

V současné době IoT Edge nemůže do vaší aplikace IoT Central automaticky zřídit zařízení pro příjem dat. Následující kroky ukazují, jak zřídit `thermostat1` zařízení. K provedení těchto kroků budete potřebovat prostředí s nainstalovaným Pythonem 3,5 (nebo novějším) a připojením k Internetu. [Azure Cloud Shell](https://shell.azure.com/) má předem nainstalovaný Python 3,5:

1. Spusťte následující příkaz, který nainstaluje `azure.iot.device` modul:

    ```bash
    pip install azure.iot.device
    ```

1. Spusťte následující příkaz ke stažení skriptu Pythonu, který provádí zřizování:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Pokud chcete zřídit `thermostat1` zařízení pro příjem dat ve vaší aplikaci IoT Central, spusťte následující příkazy, které nahradí `{your application id scope}` a `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

V aplikaci IoT Central ověřte, že se teď **zřídí** **stav zařízení** pro zařízení thermostat1. 

## <a name="configure-a-downstream-device"></a>Konfigurace zařízení pro příjem dat

V předchozí části jste nakonfigurovali `edgegateway` virtuální počítač s ukázkovými certifikáty, abyste ho mohli spustit jako bránu. `leafdevice`Virtuální počítač je připravený k instalaci simulátoru termostatu, který používá bránu pro připojení k IoT Central.

`leafdevice`Virtuální počítač potřebuje kopii certifikátu kořenové certifikační autority, který jste vytvořili na `edgegateway` virtuálním počítači. Zkopírujte soubor */Home/AzureUser/certs/certs/Azure-IoT-test-Only.root.ca.CERT.pem* z `edgegateway` virtuálního počítače do domovského adresáře na `leafdevice` virtuálním počítači. K kopírování souborů do a z virtuálního počítače se systémem Linux můžete použít příkaz **SCP** .

Informace o tom, jak zkontrolovat připojení ze zařízení pro příjem dat do brány, najdete v tématu [Test připojení brány](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Spuštění simulátoru termostatu na `leafdevice` virtuálním počítači:

1. Stáhněte si ukázku Pythonu do domovského adresáře:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Nainstalujte modul Python pro zařízení Azure IoT:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Nastavte proměnné prostředí pro konfiguraci ukázky. Nahraďte `{your device shared key}` primárním klíčem, `thermostat1` který jste si poznamenali dříve. Tyto proměnné předpokládají, že název virtuálního počítače brány je `edgegateway` a ID termostatu zařízení je `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Všimněte si, jak připojovací řetězec používá název zařízení brány a nikoli název centra IoT.

1. Chcete-li spustit kód, použijte následující příkaz:

    ```bash
    python3 simple_thermostat.py
    ```

    Výstup z tohoto příkazu vypadá takto:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Pokud chcete zobrazit telemetrii v IoT Central, přejděte na stránku **Přehled** pro zařízení **thermostat1** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Snímek obrazovky zobrazující telemetrii ze zařízení pro příjem dat.":::

    Na stránce **o produktu** můžete zobrazit hodnoty vlastností odesílané ze zařízení pro příjem dat a na **příkazovém řádku** můžete volat příkazy v zařízení pro příjem dat.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat transparentní bránu pomocí IoT Central, je doporučeným dalším krokem Další informace o [IoT Edge](../../iot-edge/about-iot-edge.md).
