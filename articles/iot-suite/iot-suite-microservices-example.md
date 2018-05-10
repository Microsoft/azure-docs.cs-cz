---
title: Změnit a znovu nasaďte mikroslužbu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak změnit a znovu nasaďte mikroslužbu ve vzdálené monitorování
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: dc18437d554889655963270cdc051fb52325f09e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>Upravit a znovu nasaďte mikroslužbu

Tento kurz ukazuje, jak upravit některý z mikroslužeb v řešení vzdáleného monitorování, vytvoření bitové kopie vašeho mikroslužbu, nasazení bitové kopie do úložiště docker hub a použít ho v řešení vzdáleného monitorování. Zavádět tento koncept, tento kurz používá základní scénář, kde volání mikroslužbu rozhraní API a změnit zprávu o stavu z "Zachování připojení a dobře" na "Nový upravuje Made tady!"

Řešení vzdáleného monitorování používá mikroslužeb, které jsou vytvořené pomocí docker bitové kopie, které jsou načtený z úložiště docker hub. 

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Upravit a vytvořit mikroslužbu v řešení vzdáleného monitorování
> * Vytvořit bitovou kopii docker
> * Push bitovou kopii docker hub docker
> * Vyžádá novou bitovou kopii docker
> * Vizualizace změny 

## <a name="prerequisites"></a>Požadavky

Chcete-li v tomto kurzu, je třeba:

>[!div class="checklist"]
> * [Nasazení předkonfigurovaného řešení vzdáleného monitorování místně](iot-suite-remote-monitoring-deploy-local.md)
> * [Účet Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) – potřeby si zobrazte odezvu rozhraní API

## <a name="call-the-api-and-view-response-status"></a>Volání rozhraní API a zobrazení stav odezvy

V této části můžete volat výchozí IoT hub manager mikroslužbu rozhraní API. Rozhraní API vrátí stavovou zprávu, která můžete změnit později na přizpůsobením mikroslužby.

1. Zajistěte, aby že řešení vzdáleného monitorování běží místně na vašem počítači.
2. Vyhledejte, kam jste stáhli Postman a otevřete jej.
3. V Postman, zadejte následující v GET: http://localhost:8080/iothubmanager/v1/status.
4. Zobrazit návratový a měli byste vidět, "Status": "OK: zachování připojení a dobře".
![Zpráva aktivní a dobře Postman](media/iot-suite-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Změnit stav a sestavení image

Nyní změníte stavovou zprávu mikroslužbu Iot Hub Manager k "Nové úpravy provedené zde!" a pak znovu vytvořte bitovou kopii docker s Tento nový stav. Pokud spustíte sem problémy, podívejte se na naše [Poradce při potížích s](#Troubleshoot) části.

1. Zkontrolujte, zda že je otevřený terminálu a přejděte do adresáře, které jste naklonovali řešení vzdáleného monitorování. 
2. Přejděte do adresáře ".. azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers".
3. Otevřete StatusController.cs v libovolném textového editoru nebo IDE, který chcete. 
4. Vyhledejte následující kód:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    a změňte jej na následující kód a uložte ho.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Přejděte zpět na terminálu, ale nyní změnit na následující adresář: "... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker".
6. Chcete-li vytvořit novou bitovou kopii docker, zadejte

    ```cmd/sh
    sh build
    ```

7. Chcete-li ověřit, zda že byl úspěšně vytvořen vaší novou bitovou kopii, zadejte

    ```cmd/sh
    docker images 
    ```

Úložiště by měl být "azureiotpcs/iothub-manager-dotnet".

![Obrázek úspěšné docker](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Značky a nabízených bitovou kopii
Předtím, než můžete push vaší novou bitovou kopii docker do úložiště docker hub, očekává Docker obrázků na být označené. Pokud spustíte sem problémy, podívejte se na naše [Poradce při potížích s](#Troubleshoot) části.

1. Vyhledejte ID bitové kopie docker bitové kopie, kterou jste vytvořili zadáním:

    ```cmd/sh
    docker images
    ```

2. K označení bitové kopie s typem "testování"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Pro uložení bitové kopie nově s příznakem do úložiště docker hub, zadejte

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otevřete internetový prohlížeč a přejděte na vaše [úložiště docker hub](https://hub.docker.com/) a přihlaste se.
5. Teď byste měli vidět image nově stisknutí docker úložiště docker hub.
![Obrázek docker v úložiště docker hub](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizovat řešení vzdáleného monitorování
Teď je potřeba aktualizovat vaše místní docker-compose.yml a si vyžádá novou bitovou kopii docker z úložiště docker hub. Pokud spustíte sem problémy, podívejte se na naše [Poradce při potížích s](#Troubleshoot) části.

1. Přejděte zpět na terminálu a změnit na následující adresář: ".. Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local".
2. V libovolném textového editoru nebo IDE, který chcete otevřít docker-compose.yml.
3. Vyhledejte následující kód:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    a změňte ji na vypadat podobně jako následující obrázek a uložte ho.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Zobrazení nového stavu odpovědi
Nakonec opětovného nasazení místní instanci řešení vzdáleného monitorování a zobrazením nový stav odpovědi v Postman.

1. Přejděte zpět na terminálu a změnit na následující adresář: ".. Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local".
2. Zadáním následujícího příkazu do terminálu spusťte místní instanci řešení vzdáleného monitorování:

    ```cmd/sh
    docker-compose up
    ```

3. Vyhledejte, kam jste stáhli Postman a otevřete jej.
4. V Postman, zadejte na požadavek GET na následující: http://localhost:8080/iothubmanager/v1/status. Teď byste měli vidět, "Status": "OK: nové úpravy provedené zde!".

![Nové provedené zde upravuje postman zprávy](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Řešení potíží

Pokud používáte systém problémy, zkuste odebrat imagí dockeru a kontejnery v místním počítači.

1. Pokud chcete odebrat všechny kontejnery, budete nejprve muset zastavit všechny spuštěné kontejnery. Otevřete okno terminálu a zadejte

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Pokud chcete odebrat všechny Image, otevřete terminál a typ 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Zkontrolujte, jestli jsou všechny kontejnery v počítači tak, že zadáte

    ```cmd/sh
    docker ps -aq 
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, nic by měl zobrazit.

4. Zkontrolujte, jestli jsou všechny Image na počítači tak, že zadáte

    ```cmd/sh
    docker images
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, nic by měl zobrazit.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste viděli, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Upravit a vytvořit mikroslužbu v řešení vzdáleného monitorování
> * Vytvořit bitovou kopii docker
> * Push bitovou kopii docker hub docker
> * Vyžádá novou bitovou kopii docker
> * Vizualizace změny 

Je další věcí a zkuste to [přizpůsobení mikroslužbu simulátoru zařízení v řešení vzdáleného monitorování](iot-suite-remote-monitoring-test.md)

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v části:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

