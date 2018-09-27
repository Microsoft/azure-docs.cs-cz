---
title: Změnit a opakované nasazení mikroslužby | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak změnit a opakované nasazení mikroslužby v vzdáleného monitorování
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 0b206d7b56fc8a65c422a4ce22b2f5585e71c8da
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219411"
---
# <a name="customize-and-redeploy-a-microservice"></a>Přizpůsobení a opakované nasazení mikroslužby

V tomto kurzu se dozvíte, jak upravit jednu z mikroslužeb v řešení vzdáleného monitorování, vytváření bitové kopie vašeho mikroslužeb, nasazení image do docker hubu a použít jej v řešení vzdáleného monitorování. Zavedení tohoto konceptu, v tomto kurzu použijete základní scénář, ve kterém volání rozhraní API mikroslužby a změnit stavová zpráva "Aktivní a dobře" k "New upraví Made tady!"

Řešení vzdáleného monitorování využívá mikroslužeb, které jsou vytvořené pomocí Image dockeru, které se berou z docker hubu. 

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Úpravy a vytváření mikroslužeb v řešení vzdáleného monitorování
> * Sestavíte image dockeru
> * Odeslání image dockeru do docker hubu
> * O přijetí změn nové image dockeru
> * Vizualizujte změny 

## <a name="prerequisites"></a>Požadavky

V tomto kurzu, budete potřebovat:

>[!div class="checklist"]
> * [Nasazení akcelerátoru řešení vzdáleného monitorování místně](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Účet Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) – potřebné k zobrazení odpovědi rozhraní API

## <a name="call-the-api-and-view-response-status"></a>Volání rozhraní API a zobrazit stav odpovědi

V této části volat výchozí IoT hub správci mikroslužeb rozhraní API. Rozhraní API vrátí zprávu o stavu můžete později změnit úpravou mikroslužbách.

1. Zajistěte, aby že řešení vzdáleného monitorování je spuštěn místně v počítači.
2. Vyhledejte, kam jste stáhli Postman a otevřete jej.
3. V nástroji Postman, zadejte následující v GET: http://localhost:8080/iothubmanager/v1/status.
4. Zobrazit návrat a byste měli vidět, "Stavu": "OK: aktivní a dobře".

    ![Aktivní a dobře Postman zprávy](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Změna stavu a sestavení image

Teď změňte stavová zpráva Iot Hub správci mikroslužeb "Nové provedeny změny, tady!" a pak znovu vytvořte image dockeru obsahující tento nový stav. Pokud narazíte na problémy většinou neřeší, podívejte se na naše [Poradce při potížích s](#Troubleshoot) oddílu.

1. Ujistěte se, že otevřete terminál a přejděte do adresáře, kam jste naklonovali řešení vzdáleného monitorování. 
2. Změňte adresář na "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/WebService/v1/Controllers".
3. Otevřete StatusController.cs v libovolném textovém editoru nebo prostředí IDE, který vás zajímá. 
4. Vyhledejte následující kód:

    ```csharp
    return new StatusApiModel(true, "Alive and well");
    ```

    změňte ho na níže uvedený kód a uložte ho.

    ```csharp
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Vraťte se do svého terminálu, ale teď přejděte do následujícího adresáře: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Pokud chcete sestavit nová image dockeru, zadejte

    ```cmd/sh
    sh build
    ```

7. Chcete-li ověřit, zda že byl úspěšně vytvořen nový obrázek, zadejte

    ```cmd/sh
    docker images 
    ```

Úložiště by měl být "azureiotpcs/iothub-manager-dotnet".

![Image dockeru úspěšné](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Označení a odeslání image
Než budete moct odeslat nová image dockeru do docker hubu, Docker očekává, že váš obrázky zařazen. Pokud narazíte na problémy většinou neřeší, podívejte se na naše [Poradce při potížích s](#Troubleshoot) oddílu.

1. Vyhledejte ID Image z image dockeru, který jste vytvořili zadáním:

    ```cmd/sh
    docker images
    ```

2. K označení image s typem "testování"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Chcete-li nahrání nově tagged image do docker hubu, zadejte

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otevřete internetový prohlížeč a přejděte do vaší [docker hubu](https://hub.docker.com/) a přihlaste se.
5. Teď byste měli vidět image dockeru nově vložené v docker hubu.
![Image dockeru do docker hubu](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizujte vaše řešení vzdálené monitorování
Teď je potřeba aktualizovat vaše místní docker-compose.yml k o přijetí změn nové image dockeru z docker hubu. Pokud narazíte na problémy většinou neřeší, podívejte se na naše [Poradce při potížích s](#Troubleshoot) oddílu.

1. Přejděte zpět do terminálu a přejděte do následujícího adresáře: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Otevřete docker-compose.yml v libovolném textovém editoru nebo prostředí IDE, který vás zajímá.
3. Vyhledejte následující kód:

    ```docker
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    a změňte ho na vypadat jako na obrázku níže a uložte ho.

    ```cmd/sh
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Zobrazení nového stavu odpovědi
Nakonec znovu nasadit instanci místní řešení vzdáleného monitorování a zobrazení nového stavu odpovědi v nástroji Postman.

1. Vraťte se do svého terminálu a přejděte do následujícího adresáře: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Spusťte místní instanci řešení vzdáleného monitorování tak, že do terminálu zadáte následující příkaz:

    ```cmd/sh
    docker-compose up
    ```

3. Vyhledejte, kam jste stáhli Postman a otevřete jej.
4. V nástroji Postman, zadejte na požadavek GET na následující: http://localhost:8080/iothubmanager/v1/status. Teď byste měli vidět, "Stavu": "OK: nové úpravy jste tady udělali!".

![Nová úpravy provedené v tomto poli postman zpráva](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Řešení potíží

Pokud máte potíže, zkuste odebrat imagí dockeru a kontejnerů na místním počítači.

1. Chcete-li odebrat všechny kontejnery, budete nejprve muset zastavit všechny spuštěné kontejnery. Otevřete terminál a zadejte

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Chcete-li odebrat všechny Image, otevřete terminál a zadejte 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Zkontrolujte, jestli jsou všechny kontejnery na počítači tak, že zadáte

    ```cmd/sh
    docker ps -aq 
    ```

    Pokud se úspěšně odebral všechny kontejnery, nic se měla zobrazit.

4. Zkontrolujte, jestli jsou všechny Image na počítači tak, že zadáte

    ```cmd/sh
    docker images
    ```

    Pokud se úspěšně odebral všechny kontejnery, nic se měla zobrazit.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste viděli, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Úpravy a vytváření mikroslužeb v řešení vzdáleného monitorování
> * Sestavíte image dockeru
> * Odeslání image dockeru do docker hubu
> * O přijetí změn nové image dockeru
> * Vizualizujte změny 

Zkuste je dalším krokem je [přizpůsobení mikroslužeb simulátor zařízení v řešení vzdáleného monitorování](iot-accelerators-microservices-example.md)

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v tématu:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

