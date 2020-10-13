---
title: Nasazení řešení vzdáleného monitorování místně – IntelliJ IDE – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí IntelliJ pro testování a vývoj.
author: v-krghan
manager: dominicbetts
ms.custom: devx-track-java
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: f7554843db247ade1cddff78902430a5d84debe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319163"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

V tomto článku se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Naučíte se, jak spustit mikroslužby v IntelliJ. Místní nasazení mikroslužeb bude používat tyto cloudové služby: IoT Hub, Azure Cosmos DB, Azure streaming Analytics a Azure Time Series Insights.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v Docker na místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nasadit služby Azure používané akcelerátorem řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení počítače

K dokončení místního nasazení potřebujete na svém místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Edice IntelliJ Community](https://www.jetbrains.com/idea/download/)
* [Modul plug-in IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Modul plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Modul plug-in prováděcího modulu SBT IntelliJ](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/)

Node.js V8 je předpokladem pro počítače CLI, které skripty používají k vytváření prostředků Azure. Nepoužívejte Node.js v10 za účelem.

> [!NOTE]
> IntelliJ IDE je k dispozici pro Windows a Mac.

## <a name="download-the-source-code"></a>Stažení zdrojového kódu

Mezi úložiště zdrojového kódu vzdáleného monitorování patří zdrojový kód a konfigurační soubory Docker, které potřebujete ke spuštění imagí Docker mikroslužeb.

Pokud chcete klonovat a vytvořit místní verzi úložiště, použijte prostředí příkazového řádku pro přechod do vhodné složky na místním počítači. Pak spuštěním jedné z následujících sad příkazů naklonujte úložiště Java:

* Chcete-li stáhnout nejnovější verzi mikroslužeb Java, spusťte následující příkaz:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Chcete-li načíst nejnovější dílčí moduly, spusťte následující příkazy:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Tyto příkazy stáhnou zdrojový kód pro všechny mikroslužby Kromě skriptů používaných k místnímu spouštění mikroslužeb. Zdrojový kód nepotřebujete ke spouštění mikroslužeb v Docker. Zdrojový kód je ale užitečný, pokud později plánujete změnit akcelerátor řešení a testovat změny místně.

## <a name="deploy-the-azure-services"></a>Nasazení služeb Azure

I když v tomto článku se dozvíte, jak místně spustit mikroslužby, závisí na službách Azure spuštěných v cloudu. Pomocí následujícího skriptu nasaďte služby Azure. V příkladech skriptu se předpokládá, že používáte úložiště Java v počítači s Windows. Pokud pracujete v jiném prostředí, upravte správně cesty, přípony souborů a oddělovače cest.

### <a name="create-new-azure-resources"></a>Vytvoření nových prostředků Azure

Pokud jste ještě nevytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. V prostředí příkazového řádku přejdete do složky **\services\scripts\local\launch** v klonované kopii úložiště.

1. Spuštěním následujících příkazů nainstalujte nástroj pro **počítače** CLI a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spusťte skript **Start. cmd** . Skript vás vyzve k zadání následujících informací:

   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datacentra Azure, které se má použít.

   Skript vytvoří v Azure skupinu prostředků, která má název vašeho řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Tuto skupinu prostředků můžete odstranit, i když už nepotřebujete odpovídající prostředky.

   Skript také přidá do místního počítače sadu proměnných prostředí. Každý název proměnné má předpony **počítačů**. Tyto proměnné prostředí poskytují podrobné informace, které umožňují vzdálené monitorování číst hodnoty konfigurace z prostředku Azure Key Vault.

   > [!TIP]
   > Po dokončení skriptu uloží proměnné prostředí do souboru s názvem ** \<your home folder\> \\ . PC \\ \<solution name\> . env**. Můžete je použít pro budoucí nasazení akcelerátoru řešení. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepíší hodnoty **v \\ souboru \\ \\ ** **docker-compose**

1. Zavřete prostředí příkazového řádku.

### <a name="use-existing-azure-resources"></a>Použití existujících prostředků Azure

Pokud jste již vytvořili požadované prostředky Azure, nastavte odpovídající proměnné prostředí v místním počítači:
* **PCS_KEYVAULT_NAME**: název prostředku Key Vault.
* **PCS_AAD_APPID**: ID aplikace Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: tajný klíč aplikace služby Azure AD.

Z tohoto prostředku Key Vault budou načteny konfigurační hodnoty. Tyto proměnné prostředí se dají uložit do souboru ** \<your home folder\> \\ . PC \\ \<solution name\> . env** z nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepíší hodnoty v souboru ** \\ \\ \\ . env skriptu služby** , když spustíte **Docker-Dock**.

Některá z konfigurací, které potřebuje mikroslužba, je uložená v instanci Key Vault, která byla vytvořena při počátečním nasazení. Odpovídající proměnné v trezoru klíčů by měly být podle potřeby upraveny.

## <a name="run-the-microservices"></a>Spustit mikroslužby

V této části spustíte mikroslužby vzdáleného monitorování. Spustíte:

* Webové uživatelské rozhraní nativně.
* Služby pro simulaci, ověřování a Azure Stream Analytics Správce zařízení Azure IoT v Docker.
* Mikroslužby v IntelliJ.

### <a name="run-the-device-simulation-service"></a>Spuštění služby simulace zařízení

Otevřete nové okno příkazového řádku. Ověřte, zda máte přístup k proměnným prostředí nastaveným skriptem **Start. cmd** v předchozí části.

Spuštěním následujícího příkazu otevřete kontejner Docker pro službu pro simulaci zařízení. Služba simuluje zařízení pro řešení vzdáleného monitorování.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Spustit ověřovací službu

Otevřete nové okno příkazového řádku a spusťte následující příkaz, kterým otevřete kontejner Docker pro ověřovací službu. Pomocí této služby můžete spravovat uživatele, kteří mají oprávnění pro přístup k řešením Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Spuštění služby Stream Analytics Manager

Otevřete nové okno příkazového řádku a spusťte následující příkaz, kterým otevřete kontejner Docker pro službu Stream Analytics Manager. Pomocí této služby můžete spravovat Stream Analytics úlohy. Taková Správa zahrnuje nastavení úlohy konfigurace a spouštění, zastavování a monitorování stavu úlohy.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Nasazení všech ostatních mikroslužeb na místním počítači

Následující kroky ukazují, jak spustit mikroslužby vzdáleného monitorování v IntelliJ.

#### <a name="import-a-project"></a>Importovat projekt

1. Otevřete integrované vývojové prostředí (IntelliJ).
1. Vyberte **Importovat projekt**.
1. Vyberte **Azure-IoT-PCs-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Vytvoření konfigurací spuštění

1. Vyberte **Spustit**  >  **úpravy konfigurace**.
1. Vyberte **Přidat nový konfigurační**  >  **SBT úlohu**.
1. Zadejte **název**a potom zadejte **úlohy** jako **Spustit**.
1. V závislosti na službě, kterou chcete spustit, vyberte **pracovní adresář** .
1. Výběrem možnosti **použít**  >  **OK** uložte své volby.
1. Vytvořit konfigurace spouštění pro následující webové služby:
    * WebService (services\config)
    * WebService (services\device-Telemetry)
    * WebService (services\iothub-Manager)
    * WebService (services\storage-Adapter)

Například následující obrázek ukazuje, jak přidat konfiguraci pro službu:

[![Snímek obrazovky okna konfigurace spuštění nebo ladění IntelliJ IDE zobrazující možnost storageAdapter zvýrazněnou v seznamu úlohy SBT v levém podokně a položky v polích název, úkoly, pracovní adresář a parametry virtuálního počítače v pravém podokně.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Vytvoření složené konfigurace

1. Chcete-li spustit všechny služby dohromady, vyberte možnost **Přidat novou konfiguraci**  >  **Compound**.
1. Zadejte **název**a pak vyberte **Přidat úlohy SBT**.
1. Výběrem možnosti **použít**  >  **OK** uložte své volby.

Například následující obrázek ukazuje, jak přidat všechny úlohy SBT do jedné konfigurace:

[![Snímek obrazovky okna konfigurace spuštění nebo ladění IntelliJ IDE zobrazující možnost AllServices zvýrazněnou v složeném seznamu v levém podokně a možnost deviceTelemetry úlohy SBT, která je v pravém podokně zvýrazněna.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Vyberte možnost **Spustit** a sestavte a spusťte webové služby na místním počítači.

Každá webová služba otevře okno příkazového řádku a okno webového prohlížeče. Na příkazovém řádku vidíte výstup z běžící služby. Okno prohlížeče vám umožní monitorovat stav. Nezavírejte okno příkazového řádku Windows ani webové stránky, protože tyto akce zastaví webovou službu.

Chcete-li získat přístup ke stavu služeb, přejděte na následující adresy URL:

* Správce IoT-Hub: `http://localhost:9002/v1/status`
* Telemetrie zařízení: `http://localhost:9004/v1/status`
* Konfigurace `http://localhost:9005/v1/status`
* adaptér úložiště: `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

Pomocí následujících kroků spusťte úlohu Stream Analytics:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
1. Přejít na **skupinu prostředků** vytvořenou pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění skriptu **Start. cmd** .
1. V seznamu prostředků vyberte **úlohu Stream Analytics** .
1. Na stránce **Přehled** úlohy Stream Analytics vyberte tlačítko **Start** a potom výběrem tlačítka **Spustit** spusťte úlohu.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spustíte webové uživatelské rozhraní. Otevřete nové okno příkazového řádku. Ověřte, zda máte přístup k proměnným prostředí nastaveným pomocí skriptu **Start. cmd** . V místní kopii úložiště otevřete složku **WebUI** a pak spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení příkazu **Start** se v prohlížeči zobrazí stránka na adrese `http://localhost:3000/dashboard` . Očekává se chyba na této stránce. Chcete-li zobrazit aplikaci bez chyb, proveďte následující kroky.

### <a name="configure-and-run-nginx"></a>Konfigurace a spuštění Nginx

Nastavte reverzní proxy server, která propojí webovou aplikaci s mikroslužbami běžícími na vašem místním počítači:

1. Zkopírujte soubor **Nginx. conf** ze složky **webui\scripts\localhost** v místní kopii úložiště do instalačního adresáře **nginx\conf** .
1. Spusťte Nginx.

Další informace o spuštění Nginx najdete v tématu [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Pokud chcete získat přístup k řídicímu panelu řešení vzdáleného monitorování, přejděte do části `http://localhost:9000` v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Abyste se vyhnuli zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, otevřete [Azure Portal](https://ms.portal.azure.com)a odstraňte skupinu prostředků, kterou vytvořil skript **Start. cmd** .

Můžete také odstranit místní kopii úložiště vzdáleného monitorování, která byla vytvořena při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
