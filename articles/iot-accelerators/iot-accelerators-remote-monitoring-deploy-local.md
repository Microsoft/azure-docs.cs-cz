---
title: Nasazení řešení vzdáleného monitorování místně – VS IDE – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí sady Visual Studio pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024142"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Místní nasazení akcelerátoru řešení vzdáleného monitorování – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

V tomto článku se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Naučíte se, jak spustit mikroslužby v aplikaci Visual Studio. Místní nasazení mikroslužeb používá následující cloudové služby: IoT Hub, Cosmos DB, Azure streaming Analytics a služby Azure Time Series Insights v cloudu.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v Docker na místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nasadit služby Azure používané akcelerátorem řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení počítače

K dokončení místního nasazení potřebujete na svém místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/) – tento software je předpokladem pro počítače CLI, které skripty používají k vytváření prostředků Azure. Nepoužívejte Node.js v10 za účelem.

> [!NOTE]
> Visual Studio je k dispozici pro Windows a Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spustit mikroslužby

V této části spustíte mikroslužby vzdáleného monitorování. Webové uživatelské rozhraní běží nativně, služba simulace zařízení v Docker a mikroslužby v nástroji Visual Studio.

### <a name="run-the-device-simulation-service"></a>Spuštění služby simulace zařízení

Otevřete nové okno příkazového řádku, abyste měli jistotu, že máte přístup k proměnným prostředí nastaveným skriptem **Start. cmd** v předchozí části.

Spuštěním následujícího příkazu spusťte kontejner Docker pro službu pro simulaci zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasadit všechny ostatní mikroslužby v místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v aplikaci Visual Studio:

1. Spusťte Visual Studio.
1. Ve složce **služby** v místní kopii úložiště otevřete řešení **Remote-Monitoring. sln** .
1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na řešení a klikněte na příkaz **vlastnosti**.
1. Vyberte **společné vlastnosti > spouštěný projekt**.
1. Vyberte **více projektů po spuštění** a nastavte **akci** na **Spustit** pro následující projekty:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Kliknutím na tlačítko **OK** uložte své volby.
1. Klikněte na **ladění > spustit ladění** a sestavte a spusťte webové služby na místním počítači.

Každá webová služba otevře příkazový řádek a okno webového prohlížeče. Na příkazovém řádku se zobrazí výstup z běžící služby a okno prohlížeče vám umožní monitorovat stav. Nezavírejte příkazy příkazového řádku nebo webové stránky, tato akce zastaví webovou službu.

### <a name="start-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

Pomocí následujících kroků spusťte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **skupiny prostředků** vytvořené pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění skriptu **Start. cmd** .
1. V seznamu prostředků klikněte na **úlohu Stream Analytics** .
1. Na stránce **Přehled** úlohy Stream Analytics klikněte na tlačítko **Start** . Potom kliknutím na **Spustit** spusťte úlohu hned teď.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spustíte webové uživatelské rozhraní. Otevřete nové okno příkazového řádku, abyste měli jistotu, že máte přístup k proměnným prostředí nastaveným pomocí skriptu **Start. cmd** . Přejděte do složky **WebUI** v místní kopii úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení spuštění v prohlížeči se zobrazí stránka **http: \/ /localhost: 3000/řídicí panel**. Očekává se chyba na této stránce. Chcete-li zobrazit aplikaci bez chyb, proveďte následující krok.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění NGINX

Nastavte reverzní proxy server pro propojení webové aplikace a mikroslužeb běžící na vašem místním počítači:

* Zkopírujte soubor **Nginx. conf** ze složky **webui\scripts\localhost** v místní kopii úložiště do adresáře pro instalaci **nginx\conf** .
* Spusťte **Nginx**.

Další informace o spuštění **Nginx** najdete v tématu [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Přístup k řídicímu panelu řešení vzdáleného monitorování získáte tak, že v prohlížeči přejdete na http: \/ /localhost: 9000.

## <a name="clean-up"></a>Vyčištění

Aby nedocházelo k zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte do [Azure Portal](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **Start. cmd** .

Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořenou při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
