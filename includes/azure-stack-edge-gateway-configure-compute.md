---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524803"
---
Pokud chcete nakonfigurovat výpočetní výkon na Azure Stack Edge pro, vytvoříte prostředek IoT Hub prostřednictvím Azure Portal.

1. V Azure Portal prostředku Azure Stack Edge, klikněte na **Přehled** a vyberte **IoT Edge**.

   ![Začínáme se službou COMPUTE](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. V **IoT Edge povolit službu** vyberte **Přidat**.

   ![Konfigurace COMPUTE](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. V okně **Konfigurovat hraniční výpočty** zadejte následující informace:
   
    |Pole  |Hodnota  |
    |---------|---------|
    |Předplatné     |Vyberte předplatné pro prostředek IoT Hub. Můžete použít stejné předplatné, jako používá prostředek Azure Stack Edge.         |
    |Skupina prostředků     |Vyberte skupinu prostředků pro prostředek IoT Hub. Můžete použít stejnou skupinu prostředků, jako kterou používá prostředek Azure Stack Edge.         |
    |IoT Hub     | Vyberte z **nových** nebo **existujících**. <br> Ve výchozím nastavení se k vytváření prostředků IoT používá úroveň Standard (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek. <br> V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, kterou používá prostředek Azure Stack Edge.     |
    |Name     |Přijměte výchozí název nebo zadejte název prostředku IoT Hub.         |

   ![Začínáme s výpočetním využitím 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Po dokončení nastavení vyberte **zkontrolovat + vytvořit**. Zkontrolujte nastavení prostředku IoT Hub a vyberte **vytvořit**.

   Vytvoření prostředku pro prostředek IoT Hub trvá několik minut. Po vytvoření prostředku bude **Přehled** označovat, že služba IoT Edge je teď spuštěná.

   ![Začínáme s výpočetním prostředím 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Pokud chcete potvrdit, že je nakonfigurovaná role hraničního výpočtu, použijte **IoT Edge > vlastností**.

   ![Začínáme se službou COMPUTE 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V tomto zařízení IoT Edge je spuštěn také modul runtime IoT Edge. V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.

Konfigurace výpočetních prostředků může trvat 20-30 minut, protože na pozadí se vytvářejí virtuální počítače a cluster Kubernetes.

Po úspěšné konfiguraci COMPUTE v Azure Portal existuje cluster Kubernetes a výchozí uživatel přidružený k oboru názvů IoT (obor názvů System řízený pomocí Azure Stack Edge pro).
