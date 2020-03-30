---
title: Nasazení řešení vzdáleného monitorování místně – kód Visual Studio - Azure | Dokumenty společnosti Microsoft
description: Tento návod ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí kódu Visual Studio pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890963"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – kód sady Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Dozvíte se, jak spustit mikroslužeb v kódu sady Visual Studio. Místní nasazení mikroslužeb používá následující cloudové služby: IoT Hub, Cosmos DB, Azure Streaming Analytics a Azure Time Series Insights.

## <a name="prerequisites"></a>Požadavky

K nasazení služeb Azure používaných akcelerátorem řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení stroje

K dokončení místního nasazení potřebujete v místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Kód visual studia](https://code.visualstudio.com/)
* [Rozšíření C# kódu VS](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) – tento software je předpokladem pro nastavení příkazu k onomu systému PCS, který skripty používají k vytvoření prostředků Azure. Nepoužívejte soubor Node.js v10

> [!NOTE]
> Visual Studio Code je k dispozici pro Windows, Mac a Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spuštění mikroslužeb

V této části spustíte mikroslužby vzdáleného monitorování. Nativně spustíte webové uživatelské rozhraní, službu Simulace zařízení v Dockeru a mikroslužby v kódu sady Visual Studio.

### <a name="build-the-code"></a>Sestavení kódu

Přejděte na azure-iot-pcs-remote-monitoring-dotnet\services v příkazovém řádku a spusťte následující příkazy k vytvoření kódu.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasazení všech ostatních mikroslužeb v místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v kódu sady Visual Studio:

1. Spusťte editor Visual Studio Code.
1. Ve službě VS Code otevřete složku **azure-iot-pcs-remote-monitoring-dotnet.**
1. Vytvořte novou složku s názvem **.vscode** ve složce **azure-iot-pcs-remote-monitoring-dotnet.**
1. Zkopírujte soubory **launch.json** a **tasks.json** ze služby\skripty\local\launch\idesettings\vscode do složky **.vscode,** kterou jste právě vytvořili.
1. Otevřete **panel Ladění** v kódu VS a spusťte konfiguraci Spustit všechny **mikroslužby.** Tato konfigurace spustí mikroslužbu simulace zařízení v Dockeru a spustí další mikroslužby v ladicím programu.

Výstup ze spuštění **spustit všechny mikrosoervices** v konzole ladění vypadá takto:

[![Nasazení místních mikroslužeb](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského uživatelského uživatelského

V tomto kroku spustíte webové uživatelské uz). Přejděte v místní kopii do složky **azure-iot-pcs-remote-monitoring-dotnet\webui** a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení startu se v prohlížeči zobrazí stránka **\/http: /localhost:3000/dashboard**. Chyby na této stránce jsou očekávány. Chcete-li zobrazit aplikaci bez chyb, proveďte následující krok.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění NGINX

Nastavte reverzní proxy server pro propojení webové aplikace a mikroslužeb spuštěných v místním počítači:

* Zkopírujte soubor **nginx.conf** ze složky **webui\scripts\localhost** do instalačního adresáře **nginx\conf.**
* Spusťte **nginx**.

Další informace o spuštění **nginx**naleznete v tématu [nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Chcete-li získat přístup k řídicímu\/panelu řešení vzdáleného monitorování, přejděte v prohlížeči na adresu http: /localhost:9000.

## <a name="clean-up"></a>Vyčištění

Chcete-li se vyhnout zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte na [portál Azure](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **start.cmd.**

Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořeného při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumání možností řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
