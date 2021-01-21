---
title: Rychlý Start vytvoření zařízení Azure IoT Edge ve Windows | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak vytvořit zařízení IoT Edge a pak vzdáleně nasadit předem vytvořený kód z Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f3af2b7839465f886d1edba01eb9988419761dac
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630891"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Rychlý Start: nasazení prvního modulu IoT Edge do zařízení s Windows (Preview)

Vyzkoušejte si Azure IoT Edge v tomto rychlém startu nasazením kontejneru kódu na Linux v zařízení se systémem Windows IoT Edge. IoT Edge vám umožní vzdáleně spravovat kód na svých zařízeních, abyste mohli do hraničních zařízení odesílat další úlohy. V tomto rychlém startu doporučujeme používat vlastní zařízení, abyste zjistili, jak snadné je použít Azure IoT Edge pro Linux ve Windows.

V tomto rychlém startu se naučíte:

* Vytvořte centrum IoT.
* Zaregistrovat zařízení IoT Edge do centra IoT Hub.
* Nainstalujte a spusťte na svém zařízení IoT Edge pro Linux ve Windows Runtime.
* Vzdáleně nasaďte modul na IoT Edge zařízení a zasílejte telemetrii.

![Architektura pro rychlé zprovoznění diagramu pro zařízení a Cloud](./media/quickstart/install-edge-full.png)

V tomto rychlém startu se dozvíte, jak nastavit Azure IoT Edge pro Linux na zařízení s Windows. Pak modul nasadíte z Azure Portal do svého zařízení. Modul použitý v tomto rychlém startu je simulovaný senzor, který generuje data o teplotě, vlhkosti a tlaku. Ostatní kurzy Azure IoT Edge sestavují na práci, kterou tady provedete, nasazením dalších modulů, které analyzují Simulovaná data pro Business Insights.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

>[!NOTE]
>IoT Edge pro Linux ve Windows je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudové prostředky:

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zařízení IoT Edge:

* Vaše zařízení musí být počítač nebo server s Windows verze 1809 nebo novější.
* Minimálně 4 GB paměti, doporučená velikost paměti 8 GB
* 10 GB volného místa na disku

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Začněte s rychlým startem vytvořením služby IoT Hub pomocí Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste v minulosti používali IoT Hub a už máte vytvořeného centra, můžete použít toto centrum IoT.

Následující kód vytvoří ve skupině prostředků bezplatný rozbočovač **F1** `IoTEdgeResources` . Nahraďte `{hub_name}` jedinečným názvem vašeho centra IoT. Vytvoření IoT Hub může trvat několik minut.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud se zobrazí chyba, že název IoT Hub není k dispozici, znamená to, že někdo jiný již má centrum s tímto názvem. Zkuste nový název.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram – registrace zařízení pomocí IoT Hub identity](./media/quickstart/register-device.png)

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Vzhledem k tomu, že se zařízení IoT Edge chovají a dají se spravovat jinak než typická zařízení IoT, deklarujte tuto identitu pro IoT Edge zařízení s `--edge-enabled` příznakem.

1. V Azure Cloud Shell zadejte následující příkaz, který ve svém rozbočovači vytvoří zařízení s názvem **myEdgeDevice** .

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Pokud se zobrazí chyba týkající se klíčů zásad iothubowner, ujistěte se, že na Cloud Shell běží nejnovější verze rozšíření Azure-IoT.

2. Zobrazení připojovacího řetězce pro vaše zařízení, který propojuje fyzické zařízení s jeho identitou v IoT Hub. Obsahuje název vašeho centra IoT, název vašeho zařízení a pak sdílený klíč, který ověřuje připojení mezi nimi.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu klíče `connectionString` z výstupu JSON a uložte ji. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec použijete ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načtení připojovacího řetězce z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte do svého zařízení IoT Edge pro Linux ve Windows a nakonfigurujte ho pomocí připojovacího řetězce zařízení.

![Diagram – spuštění IoT Edge runtime na zařízení](./media/quickstart/start-runtime.png)

1. [Stáhněte si centrum pro správu systému Windows](https://aka.ms/WACDownloadEFLOW).
2. Postupujte podle pokynů Průvodce instalací a nastavte centrum pro správu Windows na svém zařízení.
3. Až budete v centru pro správu Windows, v pravém horním rohu obrazovky vyberte **ikonu ozubeného kolečka pro nastavení** .  
4. V nabídce nastavení v části brána vyberte **rozšíření** .
5. V seznamu **dostupných rozšíření** vyberte **Azure IoT Edge**
6. **Nainstalovat** rozšíření

7. Po instalaci rozšíření přejděte na hlavní stránku řídicího panelu výběrem **centra pro správu Windows** v levém horním rohu obrazovky.

8. Zobrazí se připojení místního hostitele představující počítač, na kterém je spuštěný centrum pro správu systému Windows.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Snímek obrazovky – Úvodní stránka pro správce Windows":::

9. Vyberte **Přidat**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Snímek obrazovky – tlačítko pro přidání úvodní stránky Správce Windows":::

10. Vyhledejte dlaždici Azure IoT Edge a vyberte **vytvořit novou**. Tím se spustí Průvodce instalací nástroje.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Snímek obrazovky Azure IoT Edge pro Linux v dlaždici Windows":::

11. Pokračujte v Průvodci instalací a přijměte smlouvu EULA a klikněte na tlačítko **Další** .

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Snímek obrazovky – Vítejte v Průvodci":::

12. Vyberte **volitelná diagnostická data** pro poskytování rozšířených diagnostických dat, která pomáhají Microsoft monitorovat a udržovat kvalitu služby a klikněte na **Další: nasadit.**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Snímek obrazovky – diagnostická data":::

13. Na obrazovce **Vybrat cílové zařízení** vyberte požadované cílové zařízení, aby se ověřilo, že splňuje minimální požadavky. V tomto rychlém startu instalujeme IoT Edge na místní zařízení, takže zvolíte připojení localhost. Po potvrzení klikněte na tlačítko **Další** a pokračujte.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Snímek obrazovky – výběr cílového zařízení":::

14. Přijměte výchozí nastavení výběrem možnosti **Další**.

15. Na obrazovce nasazení se zobrazuje proces stažení balíčku, instalace balíčku, konfigurace hostitele a konečné nastavení virtuálního počítače Linux.  Úspěšné nasazení bude vypadat takto:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Snímek obrazovky – úspěšné nasazení průvodce":::

16. Klikněte na **Další: připojit** a pokračujte posledním krokem, abyste mohli zařízení Azure IoT Edge ZŘÍDIT pomocí ID zařízení z instance služby IoT Hub.

17. Zkopírujte připojovací řetězec ze zařízení do Azure IoT Hub a vložte ho do pole Připojovací řetězec zařízení. Pak zvolte **zřizování s vybranou metodou**.

    > [!NOTE]
    > Podívejte se na krok 3 v předchozí části [registrace zařízení IoT Edge](#register-an-iot-edge-device), abyste mohli načíst připojovací řetězec.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Snímek obrazovky – zřizování Průvodce":::

18. Až se zřizování dokončí, vyberte **Dokončit** a vraťte se na úvodní obrazovku centra pro správu systému Windows. Vaše zařízení by teď mělo být možné zobrazit jako zařízení IoT Edge.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Snímek obrazovky – centrum pro správu Windows Azure IoT Edge zařízení":::

19. Vyberte zařízení Azure IoT Edge pro zobrazení řídicího panelu. Měli byste vidět, že se v Azure IoT Hub nasadily úlohy ze zařízení, které jsou ve dvojím provozu. **Seznam IoT Edge modul** by měl zobrazovat jeden modul spuštěný **, edgeAgent** a **stav IoT Edge** by se měl zobrazovat jako **aktivní (spuštěný)**.

Teď je zařízení IoT Edge nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte zařízení Azure IoT Edge z cloudu a nasaďte modul, který do IoT Hub odešle data telemetrie.

![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k nasazení IoT Edge modulu pro spuštění na zařízení, aniž byste museli provádět změny samotného zařízení.

V tomto případě modul, který jste odeslali, generuje ukázková data prostředí, která můžete použít pro testování později. Simulovaný senzor sleduje počítač i prostředí v okolí počítače. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zpráva obsahuje okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. Kurzy IoT Edge používají data vytvořená tímto modulem jako testovací data pro analýzy.

Přejděte na příkazové prostředí v centru pro správu Windows a ověřte, že modul nasazený z cloudu běží na vašem zařízení IoT Edge.

1. Připojení k nově vytvořenému IoT Edge zařízení

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Snímek obrazovky – připojení zařízení":::

2. Na stránce **Přehled** se zobrazí **seznam IoT Edge modul** a **IoT Edge stav** , kde vidíte různé moduly, které byly nasazeny, a také stav zařízení.  

3. V nabídce **nástroje** vyberte **příkazové prostředí**. Příkazové prostředí je terminál prostředí PowerShell, který automaticky používá SSH (Secure Shell) pro připojení k VIRTUÁLNÍmu počítači se systémem Azure IoT Edge v počítači s Windows.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Snímek obrazovky – příkazové prostředí":::

4. Pokud chcete ověřit, že se na svém zařízení mají tři moduly, spusťte následující **příkaz bash**:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Snímek obrazovky – seznam prostředí příkazů":::

5. Umožňuje zobrazit zprávy odesílané z modulu snímače teploty do cloudu.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >V IoT Edgech příkazech se rozlišují velká a malá písmena při odkazování na názvy modulů.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Senzor teploty snímku obrazovky":::

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

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Vyčistit odebrání Azure IoT Edge pro Linux ve Windows

Azure IoT Edge pro Linux v systému Windows můžete ze svého zařízení IoT Edge odinstalovat prostřednictvím rozšíření řídicího panelu v centru pro správu systému Windows.

1. V centru pro správu Windows se připojte k Azure IoT Edge pro Linux na připojení zařízení Windows. Rozšíření nástroje řídicího panelu Azure se načte.
2. Vyberte **Odinstalovat**. Po odebrání Azure IoT Edge pro Linux ve Windows přejde centrum pro správu systému Windows na úvodní stránku a ze seznamu se odstraní položka připojení Azure IoT Edge zařízení.

Dalším způsobem, jak odebrat Azure IoT Edge ze systému Windows, je přejít na   >  **Možnosti spustit nastavení**  >  **aplikace**  >  **Azure IoT Edge**  >  **odinstalovat** na IoT Edge zařízení. Tato akce odebere Azure IoT Edge ze zařízení IoT Edge, ale ponechá připojení za centrem pro správu systému Windows. Centrum pro správu Windows je možné odinstalovat i z nabídky nastavení.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili zařízení IoT Edge a pomocí Azure IoT Edge cloudového rozhraní nasadíte kód do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení místního vývojového prostředí, abyste mohli začít vytvářet IoT Edge moduly, které spouští vaši obchodní logiku.

> [!div class="nextstepaction"]
> [Zahájení vývoje IoT Edgech modulů](tutorial-develop-for-linux.md)
