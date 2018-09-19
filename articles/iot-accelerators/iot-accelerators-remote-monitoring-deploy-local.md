---
title: Nasazení řešení vzdáleného monitorování místní – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak do svého místního počítače pro vývoj a testování nasazení akcelerátoru řešení vzdáleného monitorování.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/17/2018
ms.topic: conceptual
ms.openlocfilehash: 966af342937a36adc5932a7a4c92ee127723b4a0
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295729"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Tento přístup nasazuje mikroslužby do místní kontejner Dockeru a v cloudu pomocí služby IoT Hub, Cosmos DB a Azure Time Series Insights.

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) -tento software je předpokladem pro počítače s CLI, skripty použít k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> Tyto nástroje jsou dostupné na spoustě platforem, včetně Windows, Linuxu a iOS.

### <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Vzdálené monitorování úložiště zdrojového kódu GitHub zahrnuje Docker konfigurační soubory, které potřebujete ke stažení, konfiguraci a spuštění imagí Dockeru, které obsahují mikroslužby. Klonovat a vytvořte místní verzi úložiště, použijte prostředí příkazového řádku přejděte do vhodný složky na místním počítači a poté spustíte jeden z následujících příkazů:

Nainstalovat Java implementace mikroslužby, spusťte:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
```

Pokud chcete nainstalovat implementace .net mikroslužeb, spusťte:

```cmd\sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužby. I když není nutné zdrojový kód pro spuštění mikroslužby v Dockeru, zdrojový kód je užitečné, pokud budete později chtít upravit akcelerátor řešení a místní test provedených změn.

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

I když v tomto článku se dozvíte, jak spouštět mikroslužby lokálně, jsou závislé na spouštění v cloudu služby Azure. Tyto služby Azure můžete nasadit [ručně na webu Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), nebo pomocí dodávaného skriptu. Následující příklady skriptu se předpokládá, že používáte úložiště .NET na počítači s Windows. Pokud pracujete s jiným prostředím, cesty, přípony souboru a oddělovače cest odpovídajícím způsobem nastavte. Použití poskytnutých skriptů:

### <a name="create-new-azure-resources"></a>Vytvářet nové prostředky Azure

Pokud jste ještě nevytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. Ve vašem prostředí příkazového řádku, přejděte **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** složky v klonovaném kopii úložiště.

2. Spustit **start.cmd** skriptu a postupujte podle zobrazených výzev. Skript vyzve k zadání následujících informací:
    * Název řešení.
    * Předplatné Azure, které se má použít.
    * Umístění datového centra Azure používat.

    Tento skript vytvoří skupinu prostředků v Azure s názvem řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení.

3. Ve vašem prostředí příkazového řádku, přejděte **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch\os\win** složky v klonovaném kopii úložiště.

4. Spustit **set-env uri.cmd** skriptu.

5. Váš dílčí moduly gitu k Ujistěte se, že máte nejnovější verze aktualizace: `cd <repo-name>` a pak spusťte následující příkaz `git submodule foreach git pull origin master`

> [!NOTE]
> Pokud jste naklonovali úložiště azure-iot-pcs-remote-monitoring-dotnet, složka skripty je k dispozici v rámci služby dílčího modulu (složka). Tento skript může vyžadovat oprávnění správce nebo oprávnění sudo, při pokusu o instalaci [počítače rozhraní příkazového řádku](https://github.com/Azure/pcs-cli).

### <a name="use-existing-azure-resources"></a>Použít existující prostředky Azure

Pokud jste již vytvořili požadované prostředky Azure a stačí, když je aktualizovat, proveďte pouze **jeden** z následujících akcí:

* Globální nastavení proměnných prostředí na svém počítači.
* **VS Code:** úpravou nastavení proměnných prostředí v konfiguraci spuštění **launch.json** souboru.
* **Visual Studio:** nastavit proměnné prostředí pro webovou službu projekt mikroslužby tak, že ji přidáte **vlastnosti > ladění > proměnné prostředí**.

Nakonec aktualizujte váš dílčí moduly gitu k Ujistěte se, že máte nejnovější verze: `cd <repo-name>` a pak spusťte následující příkaz `git submodule foreach git pull origin master`.

Však není doporučena, proměnné prostředí můžete také nastavit v souboru appsettings.ini nachází ve složce webové služby pro jednotlivých mikroslužeb.

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužby v Dockeru

Ke spuštění akcelerátor řešení, přejděte na **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local** složku v prostředí příkazového řádku a spusťte následující příkaz:

```cmd\sh
docker-compose up
```

Při prvním spuštění tohoto příkazu Docker stáhne mikroslužeb Image z Docker hubu vytvářet kontejnery místně. Při dalším spuštění Docker okamžitě spustí kontejnery.

Chcete-li zobrazit protokoly z kontejneru, můžete použít samostatné prostředí. Napřed Zjistěte ID kontejneru pomocí `docker ps -a` příkazu. Pak pomocí `docker logs {container-id} --tail 1000` Chcete-li zobrazit poslední položky protokolu 1000 pro zadaný kontejner.

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:8080 ](http://localhost:8080) v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Chcete-li po dokončení testování se vyhnout zbytečným poplatkům, odeberte cloudových služeb z vašeho předplatného Azure. Nejjednodušší způsob, jak odebrat služby je přejděte [webu Azure portal](https://ms.portal.azure.com) a odstraňte skupinu prostředků, který byl vytvořen při spuštění **start.cmd** skriptu.

Použití `docker-compose down --rmi all` příkazu odeberte Image Dockeru a uvolněte místo na místním počítači. Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavení místního vývojového prostředí
> * Konfigurace akcelerátoru řešení
> * Nasazení akcelerátoru řešení
> * Přihlaste se k akcelerátoru řešení

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
