---
title: Nasazení řešení vzdáleného monitorování místní – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak do svého místního počítače pro vývoj a testování nasazení akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188789"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Tento přístup nasazuje mikroslužby do místní kontejner Dockeru a využívá služby IoT Hub, Cosmos DB a služby Azure storage v cloudu. Použijete akcelerátory řešení (počítačů) rozhraní příkazového řádku pro nasazení Azure cloud services.

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -tento software je předpokladem pro rozhraní příkazového řádku počítače.
* POČÍTAČE S CLI
* Místní úložiště zdrojového kódu

> [!NOTE]
> Tyto nástroje jsou dostupné na spoustě platforem, včetně Windows, Linuxu a iOS.

### <a name="install-the-pcs-cli"></a>Instalace rozhraní příkazového řádku počítače

Pokud chcete nainstalovat rozhraní příkazového řádku počítače přes npm, spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

Další informace o rozhraní příkazového řádku najdete v tématu [jak používat rozhraní příkazového řádku](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

 Vzdálené monitorování úložiště zdrojového kódu obsahuje konfigurační soubory Dockeru, které potřebujete ke stažení, konfiguraci a spuštění imagí Dockeru, které obsahují mikroslužby. Klonovat a vytvořte místní verzi úložiště, přejděte do vhodný složky na místním počítači prostřednictvím oblíbených příkazového řádku nebo terminálu a spusťte následující příkazy:

Nainstalovat Java implementace mikroslužby, spusťte:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Pokud chcete nainstalovat implementace .net mikroslužeb, spusťte:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Vzdálené úložiště Monioring předkonfigurované řešení & dílčí moduly [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužby. I když není nutné zdrojový kód pro spuštění mikroslužby v Dockeru, zdrojový kód je užitečné, pokud budete později chtít upravit toto předkonfigurované řešení a místní test provedených změn.

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

I když v tomto článku se dozvíte, jak spouštět mikroslužby lokálně, jsou závislé na tři služby Azure v cloudu. Tyto služby Azure můžete nasadit [ručně na webu Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), nebo pomocí rozhraní příkazového řádku počítače. V tomto článku se dozvíte, jak používat `pcs` nástroj.

### <a name="sign-in-to-the-cli"></a>Přihlaste se k rozhraní příkazového řádku

Před nasazením akcelerátor řešení, musíte se přihlásit ke svému předplatnému Azure pomocí rozhraní příkazového řádku takto:

```cmd/sh
pcs login
```

Použijte na obrazovce pokynů a dokončete proces přihlašování. Ujistěte se, že není kliknete na libovolné místo v rámci rozhraní příkazového řádku nebo přihlášení může selhat. Zobrazí se zpráva úspěšného přihlášení v rozhraní příkazového řádku a pokud jste dokončili přihlášení. 

### <a name="run-a-local-deployment"></a>Spusťte místní nasazení

Použijte následující příkaz pro spuštění místního nasazení. To vytvoří požadované prostředky azure a vytiskne proměnné prostředí do konzoly. 

```cmd/pcs
pcs -s local
```

Skript vyzve k zadání následujících informací:

* Název řešení.
* Předplatné Azure, které se má použít.
* Umístění datového centra Azure používat.

> [!NOTE]
> Skript vytvoří instanci, instance služby Cosmos DB a účet úložiště Azure ve skupině prostředků ve vašem předplatném Azure, služby IoT Hub. Název skupiny prostředků je název řešení, které jste zvolili, když jste spustili `pcs` nástroj výše. 

> [!IMPORTANT]
> Skript trvá několik minut. Po dokončení se zobrazí zpráva `Copy the following environment variables to /scripts/local/.env file:`. Kopírování prostředí definice proměnných s následující zprávou, použijeme je v pozdějším kroku.

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužby v Dockeru

Chcete-li spustit mikroslužby v Dockeru, nejprve upravte **skripty\\místní\\.env** souboru v místní kopii úložiště, které jste naklonovali v dřívějším kroku výše. Celý obsah souboru nahraďte definice proměnných prostředí jste si poznamenali při spuštění `pcs` příkaz v předchozím kroku. Tyto proměnné prostředí povolit mikroslužby v kontejneru Dockeru pro připojení ke službám Azure, které jsou vytvořené `pcs` nástroj.

Ke spuštění akcelerátor řešení, přejděte na **scripts\local** složku v prostředí příkazového řádku a spusťte následující příkaz:

```cmd\sh
docker-compose up
```

Při prvním spuštění tohoto příkazu Docker stáhne mikroslužeb Image z Docker hubu vytvářet kontejnery místně. Při dalším spuštění Docker okamžitě spustí kontejnery.

Chcete-li zobrazit protokoly z kontejneru, můžete použít samostatné prostředí. Napřed Zjistěte ID kontejneru pomocí `docker ps -a` příkazu. Pak pomocí `docker logs {container-id} --tail 1000` Chcete-li zobrazit poslední položky protokolu 1000 pro zadaný kontejner.

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:8080 ](http://localhost:8080) v prohlížeči.

## <a name="clean-up"></a>Vyčištění

Chcete-li po dokončení testování se vyhnout zbytečným poplatkům, odeberte cloudových služeb z vašeho předplatného Azure. Nejjednodušší způsob, jak odebrat služby je přejděte [webu Azure portal](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou jste vytvořili pomocí `pcs` nástroj.

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