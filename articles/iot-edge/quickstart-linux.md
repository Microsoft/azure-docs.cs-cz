---
title: Rychlý start vytvoření zařízení Azure IoT Edge v Linuxu | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak vytvořit zařízení IoT Edge a pak nasazení předem připravených kódu vzdáleně z webu Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8d2e0b4683261a06c39b9a5f335d7f4f22a2fd05
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912329"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rychlý Start: nasazení prvního modulu IoT Edge do virtuálního zařízení se systémem Linux

Vyzkoušejte si Azure IoT Edge v tomto rychlém startu nasazením kontejneru kódu na virtuální IoT Edge zařízení. IoT Edge vám umožní vzdáleně spravovat kód na svých zařízeních, abyste mohli do hraničních zařízení odesílat další úlohy. Pro tento rychlý Start doporučujeme použít pro vaše IoT Edge zařízení virtuální počítač Azure, který vám umožní rychle vytvořit testovací počítač se všemi nainstalovanými součástmi a pak ho po dokončení odstranit.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalujte a spusťte modul runtime IoT Edge na virtuálním zařízení.
4. Vzdáleně nasadit modul na zařízení IoT Edge.

![Diagram – rychlý start architektury pro zařízení a cloud](./media/quickstart-linux/install-edge-full.png)

Tento rychlý Start vás provede vytvořením virtuálního počítače se systémem Linux, který je nakonfigurován tak, aby IoT Edge zařízení. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce.

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Požadavky

Cloudové prostředky:

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zařízení IoT Edge:

* Virtuální počítač nebo zařízení s Linuxem, který bude fungovat jako zařízení IoT Edge. Na virtuálním počítači s Ubuntu byste měli použít [Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) poskytované společností Microsoft, která předinstaluje všechno, co potřebujete ke spuštění IoT Edge na zařízení. Přijměte podmínky použití a vytvořte tento virtuální počítač pomocí následujících příkazů:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Vytvoření a spuštění nového virtuálního počítače může trvat několik minut.

   Při vytváření nového virtuálního počítače, poznamenejte si, **publicIpAddress**, který je součástí výstupu příkazu create. Tato veřejná IP adresa bude používat pro připojení k virtuálnímu počítači dále v tomto rychlém startu.

* Pokud chcete spustit modul runtime Azure IoT Edge na vlastním zařízení, postupujte podle pokynů v [části instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

Začněte s rychlým startem vytvořením služby IoT Hub pomocí Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart-linux/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte `{hub_name}` jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud dojde k chybě, že název služby IoT Hub není k dispozici, znamená to, že má někdo jiný již centra s tímto názvem. Zkuste použít nový název.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram – registrace zařízení pomocí IoT Hub identity](./media/quickstart-linux/register-device.png)

Vytvořte identitu zařízení pro zařízení IoT Edge, aby mohla komunikovat se službou IoT Hub. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Protože zařízení IoT Edge se chovají a lze je spravovat jinak než typické zařízení IoT, deklarujte tuto identitu pro zařízení IoT Edge se `--edge-enabled` příznak.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Pokud se zobrazí chyba týkající se iothubowner klíče zásad, ujistěte se, že vaší služby cloud shell běží nejnovější verze rozšíření azure-cli-iot-ext, přípona.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu klíče `connectionString` z výstupu JSON a uložte ho. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec použijete ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načtení připojovacího řetězce z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Na zařízení IoT Edge spusťte modul runtime Azure IoT Edge.

![Diagram – spuštění modulu runtime na zařízení](./media/quickstart-linux/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **IoT Edge démon zabezpečení** se spustí pokaždé, když se spustí IoT Edge zařízení, a spustí agenta IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Během konfigurace modulu runtime zadáte připojovací řetězec zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Nastavit připojovací řetězec na zařízení IoT Edge

Pokud používáte Azure IoT Edge na virtuálním počítači s Ubuntu, jak je popsáno v části požadavky, má už zařízení IoT Edge runtime nainstalované. Stačí nakonfigurovat zařízení pomocí připojovacího řetězce zařízení, který jste získali v předchozí části. Můžete to provést vzdáleně bez nutnosti připojení k virtuálnímu počítači. Spusťte následující příkaz a nahraďte `{device_connection_string}` vlastním řetězcem.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Pokud používáte IoT Edge v místním počítači nebo zařízení ARM32 nebo ARM64, musíte na zařízení nainstalovat modul runtime IoT Edge a jeho součásti. Postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)a pak se vraťte k tomuto rychlému startu.

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Zbývající příkazy v tomto rychlém startu se uskuteční na svém IoT Edge samotném zařízení, abyste viděli, co se děje na zařízení. Pokud používáte virtuální počítač, připojte se k tomuto počítači nyní pomocí veřejné IP adresy, která byla výstupem příkazu vytvoření. Můžete také najít veřejnou IP adresu na stránce Přehled virtuálního počítače na webu Azure Portal. Použijte následující příkaz pro připojení k virtuálnímu počítači. Nahraďte `{azureuser}`, pokud jste použili jiné uživatelské jméno, než jaké je navržené v požadavcích. Nahraďte `{publicIpAddress}` adresou počítače.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Ověřte, že modul runtime byl úspěšně nainstalován a nakonfigurován na vašem zařízení IoT Edge.

>[!TIP]
>Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Až pak použijte `sudo` před příkazy.

1. Zkontrolujte, zda je démon zabezpečení IoT Edge spuštěn jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Viz IoT Edge démon spuštěný jako systémová služba.](./media/quickstart-linux/iotedged-running.png)

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

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k nasazení IoT Edge modulu pro spuštění na zařízení, aniž byste museli provádět změny samotného zařízení.

V takovém případě modul, který jste odeslali, vytvoří ukázková data, která můžete použít pro testování. Modul pro simulaci teplotního senzoru generuje data prostředí, která můžete použít pro testování později. Simulovaný senzor sleduje počítač i prostředí v okolí počítače. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzy.

Znovu otevřete příkazový řádek na vašem zařízení IoT Edge, ani používat připojení SSH z příkazového řádku Azure. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení tří modulů na zařízení](./media/quickstart-linux/iotedge-list-2.png)

Zobrazit zprávy odesílané z modulu snímače teploty:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >V IoT Edgech příkazech se rozlišují velká a malá písmena při odkazování na názvy modulů.

   ![Zobrazení dat z modulu](./media/quickstart-linux/iotedge-logs.png)

Zprávy doručené do služby IoT Hub můžete také sledovat pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. Jinak můžete odstranit prostředky Azure, které jste vytvořili, abyste se vyhnuli poplatkům.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Dvojitá kontrola obsah skupiny prostředků pro Ujistěte se, že existuje vaší nic, které chcete zachovat. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

Odeberte skupinu **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili zařízení IoT Edge a pomocí Azure IoT Edge cloudového rozhraní nasadíte kód do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení místního vývojového prostředí, abyste mohli začít vytvářet IoT Edge moduly, které spouští vaši obchodní logiku.

> [!div class="nextstepaction"]
> [Zahájení vývoje IoT Edge modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md)
