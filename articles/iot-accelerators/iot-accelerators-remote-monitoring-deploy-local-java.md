---
title: Nasazení řešení vzdáleného monitorování místně (pomocí IntelliJ integrované vývojové prostředí) – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak do svého místního počítače pro vývoj a testování pomocí IntelliJ nasazení akcelerátoru řešení vzdáleného monitorování.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: e1918d527afbe5aad647d84ab82889099f5f22b9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55105962"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Zjistíte, jak spouštět mikroslužby v IntelliJ. Nasazení místního mikroslužeb používá tyto cloudové služby: Služby IoT Hub, Cosmos DB, Azure Stream Analytics a Azure Time Series Insights v cloudu.

Pokud chcete spustit akcelerátor řešení vzdálené monitorování v Dockeru na místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Instalace počítače

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Plugin Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Prováděcí modul plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Server Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) -tento software je předpokladem pro počítače s CLI, skripty použít k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> IntelliJ integrovaného vývojového prostředí je k dispozici pro Windows a Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Spouštět mikroslužby

V této části spustíte mikroslužeb vzdáleného monitorování. Spuštění ve webovém uživatelském rozhraní nativně, simulace zařízení, služba vícefaktorového ověřování a správce Azure Stream Analytics v Dockeru a mikroslužby v IntelliJ.

### <a name="run-the-device-simulation-service"></a>Spusťte službu simulace zařízení

Otevřete nové okno příkazového řádku, ujistěte se, že máte přístup k proměnným prostředí nastavil **start.cmd** skript v předchozí části.

Spuštěním následujícího příkazu spusťte kontejner Dockeru pro službu pro simulaci zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Spuštění služby ověření

Otevřete nové okno příkazového řádku a spuštěním následujícího příkazu spusťte kontejner Dockeru pro službu ověřování. Služba umožňuje spravovat uživatele, oprávnění k přístupu k řešení Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Spusťte službu správce Azure Stream Analytics

Otevřete nové okno příkazového řádku a spuštěním následujícího příkazu spusťte kontejner Dockeru pro službu Azure Stream Analytics správce. Služba umožňuje správu úloh Azure Stream Analytics (ASA), včetně nastavení konfigurace a spouštění, zastavování a sledování jejich stavy.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Nasadit všechny mikroslužby v místním počítači

Následující kroky ukazují, jak spouštět mikroslužby vzdálené monitorování v IntelliJ:

#### <a name="import-project"></a>Import projektu

1. Launch IntelliJ IDE
1. Vyberte **Importovat projekt** a zvolte **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Vytvoření konfigurace spuštění

1. Vyberte **spuštění > Upravit konfigurace**
1. Vyberte **přidat novou konfiguraci > sbt úkolu** 
1. Zadejte **název** a zadejte **úlohy** spuštění 
1. Vyberte **pracovní adresář** podle služby, které chcete spustit
1. Klikněte na tlačítko **použít > Ok** uložit vaše volby.
1. Vytvoření konfigurace spuštění pro následující služby:
    * WebService (services\config)
    * Webové služby (services\device telemetrická data)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Jako příklad na následujícím obrázku zobrazuje přidává se konfigurace služby:

[![Přidat konfiguraci](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Vytvoření složeného konfigurace

1. Ke spuštění všech služeb, společně vyberte **přidat novou konfiguraci > složené**
1. Zadejte **název** a **přidat sbt úlohy**
1. Klikněte na tlačítko **použít > Ok** uložit vaše volby.

Jako příklad na následujícím obrázku ukazuje přidání všech úloh sbt jediné konfiguraci:


[![Add-All-Services](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Klikněte na tlačítko **spustit** sestavení a spuštění webové služby na místním počítači.

Každou webovou službu se otevře okno příkazového řádku a webové prohlížeče. Na příkazovém řádku se zobrazí výstup ze spuštěné služby a okna prohlížeče vám umožní monitorovat stav. Nezavírejte příkazové řádky nebo webové stránky, tato akce zastaví webové služby.


Pro přístup k stav služeb, můžete přejít na následující adresy URL:
* IoT-Hub Manager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetrie zařízení  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Konfigurace [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Adaptér úložiště [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Postupujte podle těchto kroků spustíte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte **skupiny prostředků** vytvořen pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění **start.cmd** skript **.
1. Klikněte na tlačítko **úlohy Stream Analytics** v seznamu prostředků.
1. V úloze Stream Analytics **přehled** stránky, klikněte na tlačítko **Start** tlačítko. Pak klikněte na tlačítko **Start** ihned spustíte úlohu.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spuštění ve webovém uživatelském rozhraní. Otevřete nové okno příkazového řádku, ujistěte se, že máte přístup k proměnným prostředí nastavil **start.cmd** skriptu. Přejděte **webui** složky ve své místní kopie úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení spuštění v prohlížeči zobrazí na stránce **http://localhost:3000/dashboard**. Chyby na této stránce se očekává. Chcete-li zobrazit aplikaci bez chyby, proveďte následující kroky.

### <a name="configure-and-run-nginx"></a>Nakonfigurování a spuštění serveru NGINX

Reverzní proxy server nastavte propojení webové aplikace a mikroslužeb spouštěných v místním počítači:

* Kopírování **nginx.conf** ze soubor **webui\scripts\localhost** složky místní kopie úložiště **nginx\conf** instalační adresář.
* Spustit **nginx**.

Další informace o spouštění **nginx**, naleznete v tématu [nginx pro Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojte se k řídicímu panelu

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:9000 ](http://localhost:9000) v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Aby se zabránilo zbytečným poplatky, po dokončení testování odebrat cloudovým službám z vašeho předplatného Azure. Pokud chcete odebrat služby, přejděte [webu Azure portal](https://ms.portal.azure.com) a odstranit prostředek skupiny, která **start.cmd** skript vytvořený.

Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
