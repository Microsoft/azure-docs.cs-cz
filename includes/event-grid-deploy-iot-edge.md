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
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050308"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul Event Grid IoT Edge

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge, a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje postup nasazení Event Grid IoT Edge z Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Event Grid bez trvalosti. To znamená, že všechna témata a odběry, které v tomto kurzu vytvoříte, budou při opětovném nasazení modulu odstraněny. Další informace o tom, jak nastavit trvalost, najdete v následujících článcích: [trvalé uložení stavu v systému Linux](../articles/event-grid/edge/persist-state-linux.md) nebo [trvalého stavu ve Windows](../articles/event-grid/edge/persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s persistencí.

>[!IMPORTANT]
> V tomto kurzu se Event Grid modul nasadí s vypnutým ověřováním klienta a povolí předplatitele HTTP. Pro produkční úlohy doporučujeme povolit pouze žádosti a předplatitele HTTPS s povoleným ověřováním klienta. Další informace o tom, jak bezpečně nakonfigurovat Event Grid modul, najdete v tématu [zabezpečení a ověřování](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Vyberte zařízení IoT Edge

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte na IoT Hub.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge** . 
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**. Nechejte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON.  Má tři kroky: **přidat moduly**, **zadat trasy** a **zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** .
1. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
1. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Název**: eventgridmodule
   * **Identifikátor URI image**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
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

 1. Klikněte na **Uložit**.
 1. Kliknutím na tlačítko **Další** pokračujte v části trasy.

    > [!NOTE]
    > Pokud jako hraniční zařízení používáte virtuální počítač Azure, přidejte pravidlo příchozího portu, které povolí příchozí provoz na portu 4438. Pokyny k přidání pravidla najdete v tématu [Postup otevření portů pro virtuální počítač](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Nastavení tras

 Ponechte výchozí trasy a vyberte **Další** , abyste pokračovali v části Kontrola.

### <a name="review-deployment"></a>Zkontrolovat nasazení

1. V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozích dvou částech. Ověřte, že se v seznamu zobrazují tyto dva moduly: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří modul runtime IoT Edge a jsou požadované výchozí hodnoty v každém nasazení.
1. Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**.

### <a name="verify-your-deployment"></a>Ověření nasazení

1. Po odeslání nasazení se vrátíte na stránku IoT Edge služby IoT Hub.
1. Vyberte **zařízení IoT Edge** , na které cílíte nasazení, a otevřete jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, že je modul Event Grid uvedený jako **v části nasazení** i **nahlášený zařízením**.

Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.