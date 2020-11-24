---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553540"
---
## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

V tomto kurzu pomocí rozšíření Azure IoT Tools sestavíte modul a ze souborů se vytvoří **Image kontejneru** . Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.

K uložení imagí kontejneru můžete použít libovolný registr kompatibilní s Docker. K [disAzure Container Registry](../articles/container-registry/index.yml) a [Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)jsou dvě oblíbené služby Docker Registry. V tomto kurzu se používá služba Azure Container Registry.

Pokud ještě nemáte registr kontejnerů, postupujte podle těchto kroků a vytvořte nový v Azure:

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota |
   | ----- | ----- |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Název registru | Zadejte jedinečný název. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | SKU | Vyberte **Basic**. |

3. Vyberte **Vytvořit**.

4. Po vytvoření registru kontejneru ho vyhledejte a v levém podokně vyberte **přístupové klíče** z nabídky, která se nachází v části **Nastavení**. 

5. Kliknutím povolíte uživateli s oprávněními správce zobrazit **uživatelské jméno** a **heslo** pro váš registr kontejnerů.

6. Zkopírujte hodnoty pro **přihlašovací server**, **uživatelské jméno** a **heslo** a uložte je někam do vhodného. Tyto hodnoty použijete v celém rámci tohoto kurzu k poskytnutí přístupu k registru kontejneru.

   ![Kopírovat přihlašovací server, uživatelské jméno a heslo pro registr kontejnerů](./media/iot-edge-create-container-registry/registry-access-key.png)