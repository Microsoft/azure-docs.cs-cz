---
title: Rychlý start vytvoření zařízení Azure IoT Edge ve Windows | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak vytvořit zařízení IoT Edge a pak nasazení předem připravených kódu vzdáleně z webu Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/19/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7b4fcf34831d17d35e9f4d8b38455ea22293076f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148089"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device"></a>Rychlý start: Nasazení prvního modulu IoT Edge z portálu Azure portal pro zařízení s Windows

V tomto rychlém startu použijeme cloudové rozhraní Azure IoT Edge ke vzdálenému nasazení předem připraveného kódu do zařízení IoT Edge. Chcete-li provést tento úkol, nejprve vytvoříte a nakonfigurujete virtuální počítač s Windows a fungovat jako zařízení IoT Edge, pak můžete nasadit modul k němu.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalovat na zařízení modul runtime Azure IoT Edge a spustit ho.
4. Vzdáleně nasadit modul na zařízení IoT Edge a odeslat telemetrická data do služby IoT Hub.

![Diagram – rychlý start architektury pro zařízení a cloud](./media/quickstart/install-edge-full.png)

Modul, který v tomto rychlém startu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

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

* Virtuální počítač Windows tak, aby fungoval jako zařízení IoT Edge. Můžete vytvořit tento virtuální počítač pomocí následujícího příkazu, přičemž nahraďte *{heslo}* zabezpečenou heslem:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Může trvat několik minut pro vytvoření a spuštění nového virtuálního počítače. Pak si můžete stáhnout soubor RDP pro použití při připojování k virtuálnímu počítači:

  1. Přejděte k novému virtuálnímu počítači Windows na webu Azure Portal.
  1. Vyberte **Connect** (Připojit).
  1. Na **RDP** kartu, vyberte možnost **stáhnout soubor RDP**.

  Otevřete tento soubor pomocí připojení ke vzdálené ploše pro připojení k Windows virtuální počítač pomocí jména správce a heslo jste zadali pomocí `az vm create` příkazu.


> [!NOTE]
> Tento rychlý start využívá virtuální počítač klasické pracovní plochy Windows pro zjednodušení. Informace o tom, které Windows jsou obecně dostupné pro produkční scénáře operačních systémů najdete v tématu [Azure IoT Edge podporované systémy](support.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Spuštění tohoto rychlého startu tak, že vytvoříte službu IoT hub pomocí Azure CLI.

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

3. Zkopírujte hodnotu `connectionString` klíče z výstupu JSON a uložte ho. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec budete používat ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načtení připojovacího řetězce z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na své zařízení IoT Edge modul runtime Azure IoT Edge a nakonfigurujte v něm připojovací řetězec zařízení.
![Diagram - Start modul runtime na zařízení](./media/quickstart/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Démon zabezpečení IoT Edge** spustí pokaždé, když zařízení IoT Edge se spustí a bootstraps zařízení spuštěním agenta IoT Edge. **Agenta IoT Edge** spravuje nasazení a monitorování modulů na zařízení IoT Edge, včetně hraničních zařízeních IoT hub. **Centrum IoT Edge** zpracovává komunikaci mezi moduly v hraničním zařízením IoT a mezi zařízením a centrem IoT.

Instalační skript také zahrnuje modul kontejner volá Moby, který spravuje imagí kontejneru na vašem zařízení IoT Edge.

Během instalace modulu runtime se zobrazí výzva k zadání připojovacího řetězce zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="connect-to-your-iot-edge-device"></a>Připojte se k zařízení IoT Edge

Kroky v této části všechny proběhla na zařízení IoT Edge, takže chcete připojit k tomuto virtuálnímu počítači teď přes vzdálenou plochu.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalace a konfigurace služby IoT Edge

Pomocí PowerShellu stáhněte a nainstalujte modul runtime IoT Edge. Ke konfiguraci svého zařízení použijte připojovací řetězec zařízení, který jste získali ze služby IoT Hub.

1. Pokud jste tak dosud neučinili, postupujte podle kroků v [zaregistrovat nová zařízení Azure IoT Edge](how-to-register-device-portal.md) načíst připojovací řetězec zařízení a zaregistrovat své zařízení. 

2. Spusťte PowerShell jako správce.

3. **Nasadit IoTEdge** příkaz ověří, že váš počítač Windows na podporovanou verzi, zapne funkci kontejnery, stáhne moby modulu runtime a potom stáhne modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Váš počítač může automaticky restartuje. Pokud se zobrazí výzva příkazem IoTEdge nasazení až po restartování, to teď. 

5. Znovu spusťte PowerShell jako správce.

6. **Inicializace IoTEdge** příkaz nakonfiguruje modul runtime IoT Edge na svém počítači. Příkaz výchozí hodnota je ruční zřizování s kontejnery Windows. 

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

Může trvat několik minut na dokončení instalace a modul IoT Edge agenta spustit.

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte vaše zařízení Azure IoT Edge z cloudu do nasazení modulu, který odesílá telemetrická data do služby IoT Hub.
![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu zaregistrované zařízení IoT Edge a na něm nainstalován modul runtime IoT Edge. Pak jste použili na webu Azure portal k nasazení modulu IoT Edge pro spuštění na zařízení bez nutnosti provádět změny samotné zařízení.

V takovém případě vytvoří modul, který jste odeslali ukázková data, která můžete použít pro testování. Modul simulované teplotní snímač generuje dat prostředí, který můžete použít pro testování později. Monitoruje simulovaných senzorů na počítači a prostředí kolem počítače. Senzor se například může být v místnosti serverů, na výrobním závodě nebo větrné turbíny. Zpráva obsahuje okolní teploty a vlhkosti, počítač teploty a tlaku a časové razítko. V kurzech IoT Edge pomocí dat vytvořil tento modul, protože testovací data pro analýzu.

Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu.

```powershell
iotedge list
```

   ![Zobrazení tří modulů na zařízení](./media/quickstart/iotedge-list-2.png)

Zobrazte zprávy z modulu senzoru teploty odesílané do cloudu.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Při odkazu na modul názvy jsou malá a velká písmena příkazy IoT Edge.

   ![Zobrazení dat z modulu](./media/quickstart/iotedge-logs.png)

Můžete rovněž sledovat zprávy dorazí ve službě IoT hub pomocí [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. V opačném případě můžete odstranit prostředky Azure, které jste vytvořili nemuseli platit případné poplatky.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Dvojitá kontrola obsah skupiny prostředků pro Ujistěte se, že existuje vaší nic, které chcete zachovat. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

Odeberte skupinu **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili zařízení IoT Edge a použít rozhraní Azure IoT Edge cloud k nasazení kódu do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení svoje místní vývojové prostředí, takže můžete začít vytvářet moduly, které spouští vaši obchodní logiku IoT Edge. 

> [!div class="nextstepaction"]
> [Začít s vývojem modulů IoT Edge pro zařízení s Windows](tutorial-develop-for-windows.md)
