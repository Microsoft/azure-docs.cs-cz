---
title: Nasazení řešení vzdáleného monitorování místně - Dockeru – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak do svého místního počítače pomocí Dockeru pro vývoj a testování nasazení akcelerátoru řešení vzdáleného monitorování.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 208526b745a117c9ee14bab21f8a5ce05accd1fe
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382229"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Nasazení akcelerátoru řešení vzdáleného monitorování místně - Dockeru

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Tento článek ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování do místního počítače pro vývoj a testování. Zjistíte, jak nasadit mikroslužby do místní kontejnery Dockeru. Nasazení místního mikroslužeb používá tyto cloudové služby: Služby IoT Hub, Cosmos DB, Azure Stream Analytics a Azure Time Series Insights v cloudu.

Pokud chcete spustit akcelerátor řešení vzdálené monitorování v rozhraní IDE v místním počítači, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování místně – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Požadavky

K nasazení služby Azure používá akcelerátor řešení vzdálené monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Instalace počítače

K dokončení místní nasazení, budete potřebovat následující nástroje nainstalované na místním vývojovém počítači:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – Pokud máte v plánu provést změny na mikroslužby.
* [Node.js v8](https://nodejs.org/) -tento software je předpokladem pro počítače s CLI, skripty použít k vytvoření prostředků Azure. Nepoužívejte Node.js v10.

> [!NOTE]
> Tyto nástroje jsou dostupné na spoustě platforem, včetně Windows, Linuxu a iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Spuštění mikroslužby v Dockeru

Otevřete nový příkazový řádek opravdu přístup k nastavením proměnné prostředí **start.cmd** skriptu. Na Windows můžete ověřit, že proměnné prostředí se nastavují spuštěním následujícího příkazu:

```cmd
set PCS
```

Příkaz zobrazí všechny proměnné prostředí nastavuje **start.cmd** skriptu.

Ujistěte se, že Dockeru běží na místním počítači.

Mikroslužeb spouštěných v kontejnerech Dockeru místní potřebují přístup k Azure cloud services. Můžete otestovat připojení k Internetu pomocí následujícího příkazu ping internetovou adresu z uvnitř kontejneru Docker prostředí:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Ke spuštění akcelerátor řešení, přejděte na **služby\\skripty\\místní** složku v prostředí příkazového řádku a spusťte následující příkaz:

```cmd/sh
docker-compose up
```

Při prvním spuštění tohoto příkazu Docker stáhne mikroslužeb Image z Docker hubu vytvářet kontejnery místně. V následujícím spuštění Docker spouští kontejnery okamžitě.

> [!TIP]
> Microsoft často vydává nové Image Dockeru s novými funkcemi. Následující sady příkazů k vyčištění můžete použít místní kontejnery Docker a odpovídající Image před o přijetí změn nejnovější těm, které jsou:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Chcete-li zobrazit protokoly z kontejneru, můžete použít samostatné prostředí. Napřed Zjistěte ID kontejneru pomocí `docker ps` příkazu. Pak pomocí `docker logs {container-id} --tail 1000` k zobrazení posledních 1000 položek u zadaného kontejneru.

### <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

Postupujte podle těchto kroků spustíte úlohu Stream Analytics:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte **skupiny prostředků** vytvořen pro vaše řešení. Název skupiny prostředků je název, který jste zvolili pro vaše řešení při spuštění **start.cmd** skriptu.
1. Klikněte na **úlohy Stream Analytics** v seznamu prostředků.
1. V úloze Stream Analytics **přehled** stránky, klikněte na tlačítko **Start** tlačítko. Pak klikněte na tlačítko **Start** ihned spustíte úlohu.

### <a name="connect-to-the-dashboard"></a>Připojte se k řídicímu panelu

Chcete-li získat přístup k řídicím panelu řešení vzdáleného monitorování, přejděte na [ http://localhost:8080 ](http://localhost:8080) v prohlížeči. Teď můžete použít místní mikroslužeb a webového uživatelského rozhraní.

## <a name="clean-up"></a>Vyčištění

Aby se zabránilo zbytečným poplatky, po dokončení testování odebrat cloudovým službám z vašeho předplatného Azure. Pokud chcete odebrat služby, přejděte [webu Azure portal](https://ms.portal.azure.com) a odstranit prostředek skupiny, která **start.cmd** skript vytvořený.

Použití `docker-compose down --rmi all` příkazu odeberte Image Dockeru a uvolněte místo na místním počítači. Můžete také odstranit místní kopie úložiště vzdálené monitorování vytvoří, když jste naklonovali zdrojový kód z Githubu.

## <a name="next-steps"></a>Další postup

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](quickstart-remote-monitoring-deploy.md).
