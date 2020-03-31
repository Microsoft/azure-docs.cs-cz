---
title: Nasazení vlastní bitové kopie simulace zařízení – Azure| Dokumenty společnosti Microsoft
description: V tomto návodu se dozvíte, jak nasadit vlastní image Dockeru řešení Simulace zařízení do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448391"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Nasazení vlastní bitové kopie dockeru simulace zařízení

Můžete upravit řešení Simulace zařízení a přidat vlastní funkce. Například [Serializovat telemetrická data pomocí vyrovnávacípaměti protokolu](iot-accelerators-device-simulation-protobuf.md) článek ukazuje, jak přidat vlastní zařízení do řešení, které používá vyrovnávací paměti protokolu (Protobuf) k odesílání telemetrie. Po otestování změn místně je dalším krokem nasazení změn do instance Simulace zařízení v Azure. Chcete-li dokončit tento úkol, je třeba vytvořit a nasadit image Dockeru, který obsahuje upravenou službu.

Kroky v tomto návodu vám ukážou, jak:

1. Příprava vývojového prostředí
1. Generovat novou image Dockeru
1. Konfigurace simulace zařízení pro použití nové bitové kopie Dockeru
1. Spuštění simulace pomocí nového obrázku

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete:

* Nasaditá instance [Simulace zařízení.](quickstart-device-simulation-deploy.md)
* Dockeru, co se má. Stáhněte si [verzi Docker Community Edition](https://www.docker.com/products/docker-engine#/download) pro vaši platformu.
* [Účet Docker Hub,](https://hub.docker.com/) kde můžete nahrát image Dockeru. Ve svém účtu Docker Hub vytvořte veřejné úložiště nazývané **simulace zařízení**.
* Upravené a testované [řešení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) v místním počítači. Můžete například upravit řešení [serializovat telemetrii pomocí vyrovnávacích pamětí protokolu](iot-accelerators-device-simulation-protobuf.md).
* Shell, který může spustit SSH. Pokud nainstalujete Git pro Windows, můžete použít **bash** shell, který je součástí instalace th. Můžete také použít azure [cloud shell](https://shell.azure.com/).

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, bude pravděpodobně nutné upravit některé cesty a příkazy souborů tak, aby vyhovovaly vašemu prostředí.

## <a name="create-a-new-docker-image"></a>Vytvoření nové image Dockeru

Chcete-li nasadit vlastní změny do služby Simulace zařízení, je třeba upravit skripty sestavení a nasazení ve složce **Scripts\docker** a nahrát kontejnery do účtu docker-hub

### <a name="modify-the-docker-scripts"></a>Úprava skriptů dockeru

Upravte skripty **Docker build.cmd**, **publish.cmd**a **skripty run.cmd** ve složce **scripts\docker** s informacemi o úložišti Docker Hub. Tyto kroky předpokládají, že jste vytvořili veřejné úložiště nazývané **simulace zařízení**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aktualizujte soubor **docker-compose.yml** takto:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Nakonfigurujte řešení tak, aby zahrnovalo všechny nové soubory

Pokud jste přidali nové soubory modelu zařízení, je třeba explicitně zahrnout do řešení. Přidejte položku do **services/services.csproj** pro každý další soubor, který chcete zahrnout. Pokud jste například [dokončili serializovat telemetrii pomocí návodu](iot-accelerators-device-simulation-protobuf.md) na vyrovnávací paměti protokolu, přidejte následující položky:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generování nových iobrazů Dockeru a nabízení do centra Dockeru

Publikujte novou image Dockeru do Docker Hubu pomocí svého účtu docker-hub:

1. Otevřete příkazový řádek a přejděte na místní kopii úložiště simulace zařízení.

1. Přejděte do složky **dockeru:**

    ```cmd
    cd scripts\docker
    ```

1. Chcete-li vytvořit bitovou kopii Dockeru, spusťte následující příkaz:

    ```cmd
    build.cmd
    ```

1. Spusťte následující příkaz a publikujte image Dockeru do úložiště Docker Hubu. Přihlaste se k Dockeru pomocí přihlašovacích údajů dockerhubu:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizace služby

Chcete-li aktualizovat kontejner Simulace zařízení tak, aby používal vlastní bitovou kopii, proveďte následující kroky:

* Pomocí SSH se můžete připojit k virtuálnímu počítači, který hostuje instanci Simulace zařízení. Použijte IP adresu a heslo, které jste si poznamenali v předchozí části:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Přejděte do adresáře **/app:**

    ```sh
    cd /app
    ```

* Upravte soubor **docker-compose.yml:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Upravte **bitovou kopii** tak, aby ukazovala na vlastní **bitovou kopii simulace zařízení,** kterou jste nahráli do úložiště Docker Hubu:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Uložte provedené změny.

* Chcete-li mikroslužby restartovat, spusťte následující příkaz:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Spusťte simulaci

Nyní můžete spustit simulaci pomocí přizpůsobeného řešení simulace zařízení:

1. Spusťte webové uživatelské rozhraní simulace zařízení z [akcelerátorů řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Pomocí webového uživatelského rozhraní nakonfigurujte a spusťte simulaci. Pokud jste dříve [dokončili serializovat telemetrii pomocí vyrovnávacích pamětí protokolu](iot-accelerators-device-simulation-protobuf.md), můžete použít vlastní model zařízení.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak nasadit vlastní bitovou kopii Simulace zařízení, možná se budete chtít dozvědět, jak [používat existující centrum IoT hub s akcelerátorem řešení Simulace zařízení](iot-accelerators-device-simulation-choose-hub.md).