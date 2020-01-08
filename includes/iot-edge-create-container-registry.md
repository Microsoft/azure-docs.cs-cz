---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564754"
---
## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

V tomto kurzu pomocí rozšíření Azure IoT Tools sestavíte modul a ze souborů se vytvoří **Image kontejneru** . Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.

Pro uložení imagí kontejnerů, můžete použít jakýkoli registr kompatibilní s Dockerem. Jsou dvě oblíbené služby registrů Dockeru [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry.

Pokud ještě nemáte registr kontejnerů, postupujte podle těchto kroků a vytvořte nový v Azure:

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová položka | Vyberte **Basic**. |

3. Vyberte **Vytvořit**.

4. Po vytvoření registru kontejneru ho vyhledejte a v levém podokně vyberte **přístupové klíče** z nabídky, která se nachází v části **Nastavení**.

5. Zkopírujte hodnoty pro **přihlašovací server**, **uživatelské jméno**a **heslo** a uložte je někam do vhodného. Tyto hodnoty použijete v celém rámci tohoto kurzu k poskytnutí přístupu k registru kontejneru.

   ![Kopírovat přihlašovací server, uživatelské jméno a heslo pro registr kontejnerů](./media/iot-edge-create-container-registry/registry-access-key.png)