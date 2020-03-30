---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844580"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul IoT Edge sítě událostí

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje kroky nasazení Event Grid na IoT Edge z webu Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Mřížka událostí bez trvalosti. To znamená, že všechna témata a odběry, které vytvoříte v tomto kurzu budou odstraněny při opětovném nasazení modulu. Další informace o nastavení trvalosti naleznete v následujících článcích: [Zachovat stav v Linuxu](../articles/event-grid/edge/persist-state-linux.md) nebo [Zachovat stav v systému Windows](../articles/event-grid/edge/persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s trvalostí.

>[!IMPORTANT]
> V tomto kurzu bude modul Event Grid nasazen s vypnutým ověřováním klienta a povolí předplatitele protokolu HTTP. Pro produkční úlohy doporučujeme povolit pouze požadavky HTTPS a předplatitele s povoleným ověřováním klientů. Další informace o bezpečné konfiguraci modulu Event Grid naleznete v [tématu Zabezpečení a ověřování](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Výběr zařízení IoT Edge

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Přejděte do svého iot hubu.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge.** 
1. Klikněte na ID cílového zařízení ze seznamu zařízení
1. Vyberte **možnost Nastavit moduly**. Nechte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Portál Azure má průvodce, který vás provede vytvořením manifestu nasazení, namísto ručního sestavení dokumentu JSON.  Má tři kroky: **Přidat moduly**, **Zadat trasy**a **Zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat**
1. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
1. Zadejte název, bitovou kopii, možnosti vytvoření kontejneru:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Název**: eventgridmodule
   * **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Možnosti vytvoření kontejneru**:

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Klikněte na **Uložit.**
 1. Chcete-li pokračovat v úseku trasy, klepněte na tlačítko **Další.**

    > [!NOTE]
    > Pokud používáte virtuální počítač Azure jako hraniční zařízení, přidejte pravidlo příchozího portu, které povolí příchozí provoz na portu 4438. Pokyny k přidání pravidla najdete v tématu [Jak otevřít porty k virtuálnímu počítači](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Nastavení tras

 Zachovat výchozí trasy a výběrem možnosti **Další** pokračovat do oddílu revize

### <a name="review-deployment"></a>Zkontrolovat nasazení

1. Část revize zobrazuje manifest nasazení JSON, který byl vytvořen na základě vašich výběrů v předchozích dvou částech. Potvrďte, že se v seznamu zobrazují dva moduly: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří modul runtime IoT Edge a jsou povinné výchozí hodnoty v každém nasazení.
1. Zkontrolujte informace o nasazení a vyberte **Odeslat**.

### <a name="verify-your-deployment"></a>Ověření nasazení

1. Po odeslání nasazení se vrátíte na stránku IoT Edge vašeho centra IoT hub.
1. Vyberte **zařízení IoT Edge,** na které jste cílí s nasazením otevřít jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, zda je modul Event Grid uveden jako **Zadáno v nasazení** i **Hlášeno zařízením**.

Může trvat několik okamžiků, než se modul spustí na zařízení a pak se ohlásí zpět do ioT hubu. Aktualizujte stránku, abyste viděli aktualizovaný stav.