---
title: Nasazení řešení vzdáleného monitorování místně – VS IDE – Azure | Dokumenty společnosti Microsoft
description: Tento návod ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí sady Visual Studio pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890893"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Místní nasazení akcelerátoru řešení vzdáleného monitorování – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Dozvíte se, jak spustit mikroslužeb v sadě Visual Studio. Místní nasazení mikroslužeb používá v cloudu následující cloudové služby: IoT Hub, Cosmos DB, Azure Streaming Analytics a Azure Time Series Insights.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v Dockeru v místním počítači, přečtěte si informace [o místním nasazení akcelerátoru řešení vzdáleného monitorování – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služeb Azure používaných akcelerátorem řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení stroje

K dokončení místního nasazení potřebujete v místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) – tento software je předpokladem pro nastavení příkazu k onomu systému PCS, který skripty používají k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> Visual Studio je k dispozici pro Windows a Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spuštění mikroslužeb

V této části spustíte mikroslužby vzdáleného monitorování. Nativně spustíte webové uživatelské rozhraní, službu Simulace zařízení v Dockeru a mikroslužby v sadě Visual Studio.

### <a name="run-the-device-simulation-service"></a>Spuštění služby simulace zařízení

Otevřete nové okno příkazového řádku a ujistěte se, že máte přístup k proměnným prostředí nastaveným skriptem **start.cmd** v předchozí části.

Spusťte následující příkaz a spusťte kontejner Dockeru pro simulační službu zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasazení všech ostatních mikroslužeb v místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v sadě Visual Studio:

1. Spusťte Visual Studio.
1. Otevřete řešení **remote-monitoring.sln** ve složce **služby** v místní kopii úložiště.
1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na řešení a klepněte na příkaz **Vlastnosti**.
1. Vyberte **možnost Běžné vlastnosti > projektu spuštění .**
1. Vyberte **více projektů při spuštění** a nastavte **akci** na **začátek** pro následující projekty:
    * WebService (asa-manager\WebService)
    * Webservice (auth\WebService)
    * WebService (config\WebService)
    * WebService (telemetrie zařízení\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (paměťový adaptér\WebService)
1. Kliknutím na **OK** uložte své volby.
1. Chcete-li vytvořit a spustit webové služby v místním počítači, klepněte na tlačítko **Ladění > spustit ladění.**

Každá webová služba otevře příkazový řádek a okno webového prohlížeče. Na příkazovém řádku se zobrazí výstup ze spuštěné služby a okno prohlížeče umožňuje sledovat stav. Nezavírejte příkazové příkazy ani webové stránky, tato akce zastaví webovou službu.

### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Chcete-li spustit úlohu Stream Analytics, postupujte takto:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **skupiny prostředků** vytvořené pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro řešení při spuštění skriptu **start.cmd.**
1. V seznamu zdrojů klikněte na **úlohu Stream Analytics.**
1. Na stránce **Přehled** úlohy Stream Analytics klikněte na tlačítko **Start.** Potom kliknutím na **tlačítko Start** spusťte úlohu nyní.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského uživatelského uživatelského

V tomto kroku spustíte webové uživatelské uz). Otevřete nové okno příkazového řádku a ujistěte se, že máte přístup k proměnným prostředí nastaveným skriptem **start.cmd.** Přejděte do složky **webui** v místní kopii úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení startu se v prohlížeči zobrazí stránka **\/http: /localhost:3000/dashboard**. Chyby na této stránce jsou očekávány. Chcete-li zobrazit aplikaci bez chyb, proveďte následující krok.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění NGINX

Nastavte reverzní proxy server pro propojení webové aplikace a mikroslužeb spuštěných v místním počítači:

* Zkopírujte soubor **nginx.conf** ze složky **webui\scripts\localhost** v místní kopii úložiště do instalačního adresáře **nginx\conf.**
* Spusťte **nginx**.

Další informace o spuštění **nginx**naleznete v tématu [nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Chcete-li získat přístup k řídicímu\/panelu řešení vzdáleného monitorování, přejděte v prohlížeči na adresu http: /localhost:9000.

## <a name="clean-up"></a>Vyčištění

Chcete-li se vyhnout zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte na [portál Azure](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **start.cmd.**

Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořeného při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumání možností řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
