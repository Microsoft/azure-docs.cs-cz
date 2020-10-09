---
title: Nasazení image vlastního simulace zařízení – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit vlastní image Docker řešení pro simulaci zařízení do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61448391"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Nasazení vlastní image Docker pro simulaci zařízení

Můžete upravit řešení pro simulaci zařízení a přidat vlastní funkce. Například v článku [serializace telemetrie pomocí vyrovnávací paměti protokolu](iot-accelerators-device-simulation-protobuf.md) se dozvíte, jak přidat vlastní zařízení do řešení, které používá vyrovnávací paměti protokolu (Protobuf) k odeslání telemetrie. Po otestování změn v místním prostředí je dalším krokem nasazení změn do instance simulace zařízení v Azure. K dokončení této úlohy musíte vytvořit a nasadit image Docker, která obsahuje vaši upravenou službu.

Postup v tomto návodu vám ukáže, jak:

1. Příprava vývojového prostředí
1. Vygenerovat novou bitovou kopii Docker
1. Konfigurace simulace zařízení pro použití nové image Docker
1. Spustit simulaci pomocí nového obrázku

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete:

* Instance simulace nasazeného [zařízení](quickstart-device-simulation-deploy.md) .
* Dockeru. Stáhněte si [edici Docker Community](https://www.docker.com/products/docker-engine#/download) pro vaši platformu.
* [Účet Docker Hub](https://hub.docker.com/) , kde můžete nahrát image Docker. V účtu Docker Hub vytvořte veřejné úložiště s názvem **simulace zařízení**.
* Upravené a testované [řešení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na místním počítači. Můžete například upravit řešení pro [serializaci telemetrie pomocí vyrovnávacích pamětí protokolu](iot-accelerators-device-simulation-protobuf.md).
* Prostředí, ve kterém lze spustit SSH. Pokud instalujete Git pro Windows, můžete použít prostředí **bash** , které je součástí instalace. Můžete také použít [Azure Cloud Shell](https://shell.azure.com/).

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, možná budete muset upravit některé cesty k souboru a příkazy tak, aby vyhovovaly vašemu prostředí.

## <a name="create-a-new-docker-image"></a>Vytvořit novou bitovou kopii Docker

Pokud chcete nasadit své vlastní změny do služby pro simulaci zařízení, musíte upravit skripty sestavení a nasazení ve složce **scripts\docker** , aby se kontejnery nahrály do účtu Docker – Hub.

### <a name="modify-the-docker-scripts"></a>Úprava skriptů Docker

Upravte sestavení Docker **. cmd**, **Publish. cmd**a spusťte skripty **. cmd** ve složce **Scripts\docker** s informacemi o úložišti Docker Hub. Tyto kroky předpokládají, že jste vytvořili veřejné úložiště s názvem **simulace zařízení**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aktualizujte soubor **Docker-Compose. yml** následujícím způsobem:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Nakonfigurujte řešení tak, aby zahrnovalo nové soubory.

Pokud jste přidali jakékoli nové soubory modelu zařízení, je nutné je explicitně zahrnout do řešení. Přidejte položku do **služeb/Services. csproj** pro každý další soubor, který chcete zahrnout. Pokud jste například dokončili [serializaci telemetrie pomocí vyrovnávacích pamětí protokolu](iot-accelerators-device-simulation-protobuf.md) , přidejte následující položky:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generování nových imagí Docker a nabízených oznámení do Docker Hub

Publikujte novou bitovou kopii Docker do dokovacího centra pomocí účtu Docker – Hub:

1. Otevřete příkazový řádek a přejděte do místní kopie úložiště simulace zařízení.

1. Přejděte do složky **Docker** :

    ```cmd
    cd scripts\docker
    ```

1. Spuštěním následujícího příkazu Sestavte bitovou kopii Docker:

    ```cmd
    build.cmd
    ```

1. Spusťte následující příkaz, který publikuje image Docker do úložiště Docker Hub. Přihlaste se k Docker pomocí přihlašovacích údajů k Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizace služby

Pokud chcete aktualizovat kontejner simulace zařízení tak, aby používal vlastní image, proveďte následující kroky:

* Pomocí SSH se připojte k virtuálnímu počítači, který je hostitelem vaší instance simulace zařízení. Použijte IP adresu a heslo, které jste si poznamenali v předchozí části:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Přejděte do adresáře **/App** :

    ```sh
    cd /app
    ```

* Upravte soubor **Docker-Compose. yml** :

    ```sh
    sudo nano docker-compose.yml
    ```

    Upravte **Image** tak, aby ukazovala vlastní image **simulace zařízení** , kterou jste nahráli do úložiště Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Uložte provedené změny.

* Spusťte následující příkaz, který restartuje mikroslužby:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Spuštění simulace

Nyní můžete spustit simulaci pomocí vlastního řešení simulace zařízení:

1. Spusťte webové uživatelské rozhraní simulace zařízení z [Microsoft Azure akcelerátorů řešení IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Pomocí webového uživatelského rozhraní můžete nakonfigurovat a spustit simulaci. Pokud jste dřív dokončili [serializaci telemetrie pomocí vyrovnávacích pamětí protokolu](iot-accelerators-device-simulation-protobuf.md), můžete použít vlastní model zařízení.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s tím, jak nasadit vlastní simulaci image zařízení. možná budete chtít zjistit, jak [používat stávající centrum IoT s akcelerátorem řešení pro simulaci zařízení](iot-accelerators-device-simulation-choose-hub.md).