---
title: Rychlý start vytvoření zařízení Azure IoT Edge ve Windows | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak vytvořit zařízení IoT Edge a pak nasazení předem připravených kódu vzdáleně z webu Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0c397ca630dffb7671f84919c1e86cbcef645572
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873617"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Rychlý start: Nasazení prvního IoT Edge modulu do virtuálního zařízení s Windows

Vyzkoušejte si Azure IoT Edge v tomto rychlém startu nasazením kontejneru kódu na virtuální IoT Edge zařízení. IoT Edge vám umožní vzdáleně spravovat kód na svých zařízeních, abyste mohli do hraničních zařízení odesílat další úlohy. Pro tento rychlý Start doporučujeme pro vaše zařízení IoT Edge použít virtuální počítač Azure, který vám umožní rychle vytvořit testovací počítač, nainstalovat požadované součásti a po dokončení je odstranit. 

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalujte a spusťte modul runtime IoT Edge na virtuálním zařízení.
4. Vzdáleně nasadit modul na zařízení IoT Edge a odeslat telemetrická data do služby IoT Hub.

![Diagram – rychlý start architektury pro zařízení a cloud](./media/quickstart/install-edge-full.png)

Tento rychlý Start vás provede vytvořením virtuálního počítače s Windows a konfigurací IoT Edge zařízení. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

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

* Virtuální počítač s Windows, který bude fungovat jako zařízení IoT Edge. Tento virtuální počítač můžete vytvořit pomocí následujícího příkazu, kde *{Password}* nahrazujete zabezpečené heslo:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Vytvoření a spuštění nového virtuálního počítače může trvat několik minut. Pak můžete stáhnout soubor RDP pro použití při připojování k virtuálnímu počítači:

  1. V Azure Portal přejděte na nový virtuální počítač s Windows.
  1. Vyberte **Connect** (Připojit).
  1. Na kartě **RDP** vyberte **Stáhnout soubor RDP**.

  Otevřete tento soubor s připojení ke vzdálené ploše pro připojení k virtuálnímu počítači s Windows pomocí jména správce a hesla, které jste zadali `az vm create` pomocí příkazu.


> [!NOTE]
> V tomto rychlém startu se k jednoduchosti používá virtuální počítač s Windows Desktop. Informace o tom, které operační systémy Windows jsou všeobecně dostupné pro produkční scénáře, najdete v tématu [Azure IoT Edge podporovaných systémech](support.md).
>
> Pokud jste připraveni ke konfiguraci vlastního zařízení s Windows pro IoT Edge, včetně zařízení se systémem IoT Core, postupujte podle kroků v části [Instalace modulu runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Začněte s rychlým startem vytvořením služby IoT Hub pomocí Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte *{hub_name}* jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud dojde k chybě, že název služby IoT Hub není k dispozici, znamená to, že má někdo jiný již centra s tímto názvem. Zkuste použít nový název.

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Diagram – registrace zařízení s identitou služby IoT Hub](./media/quickstart/register-device.png)

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Protože zařízení IoT Edge se chovají a lze je spravovat jinak než typické zařízení IoT, deklarujte tuto identitu pro zařízení IoT Edge se `--edge-enabled` příznak.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Pokud se zobrazí chyba týkající se iothubowner klíče zásad, ujistěte se, že vaší služby cloud shell běží nejnovější verze rozšíření azure-cli-iot-ext, přípona.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu `connectionString` klíče z výstupu JSON a uložte ho. Tato hodnota je připojovací řetězec zařízení. Pomocí tohoto připojovacího řetězce můžete nakonfigurovat modul runtime IoT Edge v další části.

   ![Načtení připojovacího řetězce z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na své zařízení IoT Edge modul runtime Azure IoT Edge a nakonfigurujte v něm připojovací řetězec zařízení.
![Diagram - Start modul runtime na zařízení](./media/quickstart/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **IoT Edge démon zabezpečení** se spustí pokaždé, když se spustí IoT Edge zařízení, a spustí agenta IoT Edge. **Agent IoT Edge** spravuje nasazení a monitorování modulů na zařízení IoT Edge, včetně centra IoT Edge. **Centrum IoT Edge** zpracovává komunikaci mezi moduly v zařízení IoT Edge a mezi zařízením a IoT Hub.

Instalační skript obsahuje také modul kontejneru s názvem Moby, který spravuje image kontejneru na zařízení IoT Edge.

Během instalace modulu runtime se zobrazí výzva k zadání připojovacího řetězce zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="connect-to-your-iot-edge-device"></a>Připojte se k zařízení IoT Edge

Kroky v této části se doplní na vaše zařízení IoT Edge, takže se k tomuto virtuálnímu počítači chcete připojit hned přes vzdálenou plochu.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalace a konfigurace služby IoT Edge

Pomocí PowerShellu stáhněte a nainstalujte modul runtime IoT Edge. Ke konfiguraci svého zařízení použijte připojovací řetězec zařízení, který jste získali ze služby IoT Hub.

1. Pokud jste to ještě neudělali, postupujte podle kroků v části [Registrace nového zařízení Azure IoT Edge](how-to-register-device-portal.md) pro registraci zařízení a načtení připojovacího řetězce zařízení. 

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge, nikoli PowerShell (x86) použijte relaci AMD64 prostředí PowerShell. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers, stáhne modul runtime Moby a pak stáhne modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Počítač se může automaticky restartovat. Pokud se zobrazí výzva k restartování příkazu Deploy-IoTEdge, udělejte to teď. 

5. Spusťte PowerShell jako správce znovu.

6. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Po zobrazení výzvy k zadání **připojovacího řetězce** zadejte řetězec, který jste si zkopírovali v předchozí části. Připojovací řetězec zadejte bez uvozovek.

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Ověřte, že se modul runtime úspěšně nainstaloval a nakonfiguroval.

1. Zkontrolujte stav služby IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Vzhledem k tomu, že jde o první spuštění služby, měl by se zobrazit pouze spuštěný modul **edgeAgent**. Modul edgeAgent se spouští ve výchozím nastavení a pomáhá s instalací a spouštěním všech dalších modulů, které do zařízení nasadíte.

   ```powershell
   iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart/iotedge-list-1.png)

Dokončení instalace může trvat několik minut a spustí se modul IoT Edge agenta.

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte zařízení Azure IoT Edge z cloudu a nasaďte modul, který do IoT Hub odešle data telemetrie.
![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste zaregistrovali zařízení IoT Edge a nainstalovali IoT Edge runtime. Pak jste použili Azure Portal k nasazení IoT Edge modulu pro spuštění na zařízení, aniž byste museli provádět změny samotného zařízení.

V takovém případě modul, který jste odeslali, vytvoří ukázková data, která můžete použít pro testování. Modul pro simulaci teplotního senzoru generuje data prostředí, která můžete použít pro testování později. Simulovaný senzor sleduje počítač i prostředí v okolí počítače. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzy.

Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu.

```powershell
iotedge list
```

   ![Zobrazení tří modulů na zařízení](./media/quickstart/iotedge-list-2.png)

Umožňuje zobrazit zprávy odesílané z modulu snímače teploty do cloudu.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >V IoT Edgech příkazech se rozlišují velká a malá písmena při odkazování na názvy modulů.

   ![Zobrazení dat z modulu](./media/quickstart/iotedge-logs.png)

Zprávy doručené do služby IoT Hub můžete také sledovat pomocí [rozšíření azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

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
> [Zahájení vývoje IoT Edge modulů pro zařízení s Windows](tutorial-develop-for-windows.md)
