---
title: Vytvoření sdíleného prostředí Integration runtime v místním prostředí pomocí PowerShellu
description: Naučte se, jak vytvořit sdílený modul runtime integrace v místním prostředí v Azure Data Factory, aby k prostředí Integration runtime měl přístup více datových továrn.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 8734247a913bdf6a44a9156f6f87705b618f7228
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632885"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Vytvoření sdíleného prostředí Integration runtime v místním prostředí v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V této příručce se dozvíte, jak vytvořit sdílený modul runtime integrace v místním prostředí v Azure Data Factory. Pak můžete použít sdílený modul runtime integrace v místním prostředí v jiném objektu pro vytváření dat.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Vytvoření sdíleného prostředí IR v místním prostředí pomocí Azure Data Factoryho uživatelského rozhraní

Pokud chcete vytvořit sdílený prostředí IR v místním prostředí pomocí Azure Data Factory uživatelského rozhraní, můžete provést následující kroky:

1. V místním prostředí IR, které se má sdílet, vyberte **udělit oprávnění jinému objektu pro vytváření dat** a na stránce "nastavení modulu runtime integrace" vyberte datovou továrnu, ve které chcete vytvořit propojený IR.
      
    ![Tlačítko pro udělení oprávnění na kartě sdílení](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Všimněte si a zkopírujte výše uvedené ID prostředku prostředí IR v místním prostředí, které se má sdílet.
         
3. V objektu pro vytváření dat, do kterého byla udělena oprávnění, vytvořte nové prostředí IR v místním prostředí (propojeno) a zadejte ID prostředku.
      
    ![Tlačítko pro vytvoření prostředí Integration runtime v místním prostředí](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Tlačítko pro vytvoření propojeného prostředí Integration runtime v místním prostředí](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Pole pro název a ID prostředku](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Vytvoření sdíleného prostředí IR s místním hostováním pomocí Azure PowerShell

Pokud chcete vytvořit sdílený prostředí IR v místním prostředí pomocí Azure PowerShell, můžete provést následující kroky: 
1. Vytvoření datové továrny 
1. Vytvořte místní prostředí Integration Runtime.
1. Sdílejte modul Integration runtime v místním prostředí s ostatními objekty pro vytváření dat.
1. Vytvořte propojený modul runtime integrace.
1. Odvolejte sdílení.

### <a name="prerequisites"></a>Předpoklady 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure** . Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/). 

- **Azure PowerShell.** Postupujte podle pokynů v tématu [instalace Azure PowerShell ve Windows pomocí PowerShellGet](/powershell/azure/install-az-ps). Pomocí PowerShellu spustíte skript pro vytvoření prostředí Integration runtime v místním prostředí, které je možné sdílet s jinými datovými továrnami. 

> [!NOTE]  
> Seznam oblastí Azure, ve kterých je Data Factory aktuálně k dispozici, vyberte oblasti, které vás zajímají na  [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell.

1. Vytvořte proměnné. Zkopírujte a vložte následující skript. Nahraďte proměnné, jako je například **Subscription** a **ResourceGroupName** , skutečnými hodnotami: 

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

1. Přihlaste se a vyberte předplatné. Přidejte do skriptu následující kód pro přihlášení a výběr vašeho předplatného Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Vytvořte skupinu prostředků a datovou továrnu.

    > [!NOTE]  
    > Tento krok je volitelný. Pokud již máte datovou továrnu, přeskočte tento krok. 

    Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění WestEurope: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Spuštěním následujícího příkazu vytvořte datovou továrnu: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

> [!NOTE]  
> Tento krok je volitelný. Pokud již máte místní prostředí Integration runtime, které chcete sdílet s jinými datovými továrnami, tento krok přeskočte.

Spusťte následující příkaz pro vytvoření místního prostředí Integration Runtime:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Získání ověřovacího klíče prostředí Integration runtime a registrace uzlu

Spusťte následující příkaz, který získá ověřovací klíč pro místní prostředí Integration Runtime:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpověď obsahuje ověřovací klíč pro tento modul runtime integrace v místním prostředí. Tento klíč použijete při registraci uzlu Integration runtime.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalace a registrace místního prostředí Integration runtime

1. Stáhněte si instalační program modulu Integration runtime v místním prostředí z [Azure Data Factory Integration runtime](https://aka.ms/dmg).

2. Spusťte instalační program a nainstalujte místně hostovanou integraci do místního počítače.

3. Zaregistrujte novou samoobslužnou integraci s ověřovacím klíčem, který jste získali v předchozím kroku.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Sdílení prostředí Integration runtime v místním prostředí s jinou datovou továrnou

#### <a name="create-another-data-factory"></a>Vytvoření jiné datové továrny

> [!NOTE]  
> Tento krok je volitelný. Pokud již máte datovou továrnu, kterou chcete sdílet, přeskočte tento krok. Aby bylo možné přidávat nebo odebírat přiřazení rolí do jiné datové továrny, musíte mít `Microsoft.Authorization/roleAssignments/write` oprávnění a `Microsoft.Authorization/roleAssignments/delete` , jako je například správce nebo [vlastník](../role-based-access-control/built-in-roles.md#owner) [přístupu uživatele](../role-based-access-control/built-in-roles.md#user-access-administrator) .

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Udělit oprávnění

Udělte oprávnění k objektu pro vytváření dat, která potřebují přístup k místnímu prostředí Integration runtime, které jste vytvořili a zaregistrovali.

> [!IMPORTANT]  
> Tento krok nepřeskakujte.

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Vytvoření propojeného prostředí Integration runtime v místním prostředí

Spuštěním následujícího příkazu vytvořte propojený modul runtime integrace v místním prostředí:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nyní můžete použít tento propojený modul runtime integrace v jakékoli propojené službě. Propojený modul runtime integrace používá ke spouštění aktivit modul Shared Integration runtime.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odvolat sdílení modulu Integration runtime z datové továrny

Pokud chcete odvolat přístup k datové továrně z prostředí Shared Integration runtime, spusťte následující příkaz:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Chcete-li odebrat existující propojený modul runtime integrace, spusťte následující příkaz pro modul Shared Integration Runtime:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Další kroky

- Projděte si [koncept prostředí Integration runtime v Azure Data Factory](./concepts-integration-runtime.md).

- Naučte se [vytvořit místní prostředí Integration runtime v Azure Portal](./create-self-hosted-integration-runtime.md).