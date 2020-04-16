---
title: Vytvoření sdíleného prostředí integrace s vlastním hostitelem pomocí prostředí PowerShell
description: Zjistěte, jak vytvořit prostředí pro sdílenou vlastní integraci v Azure Data Factory, aby k němu měly přístup více datových továren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: cabdb45467f71749184c5f9a6a112242a82d618b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416609"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Vytvoření sdíleného runtime integrace s vlastním hostitelem v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tato příručka ukazuje, jak vytvořit sdílený runtime integrace s vlastním hostitelem v Azure Data Factory. Potom můžete použít sdílený prostředí integrace s vlastním hostitelem v jiné datové továrně.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Vytvoření sdíleného infračerveného počítače s vlastním hostitelem pomocí uzly Azure Data Factory

Pokud chcete pomocí uzulinového řízení Azure Data Factory vytvořit sdílenou infračervený přenos s vlastním hostitelem, můžete provést následující kroky:

1. V infračerveného přenosu hostovaném samostatně, který má být sdílen, udělte oprávnění datové továrně, ve které chcete vytvořit propojenou infračervený přenos.
      
    ![Tlačítko pro udělení oprávnění na kartě Sdílení](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Výběry pro přiřazení oprávnění](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Poznamenejte si ID prostředku samoobslužné infračervené hodovaného souboru, který má být sdílen.
      
   ![Umístění ID prostředku](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. V datové továrně, ke které byla udělena oprávnění, vytvořte novou infračervené reziduum (propojenou) s vlastním hostitelem a zadejte ID prostředku.
      
   ![Tlačítko pro vytvoření propojeného prostředí pro vlastní integraci](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Pole pro název a ID prostředku](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Vytvoření sdíleného infračerveného počítače s vlastním hostitelem pomocí Azure PowerShellu

Pokud chcete pomocí Azure PowerShellu vytvořit sdílenou infračervený přenos s vlastním hostitelem, můžete provést následující kroky: 
1. Vytvoření datové továrny 
1. Vytvořte místní prostředí Integration Runtime.
1. Sdílejte runtime integrace s vlastním hostitelem s jinými datovými továrnami.
1. Vytvořte propojený integrační runtime.
1. Odvolejte sdílení.

### <a name="prerequisites"></a>Požadavky 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 

- **Azure PowerShell**. Postupujte podle pokynů v [části Instalace Azure PowerShellu ve Windows s PowerShellem](https://docs.microsoft.com/powershell/azure/install-az-ps). Pomocí prostředí PowerShell spustíte skript k vytvoření runtime integrace s vlastním hostitelem, který lze sdílet s jinými datovými továrnami. 

> [!NOTE]  
> Seznam oblastí Azure, ve kterých je data factory momentálně dostupná, vyberte oblasti, které vás zajímají [v produktech dostupných podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell.

1. Vytvořte proměnné. Zkopírujte a vložte následující skript. Nahraďte proměnné, například **Název předplatného** a **Název_zdroje,** skutečnými hodnotami: 

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

1. Přihlaste se a vyberte předplatné. Přidejte do skriptu následující kód, abyste se přihlásili a vyberte předplatné Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Vytvořte skupinu prostředků a továrnu dat.

    > [!NOTE]  
    > Tento krok je volitelný. Pokud již máte data factory, přeskočte tento krok. 

    Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu `myResourceGroup` prostředků s názvem v umístění WestEurope: 

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
> Tento krok je volitelný. Pokud již máte runtime integrace s vlastním hostitelem, který chcete sdílet s jinými továrnami na data, tento krok přeskočte.

Chcete-li vytvořit runtime integrace s vlastním hostitelem, spusťte následující příkaz:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Získání ověřovacího klíče za běhu integrace a registrace uzlu

Chcete-li získat ověřovací klíč pro prostředí runtime integrace s vlastním hostitelem:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpověď obsahuje ověřovací klíč pro tento vlastní hostovaný integrační runtime. Tento klíč se používá při registraci uzlu runtime integrace.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalace a registrace prostředí runtime integrace s vlastním hostitelem

1. Stáhněte si runtime instalační program integrace s vlastním hostitelem z [prostředí Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Spusťte instalační program a nainstalujte integraci s vlastním hostitelem do místního počítače.

3. Zaregistrujte novou samoobslužnou integraci s ověřovacím klíčem, který jste načetli v předchozím kroku.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Sdílení prostředí runtime integrace s vlastním hostitelem s jinou datovou továrnou

#### <a name="create-another-data-factory"></a>Vytvoření jiné datové továrny

> [!NOTE]  
> Tento krok je volitelný. Pokud již máte data factory, které chcete sdílet s, přeskočte tento krok.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Udělit oprávnění

Udělte oprávnění datové továrně, která potřebuje přístup k prostředí runtime integrace s vlastním hostitelem, který jste vytvořili a zaregistrovali.

> [!IMPORTANT]  
> Tento krok nepřeskakejte!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Vytvoření propojeného prostředí runtime s vlastním hostitelem integrace

Chcete-li vytvořit propojený runtime integrace s vlastním hostitelem, spusťte následující příkaz:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nyní můžete použít tento vázaný integrační runtime v libovolné propojené službě. Vázaný integrační runtime používá ke spuštění aktivit za běhu sdílené integrace.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odvolat sdílení za běhu integrace z datové továrny

Chcete-li odvolat přístup datové továrny z prostředí sdílené integrace, spusťte následující příkaz:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Chcete-li odebrat existující propojený integrační běh, spusťte následující příkaz proti zaběhu sdílené integrace:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Další kroky

- Zkontrolujte [koncepty runtime integrace v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Přečtěte si, jak [na webu Azure Portal vytvořit runtime integrace](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)s vlastním hostitelem .
