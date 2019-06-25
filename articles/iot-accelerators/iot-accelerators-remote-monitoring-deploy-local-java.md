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
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015431"
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

## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Úložišť zdrojového kódu vzdáleného monitorování zahrnují zdrojový kód a soubory konfigurace Dockeru, které potřebujete pro spuštění imagí Dockeru mikroslužeb.

Klonovat a vytvořte místní verzi úložiště, použijte prostředí příkazového řádku přejděte do složky na místním počítači vhodný. Poté spustíte jeden z následující sady příkazů naklonujte úložiště java:

Pokud chcete stáhnout nejnovější verzi implementace mikroslužeb java, spusťte:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužby kromě skripty, které používáte ke spouštění mikroslužby lokálně. I když není nutné zdrojový kód pro spuštění mikroslužby v Dockeru, zdrojový kód je užitečné, pokud budete později chtít upravit akcelerátor řešení a místní test provedených změn.

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

I když v tomto článku se dozvíte, jak spouštět mikroslužby lokálně, jsou závislé na spouštění v cloudu služby Azure. Pomocí následujícího skriptu pro nasazení služby Azure. Následující příklady skriptu se předpokládá, že používáte úložiště java na počítači s Windows. Pokud pracujete s jiným prostředím, cesty, přípony souboru a oddělovače cest odpovídajícím způsobem nastavte.

### <a name="create-new-azure-resources"></a>Vytvářet nové prostředky Azure

Pokud jste dosud vytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. Ve vašem prostředí příkazového řádku, přejděte **\services\scripts\local\launch** složky v klonovaném kopii úložiště.

1. Spusťte následující příkazy pro instalaci **počítače** rozhraní příkazového řádku nástroje a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spustit **start.cmd** skriptu. Skript vyzve k zadání následujících informací:
   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datového centra Azure používat.

     Tento skript vytvoří skupinu prostředků v Azure s názvem řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Jakmile už nepotřebujete odpovídající prostředky můžete odstranit tuto skupinu prostředků.

     Skript také přidá sadu proměnných prostředí s předponou **počítače** do místního počítače. Tyto proměnné prostředí zadejte podrobnosti pro vzdálené monitorování bude moct číst z prostředku Azure Key Vault. Tento prostředek Key Vault je vzdálené monitorování bude načteno jeho konfigurační hodnoty z.

     > [!TIP]
     > Po dokončení skriptu, také uloží proměnné prostředí do souboru s názvem  **\<domovskou složku\>\\.pcs\\\<název řešení\>.env** . Můžete je použít pro nasazení akcelerátoru řešení budoucí. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.

1. Výstup z prostředí příkazového řádku.

### <a name="use-existing-azure-resources"></a>Použít existující prostředky Azure

Pokud jste již vytvořili požadované prostředky Azure, vytvořte odpovídající proměnné prostředí v místním počítači.
Nastavení proměnných prostředí pro následující:
* **PCS_KEYVAULT_NAME** – název prostředku Azure Key Vault
* **PCS_AAD_APPID** – ID aplikace AAD
* **PCS_AAD_APPSECRET** -tajný klíč aplikace AAD

Hodnoty konfigurace budou číst z tohoto prostředku Azure Key Vault. Tyto proměnné prostředí mohou být uloženy v  **\<domovskou složku\>\\.pcs\\\<název řešení\>.env** souboru z nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.

Některé konfigurace vyžaduje mikroslužby je uložené v instanci **služby Key Vault** , která byla vytvořena na původním nasazení. Odpovídající proměnné v trezoru klíčů by měl být upraven podle potřeby.

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

Například následující obrázek znázorňuje přidává se konfigurace služby:

[![Přidat konfiguraci](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Vytvoření složeného konfigurace

1. Ke spuštění všech služeb, společně vyberte **přidat novou konfiguraci > složené**
1. Zadejte **název** a **přidat sbt úlohy**
1. Klikněte na tlačítko **použít > Ok** uložit vaše volby.

Například následující obrázek ukazuje, přidává všechny úkoly sbt jediné konfiguraci:

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Klikněte na tlačítko **spustit** sestavení a spuštění webové služby na místním počítači.

Každou webovou službu se otevře okno příkazového řádku a webové prohlížeče. Na příkazovém řádku se zobrazí výstup ze spuštěné služby a okna prohlížeče vám umožní monitorovat stav. Nezavírejte příkazové řádky nebo webové stránky, tato akce zastaví webové služby.


Pro přístup k stav služeb, můžete přejít na následující adresy URL:
* IoT-Hub Manager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetrie zařízení  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Konfigurace [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Adaptér úložiště [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Postupujte podle těchto kroků spustíte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte **skupiny prostředků** vytvořen pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění **start.cmd** skriptu.
1. Klikněte na tlačítko **úlohy Stream Analytics** v seznamu prostředků.
1. V úloze Stream Analytics **přehled** stránky, klikněte na tlačítko **Start** tlačítko. Pak klikněte na tlačítko **Start** ihned spustíte úlohu.

### <a name="run-the-web-ui"></a>Spuštění webového uživatelského rozhraní

V tomto kroku spuštění ve webovém uživatelském rozhraní. Otevřete nové okno příkazového řádku, ujistěte se, že máte přístup k proměnným prostředí nastavil **start.cmd** skriptu. Přejděte **webui** složky ve své místní kopie úložiště a spusťte následující příkazy:

```cmd
npm install
npm start
```

Po dokončení spuštění v prohlížeči zobrazí na stránce **http:\//localhost:3000 / řídicí panel**. Chyby na této stránce se očekává. Chcete-li zobrazit aplikaci bez chyby, proveďte následující kroky.

### <a name="configure-and-run-nginx"></a>Nakonfigurování a spuštění serveru NGINX

Reverzní proxy server nastavte propojení webové aplikace a mikroslužeb spouštěných v místním počítači:

* Kopírování **nginx.conf** ze soubor **webui\scripts\localhost** složky místní kopie úložiště **nginx\conf** instalační adresář.
* Spustit **nginx**.

Další informace o spouštění **nginx**, naleznete v tématu [nginx pro Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Připojte se k řídicímu panelu

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na http:\//localhost:9000 v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Aby se zabránilo zbytečným poplatky, po dokončení testování odebrat cloudovým službám z vašeho předplatného Azure. Pokud chcete odebrat služby, přejděte [webu Azure portal](https://ms.portal.azure.com) a odstranit prostředek skupiny, která **start.cmd** skript vytvořený.

Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
