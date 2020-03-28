---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564754"
---
## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu použijete rozšíření Nástroje Azure IoT k vytvoření modulu a vytvoření **image kontejneru** ze souborů. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.

K uložení iobrazů kontejneru můžete použít libovolný registr kompatibilní s Dockerem. Dvě oblíbené služby registru Dockeru jsou [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a Docker [Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry.

Pokud ještě nemáte registr kontejnerů, vytvořte v Azure nový:

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. |

3. Vyberte **Vytvořit**.

4. Po vytvoření registru kontejnerů vyhledejte tento registr a v levém podokně vyberte **přístupové klávesy** z nabídky umístěné v části **Nastavení**.

5. Zkopírujte hodnoty pro **přihlašovací server**, **uživatelské jméno**a **heslo** a uložte je někam, kde jsou vhodné. Tyto hodnoty v tomto kurzu slouží k poskytnutí přístupu k registru kontejneru.

   ![Kopírování přihlašovacího serveru, uživatelského jména a hesla pro registr kontejnerů](./media/iot-edge-create-container-registry/registry-access-key.png)