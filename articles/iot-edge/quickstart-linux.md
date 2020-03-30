---
title: Úvodní příručka k vytvoření zařízení Azure IoT Edge v Linuxu | Dokumenty společnosti Microsoft
description: V tomto rychlém startu se dozvíte, jak vytvořit zařízení IoT Edge a pak nasadit předem vytvořený kód vzdáleně z webu Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 52258a8bc287df36158ec143e4aad74c34455ea4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80236073"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Úvodní příručka: Nasazení prvního modulu IoT Edge do virtuálního linuxového zařízení

Vyzkoušejte Azure IoT Edge v tomto rychlém startu nasazením kontejnerizovaného kódu do virtuálního zařízení IoT Edge. IoT Edge umožňuje vzdáleně spravovat kód na vašich zařízeních, takže můžete posílat více úloh na okraj. Pro tento rychlý start doporučujeme použít virtuální počítač Azure pro vaše zařízení IoT Edge, který umožňuje rychle vytvořit testovací počítač se všemi nainstalovanými požadavky a po dokončení jej odstranit.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalujte a spusťte runtime IoT Edge na virtuálním zařízení.
4. Vzdáleně nasadit modul na zařízení IoT Edge.

![Diagram – architektura rychlého startu pro zařízení a cloud](./media/quickstart-linux/install-edge-full.png)

Tento rychlý start vás provede vytvořením virtuálního počítače linux, který je nakonfigurovaný jako zařízení IoT Edge. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce.

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
   
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Požadavky

Cloudové prostředky:

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zařízení IoT Edge:

* Virtuální počítač nebo zařízení s Linuxem, který bude fungovat jako zařízení IoT Edge. Měli byste používat Azure [IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) na virtuálním počítači Ubuntu, který předinstaluje vše, co potřebujete ke spuštění IoT Edge na zařízení. Přijměte podmínky použití a vytvořte tento virtuální počítač pomocí následujících příkazů:

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Vytvoření a spuštění nového virtuálního počítače může trvat několik minut.

   Při vytváření nového virtuálního počítače si poznamenejte **adresu publicIpAddress**, která je k dispozici jako součást výstupu příkazu create. Tuto veřejnou IP adresu použijete k připojení k virtuálnímu počítači později v rychlém startu.

* Pokud dáváte přednost spuštění runtime Azure IoT Edge na vlastním zařízení, postupujte podle pokynů na [webu Instalace runtime Azure IoT Edge na Linuxu](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Spusťte rychlý start vytvořením centra IoT s Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart-linux/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte `{hub_name}` jedinečný název pro centrum IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud se zobrazí chyba, že název služby IoT Hub není k dispozici, znamená to, že někdo jiný již má rozbočovač s tímto názvem. Zkuste nové jméno.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram – registrace zařízení s identitou služby IoT Hub](./media/quickstart-linux/register-device.png)

Vytvořte identitu zařízení pro vaše zařízení IoT Edge, aby mohlo komunikovat s vaším centrem IoT hub. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Vzhledem k tomu, že zařízení IoT Edge se chovají a lze je spravovat jinak než typická `--edge-enabled` zařízení IoT, deklarujte tuto identitu pro zařízení IoT Edge s příznakem.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Pokud se vám zobrazí chyba týkající se klíčů zásad iothubowner, ujistěte se, že Cloud Shell má spuštěnou nejnovější verzi rozšíření azure-cli-iot-ext.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu `connectionString` klíče z výstupu JSON a uložte jej. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec použijete ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načíst připojovací řetězec z výstupu CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Spusťte runtime Azure IoT Edge na zařízení IoT Edge.

![Diagram – spuštění runtime na zařízení](./media/quickstart-linux/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Dém zabezpečení IoT Edge** se spustí pokaždé, když zařízení IoT Edge spustí a spustí, spuštěním agenta IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Během konfigurace modulu runtime zadáte připojovací řetězec zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Nastavení připojovacího řetězce na zařízení IoT Edge

Pokud používáte virtuální počítač Azure IoT Edge na Ubuntu, jak je popsáno v požadavcích, pak vaše zařízení už má nainstalovaný runtime IoT Edge. Stačí nakonfigurovat zařízení pomocí připojovacího řetězce zařízení, který jste načetli v předchozí části. Můžete to provést vzdáleně bez nutnosti připojení k virtuálnímu počítači. Spusťte následující příkaz `{device_connection_string}` a nahraďte jej vlastním řetězcem.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Pokud používáte IoT Edge na místním počítači nebo na zařízení ARM32 nebo ARM64, musíte do zařízení nainstalovat runtime IoT Edge a jeho požadavky. Postupujte podle pokynů v [části Instalace runtime Azure IoT Edge na Linuxu](how-to-install-iot-edge-linux.md)a pak se vraťte k tomuto rychlému startu.

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Zbytek příkazů v tomto rychlém startu se uskuteční na samotném zařízení IoT Edge, abyste viděli, co se na zařízení děje. Pokud používáte virtuální počítač, připojte se k tomuto počítači nyní pomocí veřejné IP adresy, která byla výstup emaprovacího příkazu. Veřejnou IP adresu najdete taky na stránce s přehledem virtuálního počítače na webu Azure Portal. Pokud se chcete připojit k virtuálnímu počítači, použijte následující příkaz. Nahradit, `{azureuser}` pokud jste použili jiné uživatelské jméno, než je navrženo v předpokladech. Nahraďte `{publicIpAddress}` adresu vašeho počítače.

   ```console
   ssh azureuser@{publicIpAddress}
   ```

Ověřte, zda byl runtime úspěšně nainstalován a nakonfigurován na zařízení IoT Edge.

>[!TIP]
>Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Do té `sudo` doby použijte před příkazy.

1. Zkontrolujte, zda je déon zabezpečení IoT Edge spuštěn jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Zobrazení daemonu IoT Edge spuštěného jako systémová služba](./media/quickstart-linux/iotedged-running.png)

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```bash
   journalctl -u iotedge
   ```

3. Zobrazte moduly spuštěné na vašem zařízení.

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart-linux/iotedge-list-1.png)

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.
![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Potom jste použili portál Azure k nasazení modulu IoT Edge ke spuštění na zařízení bez nutnosti provádět změny samotného zařízení.

V tomto případě modul, který jste tlačil vytvoří ukázková data, která můžete použít pro testování. Modul simulovaného teplotního senzoru generuje data prostředí, která můžete později použít k testování. Simulovaný senzor monitoruje stroj i prostředí kolem stroje. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzu.

V zařízení IoT Edge znovu otevřete příkazový řádek nebo použijte připojení SSH z Azure CLI. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení tří modulů na zařízení](./media/quickstart-linux/iotedge-list-2.png)

Zobrazení zpráv odesílaných z modulu teplotního senzoru:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Příkazy IoT Edge rozlišují malá a velká písmena při odkazování na názvy modulů.

   ![Zobrazení dat z modulu](./media/quickstart-linux/iotedge-logs.png)

Můžete také sledovat, jak zprávy dorazí do vašeho centra IoT pomocí [rozšíření Azure IoT Hub pro visual studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. V opačném případě můžete odstranit prostředky Azure, které jste vytvořili, abyste se vyhnuli poplatkům.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Zkontrolujte obsah skupiny prostředků a ujistěte se, že není nic, co chcete zachovat. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

Odeberte skupinu **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili zařízení IoT Edge a použili cloudové rozhraní Azure IoT Edge k nasazení kódu na zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení místního vývojového prostředí, abyste mohli začít vytvářet moduly IoT Edge, které spouštějí vaši obchodní logiku.

> [!div class="nextstepaction"]
> [Začněte vyvíjet moduly IoT Edge pro linuxová zařízení](tutorial-develop-for-linux.md)
