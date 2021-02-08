---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819995"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Vytvořit prostředek komunikace Azure

Pokud chcete vytvořit prostředek služby Azure Communication Services, [Přihlaste se k](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)rozhraní příkazového řádku Azure a pak spusťte následující příkaz:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Prostředek komunikačních služeb můžete nakonfigurovat následujícími možnostmi:

* Skupina prostředků
* Název prostředku komunikačních služeb
* Zeměpisná oblast, ke které bude prostředek přidružen

V dalším kroku můžete přiřadit značky k prostředku. Značky lze použít k uspořádání prostředků Azure. Další informace o značkách najdete v [dokumentaci k označování prostředků](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Správa prostředku komunikačních služeb

Chcete-li přidat značky do prostředku komunikačních služeb, spusťte následující příkazy:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Informace o dalších příkazech naleznete v tématu [AZ Communication](https://docs.microsoft.com/cli/azure/ext/communication/communication).