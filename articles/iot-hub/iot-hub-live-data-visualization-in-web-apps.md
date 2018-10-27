---
title: Vizualizace dat v reálném čase s daty ze snímačů ze služby Azure IoT hub – Web Apps | Dokumentace Microsoftu
description: Použití funkce Web Apps služby Microsoft Azure App Service k vizualizaci dat teploty a vlhkosti, který se shromažďují ze senzoru a odeslané do služby Iot hub.
author: rangv
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: c43431cd6ddbbbf8f6cb709b8c1783179d6cf760
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158716"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Vizualizovat data ze senzorů v reálném čase ze služby Azure IoT hub pomocí funkce Web Apps služby Azure App Service

![Diagram začátku do konce](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto kurzu se dozvíte, jak k vizualizaci dat snímačů v reálném čase, který váš IoT hub přijme spuštěním webové aplikace, která je hostovaná ve webové aplikaci. Pokud chcete zkusit k vizualizaci dat ve službě IoT hub s využitím Power BI, najdete v článku [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co můžete dělat

* Vytvoření webové aplikace na webu Azure Portal.
* Připravte se službu IoT hub pro přístup k datům tak, že přidáte skupinu příjemců.
* Konfigurace webové aplikace ke čtení dat ze snímačů ze služby IoT hub.
* Odešlete webové aplikace hostované ve webové aplikaci.
* Otevřete webovou aplikaci zobrazíte data v reálném čase teploty a vlhkosti ze služby IoT hub.

## <a name="what-you-need"></a>Co potřebujete

* [Nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md), která zahrnuje následující požadavky:

  * Aktivní předplatné Azure
  * Služby Iot hub v rámci vašeho předplatného
  * Klientská aplikace, která odesílá zprávy do služby Iot hub

* [Stáhněte si Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

2. Zadejte jedinečný název úlohy, ověřte, předplatné, zadejte skupinu prostředků a umístění, vyberte **připnout na řídicí panel**a potom klikněte na tlačítko **vytvořit**.

   Doporučujeme vybrat stejné umístění jako skupina prostředků. 
   
   ![Vytvoření webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurace webové aplikace ke čtení dat ze služby IoT hub

1. Otevřete webovou aplikaci, kterou jste právě jste zřídili.

2. Klikněte na tlačítko **nastavení aplikace**a pak v části **nastavení aplikace**, přidejte následující dvojice klíč/hodnota:

   | Klíč                                   | Hodnota                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Získané z rozhraní příkazového řádku Azure                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | Název skupiny příjemců, který přidáte do služby IoT hub  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![Přidat nastavení do vaší webové aplikace s páry klíč/hodnota](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. Klikněte na tlačítko **nastavení aplikace**v části **obecné nastavení**, přepnout **webové sokety** možnost a potom klikněte na tlačítko **Uložit**.

   ![Přepněte možnost webové sokety](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Odeslat webové aplikace hostované ve webové aplikaci

Na Githubu zpřístupnili jsme webovou aplikaci, která zobrazí data ze senzorů v reálném čase ze služby IoT hub. Všechno, co musíte udělat, je nakonfigurovat ve webové aplikaci pracovat s úložišti Git, stáhnout z webu GitHub webovou aplikaci a nahrajte ho do Azure pro webovou aplikaci do hostitele.

1. Ve službě web app, klikněte na tlačítko **možnosti nasazení** > **zvolit zdroj** > **místní úložiště Git**a potom klikněte na tlačítko **OK**.

   ![Konfigurace nasazení webových aplikací používat místní úložiště Git](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Klikněte na tlačítko **přihlašovací údaje pro nasazení**, uživatelské jméno a heslo použité pro připojení k úložišti Git v Azure a potom klikněte na vytvořit **Uložit**.

3. Klikněte na tlačítko **přehled**a poznamenejte si hodnotu **adresu url pro Git clone**.

   ![Získat adresu URL klonu Git vaší webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. Otevřete příkazový nebo okno terminálu na místním počítači.

5. Stáhnout z webu GitHub webovou aplikaci a nahrajte ho do Azure pro webovou aplikaci do hostitele. Chcete-li tak učinit, spusťte následující příkazy:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Adresa URL klonu Git\> je adresa URL úložiště Git na **přehled** stránka webové aplikace.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Otevřít webovou aplikaci zobrazíte data v reálném čase teploty a vlhkosti ze služby IoT hub

Na **přehled** stránce vaší webové aplikace, klikněte na adresu URL pro otevření webové aplikace.

![Získat adresu URL webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

Měli byste vidět data v reálném čase teploty a vlhkosti ze služby IoT hub.

![Zobrazení v reálném čase teploty a vlhkosti na stránce webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Ujistěte se, že je ukázková aplikace spuštěná ve vašem zařízení. Pokud ne, zobrazí se prázdný graf, najdete v kurzech pod [nastavit zařízení](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Další postup

Úspěšně jste použili vaší webové aplikace k vizualizaci dat snímačů v reálném čase ze služby IoT hub.

Alternativní způsob vizualizace dat ze služby Azure IoT Hub, najdete v části [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]