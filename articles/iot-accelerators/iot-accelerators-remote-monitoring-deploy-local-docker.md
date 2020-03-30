---
title: Nasazení řešení vzdáleného monitorování místně – Docker – Azure | Dokumenty společnosti Microsoft
description: Tento návod vám ukáže, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí Dockeru pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888839"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně – Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Dozvíte se, jak nasadit mikroslužeb do místních kontejnerů Dockeru. Místní nasazení mikroslužeb používá v cloudu následující cloudové služby: IoT Hub, Cosmos DB, Azure Streaming Analytics a Azure Time Series Insights.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v prostředí IDE v místním počítači, přečtěte si informace [o místním nasazení akcelerátoru řešení vzdáleného monitorování – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služeb Azure používaných akcelerátorem řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení stroje

K dokončení místního nasazení potřebujete v místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - pokud máte v plánu provést změny mikroslužeb.
* [Node.js v8](https://nodejs.org/) – tento software je předpokladem pro nastavení příkazu k onomu systému PCS, který skripty používají k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> Tyto nástroje jsou k dispozici na mnoha platformách, včetně Windows, Linux a iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužeb v Dockeru

Otevřete nový příkazový řádek, abyste měli přístup k proměnným prostředí nastaveným skriptem **start.cmd.** V systému Windows můžete ověřit, že proměnné prostředí jsou nastaveny spuštěním následujícího příkazu:

```cmd
set PCS
```

Příkaz zobrazuje všechny proměnné prostředí nastavené skriptem **start.cmd.**

Ujistěte se, že Docker běží na místním počítači.
> [!NOTE]
> Docker musí být spuštěn [linuxové kontejnery,](https://docs.docker.com/docker-for-windows/) pokud je spuštěn v systému Windows.

Mikroslužby spuštěné v místních kontejnerech Dockeru potřebují přístup ke cloudovým službám Azure. Připojení k internetu prostředí Dockeru můžete otestovat pomocí následujícího příkazu ping na internetovou adresu z kontejneru:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Chcete-li spustit akcelerátor řešení, přejděte do místní složky **skriptů\\\\služeb** v prostředí příkazového řádku a spusťte následující příkaz:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Ujistěte se, že sdílíte místní `docker-compose up`disk s [Dockerem](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) před spuštěním .

Při prvním spuštění tohoto příkazu Docker stáhne imitace mikroslužeb z centra Dockeru a místně vytvoří kontejnery. Při následujících spuštěních Docker spustí kontejnery okamžitě.

> [!TIP]
> Společnost Microsoft často publikuje nové image Dockeru s novými funkcemi. Před vytažením nejnovějších příkazů můžete použít následující sadu příkazů k vyčištění místních kontejnerů Dockeru a odpovídajících bitových kopií:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Můžete použít samostatné prostředí pro zobrazení protokolů z kontejneru. Nejprve vyhledejte ID `docker ps` kontejneru pomocí příkazu. Potom `docker logs {container-id} --tail 1000` slouží k zobrazení posledních 1000 položek pro zadaný kontejner.

### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Chcete-li spustit úlohu Stream Analytics, postupujte takto:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **skupiny prostředků** vytvořené pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro řešení při spuštění skriptu **start.cmd.**
1. Klikněte na **úlohu Stream Analytics** v seznamu zdrojů.
1. Na stránce **Přehled** úlohy Stream Analytics klikněte na tlačítko **Start.** Potom kliknutím na **tlačítko Start** spusťte úlohu nyní.

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Chcete-li získat přístup k `http://localhost:8080` řídicímu panelu řešení vzdáleného monitorování, přejděte do prohlížeče. Nyní můžete použít webové uživatelské prostředí a místní mikroslužby.

## <a name="clean-up"></a>Vyčištění

Chcete-li se vyhnout zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte na [portál Azure](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **start.cmd.**

Pomocí `docker-compose down --rmi all` příkazu odeberte image Dockeru a uvolněte místo v místním počítači. Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořeného při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumání možností řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
