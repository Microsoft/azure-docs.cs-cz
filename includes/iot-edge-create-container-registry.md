---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 04/25/2019
ms.author: kgremban
ms.openlocfilehash: 485a76cb91e5146f59e6be592ffd9cbba68e585a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576765"
---
## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu pomocí rozšíření nástroje Azure IoT sestavíte modul a vytvořte **image kontejneru** ze souborů. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.

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
   | Skladová jednotka (SKU) | Vyberte **Basic**. |

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**.

7. Zkopírujte hodnoty **přihlašovací server**, **uživatelské jméno**, a **heslo** a uložit je někde vhodné. Tyto hodnoty v celém tomto kurzu použijete pro poskytnutí přístupu k registru kontejneru.

   ![Zkopírovat přihlašovací server, uživatelské jméno a heslo pro registr kontejneru](./media/iot-edge-create-container-registry/registry-access-key.png)