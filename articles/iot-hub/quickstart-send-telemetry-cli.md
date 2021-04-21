---
title: Rychlý Start – odeslání telemetrie do Azure IoT Hub (CLI) – rychlý Start
description: V tomto rychlém startu IoT Hub se dozvíte, jak začít používat Azure CLI k vytvoření centra IoT, odesílání telemetrie a zobrazování zpráv mezi zařízením a centrem.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: a34fd5480ae47678f250dbf888005c396ba32f38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792142"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub a jejich sledování pomocí Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu použijete rozhraní příkazového řádku Azure CLI k vytvoření IoT Hub a simulovaného zařízení, odeslání telemetrie zařízení do centra a odeslání zprávy typu cloud-zařízení. K vizualizaci metrik zařízení slouží také Azure Portal. Toto je základní pracovní postup pro vývojáře, kteří používají CLI k interakci s aplikací IoT Hub.

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, [vytvořte ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ještě před tím, než začnete.
- Azure CLI Všechny příkazy v tomto rychlém startu můžete spustit pomocí Azure Cloud Shell interaktivního prostředí CLI, které běží v prohlížeči. Pokud používáte Cloud Shell, nemusíte nic instalovat. Pokud dáváte přednost používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.76 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Bez ohledu na to, jestli spouštíte rozhraní příkazového řádku místně nebo v Cloud Shell, ponechejte portál otevřený v prohlížeči.  Použijete ji později v tomto rychlém startu.

## <a name="launch-the-cloud-shell"></a>Spuštění Cloud Shell
V této části spustíte instanci Azure Cloud Shell. Pokud použijete rozhraní příkazového řádku místně, přejděte k části [Příprava dvou relací](#prepare-two-cli-sessions)rozhraní příkazového řádku.

Spuštění Cloud Shell:

1. Vyberte tlačítko **Cloud Shell** v pravém horním panelu nabídek v Azure Portal. 

    ![Tlačítko Cloud Shell Azure Portal](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Pokud Cloud Shellte poprvé, zobrazí se výzva k vytvoření úložiště, které je nutné k použití Cloud Shell.  Vyberte předplatné, ve kterém chcete vytvořit účet úložiště a sdílenou složku Microsoft Azure Files. 

2. V rozevíracím seznamu **Vybrat prostředí** vyberte preferované prostředí rozhraní příkazového řádku. V tomto rychlém startu se používá prostředí **bash** . Všechny následující příkazy rozhraní příkazového řádku fungují i v prostředí PowerShellu. 

    ![Vybrat prostředí CLI](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Příprava dvou relací rozhraní příkazového řádku

V této části připravíte dvě relace Azure CLI. Pokud používáte Cloud Shell, spustíte tyto dvě relace na samostatných kartách prohlížeče. Pokud používáte místní klienta CLI, spustíte dvě samostatné instance rozhraní příkazového řádku. První relaci použijete jako simulované zařízení a druhou relaci pro monitorování a posílání zpráv. Pokud chcete spustit příkaz, vyberte **Kopírovat** a zkopírujte blok kódu v tomto rychlém startu, vložte ho do relace prostředí a spusťte ho.

Azure CLI vyžaduje, abyste se přihlásili ke svému účtu Azure. Veškerá komunikace mezi relací prostředí Azure CLI a službou IoT Hub je ověřená a šifrovaná. V důsledku toho tento rychlý Start nepotřebuje další ověřování, které byste použili u reálného zařízení, jako je například připojovací řetězec.

*  Spuštěním příkazu [AZ Extension Add](/cli/azure/extension#az_extension_add) přidejte Microsoft Azure rozšíření IoT pro Azure CLI do prostředí CLI. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Až nainstalujete rozšíření Azure IOT, nemusíte ho v žádné Cloud Shell relaci znovu instalovat. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Otevřete druhou relaci CLI.  Pokud používáte Cloud Shell, vyberte **otevřít novou relaci**. Pokud rozhraní příkazového řádku používáte místně, otevřete druhou instanci. 

    >[!div class="mx-imgBorder"]
    >![Otevřít novou relaci Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub
V této části pomocí Azure CLI vytvoříte skupinu prostředků a IoT Hub.  Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. IoT Hub funguje jako centrální Centrum zpráv pro obousměrnou komunikaci mezi vaší aplikací IoT a zařízeními. 

> [!TIP]
> Volitelně můžete pomocí [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)nebo jiných programových metod vytvořit skupinu prostředků Azure, IoT Hub a další prostředky.  

1. Spuštěním příkazu [AZ Group Create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků. Následující příkaz vytvoří skupinu prostředků s názvem *MyResourceGroup* v umístění *eastus* . 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Spuštěním příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az_iot_hub_create) vytvořte centrum IoT. Vytvoření centra IoT může trvat několik minut. 

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. Název centra IoT musí být v Azure globálně jedinečný. Tento zástupný symbol se používá ve zbývající části tohoto rychlého startu k vyjádření názvu služby IoT Hub.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Vytvoření a monitorování zařízení
V této části vytvoříte simulované zařízení v první relaci CLI. Simulované zařízení odesílá telemetrii zařízení do služby IoT Hub. V druhé relaci rozhraní příkazového řádku můžete monitorovat události a telemetrii a odesílat do simulovaného zařízení zprávu typu cloud-zařízení.

Vytvoření a spuštění simulovaného zařízení:
1. Spusťte příkaz [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) v první relaci CLI. Tím se vytvoří simulovaná identita zařízení. 

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    *simDevice*. Tento název můžete použít přímo pro simulované zařízení ve zbývající části tohoto rychlého startu. Volitelně použijte jiný název. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Spusťte příkaz [AZ IoT Device simuluje](/cli/azure/ext/azure-iot/iot/device#ext-azure-iot-az-iot-device-simulate) v první relaci CLI.  Tím se spustí simulované zařízení. Zařízení pošle telemetrii do služby IoT Hub a přijímá z ní zprávy.  

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Postup monitorování zařízení:
1. V druhé relaci CLI spusťte příkaz [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) . Tím se spustí monitorování simulovaného zařízení. Výstup zobrazuje telemetrii, kterou simulované zařízení odesílá do centra IoT.

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell monitorování událostí](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Po monitorování simulovaného zařízení v druhé relaci CLI ukončete monitorování stisknutím kombinace kláves CTRL + C. 

## <a name="use-the-cli-to-send-a-message"></a>Použití rozhraní příkazového řádku k odeslání zprávy
V této části použijete druhou relaci CLI k odeslání zprávy na simulované zařízení.

1. V první relaci CLI potvrďte, že je simulované zařízení spuštěné. Pokud se zařízení zastavilo, spusťte následující příkaz, který ho spustí:

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. V druhé relaci CLI spusťte příkaz [AZ IoT Device C2D-Message Send](/cli/azure/ext/azure-iot/iot/device/c2d-message#ext-azure-iot-az-iot-device-c2d-message-send) . Tím se do simulovaného zařízení pošle zpráva typu cloud-zařízení ze služby IoT Hub. Zpráva obsahuje řetězec a dvě páry klíč-hodnota.  

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Volitelně můžete odesílat zprávy z cloudu na zařízení pomocí Azure Portal. Provedete to tak, že přejdete na stránku Přehled pro váš IoT Hub, vyberete **zařízení IoT**, vyberete simulované zařízení a vyberete **zprávu na zařízení**. 

1. V první relaci CLI potvrďte, že simulované zařízení přijalo zprávu. 

    ![Cloud Shell zpráv z cloudu na zařízení](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Po zobrazení zprávy Zavřete druhou relaci CLI. Nechejte první relaci rozhraní příkazového řádku otevřenou. Použijete ho k vyčištění prostředků v pozdějším kroku.

## <a name="view-messaging-metrics-in-the-portal"></a>Zobrazit metriky zasílání zpráv na portálu
Azure Portal vám umožní spravovat všechny aspekty IoT Hub a zařízení. V typické IoT Hub aplikaci, která ingestuje telemetrii ze zařízení, můžete chtít monitorovat zařízení nebo zobrazovat metriky v telemetrie zařízení. 

Chcete-li vizualizovat metriky zasílání zpráv v Azure Portal:
1. V navigační nabídce vlevo na portálu vyberte **všechny prostředky**. Zobrazí se seznam všech prostředků ve vašem předplatném, včetně služby IoT Hub, kterou jste vytvořili. 

1. Vyberte odkaz ve službě IoT Hub, kterou jste vytvořili. Portál zobrazí stránku Přehled pro centrum.

1. V levém podokně IoT Hub vyberte **metriky** . 

    ![IoT Hub metriky zasílání zpráv](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Do **oboru** zadejte název služby IoT Hub.

2. V **oboru názvů metriky** vyberte *Standardní metriky IoT Hub* .

3. Vyberte *Celkový počet zpráv použitých* v **metrikě**. 

4. Najeďte ukazatelem myši na oblast časové osy, do které zařízení odesílá zprávy. Celkový počet zpráv k určitému bodu v čase se zobrazí v levém dolním rohu časové osy.

    ![Zobrazit metriky služby Azure IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Volitelně můžete pomocí rozevíracího seznamu **metriky** zobrazit další metriky na simulovaném zařízení. Například *doručení zpráv C2d bylo dokončeno* nebo *Celkový počet zařízení (Preview)*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete prostředky Azure vytvořené v rámci tohoto rychlého startu, můžete je odstranit pomocí Azure CLI.

Pokud budete pokračovat k dalšímu doporučenému článku, můžete zachovat prostředky, které jste už vytvořili, a znovu je použít. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Odstranění skupiny prostředků podle názvu:
1. Spusťte příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) . Tím odeberete skupinu prostředků, IoT Hub a registraci zařízení, kterou jste vytvořili.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Spuštěním příkazu [AZ Group list](/cli/azure/group#az_group_list) potvrďte odstranění skupiny prostředků.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste pomocí Azure CLI vytvořili službu IoT Hub, vytvořili simulované zařízení, odesílali telemetrii, sledovali telemetrii, odesílali zprávy typu cloud-zařízení a vyčistili prostředky. Pomocí Azure Portal vizualizujete metriky zasílání zpráv na vašem zařízení.

Pokud jste vývojářem zařízení, navržený další krok vám umožní zobrazit rychlý Start telemetrie, který používá sadu SDK pro zařízení Azure IoT pro jazyk C. volitelně si můžete prohlédnout jeden z dostupných článků o rychlý Start telemetrie Azure IoT Hub ve vašem preferovaném jazyce nebo sadě SDK.

> [!div class="nextstepaction"]
> [Rychlý Start: odeslání telemetrie ze zařízení do služby IoT Hub (C)](quickstart-send-telemetry-c.md)