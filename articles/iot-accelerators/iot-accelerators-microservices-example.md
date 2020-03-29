---
title: Změna a opětovné nasazení mikroslužeb – Azure | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jak změnit a znovu nasadit mikroslužbu ve vzdáleném monitorování
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447585"
---
# <a name="customize-and-redeploy-a-microservice"></a>Přizpůsobení a opakované nasazení mikroslužby

Tento kurz ukazuje, jak upravit jednu z [mikroslužeb](https://azure.com/microservices) v řešení vzdáleného monitorování, vytvořit bitovou kopii vaší mikroslužby, nasadit image do docker hubu a pak ji použít v řešení vzdáleného monitorování. Chcete-li zavést tento koncept, kurz používá základní scénář, kde zavoláte rozhraní API mikroslužeb a změnit stavovou zprávu z "Alive and Well" na "Nové úpravy provedené zde!"

Vzdálené monitorování řešení používá mikroslužeb, které jsou vytvořeny pomocí imblin dockeru, které jsou vyčádkání z docker hubu. 

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Úprava a sestavení mikroslužby v řešení vzdáleného monitorování
> * Vytvoření image dockeru
> * Zasunutí image dockeru do centra dockeru
> * Vytáhnout novou bitovou kopii dockeru
> * Vizualizace změn 

## <a name="prerequisites"></a>Požadavky

Chcete-li sledovat tento výukový program, musíte:

>[!div class="checklist"]
> * [Místní nasazení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Účet Dockeru](https://hub.docker.com/)
> * [Pošťák](https://www.getpostman.com/) - Potřebné k zobrazení odpovědi rozhraní API

## <a name="call-the-api-and-view-response-status"></a>Volání rozhraní API a zobrazení stavu odpovědi

V této části zavoláte výchozí rozhraní API mikroslužeb správce centra IoT. Rozhraní API vrátí stavovou zprávu, kterou později změníte přizpůsobením mikroslužby.

1. Ujistěte se, že řešení vzdáleného monitorování běží místně v počítači.
2. Vyhledejte, kde jste stáhli Pošťáka a otevřete ho.
3. V pošťák, zadejte následující `http://localhost:8080/iothubmanager/v1/status`get: .
4. Zobrazit návrat a měli byste vidět, "Status": "OK: Alive a Dobře".

    ![Alive a dobře Pošťák zpráva](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Změna stavu a sestavení obrázku

Teď změňte stavovou zprávu mikroslužby Správce centra Iot na "Nové úpravy provedené zde!" a potom znovu vytvořit image dockeru s tímto novým stavem. Pokud zde narazíte na problémy, přečtěte si naši část [Řešení potíží.](#Troubleshoot)

1. Ujistěte se, že váš terminál je otevřený a převyšte adresář, do kterého jste naklonovali řešení vzdáleného monitorování. 
1. Změňte svůj adresář na "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Otevřete StatusService.cs v libovolném textovém editoru nebo ide, které se vám líbí. 
1. Vyhledejte následující kód:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    a změnit jej na níže uvedený kód a uložit jej.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Vraťte se do terminálu, ale nyní přejděte na následující adresář: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Chcete-li vytvořit novou bitové kopie dockeru, zadejte

    ```sh
    sh build
    ```
    
    nebo v systému Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Chcete-li ověřit, zda byl nový obrázek úspěšně vytvořen, zadejte

    ```cmd/sh
    docker images 
    ```

Úložiště by mělo být "azureiotpcs/iothub-manager-dotnet".

![Úspěšná image dockeru](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Označení a odeslání image
Než budete moci vaši novou image dockeru přemístit do dockerhubu, Docker očekává, že vaše image budou tagované. Pokud zde narazíte na problémy, přečtěte si naši část [Řešení potíží.](#Troubleshoot)

1. Vyhledejte ID image image image dockeru, kterou jste vytvořili zadáním:

    ```cmd/sh
    docker images
    ```

2. Označení obrázku pomocí typu "testování"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Pokud chcete nově tagovaný obrázek přetlačit do docker hubu, zadejte

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otevřete internetový prohlížeč a přejděte do [docker hubu](https://hub.docker.com/) a přihlaste se.
5. Nyní byste měli vidět nově posunutou image dockeru na vašem docker hubu.
![Image Dockeru v centru dockeru](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizace řešení vzdáleného monitorování
Nyní je potřeba aktualizovat místní docker-compose.yml natáhnout novou image dockeru z vašeho docker hubu. Pokud zde narazíte na problémy, přečtěte si naši část [Řešení potíží.](#Troubleshoot)

1. Vraťte se do terminálu a přejděte na následující adresář: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Otevřete docker-compose.yml v libovolném textovém editoru nebo IDE, které se vám líbí.
3. Vyhledejte následující kód:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    a změňte ji tak, aby vypadala jako obrázek níže a uložte jej.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Zobrazit nový stav odpovědi
Dokončete opětovné nasazení místní instance řešení vzdáleného monitorování a zobrazení nové odpovědi na stav v Postman.

1. Vraťte se do terminálu a přejděte na následující adresář: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Spusťte místní instanci řešení vzdáleného monitorování zadáním následujícího příkazu do terminálu:

    ```cmd/sh
    docker-compose up
    ```

3. Vyhledejte, kde jste stáhli Pošťáka a otevřete ho.
4. V pošťáku zadejte do `http://localhost:8080/iothubmanager/v1/status`příkazu GET následující požadavek: . Nyní byste měli vidět, "Status": "OK: Nové úpravy made here!".

![Nová zpráva o úpravě made here postman](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Řešení potíží

Pokud narážíte na problémy, zkuste odebrat image dockeru a kontejnery v místním počítači.

1. Chcete-li odebrat všechny kontejnery, musíte nejprve zastavit všechny spuštěné kontejnery. Otevřete terminál a zadejte

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Chcete-li odstranit všechny obrázky, otevřete terminál a zadejte 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Můžete zkontrolovat, zda jsou na stroji nějaké kontejnery, zadáním

    ```cmd/sh
    docker ps -aq 
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, nic by se nemělo zobrazit.

4. Můžete zkontrolovat, zda jsou na zařízení nějaké obrázky zadáním

    ```cmd/sh
    docker images
    ```

    Pokud jste úspěšně odebrali všechny kontejnery, nic by se nemělo zobrazit.

## <a name="next-steps"></a>Další kroky

V tomto tutoriálu jste viděli, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Úprava a sestavení mikroslužby v řešení vzdáleného monitorování
> * Vytvoření image dockeru
> * Zasunutí image dockeru do centra dockeru
> * Vytáhnout novou bitovou kopii dockeru
> * Vizualizace změn 

Další věcí, kterou můžete vyzkoušet, je [přizpůsobení mikroslužby simulátoru zařízení v řešení vzdáleného monitorování](iot-accelerators-microservices-example.md)

Další informace pro vývojáře o řešení vzdáleného monitorování naleznete v následujících tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

