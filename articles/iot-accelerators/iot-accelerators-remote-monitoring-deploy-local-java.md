---
title: Nasazení řešení vzdáleného monitorování místně – IntelliJ IDE – Azure | Dokumenty společnosti Microsoft
description: Tento návod vám ukáže, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí IntelliJ pro testování a vývoj.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888812"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Dozvíte se, jak spustit mikroslužeb v IntelliJ. Místní nasazení mikroslužeb bude používat následující cloudové služby: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics a Azure Time Series Insights.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v Dockeru v místním počítači, přečtěte si informace [o místním nasazení akcelerátoru řešení vzdáleného monitorování – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služeb Azure používaných akcelerátorem řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení stroje

K dokončení místního nasazení potřebujete v místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Komunitní edice IntelliJ](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala plugin](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plugin IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT Executor plugin](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 je předpokladem pro nastavení příkazu k onomu počítači PCS, které skripty používají k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> IntelliJ IDE je k dispozici pro Windows a Mac.

## <a name="download-the-source-code"></a>Stáhnout zdrojový kód

Úložiště zdrojového kódu vzdáleného monitorování zahrnují zdrojový kód a konfigurační soubory Dockeru, které potřebujete ke spuštění bitových kopií Dockeru mikroslužeb.

Chcete-li klonovat a vytvořit místní verzi úložiště, přejděte pomocí prostředí příkazového řádku do vhodné složky v místním počítači. Potom spusťte jednu z následujících sad příkazů, abyste naklonovat úložiště Java:

* Chcete-li stáhnout nejnovější verzi implementací mikroslužeb java, spusťte následující příkaz:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Chcete-li načíst nejnovější dílčí moduly, spusťte následující příkazy:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužeb kromě skripty, které používáte ke spuštění mikroslužeb místně. Nepotřebujete zdrojový kód ke spuštění mikroslužeb v Dockeru. Zdrojový kód je však užitečné, pokud později plánujete upravit akcelerátor řešení a otestovat změny místně.

## <a name="deploy-the-azure-services"></a>Nasazení služeb Azure

I když tento článek ukazuje, jak spustit mikroslužeb místně, závisí na službách Azure spuštěných v cloudu. K nasazení služeb Azure použijte následující skript. Příklady skriptů předpokládají, že používáte úložiště Java na počítači se systémem Windows. Pokud pracujete v jiném prostředí, upravte odpovídajícím způsobem cesty, přípony souborů a oddělovače cest.

### <a name="create-new-azure-resources"></a>Vytvoření nových prostředků Azure

Pokud jste ještě nevytvořili požadované prostředky Azure, postupujte takto:

1. V prostředí příkazového řádku přejděte do složky **\services\scripts\local\launch** v klonované kopii úložiště.

1. Spusťte následující příkazy k instalaci nástroje **příkazu příkazu k lis ování počítačů** a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spusťte skript **start.cmd.** Skript vás vyzve k zadání následujících informací:

   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datového centra Azure, které chcete použít.

   Skript vytvoří skupinu prostředků v Azure, která má název vašeho řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Tuto skupinu prostředků můžete odstranit poté, co již nebudete potřebovat odpovídající prostředky.

   Skript také přidá sadu proměnných prostředí do místního počítače. Každý název proměnné má předponu **PCS**. Tyto proměnné prostředí poskytují podrobnosti, které umožňují vzdálené monitorování číst hodnoty konfigurace z prostředku Azure Key Vault.

   > [!TIP]
   > Po dokončení skriptu uloží proměnné prostředí do souboru nazývaného ** \<\>\\název\\\<\>řešení .env domovské složky .pcs**. Můžete je použít pro budoucí nasazení akcelerátoru řešení. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepsat hodnoty ve **skriptech služby\\\\místní\\soubor ENV** při spuštění **docker-compose**.

1. Zavřete prostředí příkazového řádku.

### <a name="use-existing-azure-resources"></a>Použití existujících prostředků Azure

Pokud jste už vytvořili požadované prostředky Azure, nastavte odpovídající proměnné prostředí v místním počítači:
* **PCS_KEYVAULT_NAME**: Název prostředku trezoru klíčů.
* **PCS_AAD_APPID:** ID aplikace Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: Tajný klíč aplikace Azure AD.

Hodnoty konfigurace budou přečteny z tohoto prostředku trezoru klíčů. Tyto proměnné prostředí lze uložit do souboru ** \<\>\\\\\<\>.env složky .pcs** z nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepíší hodnoty v **místním\\\\\\** souboru ENV služby při spuštění **docker-compose**.

Některé konfigurace potřebné mikroslužby je uložen v instanci trezoru klíčů, který byl vytvořen při počátečním nasazení. Odpovídající proměnné v trezoru klíčů by měly být podle potřeby upraveny.

## <a name="run-the-microservices"></a>Spuštění mikroslužeb

V této části spustíte mikroslužby vzdáleného monitorování. Spustíte:

* Webové uživatelské uživatelské stránky nativně.
* Služby Azure IoT Device Simulation, Auth a Azure Stream Analytics Manager v Dockeru.
* Mikroslužeb v IntelliJ.

### <a name="run-the-device-simulation-service"></a>Spuštění služby Simulace zařízení

Otevřete nové okno příkazového řádku. Zkontrolujte, zda máte přístup k proměnným prostředí nastaveným skriptem **start.cmd** v předchozí části.

Spuštěním následujícího příkazu otevřete kontejner Dockeru pro službu Simulace zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Spuštění služby Auth

Otevřete nové okno příkazového řádku a spusťte následující příkaz a otevřete kontejner Dockeru pro službu Auth. Pomocí této služby můžete spravovat uživatele, kteří mají oprávnění k přístupu k řešením Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Spuštění služby Stream Analytics Manager

Otevřete nové okno příkazového řádku a spusťte následující příkaz a otevřete kontejner Dockeru pro službu Stream Analytics Manager. Pomocí této služby můžete spravovat úlohy Služby Stream Analytics. Tato správa zahrnuje nastavení konfigurace úlohy a spuštění, zastavení a sledování stavu úlohy.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Nasazení všech ostatních mikroslužeb v místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v IntelliJ.

#### <a name="import-a-project"></a>Import projektu

1. Otevřete ide IntelliJ.
1. Vyberte **možnost Importovat projekt**.
1. Zvolte **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Vytvořit konfigurace spuštění

1. Vyberte **možnost Spustit** > **upravit konfigurace**.
1. Vyberte **přidat novou konfiguraci** > **úkolu sbt**.
1. Zadejte **název**a potom zadejte **úkoly** jako **spustit**.
1. Vyberte **pracovní adresář** na základě služby, kterou chcete spustit.
1. **Chcete-li** > uložit volby, vyberte Použít**OK.**
1. Vytvořte konfigurace spuštění pro následující webové služby:
    * WebService (služby\konfigurace)
    * WebService (služby\telemetrie zařízení)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Například následující obrázek ukazuje, jak přidat konfiguraci pro službu:

[![Snímek obrazovky okna IntelliJ IDE Run/Debug Configurations zobrazující ho v seznamu úloh sbt v levém podokně zvýrazněnou možností storageAdapter a položky v polích Název, Úkoly, Pracovní adresář a Parametry virtuálního počítače v pravém podokně.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Vytvoření složené konfigurace

1. Chcete-li spustit všechny služby společně, vyberte **přidat novou konfigurační** > **složenou hodnotu**.
1. Zadejte **název**a vyberte **přidat úkoly sbt**.
1. **Chcete-li** > uložit volby, vyberte Použít**OK.**

Například následující obrázek ukazuje, jak přidat všechny úlohy sbt do jedné konfigurace:

[![Snímek obrazovky s oknem Konfigurace spuštění/ladění IntelliJ IDE se zvýrazněnou možností AllServices zvýrazněnou v seznamu Složená v levém podokně a v pravém podokně zvýrazněná možnost úlohy sbt "deviceTelemetry".](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Chcete-li vytvořit a spustit webové služby v místním počítači, vyberte **možnost Spustit.**

Každá webová služba otevře okno příkazového řádku a okno webového prohlížeče. Na příkazovém řádku se zobrazí výstup ze spuštěné služby. Okno prohlížeče umožňuje sledovat stav. Nezavírejte okna nebo webové stránky příkazového řádku, protože tyto akce zastavují webovou službu.

Chcete-li získat přístup ke stavu služeb, přejděte na následující adresy URL:

* Správce IoT-Hubu:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetrie zařízení:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adaptér úložiště:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Chcete-li spustit úlohu Stream Analytics, postupujte takto:

1. Přejděte na [portál Azure](https://portal.azure.com).
1. Přejděte do **skupiny prostředků** vytvořené pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro řešení při spuštění skriptu **start.cmd.**
1. V seznamu zdrojů vyberte **úlohu Stream Analytics.**
1. Na stránce **Přehled** úlohy Stream Analytics vyberte tlačítko **Start** a pak vyberte **Start,** chcete-li úlohu spustit.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského uživatelského uživatelského

V tomto kroku spustíte webové uživatelské uz). Otevřete nové okno příkazového řádku. Zkontrolujte, zda máte přístup k proměnným prostředí nastaveným skriptem **start.cmd.** Přejděte do složky **webui** v místní kopii úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení příkazu **Start** se v prohlížeči [http://localhost:3000/dashboard](http://localhost:3000/dashboard)zobrazí stránka na adrese . Chyby na této stránce jsou očekávány. Chcete-li zobrazit aplikaci bez chyb, proveďte následující kroky.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění nginxu

Nastavte server reverzního proxy serveru, který propojuje webovou aplikaci s mikroslužbami spuštěným v místním počítači:

1. Zkopírujte soubor **nginx.conf** ze složky **webui\scripts\localhost** v místní kopii úložiště do instalačního adresáře **nginx\conf.**
1. Spusťte Nginx.

Další informace o spuštění nginxu naleznete v [tématu nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Chcete-li získat přístup k http://localhost:9000 řídicímu panelu řešení vzdáleného monitorování, přejděte do prohlížeče.

## <a name="clean-up"></a>Vyčištění

Chcete-li se vyhnout zbytečným poplatkům, odeberte cloudové služby z předplatného Azure po dokončení testování. Chcete-li odebrat služby, přejděte na [portál Azure](https://ms.portal.azure.com)a odstraňte skupinu prostředků, kterou vytvořil skript **start.cmd.**

Můžete také odstranit místní kopii úložiště vzdáleného monitorování, která byla vytvořena při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumání možností řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
