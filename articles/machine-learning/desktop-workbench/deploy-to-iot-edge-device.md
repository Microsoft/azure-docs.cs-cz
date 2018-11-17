---
title: Nasadit model ve službě Azure Machine Learning pro zařízení Azure IoT Edge | Dokumentace Microsoftu
description: Tento dokument popisuje, jak je možné nasadit modely Azure Machine Learning pro zařízení Azure IoT Edge.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 792ac3f26bdea6c6ccb084d893925d60e6333edb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852448"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Nasadit model ve službě Azure Machine Learning pro zařízení Azure IoT Edge

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Modely Azure Machine Learning můžete kontejnerizovaných jako webových služeb využívajících Docker. Azure IoT Edge umožňuje nasazení kontejnerů do zařízení vzdáleně. Tyto služby použijte současně ke spuštění vašich modelů na hraničních zařízeních pro kratší doby odezvy a menší datové přenosy. 

Další skripty a pokyny najdete v [AI Toolkit pro Azure IoT Edge](https://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Zprovoznění modelu

Moduly Azure IoT Edge jsou založeny na Image kontejneru. Chcete-li nasadit model strojového učení na zařízení IoT Edge, budete muset vytvoření image Dockeru.

Zprovoznění vašeho modelu podle pokynů v [Azure Machine Learning Model správy webové nasazení služby](model-management-service-deploy.md) k vytvoření image Dockeru obsahující váš model.

## <a name="deploy-to-azure-iot-edge"></a>Nasazení do Azure IoT Edge

Až budete mít image váš model, můžete ho nasadit do libovolného zařízení Azure IoT Edge. Všechny modely Machine Learning můžete spustit na zařízeních IoT Edge. 

### <a name="set-up-an-iot-edge-device"></a>Nastavení zařízení IoT Edge

Připravte zařízení pomocí dokumentace ke službě Azure IoT Edge. 

1. [Registrace zařízení s Azure IoT Hub](../../iot-edge/how-to-register-device-portal.md). Výstup této procesů je připojovací řetězec, který můžete použít ke konfiguraci fyzického zařízení. 
2. Nainstalujte modul runtime IoT Edge na fyzické zařízení a nakonfigurovat připojovací řetězec. Můžete nainstalovat modul runtime na [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) nebo [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) zařízení.  


### <a name="find-the-machine-learning-container-image-location"></a>Vyhledejte umístění image kontejneru Machine Learning
Je třeba umístění image kontejneru s Machine Learning. K vyhledání umístění image kontejneru:

1. Přihlaste se na [Azure Portal](http://portal.azure.com/).
2. V **Azure Container Registry**, vyberte registru, kterou chcete zkontrolovat.
3. V registru, klikněte na tlačítko **úložišť** zobrazíte seznam všechna úložiště a jejich bitové kopie.

Když se díváte na svůj registr kontejneru na webu Azure Portal, načtení přihlašovacích údajů registru kontejneru. Tyto přihlašovací údaje musí být uvedeny do zařízení IoT Edge, tak, aby ho stáhněte si image z privátního registru. 

1. V registru kontejneru, klikněte na tlačítko **přístupové klíče**. 
2. **Povolit** správce, pokud ještě není. 
3. Uložte příslušné hodnoty pro **přihlašovací server**, **uživatelské jméno**, a **heslo**. 

### <a name="deploy-the-container-image-to-your-device"></a>Nasazení image kontejneru do svého zařízení

Image kontejneru a přihlašovací údaje registru kontejneru jste připraveni nasadit do zařízení IoT Edge modelu strojového učení. 

Postupujte podle pokynů v [moduly nasazení IoT Edge z portálu Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) ke spuštění vašeho modelu zařízení IoT Edge. 











