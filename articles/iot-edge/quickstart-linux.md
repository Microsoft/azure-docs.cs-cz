---
title: Rychlý Start vytvoření zařízení Azure IoT Edge v systému Linux | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak vytvořit zařízení IoT Edge v systému Linux a jak vzdáleně nasadit předem vytvořený kód z Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/02/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9ba73e71e4525fe56a3cbb54626030f57e990b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920804"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rychlý Start: nasazení prvního modulu IoT Edge do virtuálního zařízení se systémem Linux

Vyzkoušejte si Azure IoT Edge v tomto rychlém startu nasazením kontejneru kódu na virtuální Linux IoT Edge zařízení. IoT Edge vám umožní vzdáleně spravovat kód na svých zařízeních, abyste mohli do hraničních zařízení odesílat další úlohy. Pro tento rychlý Start doporučujeme použít pro zařízení IoT Edge virtuální počítač Azure, který vám umožní rychle vytvořit testovací počítač s nainstalovanou službou IoT Edge a pak ho po dokončení odstranit.

V tomto rychlém startu se naučíte:

* Vytvořit IoT Hub.
* Zaregistrovat zařízení IoT Edge do centra IoT Hub.
* Nainstalujte a spusťte modul runtime IoT Edge na virtuálním zařízení.
* Vzdáleně nasadit modul na zařízení IoT Edge.

![Architektura pro rychlé zprovoznění diagramu pro zařízení a Cloud](./media/quickstart-linux/install-edge-full.png)

Tento rychlý Start vás provede vytvořením virtuálního počítače se systémem Linux, který je nakonfigurován jako zařízení IoT Edge. Pak modul nasadíte z Azure Portal do svého zařízení. Modul použitý v tomto rychlém startu je simulovaný senzor, který generuje data o teplotě, vlhkosti a tlaku. Ostatní kurzy Azure IoT Edge sestavují na práci, kterou tady provedete, nasazením dalších modulů, které analyzují Simulovaná data pro Business Insights.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudové prostředky:

- Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete. V tomto rychlém startu používáme příklad názvu skupiny prostředků **IoTEdgeResources** a následujících kurzech.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Začněte s rychlým startem vytvořením služby IoT Hub pomocí Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart-linux/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste v minulosti používali IoT Hub a už máte vytvořeného centra, můžete použít toto centrum IoT.

Následující kód vytvoří bezplatný rozbočovač **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte `{hub_name}` jedinečným názvem vašeho centra IoT. Vytvoření IoT Hub může trvat několik minut.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. V každém předplatném může být jenom jeden bezplatný IoT Hub. Pokud se zobrazí chyba, že název IoT Hub není k dispozici, znamená to, že někdo jiný již má centrum s tímto názvem. Zkuste nový název.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram – registrace zařízení pomocí IoT Hub identity](./media/quickstart-linux/register-device.png)

Vytvořte identitu zařízení pro zařízení IoT Edge, aby mohla komunikovat se službou IoT Hub. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Vzhledem k tomu, že se zařízení IoT Edge chovají a dají se spravovat jinak než typická zařízení IoT, deklarujte tuto identitu pro IoT Edge zařízení s `--edge-enabled` příznakem.

1. V Azure Cloud Shell zadejte následující příkaz, který ve svém rozbočovači vytvoří zařízení s názvem **myEdgeDevice** .

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Pokud se zobrazí chyba týkající se klíčů zásad iothubowner, ujistěte se, že na Cloud Shell běží nejnovější verze rozšíření Azure-IoT.

2. Zobrazení připojovacího řetězce pro vaše zařízení, který propojuje fyzické zařízení s jeho identitou v IoT Hub. Obsahuje název vašeho centra IoT, název vašeho zařízení a pak sdílený klíč, který ověřuje připojení mezi nimi. Až nakonfigurujete zařízení IoT Edge, budeme se k tomuto připojovacímu řetězci znovu odkazovat v další části.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Zobrazit připojovací řetězec z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Vytvořte virtuální počítač s modulem runtime Azure IoT Edge.

![Diagram – spuštění modulu runtime na zařízení](./media/quickstart-linux/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. *IoT Edge démon zabezpečení* se spustí pokaždé, když se spustí IoT Edge zařízení, a spustí agenta IoT Edge. *Agent IoT Edge* usnadňuje nasazení a monitorování modulů na zařízení IoT Edge, včetně centra IoT Edge. *Centrum IoT Edge* spravuje komunikaci mezi moduly v zařízení IoT Edge a mezi zařízením a IoT Hub.

Během konfigurace modulu runtime zadáte připojovací řetězec zařízení. Toto je řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="deploy-the-iot-edge-device"></a>Nasazení zařízení IoT Edge

Tato část používá šablonu Azure Resource Manager k vytvoření nového virtuálního počítače a instalaci IoT Edge modulu runtime. Pokud místo toho chcete použít vlastní zařízení se systémem Linux, můžete postupovat podle pokynů k instalaci [Azure IoT Edge modul runtime](how-to-install-iot-edge.md)a pak se vrátit k tomuto rychlému startu.

Pomocí následujícího příkazu rozhraní příkazového řádku vytvořte zařízení IoT Edge na základě předem sestavené šablony [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) .

* Pro uživatele bash nebo Cloud Shell zkopírujte následující příkaz do textového editoru, nahraďte zástupný text vašimi informacemi a pak zkopírujte do svého bash a Cloud Shell okna:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name
   <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* V případě uživatelů PowerShellu zkopírujte do okna PowerShellu následující příkaz a potom zástupný text nahraďte vlastními informacemi:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Tato šablona používá následující parametry:

| Parametr | Popis |
| --------- | ----------- |
| **Skupina prostředků** | Skupina prostředků, ve které se prostředky vytvoří. Použijte výchozí **IoTEdgeResources** , který jsme používali v rámci tohoto článku, nebo zadejte název existující skupiny prostředků v rámci vašeho předplatného. |
| **identifikátor URI šablony** | Ukazatel na šablonu Správce prostředků, kterou používáme. |
| **dnsLabelPrefix** | Řetězec, který se použije k vytvoření názvu hostitele virtuálního počítače. Nahraďte zástupný text názvem pro váš virtuální počítač. |
| **adminUsername** | Uživatelské jméno pro účet správce virtuálního počítače. Použijte vzorový **azureUser** nebo zadejte nové uživatelské jméno. |
| **deviceConnectionString** | Připojovací řetězec z identity zařízení v IoT Hub, který se používá ke konfiguraci modulu runtime IoT Edge na virtuálním počítači. Příkaz CLI v rámci tohoto parametru přiřadí připojovací řetězec za vás. Zástupný text nahraďte názvem služby IoT Hub. |
| **authenticationType** | Metoda ověřování pro účet správce. V tomto rychlém startu se používá ověřování **hesla** , ale můžete tento parametr nastavit i na **sshPublicKey**. |
| **adminPasswordOrKey** | Heslo nebo hodnota klíče SSH pro účet správce. Zástupný text nahraďte zabezpečeným heslem. Heslo musí mít aspoň 12 znaků a musí obsahovat tři ze čtyř z následujících znaků: malá písmena, Velká písmena, číslice a speciální znaky. |

Po dokončení nasazení byste měli v rozhraní příkazového řádku (CLI) zobrazit výstup ve formátu JSON, který obsahuje informace SSH pro připojení k virtuálnímu počítači. Zkopírujte hodnotu **veřejné položky SSH** v části **výstupy** :

   ![Načtení veřejné hodnoty SSH z výstupu](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Zbývající příkazy v tomto rychlém startu se uskuteční na svém IoT Edge samotném zařízení, abyste viděli, co se děje na zařízení. Pokud používáte virtuální počítač, připojte se k tomuto počítači teď pomocí uživatelského jména správce, které jste nastavili, a názvu DNS, který byl vydaný příkazem nasazení. Název DNS můžete také najít na stránce Přehled na svém virtuálním počítači v Azure Portal. Pokud se chcete připojit k virtuálnímu počítači, použijte následující příkaz. Nahraďte `{admin username}` a `{DNS name}` vlastními hodnotami.

   ```console
   ssh {admin username}@{DNS name}
   ```

Po připojení k virtuálnímu počítači ověřte, že modul runtime byl úspěšně nainstalován a nakonfigurován na vašem zařízení IoT Edge.

1. Zkontrolujte, zda je démon zabezpečení IoT Edge spuštěn jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Viz IoT Edge démon spuštěný jako systémová služba.](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Do té doby použijte `sudo` před příkazy.

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```bash
   journalctl -u iotedge
   ```

3. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Vzhledem k tomu, že jde o první spuštění služby, měl by se zobrazit pouze spuštěný modul **edgeAgent**. Ve výchozím nastavení se spustí modul edgeAgent a pomůže vám nainstalovat a spustit všechny další moduly, které nasadíte do svého zařízení.

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart-linux/iotedge-list-1.png)

Teď je zařízení IoT Edge nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.

![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k nasazení IoT Edge modulu pro spuštění na zařízení, aniž byste museli provádět změny samotného zařízení.

V tomto případě modul, který jste odeslali, generuje ukázková data prostředí, která můžete použít pro testování později. Simulovaný senzor sleduje počítač i prostředí v okolí počítače. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzy.

V zařízení IoT Edge znovu otevřete příkazový řádek nebo použijte připojení SSH z Azure CLI. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

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

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Zkontrolujte obsah skupiny prostředků, abyste se ujistili, že nemáte nic, co chcete zachovat. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné.

Odeberte skupinu **IoTEdgeResources**. Odstranění skupiny prostředků může trvat několik minut.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Skupinu prostředků můžete potvrdit tak, že si zobrazíte seznam skupin prostředků.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili zařízení IoT Edge a pomocí Azure IoT Edge cloudového rozhraní nasadíte kód do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení místního vývojového prostředí, abyste mohli začít vytvářet IoT Edge moduly, které spouští vaši obchodní logiku.

> [!div class="nextstepaction"]
> [Zahájení vývoje IoT Edge modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md)
