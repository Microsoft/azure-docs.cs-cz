---
title: Vytvořit sdílené místní prostředí integration runtime ve službě Azure Data Factory pomocí Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit sdílené místní prostředí integration runtime ve službě Azure Data Factory, aby více datových továren můžete přístup k modulu integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: 76b0d1728b46834270e9a5b53709de62b4a8b3fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429374"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Vytvořit sdílené místní prostředí integration runtime ve službě Azure Data Factory pomocí Powershellu

Tento podrobný návod ukazuje, jak vytvořit sdílené místní prostředí integration runtime ve službě Azure Data Factory pomocí Azure Powershellu. Pak můžete použít sdílené místní prostředí integration runtime v jiné služby data factory. V tomto kurzu provedete následující kroky: 

1. Vytvoření datové továrny 
1. Vytvořte místní prostředí Integration Runtime.
1. Sdílejte místní prostředí integration runtime pomocí jiných datových továren.
1. Vytvoření propojené integration runtime.
1. Odvolat, jejich sdílení.

## <a name="prerequisites"></a>Požadavky 

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

- **Azure PowerShell**. Postupujte podle pokynů v [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.11.0). Pomocí Powershellu spusťte skript pro vytvoření místního prostředí integration runtime, který je možné sdílet s další datové továrny. 

> [!NOTE]  
> Seznam oblastí Azure, ve kterých je momentálně dostupná Data Factory, vyberte oblasti, které vás zajímají na [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell.

1. Vytvořte proměnné. Zkopírujte a vložte následující skript. Nahraďte proměnné, jako například **SubscriptionName** a **ResourceGroupName**, skutečnými hodnotami: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Přihlaste se a vyberte předplatné. Přidejte následující kód do skriptu pro přihlášení a výběr vašeho předplatného Azure:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Vytvořte skupinu prostředků a data factory.

    > [!NOTE]  
    > Tento krok je volitelný. Pokud už máte datovou továrnu, tento krok přeskočte. 

    Vytvoření [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0) příkazu. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` WestEurope umístění: 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Spuštěním následujícího příkazu vytvořte datovou továrnu: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

> [!NOTE]  
> Tento krok je volitelný. Pokud již máte místní prostředí integration runtime, který chcete sdílet s další datové továrny, tento krok přeskočte.

Spusťte následující příkaz k vytvoření místního prostředí integration runtime:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Získat ověřovací klíč modulu runtime integrace a zaregistrujte uzel

Spuštěním následujícího příkazu získejte ověřovací klíč pro místní prostředí integration runtime:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpověď obsahuje ověřovací klíč pro tento modul runtime integrace v místním prostředí. Tento klíč použijete při registraci uzlu integration runtime.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalace a registrace místního prostředí integration runtime

1. Stažení instalačního programu místní prostředí integration runtime z [modulu Runtime integrace pro Azure Data Factory](https://aka.ms/dmg).

2. Spusťte instalační program pro instalaci místní prostředí integration na místním počítači.

3. Zaregistrujte novou místní prostředí integration s ověřovacím klíčem, který jste získali v předchozím kroku.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Sdílet místní prostředí integration runtime pomocí jiné služby data factory

### <a name="create-another-data-factory"></a>Vytvoření další datové továrny

> [!NOTE]  
> Tento krok je volitelný. Pokud už máte služby data factory, který chcete sdílet, tento krok přeskočte.

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Udělit oprávnění

Udělení oprávnění k datové továrně, který potřebuje přístup k místní prostředí integration runtime vytvořen a zaregistrován.

> [!IMPORTANT]  
> Tento krok není přeskočit!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Vytvořit propojené prostředí integration runtime

Spuštěním následujícího příkazu vytvořte propojenou místní prostředí integration runtime:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Tento modul runtime integrace propojené teď můžete všechny propojené služby. Propojené prostředí IR použije sdílené prostředí IR pro spuštění aktivity.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odvolat prostředí integration runtime sdílení ze služby data factory

K odvolání přístupu k objektu pro vytváření dat ze sdílené integration runtime, spusťte následující příkaz:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Pokud chcete odebrat existující propojená prostředí IR, spusťte následující příkaz sdílené integration runtime:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Další postup

- Kontrola [koncepty runtime integrace v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Zjistěte, jak [vytvořit místní prostředí integration runtime na webu Azure Portal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
