---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644750"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- Instalace [Azure AZ PowerShell Module](https://docs.microsoft.com/powershell/azure/)

## <a name="create-azure-communication-resource"></a>Vytvořit prostředek komunikace Azure

Pokud chcete vytvořit prostředek služby Azure Communication Services, [Přihlaste se k Azure CLI](/cli/azure/authenticate-azure-cli). To můžete provést pomocí terminálu pomocí ```Connect-AzAccount``` příkazu a zadáním přihlašovacích údajů. Spusťte následující příkaz pro vytvoření prostředku:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Pokud chcete vybrat konkrétní předplatné, můžete také zadat ```--subscription``` příznak a zadat ID předplatného.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Prostředek komunikačních služeb můžete nakonfigurovat následujícími možnostmi:

* Skupina prostředků
* Název prostředku komunikačních služeb
* Zeměpisná oblast, ke které bude prostředek přidružen

V dalším kroku můžete přiřadit značky k prostředku. Značky lze použít k uspořádání prostředků Azure. Další informace o značkách najdete v [dokumentaci k označování prostředků](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Správa prostředku komunikačních služeb

Chcete-li přidat značky do prostředku služby Communication Services, spusťte následující příkazy. Můžete také cílit na konkrétní předplatné.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Pokud chcete zobrazit seznam všech prostředků komunikačních služeb Azure v daném předplatném, použijte následující příkaz:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Chcete-li zobrazit seznam všech informací o daném prostředku, použijte následující příkaz:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
