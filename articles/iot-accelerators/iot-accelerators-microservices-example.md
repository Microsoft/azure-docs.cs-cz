---
title: Změna a opětovné nasazení mikroslužeb – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak změnit a znovu nasadit mikroslužbu ve vzdáleném monitorování.
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998318"
---
# <a name="customize-and-redeploy-a-microservice"></a>Přizpůsobení a opakované nasazení mikroslužby

V tomto kurzu se dozvíte, jak upravit jednu z [mikroslužeb](https://azure.com/microservices) v řešení vzdáleného monitorování, jak vytvořit image mikroslužeb, nasadit image do Docker Hub a pak ji použít v řešení vzdáleného monitorování. K zavedení tohoto konceptu se v kurzu používá základní scénář, ve kterém voláte rozhraní API mikroslužeb a měníte stavovou zprávu z možnosti Alive a well na nové úpravy provedené v tomto případě.

Řešení vzdáleného monitorování používá mikroslužby vytvořené pomocí imagí Docker, které jsou načítány z Docker Hub. 

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Úprava a vytvoření mikroslužby v řešení vzdáleného monitorování
> * Sestavení image Docker
> * Vložení image Docker do vašeho centra Docker
> * Načíst novou bitovou kopii Docker
> * Vizualizace změn 

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto kurzu, budete potřebovat:

>[!div class="checklist"]
> * [Místní nasazení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Účet Docker](https://hub.docker.com/)
> * [Pozálohovací](https://www.getpostman.com/) – potřebný k zobrazení odpovědi rozhraní API

## <a name="call-the-api-and-view-response-status"></a>Volání rozhraní API a zobrazení stavu odpovědi

V této části zavoláte výchozí rozhraní API mikroslužeb Správce služby IoT Hub. Rozhraní API vrátí stavovou zprávu, kterou později změníte, přizpůsobením mikroslužby.

1. Ujistěte se, že je na vašem počítači místně spuštěné řešení vzdáleného monitorování.
2. Vyhledejte, kam jste stáhli po stažení, a otevřete ji.
3. V části post zadejte následující příkaz GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Zobrazte si návrat a měli byste vidět "stav": "OK: Alive a well".

    ![Alive a správně odeslaná zpráva](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Změna stavu a sestavení image

Teď změňte stavovou zprávu mikroslužeb Správce služby IoT Hub na nové úpravy, které tady udělali! a pak znovu sestavte image Docker s tímto novým stavem. Pokud narazíte na problémy, přečtěte si část věnované [řešení potíží](#Troubleshoot) .

1. Ujistěte se, že je terminál otevřený, a přejděte do adresáře, do kterého jste naklonováni řešení vzdáleného monitorování. 
1. Změňte adresář na Azure-IoT-PC-Remote-Monitoring-dotnet/Services/iothub-Manager/Services.
1. Otevřete StatusService.cs v libovolném textovém editoru nebo integrovaném vývojovém prostředí, které chcete. 
1. Vyhledejte následující kód:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    a pak ho změňte na následující kód a uložte ho.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Vraťte se do terminálu, ale teď se změňte na následující adresář: Azure-IoT-PC-Remote-Monitoring-dotnet/Services/iothub-Manager/Scripts/Docker.
6. Pokud chcete vytvořit novou image Docker, zadejte

    ```sh
    sh build
    ```
    
    nebo ve Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Pokud chcete ověřit, že se nová image úspěšně vytvořila, zadejte

    ```cmd/sh
    docker images 
    ```

Úložiště by mělo být "azureiotpcs/iothub-Manager-dotnet".

![Úspěšná image Docker](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Označení a odeslání image
Před nahráním nové image Docker do dokovacího centra očekává Docker, že vaše obrázky budou označené. Pokud narazíte na problémy, přečtěte si část věnované [řešení potíží](#Troubleshoot) .

1. Vyhledejte ID image image Docker, kterou jste vytvořili zadáním:

    ```cmd/sh
    docker images
    ```

2. Označení obrázku pomocí typu "testování"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Pokud chcete nově označenou bitovou kopii nasdílet do vašeho rozbočovače Docker, zadejte

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otevřete internetový prohlížeč a přihlaste se k [centru Docker](https://hub.docker.com/) a přihlaste se.
5. Nyní by se měla zobrazit vaše nově vložená image Docker do vašeho centra Docker.
![Image Docker v Docker Hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizace řešení vzdáleného monitorování
Teď je potřeba aktualizovat místní Docker-Compose. yml a načíst novou image Docker z vašeho centra Docker. Pokud narazíte na problémy, přečtěte si část věnované [řešení potíží](#Troubleshoot) .

1. Vraťte se do terminálu a přejděte do následujícího adresáře: "Azure-IoT-PC-Remote-Monitoring-dotnet/Services/Scripts/local".
2. Otevřete Docker-Compose. yml v libovolném textovém editoru nebo integrovaném vývojovém prostředí, které chcete.
3. Vyhledejte následující kód:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    a pak ho změňte tak, aby vypadal jako obrázek níže, a uložte ho.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Zobrazit nový stav odpovědi
Dokončete tak, že znovu nasadíte místní instanci řešení vzdáleného monitorování a zobrazíte novou odpověď na stav v poli post.

1. Vraťte se do terminálu a přejděte do následujícího adresáře: "Azure-IoT-PC-Remote-Monitoring-dotnet/Scripts/local".
2. Spusťte místní instanci řešení vzdáleného monitorování zadáním následujícího příkazu do terminálu:

    ```cmd/sh
    docker-compose up
    ```

3. Vyhledejte, kam jste stáhli po stažení, a otevřete ji.
4. V části post zadejte do GET: následující požadavek `http://localhost:8080/iothubmanager/v1/status` . Nyní byste měli vidět "stav": "OK: nové úpravy provedené!".

![Nové úpravy provedené v tomto příspěvku odesílají zprávy](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Řešení potíží

Pokud máte problémy s problémy, zkuste z místního počítače odebrat image a kontejnery Docker.

1. Pokud chcete odebrat všechny kontejnery, musíte nejprve zastavit všechny spuštěné kontejnery. Otevřete terminál a zadejte

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Pokud chcete odebrat všechny image, otevřete terminál a zadejte 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Můžete zjistit, jestli na počítači nejsou nějaké kontejnery, a to tak, že zadáte

    ```cmd/sh
    docker ps -aq 
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, neměli byste nic zobrazovat.

4. Můžete zjistit, jestli na počítači nejsou žádné image, a to tak, že zadáte

    ```cmd/sh
    docker images
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, neměli byste nic zobrazovat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Úprava a vytvoření mikroslužby v řešení vzdáleného monitorování
> * Sestavení image Docker
> * Vložení image Docker do vašeho centra Docker
> * Načíst novou bitovou kopii Docker
> * Vizualizace změn 

Další věc, kterou si můžete vyzkoušet, je [přizpůsobení mikroslužby simulátoru zařízení v řešení vzdáleného monitorování](iot-accelerators-microservices-example.md) .

Další informace o vývojářích řešení vzdáleného monitorování najdete v těchto tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

