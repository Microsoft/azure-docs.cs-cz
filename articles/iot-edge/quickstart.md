---
title: Rychlý Start k vytvoření zařízení Azure IoT Edge ve Windows | Microsoft Docs
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
ms.openlocfilehash: 5444f6adb9d441cb6253c180cf2d079c1c36316c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562677"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Rychlý Start: nasazení prvního modulu IoT Edge do zařízení s Windows (Preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Vyzkoušejte si Azure IoT Edge v tomto rychlém startu nasazením kontejneru kódu na Linux v zařízení se systémem Windows IoT Edge. IoT Edge vám umožní vzdáleně spravovat kód na svých zařízeních, abyste mohli do hraničních zařízení odesílat další úlohy. V tomto rychlém startu doporučujeme používat vlastní zařízení, abyste zjistili, jak snadné je použít Azure IoT Edge pro Linux ve Windows.

V tomto rychlém startu se naučíte:

* Vytvořte centrum IoT.
* Zaregistrovat zařízení IoT Edge do centra IoT Hub.
* Nainstalujte a spusťte na svém zařízení IoT Edge pro Linux ve Windows Runtime.
* Vzdáleně nasaďte modul na IoT Edge zařízení a zasílejte telemetrii.

![Diagram znázorňující architekturu tohoto rychlého startu pro vaše zařízení a Cloud.](./media/quickstart/install-edge-full.png)

V tomto rychlém startu se dozvíte, jak nastavit Azure IoT Edge pro Linux na zařízení s Windows. Pak modul nasadíte z Azure Portal do svého zařízení. Modul, který budete používat, je simulovaný senzor, který generuje data o teplotě, vlhkosti a tlaku. Další kurzy Azure IoT Edge sestavují na práci, kterou tady provedete, nasazováním modulů, které analyzují Simulovaná data pro Business Insights.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

>[!NOTE]
>IoT Edge pro Linux ve Windows je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Vytvořte cloudovou skupinu prostředků pro správu všech prostředků, které budete používat v rámci tohoto rychlého startu.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Ujistěte se, že vaše zařízení IoT Edge splňuje následující požadavky:

* Edice
  * Windows 10 verze 1809 nebo novější; Build 17763 nebo novější
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 Build 17763 nebo novější

  
* Požadavky na hardware
  * Minimální volná paměť: 2 GB
  * Minimální volné místo na disku: 10 GB


>[!NOTE]
>V tomto rychlém startu se pomocí centra pro správu Windows vytvoří nasazení IoT Edge pro Linux ve Windows. Můžete také použít PowerShell. Pokud chcete k vytvoření nasazení použít PowerShell, postupujte podle pokynů v příručce k [instalaci a zřízení Azure IoT Edge pro Linux na zařízení s Windows](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Začněte vytvořením služby IoT Hub pomocí Azure CLI.

![Diagram, který ukazuje krok vytvoření rozbočovače I o T.](./media/quickstart/create-iot-hub.png)

Úroveň Free služby Azure IoT Hub funguje pro účely tohoto rychlého startu. Pokud jste v minulosti používali IoT Hub a už máte vytvořeného centra, můžete použít toto centrum IoT.

Následující kód vytvoří ve skupině prostředků bezplatný rozbočovač **F1** `IoTEdgeResources` . Nahraďte `{hub_name}` jedinečným názvem vašeho centra IoT. Vytvoření centra IoT může trvat několik minut.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Pokud se zobrazí chyba, protože už máte ve svém předplatném jeden bezplatný rozbočovač, změňte SKLADOVOU položku na `S1` . Pokud se zobrazí chyba, že název centra IoT není k dispozici, někdo jiný už má centrum s tímto názvem. Zkuste nový název.

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram, který ukazuje krok registrace zařízení s identitou služby IoT Hub.](./media/quickstart/register-device.png)

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Zařízení IoT Edge se chovají a dají se spravovat jinak než typická zařízení IoT. Pomocí `--edge-enabled` příznaku deklarujete, že tato identita je určena pro IoT Edge zařízení.

1. V Azure Cloud Shell zadejte následující příkaz, který ve svém rozbočovači vytvoří zařízení s názvem **myEdgeDevice** .

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Pokud se zobrazí chyba týkající `iothubowner` se klíčů zásad, ujistěte se, že je v Cloud Shell spuštěná nejnovější verze rozšíření Azure IoT.

1. Zobrazení připojovacího řetězce pro vaše zařízení, který propojuje fyzické zařízení s jeho identitou v IoT Hub. Obsahuje název vašeho centra IoT, název vašeho zařízení a sdílený klíč, který ověřuje spojení mezi nimi.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Zkopírujte hodnotu klíče `connectionString` z výstupu JSON a uložte ji. Tato hodnota je připojovací řetězec zařízení. Použijete ho ke konfiguraci modulu runtime IoT Edge v další části.

     ![Snímek obrazovky, který zobrazuje výstup connectionString v Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte do svého zařízení IoT Edge pro Linux ve Windows a nakonfigurujte ho pomocí připojovacího řetězce zařízení.

![Diagram, který ukazuje krok spuštění modulu runtime IoT Edge.](./media/quickstart/start-runtime.png)

1. [Stáhněte si centrum pro správu systému Windows](https://aka.ms/wacdownload).

1. Podle pokynů v Průvodci instalací nastavte centrum pro správu Windows na svém zařízení.

1. Otevřete centrum pro správu systému Windows.

1. Vyberte ikonu **ozubeného kolečka pro nastavení** v pravém horním rohu a pak vyberte **rozšíření**.

1. Na kartě **informační kanály** vyberte **Přidat**.

1. `https://aka.ms/wac-insiders-feed`Do textového pole zadejte a pak vyberte **Přidat**.

1. Po přidání informačního kanálu otevřete kartu **rozšíření k dispozici** a počkejte, než se seznam rozšíření aktualizuje.

1. V seznamu **dostupných rozšíření** vyberte **Azure IoT Edge**.

1. Nainstalujte rozšíření.

1. Po instalaci rozšíření vyberte **Centrum pro správu Windows** v levém horním rohu, abyste přešli na hlavní stránku řídicího panelu.

     Připojení **localhost** představuje počítač, na kterém používáte centrum pro správu systému Windows.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Snímek obrazovky úvodní stránky Správce Windows":::

1. Vyberte **Přidat**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Snímek obrazovky, který zobrazuje výběr tlačítka Přidat v centru pro správu systému Windows.":::

1. Na dlaždici Azure IoT Edge vyberte **vytvořit novou** a spusťte tak Průvodce instalací.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Snímek obrazovky, který ukazuje vytvoření nového nasazení v Azure IoT Edge dlaždicí.":::

1. Pokračujte v Průvodci instalací a přijměte licenční podmínky pro software společnosti Microsoft a potom vyberte možnost **Další**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Snímek obrazovky, na kterém se zobrazí další možnosti, pokud chcete pokračovat v Průvodci instalací.":::

1. Vyberte **volitelná diagnostická data** a pak vyberte **Další: nasadit**. Tento výběr poskytuje rozšířená diagnostická data, která pomáhají společnosti Microsoft monitorovat a udržovat kvalitu služby.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Snímek obrazovky zobrazující možnosti diagnostických dat":::

1. Na obrazovce **Vybrat cílové zařízení** vyberte požadované cílové zařízení, aby se ověřilo, že splňuje minimální požadavky. V tomto rychlém startu instalujeme IoT Edge na místní zařízení, takže zvolíte připojení **localhost** . Pokud cílové zařízení splňuje požadavky, klikněte na tlačítko **Další** a pokračujte.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Snímek obrazovky, který zobrazuje seznam cílových zařízení.":::

1. Kliknutím na tlačítko **Další** přijměte výchozí nastavení. Na obrazovce nasazení se zobrazuje proces stahování balíčku, instalace balíčku, konfigurace hostitele a konečné nastavení virtuálního počítače se systémem Linux (VM). Úspěšné nasazení vypadá takto:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Snímek obrazovky úspěšného nasazení.":::

1. Vyberte **Další: připojit** , abyste pokračovali v posledním kroku, abyste Azure IoT Edge zařízení zřídili pomocí ID zařízení z instance služby IoT Hub.

1. Vložte připojovací řetězec, který jste zkopírovali [dříve v tomto rychlém](#register-an-iot-edge-device) startu do pole **připojovací řetězec zařízení** . Pak vyberte **zřizování s vybranou metodou**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Snímek obrazovky zobrazující připojovací řetězec v poli připojovací řetězec zařízení":::

1. Po dokončení zřizování vyberte **Dokončit** pro dokončení a vraťte se na úvodní obrazovku centra pro správu systému Windows. Měli byste vidět, že vaše zařízení je uvedené jako zařízení IoT Edge.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Snímek obrazovky zobrazující všechna připojení v centru pro správu systému Windows.":::

1. Vyberte zařízení Azure IoT Edge pro zobrazení řídicího panelu. Měli byste vidět, že se v Azure IoT Hub nasadily úlohy ze zařízení, které jsou ve dvojím provozu. V **seznamu IoT Edge modulu** by se měl zobrazit jeden modul se systémem **edgeAgent** a **stav IoT Edge** by měl být **aktivní (spuštěný)**.

Teď je zařízení IoT Edge nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte zařízení Azure IoT Edge z cloudu a nasaďte modul, který do IoT Hub odešle data telemetrie.

![Diagram, který ukazuje krok nasazení modulu.](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k nasazení IoT Edge modulu pro spuštění na zařízení, aniž byste museli provádět změny samotného zařízení.

Modul, který jste odeslali, vygeneruje ukázková data prostředí, která můžete použít pro testování později. Simulovaný senzor sleduje počítač i prostředí v okolí počítače. Tento senzor může být například v serverové místnosti, v továrně nebo na větrné turbíně. Zprávy, které posílá, zahrnují okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko. IoT Edge kurzy používají data vytvořená tímto modulem jako testovací data pro analýzy.

Z příkazového prostředí v centru pro správu Windows ověřte, že modul, který jste nasadili z cloudu, běží na vašem zařízení IoT Edge.

1. Připojte se k nově vytvořenému IoT Edge zařízení.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Snímek obrazovky, který zobrazuje výběr možnosti připojit v centru pro správu systému Windows.":::

     Na stránce **Přehled** se zobrazí **seznam IoT Edge modul** a **stav IoT Edge**. Můžete zobrazit moduly, které byly nasazeny, a stav zařízení.  

1. V části **nástroje** vyberte **příkazové prostředí**. Příkazové prostředí je terminál prostředí PowerShell, který automaticky používá Secure Shell (SSH) pro připojení k VIRTUÁLNÍmu počítači se systémem Linux Azure IoT Edge na počítači s Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Snímek obrazovky, který ukazuje otevření příkazového prostředí.":::

1. Pokud chcete ověřit, že se na svém zařízení mají tři moduly, spusťte následující příkaz bash:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Snímek obrazovky, který zobrazuje výstup z příkazového prostředí I o Edge na list.":::

1. Umožňuje zobrazit zprávy odesílané z modulu snímače teploty do cloudu.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >V příkazech IoT Edge jsou rozlišována velká a malá písmena, pokud odkazují na názvy modulů.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Snímek obrazovky, který zobrazuje seznam zpráv odeslaných z modulu do cloudu.":::

K doručování zpráv do služby IoT Hub můžete použít také [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat na kurzy IoT Edge, přeskočte tento krok. V tomto rychlém startu můžete použít zařízení, které jste zaregistrovali a nastavili. Jinak můžete odstranit prostředky Azure, které jste vytvořili, abyste se vyhnuli poplatkům.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

> [!IMPORTANT]
> Zkontrolujte obsah skupiny prostředků, abyste se ujistili, že nemáte nic, co chcete zachovat. Odstranění skupiny prostředků je nevratné.

Pro odebrání skupiny **IoTEdgeResources** použijte následující příkaz. Odstranění může trvat několik minut.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Pomocí tohoto příkazu si můžete ověřit, že se skupina prostředků odebírá, a to tak, že se zobrazí seznam skupin prostředků.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Odebrání Azure IoT Edge pro Linux ve Windows

Pomocí rozšíření řídicího panelu v centru pro správu Windows odinstalujte Azure IoT Edge pro Linux ve Windows.

1. Připojte se k zařízení IoT Edge v centru pro správu systému Windows. Rozšíření nástroje řídicího panelu Azure se načte.

1. Vyberte **Odinstalovat**. Po odebrání Azure IoT Edge odebere centrum pro správu systému Windows z **úvodní** stránky položku připojení zařízení Azure IoT Edge.

>[!Note]
>Dalším způsobem, jak odebrat Azure IoT Edge ze systému Windows, je vybrat možnost **Spustit**  >  **Nastavení**  >  **aplikace**  >  **Azure IoT Edge**  >  **odinstalovat** na IoT Edge zařízení. Tato metoda odebere ze zařízení IoT Edge Azure IoT Edge, ale ponechá připojení za centrem pro správu systému Windows. K dokončení odebrání odinstalujte centrum pro správu systému Windows z nabídky **Nastavení** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili zařízení IoT Edge a pomocí Azure IoT Edge cloudového rozhraní nasadíte kód do zařízení. Nyní máte testovací zařízení generující nezpracovaná data o jeho prostředí.

V dalším kroku nastavte své místní vývojové prostředí, abyste mohli začít vytvářet IoT Edge moduly, které spouští vaši obchodní logiku.

> [!div class="nextstepaction"]
> [Zahájení vývoje IoT Edgech modulů](tutorial-develop-for-linux.md)
