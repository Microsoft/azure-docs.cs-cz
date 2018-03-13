---
title: "Nasazení řešení vzdáleného monitorování místně - Azure | Microsoft Docs"
description: "V tomto kurzu se dozvíte, jak nasadit předkonfigurované řešení vzdáleného monitorování do místního počítače pro vývoj a testování."
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Nasazení předkonfigurovaného řešení vzdáleného monitorování místně

Tento článek ukazuje, jak nasadit předkonfigurované řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Tento přístup na místní kontejner Docker nasadí mikroslužeb a používá IoT Hub, Cosmos DB a služby Azure storage v cloudu. Použijete předkonfigurovaná řešení (počítače) rozhraní příkazového řádku pro nasazení Azure cloud services.

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá předkonfigurovaného řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

K dokončení místní nasazení, potřebujete následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -tento software je předpokladem pro rozhraní příkazového řádku počítače.
* ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU POČÍTAČE

> [!NOTE]
> Tyto nástroje jsou k dispozici na mnoha platformách, včetně systému Windows, Linux a iOS.

### <a name="install-the-pcs-cli"></a>Instalace rozhraní příkazového řádku počítače

Pokud chcete nainstalovat rozhraní příkazového řádku, spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

Další informace o rozhraní příkazového řádku najdete v tématu [použití rozhraní příkazového řádku](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

Přestože tento článek ukazuje, jak spustit místně mikroslužeb, jsou závislé na tři služby Azure běží v cloudu. Můžete nasadit tyto služby Azure ručně přes portál Azure nebo pomocí rozhraní příkazového řádku počítače. V tomto článku se dozvíte, jak používat `pcs` nástroj.

### <a name="sign-in-to-the-cli"></a>Přihlaste se k rozhraní příkazového řádku

Než bude možné nasadit předkonfigurované řešení, musíte se přihlásit k vašemu předplatnému Azure pomocí rozhraní příkazového řádku takto:

```cmd/sh
pcs login
```

Postupujte podle na obrazovce pokyny k dokončení procesu přihlášení.

### <a name="run-a-local-deployment"></a>Spuštění místní nasazení

Ke spuštění místní nasazení použijte následující příkaz:

```cmd/pcs
pcs -s local
```

Skript vyzve k zadání následujících informací:

* Název řešení.
* Předplatné Azure, které se má použít.
* Umístění datové centrum Azure používat.

Skript vytvoří služby IoT Hub instance, Cosmos DB instanci a účet úložiště Azure ve skupině prostředků ve vašem předplatném Azure. Název skupiny prostředků je název řešení, které jste zvolili, když jste spustili `pcs` nástroj.

Skript trvá několik minut. Po dokončení, zobrazí se zpráva `Copy the following environment variables to /scripts/local/.env file:`. Zkopírujte definice proměnné prostředí následující zprávu, použijte v pozdější fázi.

## <a name="download-the-source-code"></a>Stáhnout zdrojový kód

Vzdálené monitorování úložiště zdrojového kódu obsahuje Docker konfigurační soubory, které potřebujete ke stažení, konfiguraci a spuštění imagí Dockeru, které obsahují mikroslužeb. Naklonujte úložiště, přejděte do vhodný složky na místním počítači a spusťte jeden z následujících příkazů:

Chcete-li nainstalovat Java implementace mikroslužeb, spusťte:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Chcete-li nainstalovat rozhraní .net implementace mikroslužeb, spusťte:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužeb. I když nepotřebujete zdrojový kód pro spuštění mikroslužeb v Docker, zdrojový kód je užitečné, pokud budete později chtít upravit předkonfigurované řešení a otestovat změny místně.

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužeb v Docker

Pokud chcete spustit mikroslužeb v Docker, nejprve upravit **skripty\\místní\\.env** soubor ve vaší místní kopii úložiště. Celý obsah souboru nahraďte definice proměnné prostředí proveden poznamenejte si jste spustili `pcs` příkaz dříve. Tyto proměnné prostředí povolit mikroslužeb v kontejner Docker připojit ke službám Azure vytvořené `pcs` nástroj.

Chcete-li spustit předkonfigurované řešení, přejděte na **scripts\local** složky ve vašem prostředí příkazového řádku a spusťte následující příkaz:

```cmd\sh
docker-compose up
```

Při prvním spuštění tohoto příkazu Docker stáhne Image mikroslužbu úložiště Docker hub k vytvoření kontejnerů místně. Při dalším spuštění Docker okamžitě spustí kontejnery.

Samostatné prostředí můžete použít k zobrazení protokolů z kontejneru. Nejprve najít pomocí ID kontejneru `docker ps -a` příkaz. Potom pomocí `docker logs {container-id} --tail 1000` k zobrazení poslední položky protokolu 1000 pro zadaný kontejner.

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [adrese http://localhost: 8080](http://localhost:8080) v prohlížeči.

## <a name="tidy-up"></a>Přehledné

Aby se zabránilo zbytečným poplatky, po dokončení testování, odeberte ze svého předplatného Azure cloudové služby. Nejjednodušší způsob, jak odebrat služby je používat portál Azure k odstranění skupiny prostředků vytvořené `pcs` nástroj.

Použití `docker-compose down --rmi all` příkazu odeberte imagí Dockeru a uvolněte místo na místním počítači. Můžete také odstranit místní kopii vzdáleného monitorování úložiště vytvoří, když jste naklonovali zdrojového kódu z Githubu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace předkonfigurované řešení
> * Nasadit předkonfigurované řešení
> * Přihlaste se k předkonfigurované řešení

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumejte možnosti řídicí panel řešení](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->