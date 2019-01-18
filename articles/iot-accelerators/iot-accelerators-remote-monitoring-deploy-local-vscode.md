---
title: Nasazení řešení vzdáleného monitorování místně (Visual Studio Code) – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak do svého místního počítače pomocí nástroje Visual Studio Code pro vývoj a testování nasazení akcelerátoru řešení vzdáleného monitorování.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6e2fafa398b09d0822c4582e196345b812e6fc52
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392242"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Zjistíte, jak spouštět mikroslužby v aplikaci Visual Studio Code. Nasazení místního mikroslužeb používá tyto cloudové služby: IoT Hub, Cosmos DB, Azure Streamovou analýzu a služby Azure Time Series Insights.

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Instalace počítače

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [.Net Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Server Nginx](http://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code C# rozšíření](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) -tento software je předpokladem pro počítače s CLI, skripty použít k vytvoření prostředků Azure. Nepoužívejte Node.js v10

> [!NOTE]
> Visual Studio Code je k dispozici pro Windows, Mac OS a Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Spouštět mikroslužby

V této části spustíte mikroslužeb vzdáleného monitorování. Spuštění ve webovém uživatelském rozhraní nativně, simulace zařízení služby v Dockeru a mikroslužeb ve Visual Studio Code.

### <a name="build-the-code"></a>Sestavení kódu

Přejděte do azure-iot-pcs-remote-monitoring-dotnet\services v příkazovém řádku a spusťte následující příkazy sestavení kódu.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasadit všechny mikroslužby v místním počítači

Následující kroky ukazují, jak spouštět mikroslužby vzdálené monitorování v sadě Visual Studio 2017:

1. Spusťte editor Visual Studio Code.
1. Otevřít **azure-iot-pcs-remote-monitoring-dotnet** modul z místní kopie v nástroji VS Code.
1. Zkopírujte soubory **launch.json** a **tasks.json** z scripts\local\launch\idesettings\vscode\. Vytvořte novou složku **azure-iot-pcs-remote-monitoring-dotnet\.vscode** a vkládání souborů existuje.
1. Otevřete panel ladění ve VS Code a spustit **spustit všechny mikroslužby** konfigurace. Tato konfigurace mikroslužeb simulace zařízení spustí v Dockeru a běží mikroslužby v ladicím programu.

Jako příklad výstupu pro **Auth** službu **ladění konzoly** vypadá podobně jako následující:

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spuštění ve webovém uživatelském rozhraní. Přejděte do **azure-iot-pcs-remote-monitoring-dotnet\webui** z vaší místní složky zkopírujte a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení spuštění v prohlížeči zobrazí na stránce **http://localhost:3000/dashboard**. Chyby na této stránce se očekává. Chcete-li zobrazit aplikaci bez chyby, proveďte následující kroky.

### <a name="configure-and-run-nginx"></a>Nakonfigurování a spuštění serveru NGINX

Reverzní proxy server nastavte propojení webové aplikace a mikroslužeb spouštěných v místním počítači:

* Kopírování **nginx.conf** soubor **webui\scripts\localhost** složku **nginx\conf** instalační adresář.
* Spustit **nginx**.

Další informace o spouštění **nginx**, naleznete v tématu [nginx pro Windows](http://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojte se k řídicímu panelu

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:9000 ](http://localhost:9000) v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Aby se zabránilo zbytečným poplatky, po dokončení testování odebrat cloudovým službám z vašeho předplatného Azure. Pokud chcete odebrat služby, přejděte [webu Azure portal](https://ms.portal.azure.com) a odstranit prostředek skupiny, která **start.cmd** skript vytvořený.

Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).