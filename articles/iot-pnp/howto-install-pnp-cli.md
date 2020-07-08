---
title: Použití rozšíření Azure IoT pro Azure CLI k interakci se zařízeními IoT technologie Plug and Play Preview | Microsoft Docs
description: Nainstalujte rozšíření Azure IoT pro Azure CLI a použijte ho k interakci se zařízeními IoT technologie Plug and Play připojenými ke službě IoT Hub.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770447"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalace a použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Hub. Rozhraní příkazového řádku Azure je dostupné v systémech Windows, Linux a MacOS. Rozhraní příkazového řádku Azure je také předem nainstalováno v [Azure Cloud Shell](https://shell.azure.com). Rozhraní příkazového řádku Azure umožňuje spravovat prostředky Azure IoT Hub, instance služby Device Provisioning a propojená centra bez nutnosti instalovat jakákoli rozšíření.

Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování zařízení IoT technologie Plug and Play ve verzi Preview. Rozšíření můžete použít k těmto akcím:

- Připojte se k zařízení.
- Podívejte se na telemetrii, kterou zařízení odesílá.
- Práce s vlastnostmi zařízení
- Zavolejte příkazy zařízení.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI.
- Použijte rozšíření pro interakci a testování vašich zařízení.
- Pomocí tohoto rozšíření můžete spravovat rozhraní v úložišti modelu.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalace rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1 – instalace rozhraní příkazového řádku Azure

Podle [pokynů k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nastavte Azure CLI ve vašem prostředí. Pokud chcete použít všechny níže uvedené příkazy, musí mít vaše verze Azure CLI verzi 2.0.73 nebo vyšší. Ke kontrole použijte příkaz `az -–version`.

### <a name="step-2---install-iot-extension"></a>Krok 2 – instalace rozšíření IoT

Soubor [Readme rozšíření IoT](https://github.com/Azure/azure-iot-cli-extension) obsahuje popis několika způsobů instalace rozšíření. Nejjednodušším způsobem je spustit příkaz `az extension add --name azure-iot`. Po instalaci můžete pomocí příkazu `az extension list` ověřit aktuálně nainstalovaná rozšíření nebo pomocí příkazu `az extension show --name azure-iot` zobrazit podrobnosti o rozšíření IoT. K odebrání rozšíření můžete použít příkaz `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure

### <a name="prerequisites"></a>Požadavky

Pokud se chcete přihlásit ke svému předplatnému Azure, spusťte následující příkaz:

```azurecli
az login
```

> [!NOTE]
> Pokud používáte Azure Cloud Shell, jste přihlášeni automaticky a nemusíte spouštět předchozí příkaz.

Pokud chcete používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI, budete potřebovat:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je například [vytvoření centra IoT pomocí Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Abyste mohli spouštět příkazy rozšíření Azure IoT, potřebujete připojovací řetězec služby IoT Hub. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

- Zařízení zaregistrované ve službě IoT Hub. K registraci zařízení můžete použít následující příkaz rozhraní příkazového řádku Azure, nezapomeňte nahradit `{YourIoTHubName}` `{YourDeviceID}` zástupné symboly a pomocí vašich hodnot:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Některé příkazy potřebují připojovací řetězec pro úložiště podnikového modelu. Úložiště modelu pro vaši společnost se vytvoří při první [registraci na portálu Azure Certified for IoT](howto-onboard-portal.md). Třetí strana může sdílet připojovací řetězec úložiště modelu s vámi, abyste měli přístup k jejich rozhraním a modelům.

### <a name="interact-with-a-device"></a>Interakce se zařízením

Rozšíření můžete použít k zobrazení a interakci se zařízeními IoT technologie Plug and Play, která jsou připojená ke službě IoT Hub. Toto rozšíření spolupracuje s digitálním vlákna, které představuje zařízení IoT technologie Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Vypsat zařízení a rozhraní

Vypsat všechna zařízení v IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Vypíše všechna rozhraní registrovaná zařízením IoT technologie Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Vlastnosti

Vypíše všechny vlastnosti a hodnoty vlastností pro rozhraní na zařízení:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Nastavte hodnotu vlastnosti pro čtení i zápis:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Příkladem souboru datové části pro nastavení vlastnosti **název** v rozhraní **snímače** zařízení na **Contoso** vypadá takto:

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

Vypíše všechny příkazy pro rozhraní v zařízení:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Bez `--repo-login` parametru tento příkaz použije úložiště veřejného modelu.

Vyvolat příkaz:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitální zdvojené události

Monitorujte všechny události IoT technologie Plug and Play digitálních událostí z konkrétního zařízení a rozhraní, které se přesměrují do skupiny uživatelů centra událostí **$Default** :

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorujte všechny události IoT technologie Plug and Play digitálních událostí z konkrétního zařízení a rozhraní, které se přesměrují na konkrétní skupinu příjemců:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Správa rozhraní v úložišti modelu

V následujících příkazech se používá veřejné úložiště modelu IoT technologie Plug and Play. Pokud chcete použít úložiště podnikového modelu, přidejte `--login` argument s připojovacím řetězcem úložiště modelu.

Vypíše rozhraní ve veřejném úložišti IoT technologie Plug and Play model:

```azurecli
az iot pnp interface list
```

Zobrazení rozhraní ve veřejném úložišti IoT technologie Plug and Play modelu:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

V úložišti IoT technologie Plug and Play společnosti vytvořte rozhraní:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

V úložišti veřejného modelu nemůžete přímo vytvořit rozhraní.

Aktualizujte rozhraní v úložišti IoT technologie Plug and Play společnosti:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nemůžete přímo aktualizovat rozhraní v úložišti veřejného modelu.

Publikujte rozhraní z úložiště IoT technologie Plug and Play společnosti do úložiště veřejného modelu. Tato operace způsobí neproměnlivé rozhraní:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Pouze partneři Microsoftu mohou publikovat rozhraní do úložiště veřejného modelu.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Správa modelů schopností zařízení v úložišti modelu

V následujících příkazech se používá veřejné úložiště modelu IoT technologie Plug and Play. Pokud chcete použít úložiště podnikového modelu, přidejte `--login` argument s připojovacím řetězcem úložiště modelu.

Seznamte se s možnostmi modelu zařízení v úložišti IoT technologie Plug and Play Public model:

```azurecli
az iot pnp capability-model list
```

Zobrazit model schopností zařízení v úložišti IoT technologie Plug and Play Public model:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Vytvoření modelu schopností zařízení v úložišti IoT technologie Plug and Play společnosti:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Model nelze přímo vytvořit v úložišti veřejného modelu.

Aktualizujte model schopností zařízení v úložišti IoT technologie Plug and Play společnosti:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Model nelze přímo aktualizovat v úložišti veřejného modelu.

Publikujte model schopností zařízení z úložiště IoT technologie Plug and Play společnosti do úložiště veřejného modelu. Tato operace způsobuje neproměnlivý model:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Pouze partneři Microsoftu mohou publikovat modely do úložiště veřejného modelu.

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat rozšíření Azure IoT pro rozhraní příkazového řádku Azure pro interakci s technologie Plug and Playmi zařízeními. Navržený další krok se naučíte, jak [Spravovat modely](./howto-manage-models.md).
