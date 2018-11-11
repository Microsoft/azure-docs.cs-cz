---
title: Nasadit vlastní image simulace zařízení – Azure | Dokumentace Microsoftu
description: V této příručce s postupy se dozvíte, jak nasadit řešení pro simulaci zařízení vlastní image Dockeru do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284884"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Nasadit vlastní image dockeru simulace zařízení

Můžete upravit řešení simulace zařízení k přidání vlastních funkcí. Například [serializovat telemetrie pomocí Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) článku se dozvíte, jak přidat vlastní zařízení do řešení, které používá Protocol Buffers (Protobuf) odesílat telemetrická data. Dalším krokem po otestujete své změny místně, je vaše změny nasadí do vaší instance simulaci zařízení v Azure. K dokončení této úlohy, budete muset vytvořit a nasadit image Dockeru obsahující váš upravenou službu.

Kroky v tomto postupy-k-Průvodci ukazují, jak do:

1. Příprava vývojového prostředí
1. Generovat nové image Dockeru
1. Konfigurace simulace zařízení používat nová image Dockeru
1. Spuštění simulace pomocí nové bitové kopie

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte:

* Nasazený [simulace zařízení](quickstart-device-simulation-deploy.md) instance.
* Docker. Stáhněte si [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) pro vaši platformu.
* A [účtu Docker Hubu](https://hub.docker.com/) kde můžete nahrát imagí Dockeru. Ve vašem účtu Docker Hubu vytvoření veřejného úložiště volána **simulace zařízení**.
* A upravovat a testovat [řešení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na místním počítači. Například můžete upravit řešení tak, aby [serializovat telemetrie pomocí Protocol Buffers](iot-accelerators-device-simulation-protobuf.md).
* Prostředí, které můžete spustit SSH. Pokud nainstalujete Git pro Windows, můžete použít **bash** prostředí, které je součástí instalace th. Můžete také použít vaše [Azure Cloud Shell](https://shell.azure.com/).

Pokyny v tomto článku se předpokládá, že používáte Windows. Pokud používáte jiný operační systém, budete muset upravit některé cesty k souborům a příkazů podle vašich potřeb.

## <a name="create-a-new-docker-image"></a>Vytvoření nové image Dockeru

Pokud chcete nasadit vlastní změny ve službě simulace zařízení, budete muset upravit skripty sestavení a nasazení v **scripts\docker** složku k nahrání kontejnery do vašeho účtu docker hubu

### <a name="modify-the-docker-scripts"></a>Upravit skripty dockeru

Upravit Dockeru **build.cmd**, **publish.cmd**, a **run.cmd** skripty v **scripts\docker** složky Docker hubu informace o úložišti. Tento postup předpokládá, že jste vytvořili veřejného úložiště volána **simulace zařízení**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aktualizace **docker-compose.yml** to následujícím způsobem:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Nakonfigurujte řešení, aby všechny nové soubory k zahrnutí

Pokud jste přidali všechny nové soubory model zařízení, musíte výslovně nezahrnete do řešení. Přidání položky do **services/services.csproj** pro každý další soubor zahrnout. Například, pokud jste dokončili [serializovat telemetrie pomocí Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) s postupy, přidejte následující položky:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generovat nové Image Dockeru a odeslání do Docker Hubu

Publikujte nové image Dockeru do Docker Hubu pomocí svého účtu docker hubu:

1. Otevřete příkazový řádek a přejděte do místní kopie úložiště simulaci zařízení.

1. Přejděte **docker** složky:

    ```cmd
    cd scripts\docker
    ```

1. Spuštěním následujícího příkazu sestavte image Dockeru:

    ```cmd
    build.cmd
    ```

1. Spusťte následující příkaz k publikování image Dockeru do Docker Hubu úložiště. Přihlaste se k Dockeru pomocí svých přihlašovacích údajů Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizace služby

K aktualizaci kontejneru simulace zařízení pro použití vlastní image, proveďte následující kroky:

* Pomocí SSH se připojte k virtuálnímu počítači, který je hostitelem vaší instance simulace zařízení. Použijte IP adresu a heslo, které jste si poznamenali v předchozí části:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Přejděte **/app** adresáře:

    ```sh
    cd /app
    ```

* Upravit **docker-compose.yml** souboru:

    ```sh
    sudo nano docker-compose.yml
    ```

    Upravit **image** tak, aby odkazoval vlastní **simulace zařízení** image se odeslala do úložiště Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Uložte provedené změny.

* Spuštěním následujícího příkazu restartujete mikroslužby:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Spustit simulaci

Teď můžete spouštět simulace využívat vaše vlastní řešení simulaci zařízení:

1. Spuštění simulace vaše zařízení webové uživatelské rozhraní z [akcelerátory řešení IoT Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Použijte ke konfiguraci a spuštění simulace ve webovém uživatelském rozhraní. Pokud jste již dříve dokončili [serializovat telemetrie pomocí Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), můžete použít model vlastní zařízení.

## <a name="next-steps"></a>Další postup

Nyní jste zjistili, jak chcete nasadit vlastní image simulace zařízení, možná budete chtít zjistěte, jak [akcelerátor řešení simulace zařízení pomocí stávající služby IoT hub](iot-accelerators-device-simulation-choose-hub.md).