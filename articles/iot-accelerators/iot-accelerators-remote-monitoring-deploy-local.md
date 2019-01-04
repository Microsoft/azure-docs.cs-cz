---
title: Nasazení řešení vzdáleného monitorování místně (přes Visual Studio IDE) – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak do svého místního počítače pomocí sady Visual Studio pro vývoj a testování nasazení akcelerátoru řešení vzdáleného monitorování.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: a30311f8b171d80e036b4e554b2f1026b43c8a67
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604767"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Zjistíte, jak spouštět mikroslužby v sadě Visual Studio. Nasazení místního mikroslužeb používá tyto cloudové služby: Služby IoT Hub, Cosmos DB, Azure Stream Analytics a Azure Time Series Insights v cloudu.

Pokud chcete spustit akcelerátor řešení vzdálené monitorování v Dockeru na místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Instalace počítače

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Server Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) -tento software je předpokladem pro počítače s CLI, skripty použít k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> Visual Studio je k dispozici pro Windows a Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spouštět mikroslužby

V této části spustíte mikroslužeb vzdáleného monitorování. Spuštění ve webovém uživatelském rozhraní nativně, simulace zařízení služby v Dockeru a mikroslužby v sadě Visual Studio.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spuštění ve webovém uživatelském rozhraní. Přejděte **webui** složky ve své místní kopie úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

### <a name="run-the-device-simulation-service"></a>Spusťte službu simulace zařízení

Spuštěním následujícího příkazu spusťte kontejner Dockeru pro službu pro simulaci zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasadit všechny mikroslužby v místním počítači

Následující kroky ukazují, jak spouštět mikroslužby vzdálené monitorování v sadě Visual Studio 2017:

1. Spusťte Visual Studio 2017
1. Otevřít **vzdálené monitoring.sln** řešení **služby** složky místní kopie úložiště.
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na řešení a potom na **vlastnosti**.
1. Vyberte **společná nastavení > projekt po spuštění**.
1. Vyberte **více projektů po spuštění** a nastavte **akce** k **Start** pro následující projekty:
    * Webové služby (asa manager\WebService)
    * Webové služby (auth\WebService)
    * Webové služby (config\WebService)
    * Webové služby (zařízení telemetry\WebService)
    * Webové služby (iothub-manager\WebService)
    * Webové služby (úložiště adapter\WebService)
1. Klikněte na tlačítko **OK** uložit vaše volby.
1. Klikněte na tlačítko **ladit > Spustit ladění** sestavení a spuštění webové služby na místním počítači.

Každou webovou službu se otevře okno příkazového řádku a webové prohlížeče. Na příkazovém řádku se zobrazí výstup ze spuštěné služby a okna prohlížeče vám umožní monitorovat stav. Nezavírejte příkazové řádky nebo webové stránky, tato akce zastaví webové služby.

### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Postupujte podle těchto kroků spustíte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte **skupiny prostředků** vytvořen pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění **start.cmd** skript **.
1. Klikněte na **úlohy Stream Analytics** v seznamu prostředků.
1. V úloze Stream Analytics **přehled** stránky, klikněte na tlačítko **Start** tlačítko. Pak klikněte na tlačítko **Start** ihned spustíte úlohu.

### <a name="configure-and-run-nginx"></a>Nakonfigurování a spuštění serveru NGINX

Reverzní proxy server nastavte propojení webové aplikace a mikroslužeb spouštěných v místním počítači:

* Kopírování **nginx.conf** soubor **webui\scripts\localhost** složku **nginx\conf** instalační adresář.
* Spustit **nginx**.

Další informace o spouštění **nginx**, naleznete v tématu [nginx pro Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojte se k řídicímu panelu

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:9000 ](http://localhost:9000) v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Aby se zabránilo zbytečným poplatky, po dokončení testování odebrat cloudovým službám z vašeho předplatného Azure. Pokud chcete odebrat služby, přejděte [webu Azure portal](https://ms.portal.azure.com) a odstranit prostředek skupiny, která **start.cmd** skript vytvořený.

Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
