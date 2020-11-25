---
title: Nasazení řešení vzdáleného monitorování místně Docker – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pomocí Docker pro testování a vývoj.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020231"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Nasaďte místně Docker akcelerátor řešení vzdáleného monitorování

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

V tomto článku se dozvíte, jak nasadit akcelerátor řešení vzdáleného monitorování do místního počítače pro testování a vývoj. Naučíte se, jak nasazovat mikroslužby do místních kontejnerů Docker. Místní nasazení mikroslužeb používá následující cloudové služby: IoT Hub, Cosmos DB, Azure streaming Analytics a služby Azure Time Series Insights v cloudu.

Pokud chcete spustit akcelerátor řešení vzdáleného monitorování v integrovaném vývojovém prostředí (IDE) na místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nasadit služby Azure používané akcelerátorem řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Nastavení počítače

K dokončení místního nasazení potřebujete na svém místním vývojovém počítači nainstalované následující nástroje:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – Pokud plánujete provádět změny mikroslužeb.
* [Node.js V8](https://nodejs.org/) – tento software je předpokladem pro počítače CLI, které skripty používají k vytváření prostředků Azure. Nepoužívejte Node.js v10 za účelem.

> [!NOTE]
> Tyto nástroje jsou k dispozici na mnoha platformách, včetně systémů Windows, Linux a iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužeb v Docker

Otevřete nový příkazový řádek a ujistěte se, že máte přístup k proměnným prostředí nastaveným pomocí skriptu **Start. cmd** . V systému Windows můžete ověřit, zda jsou proměnné prostředí nastaveny spuštěním následujícího příkazu:

```cmd
set PCS
```

Příkaz zobrazí všechny proměnné prostředí nastavené skriptem **Start. cmd** .

Ujistěte se, že je na vašem místním počítači spuštěný Docker.
> [!NOTE]
> Docker musí mít spuštěné [kontejnery Linux](https://docs.docker.com/docker-for-windows/) , pokud je spuštěný ve Windows.

Mikroslužby běžící v místních kontejnerech Docker potřebují přístup ke cloudovým službám Azure. K otestování připojení k Internetu prostředí Docker můžete použít následující příkaz k otestování internetové adresy z kontejneru v rámci služby:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Chcete-li spustit akcelerátor řešení, přejděte do **\\ \\ místní složky skripty služby** v prostředí příkazového řádku a spusťte následující příkaz:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Před spuštěním se ujistěte, že jste [nasdíleli místní jednotku](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) s Docker `docker-compose up` .

Při prvním spuštění tohoto příkazu Docker stáhne image mikroslužeb z dokovacího centra a vytvoří kontejnery místně. V následujících spuštěních Docker spouští kontejnery hned.

> [!TIP]
> Microsoft často zveřejňuje nové image Docker s novými funkcemi. Následující sadu příkazů můžete použít k vyčištění místních kontejnerů Docker a odpovídajících imagí předtím, než budete moci načíst nejnovější položky:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

K zobrazení protokolů z kontejneru můžete použít samostatné prostředí. Nejdřív Najděte ID kontejneru pomocí `docker ps` příkazu. Pak použijte `docker logs {container-id} --tail 1000` k zobrazení posledních 1000 záznamů pro zadaný kontejner.

### <a name="start-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

Pomocí následujících kroků spusťte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte do **skupiny prostředků** vytvořené pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění skriptu **Start. cmd** .
1. V seznamu prostředků klikněte na **úlohu Stream Analytics** .
1. Na stránce **Přehled** úlohy Stream Analytics klikněte na tlačítko **Start** . Potom kliknutím na **Spustit** spusťte úlohu hned teď.

### <a name="connect-to-the-dashboard"></a>Připojení k řídicímu panelu

Přístup k řídicímu panelu řešení vzdáleného monitorování získáte tak, že v prohlížeči přejdete na `http://localhost:8080` . Teď můžete použít webové uživatelské rozhraní a místní mikroslužby.

## <a name="clean-up"></a>Vyčištění

Aby nedocházelo k zbytečným poplatkům, po dokončení testování odeberte cloudové služby z předplatného Azure. Chcete-li odebrat služby, přejděte do [Azure Portal](https://ms.portal.azure.com) a odstraňte skupinu prostředků, kterou vytvořil skript **Start. cmd** .

Pomocí `docker-compose down --rmi all` příkazu odeberte image Docker a uvolněte místo na svém místním počítači. Můžete také odstranit místní kopii úložiště vzdáleného monitorování vytvořenou při klonování zdrojového kódu z GitHubu.

## <a name="next-steps"></a>Další kroky

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
