---
title: Rychlý start odesílání telemetrie do služby Azure IoT Hub (CLI)
description: Tento rychlý start ukazuje vývojářům nové služby IoT Hub, jak začít pomocí rozhraní příkazového příkazu K On-Azure k vytvoření centra IoT, odesílání telemetrie a zobrazení zpráv mezi zařízením a centrem.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851425"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Úvodní příručka: Odeslání telemetrie ze zařízení do centra IoT a jeho monitorování pomocí rozhraní příkazového příkazového příkazu Azure

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub je služba Azure, která umožňuje ingestovat velké objemy telemetrických dat ze zařízení IoT do cloudu pro účely uložení nebo zpracování. V tomto rychlém startu použijete rozhraní příkazového příkazu K řešení Azure k vytvoření služby IoT Hub a simulované zařízení, odeslání telemetrie zařízení do centra a odeslání zprávy z cloudu na zařízení. Portál Azure se také používá k vizualizaci metrik zařízení. Toto je základní pracovní postup pro vývojáře, kteří používají rozhraní příkazového příkazu k interakci s aplikací služby IoT Hub.

## <a name="prerequisites"></a>Požadavky
- Pokud nemáte předplatné Azure, [vytvořte si ho zdarma,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Azure CLI. Všechny příkazy v tomto rychlém startu můžete spustit pomocí prostředí Azure Cloud Shell, interaktivního prostředí příkazového příkazu, které běží ve vašem prohlížeči. Pokud používáte Cloud Shell, nemusíte nic instalovat. Pokud dáváte přednost použití příkazového příkazového příkazu místně, tento rychlý start vyžaduje Azure CLI verze 2.0.76 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz az --version. Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Bez ohledu na to, zda spustíte cli místně nebo v prostředí Cloud Shell, udržujte portál otevřený ve vašem prohlížeči.  Můžete použít později v tomto rychlém startu.

## <a name="launch-the-cloud-shell"></a>Spuštění prostředí Cloud Shell
V této části spustíte instanci prostředí Azure Cloud Shell. Pokud používáte cli místně, přejděte do části [Příprava dvou relací v rámci funkce VPOŘÁDKU](#prepare-two-cli-sessions).

Spuštění prostředí Cloud Shell:

1. Na pravém horním panelu nabídek na webu Azure Portal vyberte tlačítko **Cloud Shell.** 

    ![Tlačítko Cloud shellu portálu Azure portal](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Pokud je to poprvé, co používáte Cloud Shell, vyzve vás k vytvoření úložiště, které je nutné používat Cloud Shell.  Vyberte předplatné a vytvořte účet úložiště a sdílenou složku Microsoft Azure Files. 

2. V rozevíracím souboru **Select environment** vyberte preferované prostředí příkazového příkazového příkazu. Tento rychlý start používá prostředí **Bash.** Všechny následující příkazy příkazu příkazu CLI fungují také v prostředí Powershellu. 

    ![Vybrat prostředí příkazové kliby](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Příprava dvou relací v rámci cli

V této části připravíte dvě relace Azure CLI. Pokud používáte cloudové prostředí, spustíte dvě relace v samostatných kartách prohlížeče. Pokud používáte místní klienta CLI, spustíte dvě samostatné instance CLI. První relaci použijete jako simulované zařízení a druhou relaci ke sledování a odesílání zpráv. Chcete-li spustit příkaz, vyberte **Kopírovat,** chcete-li zkopírovat blok kódu v tomto rychlém startu, vložte jej do relace prostředí a spusťte jej.

Azure CLI vyžaduje, abyste byli přihlášeni ke svému účtu Azure. Veškerá komunikace mezi relací prostředí Azure CLI a centrem IoT hub je ověřená a šifrovaná. V důsledku toho tento rychlý start nepotřebuje další ověřování, které byste použili se skutečným zařízením, jako je například připojovací řetězec.

*  Spusťte příkaz [add rozšíření az](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do prostředí rozhraní příkazového příkazu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Po instalaci rozšíření Azure IOT, není nutné znovu nainstalovat v žádné relaci Cloud Shell. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Otevřete druhou relaci zadák.  Pokud používáte Cloud Shell, vyberte **Otevřít novou relaci**. Pokud používáte cli místně, otevřete druhou instanci. 

    >[!div class="mx-imgBorder"]
    >![Otevření nové relace cloudového prostředí](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub
V této části použijete azure cli k vytvoření skupiny prostředků a služby IoT Hub.  Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Služba IoT Hub funguje jako centrální centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními. 

> [!TIP]
> Volitelně můžete vytvořit skupinu prostředků Azure, centrum IoT hub a další prostředky pomocí [portálu Azure](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)nebo jiných programových metod.  

1. Spusťte příkaz [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) a vytvořte skupinu prostředků. Následující příkaz vytvoří skupinu prostředků s názvem *MyResourceGroup* v umístění *eastus.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Spusťte příkaz [az iot hub create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) a vytvořte centrum IoT. Vytvoření centra IoT může trvat několik minut. 

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. Název centra IoT centra musí být v Azure globálně jedinečný. Tento zástupný symbol se používá ve zbývající části tohoto rychlého startu k reprezentaci názvu centra IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Vytvoření a sledování zařízení
V této části vytvoříte simulované zařízení v první relaci rozhraní se klis. Simulované zařízení odesílá telemetrii zařízení do vašeho centra IoT hub. V druhé relaci rozhraní rozhraní se stavu nastavení rozhraní ZABEZPEČENÍ můžete sledovat události a telemetrii a odesílat zprávu z cloudu na zařízení do simulovaného zařízení.

Vytvoření a spuštění simulovaného zařízení:
1. Spusťte příkaz [vytvoření identity zařízení az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) v první relaci rozhraní příkazového příkazu. Tím se vytvoří identita simulované zařízení. 

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. 

    *simDevice*. Tento název můžete použít přímo pro simulované zařízení ve zbývající části tohoto rychlého startu. Volitelně můžete použít jiný název. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Spusťte [příkaz az iot simulovat](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) v první relaci rozhraní příkazu CLI.  Tím se spustí simulované zařízení. Zařízení odesílá telemetrii do služby IoT hub a přijímá z něj zprávy.  

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Sledování zařízení:
1. V druhé relaci příkazového příkazu příkazu příkazu CLI spusťte příkaz [sledování az iot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) Tím se spustí sledování simulované zařízení. Výstup ukazuje telemetrii, kterou simulované zařízení odesílá do centra IoT.

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Události monitorování cloudového prostředí](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Po sledování simulované zařízení v druhé relaci rozhraní rozhraní se konzumují sledování stisknutím kombinace kláves Ctrl+C. 

## <a name="use-the-cli-to-send-a-message"></a>Odeslání zprávy pomocí zapisovacího mise
V této části použijete druhou relaci rozhraní rozhraní se konfrontací rozhraní SE zprávou do simulovaného zařízení.

1. V první relaci rozhraní rozhraní se konzumujte, že je spuštěno simulované zařízení. Pokud se zařízení zastavilo, spusťte jej následujícím příkazem:

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. V druhé relaci rozhraní příkazového příkazu CLI spusťte příkaz odeslat zprávu [c2d zařízení az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) Tím se do simulovacího zařízení odešle zpráva z cloudu na zařízení z vašeho centra IoT. Zpráva obsahuje řetězec a dva páry klíč hodnota.  

    *YourIotHubName*. Tento zástupný symbol nahraďte níže názvem, který jste zvolili pro svůj centrum IoT Hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Volitelně můžete odesílat zprávy z cloudu na zařízení pomocí portálu Azure. Chcete-li to provést, přejděte na stránku s přehledem služby IoT Hub, vyberte **zařízení IoT**, vyberte simulované zařízení a vyberte **Zprávu zařízení**. 

1. V první relaci rozhraní rozhraní rozhraní SE konfrontací se potvrzuje, že simulované zařízení přijalo zprávu. 

    ![Zpráva cloud-to-device z cloudshellu](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Po zobrazení zprávy zavřete druhou relaci vykreslování lisování a odpovědí. Ponechte první relaci vykreslování lichva otevřených. Můžete použít k vyčištění prostředků v pozdějším kroku.

## <a name="view-messaging-metrics-in-the-portal"></a>Zobrazení metrik zasílání zpráv na portálu
Portál Azure umožňuje spravovat všechny aspekty vašeho ioT hubu a zařízení. V typické aplikaci IoT Hub, která ingestuje telemetrická data ze zařízení, můžete chtít sledovat zařízení nebo zobrazit metriky na telemetrii zařízení. 

Vizualizujte metriky zasílání zpráv na webu Azure Portal:
1. V levé navigační nabídce na portálu vyberte **možnost Všechny prostředky**. V tomto seznamu jsou uvedeny všechny prostředky ve vašem předplatném, včetně služby IoT hub, kterou jste vytvořili. 

1. Vyberte odkaz na službě IoT hub, kterou jste vytvořili. Portál zobrazuje stránku s přehledem centra.

1. V levém podokně ioT hubu vyberte **Metriky.** 

    ![Metriky zasílání zpráv služby IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Zadejte název centra IoT v **oboru**.

2. Vyberte *standardní metriky iot hub* v **oboru názvů metrik**.

3. Vyberte *možnost Celkový počet zpráv použitých* v **metrice**. 

4. Najeďte myší na oblast časové osy, ve které zařízení odesílá zprávy. Celkový počet zpráv v určitém okamžiku se zobrazí v levém dolním rohu časové osy.

    ![Zobrazení metrik služby Azure IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Můžete také použít **rozbalovací rozbalovací soubor Metrika** k zobrazení dalších metrik na simulovaném zařízení. Například *c2d doručení zpráv dokončena* nebo *celkový počet zařízení (náhled)*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete prostředky Azure vytvořené v tomto rychlém startu, můžete je odstranit pomocí azure cli.

Pokud budete pokračovat k dalšímu doporučenému článku, můžete zachovat prostředky, které jste již vytvořili, a znovu je použít. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Odstranění skupiny prostředků podle názvu:
1. Spusťte příkaz [delete skupiny az.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) Tím odeberete skupinu prostředků, centrum IoT Hub a registraci zařízení, kterou jste vytvořili.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Spusťte příkaz [az group a](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) potvrďte, že skupina prostředků je odstraněna.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste pomocí rozhraní příkazového příkazu Konfronta azure vytvořili centrum IoT, vytvořili simulované zařízení, odeslali telemetrii, monitorovali telemetrii, odeslali zprávu z cloudu na zařízení a vyčistili prostředky. Portál Azure jste použili k vizualizaci metrik zasílání zpráv na vašem zařízení.

Pokud jste vývojář zařízení, dalším krokem je zobrazit rychlý start telemetrie, který používá azure iot zařízení SDK pro C. Volitelně, podívejte se na jeden z dostupných článků Azure IoT Hub telemetry rychlé spuštění ve vašem preferovaném jazyce nebo SDK.

> [!div class="nextstepaction"]
> [Úvodní příručka: Odeslání telemetrie ze zařízení do centra IoT hub (C)](quickstart-send-telemetry-c.md)
