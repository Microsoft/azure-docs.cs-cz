---
title: Úvodní příručka k vytvoření zařízení Azure IoT Edge ve Windows | Dokumenty společnosti Microsoft
description: V tomto rychlém startu se dozvíte, jak vytvořit zařízení IoT Edge a pak nasadit předem vytvořený kód vzdáleně z webu Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 54efe7b5c392ad2b4cc3a0de414e04951b268508
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674246"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Úvodní příručka: Nasazení prvního modulu IoT Edge do virtuálního zařízení s Windows

Vyzkoušejte Azure IoT Edge nasazením kontejnerizovaného kódu do virtuálního zařízení IoT Edge. IoT Edge umožňuje vzdáleně spravovat kód na vašich zařízeních, takže můžete posílat více úloh na okraj. Pro tento rychlý start doporučujeme použít virtuální počítač Azure pro vaše zařízení IoT Edge. Použití virtuálního počítače umožňuje rychle vytvořit testovací počítač, nainstalovat požadavky a po dokončení je odstranit.

V tomto rychlém startu se naučíte:

1. Vytvořte centrum IoT.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalujte a spusťte runtime IoT Edge na virtuálním zařízení.
4. Vzdáleně nasadit modul na zařízení IoT Edge a odeslat telemetrická data do služby IoT Hub.

![Diagram – architektura rychlého startu pro zařízení a cloud](./media/quickstart/install-edge-full.png)

Tento rychlý start vás provede vytvořením virtuálního počítače s Windows a konfigurací jako zařízení IoT Edge. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pomocí azure cli k dokončení mnoha kroků v tomto rychlém startu. Azure IoT má rozšíření, které umožňuje další funkce.

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

* Virtuální počítač s Windows, který bude fungovat jako vaše zařízení IoT Edge. Tento virtuální počítač můžete vytvořit pomocí následujícího příkazu a nahradit `{password}` jej zabezpečeným heslem:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Vytvoření a spuštění nového virtuálního počítače může trvat několik minut. Potom si můžete stáhnout soubor RDP pro použití při připojování k virtuálnímu počítači:

  1. Přejděte na nový virtuální počítač s Windows na webu Azure Portal.
  1. Vyberte **Connect** (Připojit).
  1. Na kartě **RDP** vyberte **Stáhnout soubor RDP**.

  Otevřete tento soubor pomocí programu Připojení ke vzdálené ploše a připojte se `az vm create` k virtuálnímu počítači windows pomocí názvu správce a hesla, které jste zadali pomocí příkazu.

> [!NOTE]
> Tento rychlý start používá pro jednoduchost virtuální počítač se systémem Windows. Informace o tom, které operační systémy Windows jsou obecně dostupné pro produkční scénáře, naleznete v [tématu Azure IoT Edge podporované systémy](support.md).
>
> Pokud jste připraveni nakonfigurovat vlastní zařízení s Windows pro IoT Edge, včetně zařízení se systémem IoT Core, postupujte podle pokynů v [části Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Spusťte rychlý start vytvořením centra IoT s Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří volné centrum **F1** `IoTEdgeResources`ve skupině prostředků . Nahraďte `{hub_name}` jedinečný název pro centrum IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud se zobrazí chyba, že název služby IoT Hub není k dispozici, znamená to, že někdo jiný již má rozbočovač s tímto názvem. Zkuste nové jméno.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.
![Diagram – registrace zařízení s identitou služby IoT Hub](./media/quickstart/register-device.png)

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Vzhledem k tomu, že zařízení IoT Edge se chovají a lze je spravovat jinak než typická `--edge-enabled` zařízení IoT, deklarujte tuto identitu pro zařízení IoT Edge s příznakem.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Pokud se zobrazí chyba o klíčích zásad iothubowner, ujistěte se, že vaše cloudové prostředí běží nejnovější verze rozšíření azure-iot.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu `connectionString` klíče z výstupu JSON a uložte jej. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec použijete ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načíst připojovací řetězec z výstupu CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na své zařízení IoT Edge modul runtime Azure IoT Edge a nakonfigurujte v něm připojovací řetězec zařízení.
![Diagram – spuštění runtime na zařízení](./media/quickstart/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Dém zabezpečení IoT Edge** se spustí pokaždé, když zařízení IoT Edge spustí a spustí, spuštěním agenta IoT Edge. **Agent IoT Edge** spravuje nasazení a monitorování modulů na zařízení IoT Edge, včetně centra IoT Edge. **Centrum IoT Edge** zpracovává komunikaci mezi moduly na zařízení IoT Edge a mezi zařízením a ioT hubem.

Instalační skript obsahuje také modul kontejneru s názvem Moby, který spravuje image kontejneru na zařízení IoT Edge.

Během instalace modulu runtime se zobrazí výzva k zadání připojovacího řetězce zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="connect-to-your-iot-edge-device"></a>Připojení k zařízení IoT Edge

Všechny kroky v této části probíhají na vašem zařízení IoT Edge, takže se chcete k tomuto virtuálnímu počítači připojit teď přes vzdálenou plochu.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalace a konfigurace služby IoT Edge

Pomocí PowerShellu stáhněte a nainstalujte modul runtime IoT Edge. Ke konfiguraci svého zařízení použijte připojovací řetězec zařízení, který jste získali ze služby IoT Hub.

1. Pokud jste tak ještě neučinili, postupujte podle kroků v [části Registrace nového zařízení Azure IoT Edge,](how-to-register-device.md) abyste zařízení zaregistrovali a načetli připojovací řetězec zařízení.

2. Ve virtuálním počítači spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge použijte relaci AMD64 v Prostředí PowerShell, ne powershellu (x86). Pokud si nejste jisti, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů, stáhne modul runtime Moby a potom stáhne modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Zařízení se může automaticky restartovat. Pokud se zobrazí výzva příkazu Deploy-IoTEdge k restartování, uvažte to nyní.

5. Spusťte prostředí PowerShell jako správce znovu.

6. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz výchozí ruční zřizování s kontejnery systému Windows.

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

3. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Vzhledem k tomu, že jde o první spuštění služby, měl by se zobrazit pouze spuštěný modul **edgeAgent**. Modul edgeAgent běží ve výchozím nastavení a pomáhá nainstalovat a spustit všechny další moduly, které nasadíte do zařízení.

    ```powershell
    iotedge list
    ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart/iotedge-list-1.png)

Dokončení instalace a spuštění modulu agenta IoT Edge může trvat několik minut.

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte zařízení Azure IoT Edge z cloudu a nasaďte modul, který odesílá telemetrická data do služby IoT Hub.
![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste zaregistrovali zařízení IoT Edge a nainstalovali na něj runtime IoT Edge. Potom jste použili portál Azure k nasazení modulu IoT Edge ke spuštění na zařízení bez nutnosti provádět změny samotného zařízení.

V tomto případě modul, který jste tlačil vytvoří ukázková data, která můžete použít pro testování. Modul simulovaného teplotního senzoru generuje data prostředí, která můžete později použít k testování. Simulovaný senzor monitoruje stroj i prostředí kolem stroje. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzu.

Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu.

```powershell
iotedge list
```

   ![Zobrazení tří modulů na zařízení](./media/quickstart/iotedge-list-2.png)

Zobrazení zpráv odesílaných z modulu teplotního senzoru do cloudu.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Příkazy IoT Edge rozlišují malá a velká písmena při odkazování na názvy modulů.

   ![Zobrazení dat z modulu](./media/quickstart/iotedge-logs.png)

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
> [Zahájení vývoje modulů IoT Edge pro zařízení s Windows](tutorial-develop-for-windows.md)
