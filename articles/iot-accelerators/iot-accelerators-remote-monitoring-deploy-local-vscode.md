---
title: Nasazení řešení vzdáleného monitorování místně – Visual Studio Code – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí Visual Studio Code pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024176"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Nasaďte místně Visual Studio Code akcelerátor řešení vzdáleného monitorování.

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

V tomto článku se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Naučíte se, jak spustit mikroslužby v Visual Studio Code. Místní nasazení mikroslužeb používá následující cloudové služby: IoT Hub, Cosmos DB, Azure streaming Analytics a Azure Time Series Insights.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nasadit služby Azure používané akcelerátorem řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení počítače

K dokončení místního nasazení potřebujete na svém místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozšíření C# VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js V8](https://nodejs.org/) – tento software je předpokladem pro počítače CLI, které skripty používají k vytváření prostředků Azure. Nepoužívat Node.js v10 za účelem

> [!NOTE]
> Visual Studio Code je k dispozici pro Windows, Mac a Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spustit mikroslužby

V této části spustíte mikroslužby vzdáleného monitorování. Webové uživatelské rozhraní běží nativně, služba simulace zařízení v Docker a mikroslužby v Visual Studio Code.

### <a name="build-the-code"></a>Sestavení kódu

Přejděte na Azure-IoT-PCs-Remote-Monitoring-dotnet\services na příkazovém řádku a spusťte následující příkazy a sestavte kód.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasadit všechny ostatní mikroslužby v místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v Visual Studio Code:

1. Spusťte editor Visual Studio Code.
1. V VS Code otevřete složku **Azure-IoT-PC-Remote-Monitoring-dotnet** .
1. Ve složce **Azure-IoT-PC-Remote-Monitoring-dotnet** vytvořte novou složku s názvem **. VSCode** .
1. Zkopírujte soubory **launch.jsna** a **tasks.js** z services\scripts\local\launch\idesettings\vscode do složky **. VSCode** , kterou jste právě vytvořili.
1. Otevřete **panel ladění** v vs Code a spusťte konfiguraci **spuštění všech mikroslužeb** . Tato konfigurace spustí mikroslužbu simulace zařízení v Docker a spustí další mikroslužby v ladicím programu.

Výstup ze spuštěných **běhů spustit všechny microsoervices** v konzole ladění vypadá následovně:

[![Nasazení – místní mikroslužby](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spustíte webové uživatelské rozhraní. Přejděte do složky **Azure-IoT-PCs-Remote-Monitoring-dotnet\webui** v místní kopii a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení spuštění v prohlížeči se zobrazí stránka **http: \/ /localhost: 3000/řídicí panel**. Očekává se chyba na této stránce. Chcete-li zobrazit aplikaci bez chyb, proveďte následující krok.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění NGINX

Nastavte reverzní proxy server pro propojení webové aplikace a mikroslužeb běžící na vašem místním počítači:

* Zkopírujte soubor **Nginx. conf** ze složky **webui\scripts\localhost** do instalačního adresáře **nginx\conf** .
* Spusťte **Nginx**.

Další informace o spuštění **Nginx** najdete v tématu [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Přístup k řídicímu panelu řešení vzdáleného monitorování získáte tak, že v prohlížeči přejdete na http: \/ /localhost: 9000.

## <a name="clean-up"></a>Vyčištění

Aby nedocházelo k zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte do [Azure Portal](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **Start. cmd** .

Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořenou při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
