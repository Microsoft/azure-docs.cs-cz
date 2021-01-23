---
title: Spustit detektor anomálií na IoT Edge
titleSuffix: Azure Cognitive Services
description: Nasaďte modul anomálie na IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: 370883b90902dad5a6e222897e68e1d3e9dd2acf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737986"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Nasazení modulu pro detekci anomálií pro IoT Edge

Přečtěte si, jak nasadit modul Cognitive Services [anomálií](../anomaly-detector-container-howto.md) do IoT Edge zařízení. Po nasazení do IoT Edge se modul spouští v IoT Edge společně s jinými moduly jako instance kontejnerů. Zpřístupňuje stejné rozhraní API jako instance kontejneru detektoru anomálií běžící ve standardním prostředí kontejneru Docker. 

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).
* Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) a zařízení [IoT Edge](../../../iot-edge/quickstart-linux.md) .

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Nasazení modulu pro detekci anomálií do hraničních zařízení

1. V Azure Portal do hledání zadejte **detektor anomálií IoT Edge** a otevřete výsledek Azure Marketplace.
2. Přejdete na [stránku Azure Portal cílovými zařízeními pro IoT Edge modul](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Zadejte následující povinné údaje.

    1. Vyberte předplatné.

    1. Vyberte IoT Hub.

    1. Vyberte **Najít zařízení** a vyhledejte zařízení IoT Edge.

3. Vyberte tlačítko **Vytvořit**.

4. Vyberte modul **AnomalyDetectoronIoTEdge** .

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Obrázek uživatelského rozhraní IoT Edge modulů s odkazem na AnomalyDetectoronIoTEdge, který je zvýrazněný červeným polem, aby označoval, že se jedná o položku, kterou chcete vybrat.":::

5. Přejděte na **Proměnné prostředí** a zadejte následující údaje.

    1.  Ponechte hodnotu accept u položky **Eula**.

    1. Do pole pro fakturaci (**Billing**) zadejte koncový bod Cognitive Services.

    1. Jako klíč rozhraní API (**ApiKey**) zadejte klíč rozhraní API služeb Cognitive Services.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Proměnné prostředí s červenými poli kolem oblastí, které potřebují vyplnit hodnoty pro koncový bod a klíč rozhraní API":::

6. Vyberte **Aktualizovat**.

7. Vyberte **Další: trasy** pro definování trasy. Definujte, že všechny zprávy ze všech modulů půjdou do Azure IoT Hubu.

8. Vyberte **Další: Zkontrolovat a vytvořit**. Můžete zobrazit náhled souboru JSON, který definuje všechny moduly nasazované do zařízení IoT Edge.
    
9. Výběrem možnosti **Vytvořit** zahajte nasazování modulu.

10. Jakmile dokončíte nasazení modulu, vraťte se na stránku IoT Edge svého IoT Hubu. V seznamu zařízení IoT Edge vyberte své zařízení a podívejte se na podrobnosti.

11. Posuňte se dolů a prohlédněte si seznam modulů. Ověřte, že je pro nový modul spuštěný běhový stav. 

Pokud chcete řešit potíže s běhovým stavem zařízení IoT Edge, přečtěte si [příručku k odstraňování potíží](../../../iot-edge/troubleshoot.md) .

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Testování anomálií detektoru na zařízení IoT Edge

Vytvoříte volání HTTP zařízení Azure IoT Edge, na kterém je spuštěný kontejner Azure Cognitive Services. Kontejner poskytuje rozhraní API koncového bodu na bázi REST. `http://<your-edge-device-ipaddress>:5000`Pro rozhraní API modulu použijte hostitele.

Pokud vaše hraniční zařízení ještě nepovoluje příchozí komunikaci na portu 5000, budete muset vytvořit nové **pravidlo pro příchozí port**. 

V případě virtuálního počítače Azure to můžete nastavit v části pravidlo pro příchozí porty sítě nastavení **virtuálního počítače** přidat pravidlo portu pro příchozí  >    >    >    >  **spojení**.

Existuje několik způsobů, jak ověřit, zda je modul spuštěn. Vyhledejte *externí IP* adresu a vystavený port daného hraničního zařízení a otevřete oblíbený webový prohlížeč. K ověření, že je kontejner spuštěný, použijte různé adresy URL žádostí níže. Níže uvedené příklady adres URL žádostí se `http://<your-edge-device-ipaddress:5000` můžou lišit, ale váš konkrétní kontejner se může lišit. Mějte na paměti, že musíte použít *externí IP* adresu hraničního zařízení.

| Adresa URL požadavku | Účel |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Kontejner poskytuje domovskou stránku. |
| `http://<your-edge-device-ipaddress>:5000/status` | Požadavek s funkcí GET ověří, zda je klíč rozhraní API použitý ke spuštění kontejneru platný, aniž by to způsobilo dotaz na koncový bod. Tento požadavek se dá použít k Kubernetesi [živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://<your-edge-device-ipaddress>:5000/swagger` | V kontejneru je ke koncovým bodům a k funkci **Vyzkoušet** kompletní dokumentace. Pomocí této funkce můžete zadat nastavení do webového formuláře HTML a vytvořit dotaz bez nutnosti psát jakýkoli kód. Jakmile se dotaz vrátí, je k dispozici vzorový příkaz SLOŽENÉho příkazu, který předvádí hlavičku protokolu HTTP a požadovaný formát textu. |

![Domovská stránka kontejneru](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [instalace a spuštění kontejnerů](../anomaly-detector-container-configuration.md) pro vyžádání image kontejneru a spuštění kontejneru.
* Přečtěte si téma [konfigurace kontejnerů](../anomaly-detector-container-configuration.md) pro nastavení konfigurace
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)