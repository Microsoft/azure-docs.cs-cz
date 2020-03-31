---
title: Použití rozšíření Azure IoT pro Azure CLI k interakci se zařízeními IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Nainstalujte rozšíření Azure IoT pro Azure CLI a použijte ho k interakci se zařízeními IoT Plug and Play připojenými k mému centru IoT.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234687"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalace a použití rozšíření Azure IoT pro azure cli

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source multiplatformní příkazový řádek nástroj pro správu prostředků Azure, jako je IoT Hub. Rozhraní příkazového odpočitatelů Azure je dostupné ve Windows, Linuxu a MacOS. Vytelatá podávací miska Azure se taky předinstaluje v [prostředí Azure Cloud Shell](https://shell.azure.com). Rozhraní příkazového příkazu k řešení Azure umožňuje spravovat prostředky azure iot hubu, instance služby zřizování zařízení a propojená centra bez nutnosti instalace rozšíření.

Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování zařízení IoT Plug and Play Preview. Rozšíření můžete použít k:

- Připojte se k zařízení.
- Zobrazení telemetrie, kterou zařízení odesílá.
- Práce s vlastnostmi zařízení.
- Volání příkazů zařízení.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte rozšíření Azure IoT pro rozhraní příkazového příkazového příkazu k Azure.
- Pomocí rozšíření můžete se zařízeními komunikovat a testovat je.
- Rozšíření slouží ke správě rozhraní v úložišti modelu.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalace rozšíření Azure IoT pro azure cli

### <a name="step-1---install-the-azure-cli"></a>Krok 1 – Instalace příkazového příkazového příkazu Azure

Podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nastavte vnastavení příkazového příkazu Azure ve vašem prostředí. Chcete-li použít všechny příkazy níže, vaše verze Azure CLI musí být verze 2.0.73 nebo vyšší. Ke kontrole použijte příkaz `az -–version`.

### <a name="step-2---install-iot-extension"></a>Krok 2 – Instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Použití rozšíření Azure IoT pro azure cli

### <a name="prerequisites"></a>Požadavky

Pokud se chcete přihlásit k předplatnému Azure, spusťte následující příkaz:

```azurecli
az login
```

> [!NOTE]
> Pokud používáte cloudové prostředí Azure, jste automaticky přihlášeni a nemusíte spouštět předchozí příkaz.

Chcete-li použít rozšíření Azure IoT pro azure cli, potřebujete:

- Azure IoT hub. Existuje mnoho způsobů, jak přidat službu IoT hub do vašeho předplatného Azure, jako je [například vytvoření centra IoT pomocí azure cli](../iot-hub/iot-hub-create-using-cli.md). Ke spuštění příkazů rozšíření Azure IoT potřebujete připojovací řetězec služby IoT hub. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- Zařízení registrované ve vašem centru IoT hub. K registraci zařízení můžete použít následující příkaz příkazu Azure `{YourIoTHubName}` `{YourDeviceID}` CLI, nezapomeňte nahradit zástupné symboly vašimi hodnotami:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Některé příkazy potřebují připojovací řetězec pro úložiště modelu společnosti. Úložiště modelu pro vaši společnost se vytvoří při prvním [na palubě portálu Azure Certified for IoT](howto-onboard-portal.md). Třetí strana může sdílet svůj řetězec připojení úložiště modelu s vámi, aby vám přístup k jejich rozhraní a modely.

### <a name="interact-with-a-device"></a>Interakce se zařízením

Rozšíření můžete použít k zobrazení a interakci se zařízeními IoT Plug and Play, která jsou připojená k centru IoT Hub. Rozšíření funguje s digitální dvojče, které představuje zařízení IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Seznam zařízení a rozhraní

Seznam všech zařízení na ioT hubu:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Seznam všech rozhraní registrovaných zařízením IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Vlastnosti

Seznam všech vlastností a hodnot vlastností rozhraní na zařízení:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Nastavte hodnotu vlastnosti pro čtení a zápis:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Ukázkový soubor datové části pro nastavení vlastnosti **name** v rozhraní **senzoru** zařízení na **Contoso** vypadá takto:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Příkazy

Seznam všech příkazů pro rozhraní na zařízení:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Bez `--repo-login` parametru tento příkaz používá úložiště veřejného modelu.

Vyvolat příkaz:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Události digitálního dvojčete

Sledujte všechny události digitálního dvojčete IoT Plug and Play z konkrétního zařízení a rozhraní, které přejdete do skupiny spotřebitelů **centra událostí $Default:**

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Sledujte všechny události digitálního dvojčete IoT Plug and Play z konkrétního zařízení a rozhraní, které bude pro konkrétní skupinu spotřebitelů:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Správa rozhraní v úložišti modelů

Následující příkazy používají veřejné úložiště modelu IoT Plug and Play. Chcete-li použít úložiště modelu `--login` společnosti, přidejte argument s připojovacím řetězcem úložiště modelu.

Seznam rozhraní ve veřejném úložišti modelů IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Zobrazení rozhraní ve veřejném úložišti modelu IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Vytvořte rozhraní v úložišti firemních modelů IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nelze přímo vytvořit rozhraní v úložišti veřejného modelu.

Aktualizace rozhraní v úložišti firemních modelů IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Rozhraní nelze přímo aktualizovat v úložišti veřejného modelu.

Publikujte rozhraní z úložiště firemních modelů IoT Plug and Play do úložiště veřejných modelů. Tato operace umožňuje rozhraní neměnné:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Rozhraní do úložiště veřejného modelu mohou publikovat pouze partneři společnosti Microsoft.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Správa modelů schopností zařízení v úložišti modelů

Následující příkazy používají veřejné úložiště modelu IoT Plug and Play. Chcete-li použít úložiště modelu `--login` společnosti, přidejte argument s připojovacím řetězcem úložiště modelu.

Seznam modelů schopností zařízení v úložišti veřejných modelů IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Zobrazení modelu schopností zařízení v úložišti veřejného modelu IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Vytvořte model schopností zařízení v úložišti firemních modelů IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nelze přímo vytvořit model v úložišti veřejného modelu.

Aktualizace modelu schopností zařízení v úložišti firemních modelů IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Model nelze přímo aktualizovat v úložišti veřejného modelu.

Publikujte model schopností zařízení z úložiště firemních modelů IoT Plug and Play do úložiště veřejných modelů. Tato operace činí model neměnným:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Modely do úložiště veřejných modelů mohou publikovat pouze partneři společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste se naučili, jak nainstalovat a používat rozšíření Azure IoT pro Azure CLI pro interakci s vašimi zařízeními Plug and Play. Dalším navrhovaným krokem je naučit se [spravovat modely](./howto-manage-models.md).
