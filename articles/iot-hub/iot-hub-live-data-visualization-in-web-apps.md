---
title: Vizualizace dat v reálném čase s daty ze snímačů z Azure IoT hub – Web Apps | Microsoft Docs
description: Použijte funkci webové aplikace Microsoft Azure App Service k vizualizaci dat teploty a vlhkosti, který se shromažďují ze senzoru a odesílá do služby Iot hub.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: vizualizace dat reálném čase, vizualizace dat za provozu, vizualizace dat snímačů
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0edf72595f67dd9f6b33f9bfeaf4cd98405dc3ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Vizualizovat data snímačů v reálném čase ze služby Azure IoT hub pomocí funkce Web Apps služby Azure App Service

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto kurzu zjistěte, jak k vizualizaci dat snímačů v reálném čase, který IoT hub přijímá spuštěním webové aplikace, která je hostovaná ve webové aplikaci. Pokud chcete, zkuste k vizualizaci dat ve službě IoT hub pomocí Power BI, najdete v článku [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co dělat

- Vytvořte webovou aplikaci na portálu Azure.
- Služby IoT hub připravte pro přístup k datům přidáním skupiny příjemců.
- Konfigurace webové aplikace načíst data snímačů ze služby IoT hub.
- Nahrajte webovou aplikaci pro hostování webových aplikací.
- Otevřete webovou aplikaci se zobrazí v reálném čase teploty a vlhkosti data ze služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

- [Nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md), která zahrnuje následující požadavky:
  - Aktivní předplatné Azure
  - Služby Iot hub v rámci svého předplatného
  - Klientská aplikace, která odesílá zprávy do služby Iot hub
- [Stáhněte si Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **Web + mobilní** > **webové aplikace**.
2. Zadejte název jedinečné úlohy ověřte předplatné, zadejte skupinu prostředků a umístění, vyberte **připnout na řídicí panel**a potom klikněte na **vytvořit**.

   Doporučujeme vám, vyberte stejné umístění, jako vaší skupiny prostředků. To pomáhá zajistit s rychlost zpracování a snižuje náklady na přenos dat.

   ![Vytvoření webové aplikace](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurace webové aplikace načíst data ze služby IoT hub

1. Otevřete webovou aplikaci, kterou jste právě zřídili.
2. Klikněte na tlačítko **nastavení aplikace**a pak v části **nastavení aplikace**, přidejte následující páry klíč – hodnota:

   | Klíč                                   | Hodnota                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Získat z Průzkumníka iothub                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Název skupiny uživatelů, která přidáte do služby IoT hub  |

   ![Přidat nastavení do vaší webové aplikace s páry klíč – hodnota](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Klikněte na tlačítko **nastavení aplikace**v části **obecné nastavení**, přepnutí **webové sokety** a potom klikněte na **Uložit**.

   ![Přepněte možnost webové sokety](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Nahrát webovou aplikaci pro hostování webových aplikací

Na Githubu jsme provedli jsme k dispozici webové aplikace, které se zobrazují data snímačů v reálném čase ze služby IoT hub. Všechny, které musíte udělat, je konfigurace webové aplikace pro práci s úložiště Git, stahování webové aplikace z webu GitHub a nahrajte ho do Azure pro webovou aplikaci na hostitele.

1. Ve webové aplikaci, klikněte na tlačítko **možnosti nasazení** > **zvolit zdroj** > **místní úložiště Git**a potom klikněte na **OK**.

   ![Konfigurace nasazení webové aplikace použít místní úložiště Git](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Klikněte na tlačítko **přihlašovací údaje nasazení**, vytvořte uživatelské jméno a heslo použít k připojení do úložiště Git v Azure a pak klikněte na tlačítko **Uložit**.

3. Klikněte na tlačítko **přehled**a poznamenejte si hodnotu **adresu url pro Git clone**.

   ![Získat adresu URL klonu Git webové aplikace](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Otevřete okno terminálu v místním počítači nebo příkaz.

5. Stažení webové aplikace z webu GitHub a nahrajte ho do Azure pro webovou aplikaci na hostitele. Uděláte to tak, spusťte následující příkazy:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Adresa URL klonu Git\> je adresa URL úložiště Git v nalezen **přehled** stránku webové aplikace.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Otevřete webovou aplikaci se zobrazí v reálném čase teploty a vlhkosti data ze služby IoT hub

Na **přehled** stránku vaší webové aplikace, klikněte na adresu URL otevřete webovou aplikaci.

![Získat adresu URL webové aplikace](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Měli byste vidět data v reálném čase teploty a vlhkosti ze služby IoT hub.

![Webové stránky aplikace zobrazuje v reálném čase teploty a vlhkosti](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Zkontrolujte, jestli že ukázkové aplikace běží na vašem zařízení. Pokud není, zobrazí se prázdný graf, najdete podrobné pokyny v části [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Další postup
Webové aplikace jste úspěšně použili k vizualizaci dat snímačů v reálném čase ze služby IoT hub.

Alternativní způsob k vizualizaci dat ze služby Azure IoT Hub, najdete v části [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
