---
title: Kompilovat konfigurace DSC v konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak zkompilovat konfigurace požadovaných stavů (DSC) pro Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b45aad71e04418c7c7dda4fc3f0c84a5fe99ecdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896338"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilovat konfigurace DSC v konfiguraci stavu Azure Automation

Konfigurace Konfigurace požadovaných stavů (DSC) můžete zkompilovat v konfiguraci Azure Automation stavů následujícími způsoby:

- Služba kompilace konfigurace stavu Azure
  - Metoda začátečník s interaktivním uživatelským rozhraním
   - Snadné sledování stavu úlohy

- Windows PowerShell
  - Volání z Windows PowerShellu na místní pracovní stanici nebo službu sestavení
  - Integrace s vývojovým testovacím kanálem
  - Zadejte komplexní hodnoty parametrů
  - Práce se škálováním uzlů a dat mimo uzel
  - Výrazné zlepšení výkonu

Pro nabízení konfigurací do virtuálních počítačů Azure můžete také použít šablony Azure Resource Manager s rozšířením konfigurace požadovaného stavu Azure (DSC). Rozšíření Azure DSC využívá architekturu agenta virtuálního počítače Azure k doručování, přijetí a vytváření sestav o konfiguracích DSC spuštěných na virtuálních počítačích Azure. Podrobnosti o kompilaci pomocí Azure Resource Manager šablon naleznete v části [požadované rozšíření konfigurace stavu pomocí šablon Azure Resource Manager](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilace konfigurace DSC v konfiguraci stavu Azure

### <a name="portal"></a>Portál

1. V účtu Automation klikněte na **Konfigurace stavu (DSC)**.
1. Klikněte na kartu **Konfigurace** a potom klikněte na název konfigurace pro zkompilování.
1. Klikněte na **kompilovat**.
1. Pokud konfigurace nemá žádné parametry, budete vyzváni k potvrzení, jestli ho chcete zkompilovat. Pokud má konfigurace parametry, otevře se okno **Konfigurace kompilace** , ve kterém můžete zadat hodnoty parametrů.
1. Stránka úlohy kompilace je otevřena, abyste mohli sledovat stav úlohy kompilace. Na této stránce můžete také sledovat konfigurace uzlů (konfigurační dokumenty MOF), které jsou umístěny na serveru Pull konfigurace stavu Azure Automation.

### <a name="azure-powershell"></a>Azure PowerShell

K zahájení kompilace prostředí Windows PowerShell můžete použít rutinu [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) . Následující vzorový kód zahájí kompilaci konfigurace DSC s názvem **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` Vrátí objekt úlohy kompilace, který můžete použít ke sledování stavu úlohy. Pak můžete použít tento objekt úlohy kompilace s příkazem [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) k určení stavu úlohy kompilace a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) k zobrazení datových proudů (výstup). Následující ukázka spustí kompilaci konfigurace SampleConfig, počká, až se dokončí, a pak zobrazí své streamy.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Deklarovat základní parametry

Deklarace parametru v konfiguracích DSC, včetně typů parametrů a vlastností, funguje stejně jako v Azure Automation Runbooky. Další informace o parametrech Runbooku najdete [v tématu Spuštění Runbooku v Azure Automation](./start-runbooks.md) .

V následujícím příkladu jsou `FeatureName` použity `IsPresent` parametry a k určení hodnot vlastností v konfiguraci uzlu **ParametersExample. Sample** , vygenerované při kompilaci.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Konfigurace DSC, které používají základní parametry, můžete zkompilovat na portálu konfigurace stavu Azure Automation nebo pomocí Azure PowerShell.

#### <a name="portal"></a>Portál

Na portálu můžete zadat hodnoty parametrů po kliknutí na **kompilovat**.

![Parametry kompilace konfigurace](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Prostředí PowerShell vyžaduje parametry v [zatřiďovací](/powershell/module/microsoft.powershell.core/about/about_hash_tables)tabulce, kde klíč odpovídá názvu parametru a hodnota se rovná hodnotě parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informace o předávání `PSCredential` objektů jako parametrů najdete v tématu [Assety přihlašovacích údajů](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilovat konfigurace obsahující složené prostředky v Azure Automation

Funkce **kompozitních prostředků** umožňuje používat konfigurace DSC jako vnořené prostředky v rámci konfigurace. Tato funkce umožňuje použití více konfigurací na jeden prostředek. Další informace o složených prostředcích najdete v tématu [složené prostředky: použití konfigurace DSC jako prostředku](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> Aby se konfigurace obsahující složené prostředky správně zkompiluje, musíte nejdřív naimportovat do Azure Automation všech prostředků DSC, na kterých se spoléhají. Přidání složeného prostředku DSC se neliší od přidání žádného modulu prostředí PowerShell do Azure Automation. Tento proces je popsán v dokumentaci [Správa modulů v Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Správa ConfigurationData při kompilování konfigurací v Azure Automation

`ConfigurationData` je vestavěný parametr DSC, který umožňuje oddělit strukturální konfiguraci z libovolné konfigurace specifické pro prostředí při použití prostředí PowerShell DSC. Další informace najdete v tématu [oddělení "co" od "Where" v prostředí POWERSHELL DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Při kompilaci v konfiguraci stavu Azure Automation můžete použít `ConfigurationData` v Azure PowerShell, ale ne v Azure Portal.

Následující příklad konfigurace DSC se používá `ConfigurationData` pomocí `$ConfigurationData` `$AllNodes` klíčových slov a. Pro tento příklad potřebujete také [modul xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) .

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Předchozí konfiguraci DSC můžete zkompilovat pomocí prostředí Windows PowerShell. Následující skript přidá dvě konfigurace uzlů do služby vyžádané konfigurace stavu Azure Automation: **ConfigurationDataSample. MyVM1** a **ConfigurationDataSample. MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Práce s prostředky v Azure Automation během kompilace

Odkazy na prostředky jsou stejné v konfiguraci Azure Automation stavech i v sadách Runbook. Další informace najdete v následujících článcích:

- [Certifikáty](./shared-resources/certificates.md)
- [Připojení](automation-connections.md)
- [Přihlašovací údaje](./shared-resources/credentials.md)
- [Proměnné](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Assety přihlašovacích údajů

Konfigurace DSC v Azure Automation můžou odkazovat na assety přihlašovacích údajů automatizace pomocí `Get-AutomationPSCredential` rutiny. Pokud má konfigurace parametr, který určuje `PSCredential` objekt, použijte `Get-AutomationPSCredential` předáním názvu řetězce Azure Automation prostředku přihlašovacího údaje do rutiny pro načtení přihlašovacích údajů. Pak použijte tento objekt pro parametr, který vyžaduje `PSCredential` objekt. Na pozadí se Azure Automation Asset přihlašovacích údajů s tímto názvem načte a předává do konfigurace. Následující příklad ukazuje tento scénář v akci.

Zabezpečení přihlašovacích údajů v konfiguracích uzlů (konfigurační dokumenty MOF) vyžaduje šifrování přihlašovacích údajů v souboru MOF konfigurace uzlu. V současné době musíte poskytnout oprávnění prostředí PowerShell DSC pro výstup přihlašovacích údajů v prostém textu během generování konfigurace MOF uzlu. Prostředí PowerShell DSC neví, že Azure Automation zašifruje celý soubor MOF po jeho generování prostřednictvím úlohy kompilace.

Můžete říct prostředí PowerShell DSC, že je v pořádku, aby se přihlašovací údaje vygenerovaly v podobě prostého textu v konfiguraci generovaného uzlu soubory MOF pomocí konfiguračních dat. Měli byste předat `PSDscAllowPlainTextPassword = $true` přes `ConfigurationData` každý název bloku uzlu, který se zobrazí v konfiguraci DSC a používá přihlašovací údaje.

Následující příklad ukazuje konfiguraci DSC, která používá prostředek přihlašovacích údajů Automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Předchozí konfiguraci DSC můžete zkompilovat pomocí PowerShellu. Následující kód PowerShellu přidá dvě konfigurace uzlů do konfigurace stavu Azure Automation vyžádaného serveru: **CredentialSample. MyVM1** a **CredentialSample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Po dokončení kompilace se může zobrazit chybová zpráva `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` . tuto zprávu můžete bez obav ignorovat.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilace konfigurace DSC ve Windows PowerShellu

Proces kompilace konfigurací DSC ve Windows PowerShellu je zahrnutý v dokumentaci k PowerShellu DSC [zápis, kompilace a použití konfigurace](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Tento proces můžete spustit z pracovní stanice pro vývojáře nebo ze služby sestavení, jako je například [Azure DevOps](https://dev.azure.com). Pak můžete importovat soubory MOF vytvořené kompilací konfigurace do služby Azure State Configuration.

Kompilace v prostředí Windows PowerShell také nabízí možnost podepisování obsahu konfigurace. Agent DSC ověřuje konfiguraci podepsaného uzlu místně na spravovaném uzlu. Ověřování zajišťuje, že konfigurace použitá pro uzel pochází z autorizovaného zdroje.

Můžete také importovat konfigurace uzlů (soubory MOF), které byly zkompilovány mimo Azure. Import zahrnuje kompilaci z pracovní stanice pro vývojáře nebo do služby, jako je například [Azure DevOps](https://dev.azure.com). Tento přístup má několik výhod, včetně výkonu a spolehlivosti.

> [!NOTE]
> Konfigurační soubor uzlu nesmí být větší než 1 MB, aby bylo možné Azure Automation importovat.

Další informace o podepisování konfigurací uzlů najdete v tématu [vylepšení v WMF 5,1 – jak podepsat konfiguraci a modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu do Azure Portal

1. Ve svém účtu Automation klikněte v části **Správa konfigurace** na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **Přidat**.
1. Na stránce Import klikněte na ikonu složky vedle pole **konfigurační soubor uzlu** a vyhledejte v místním počítači soubor MOF konfigurace uzlu.

   ![Vyhledat místní soubor](./media/automation-dsc-compile/import-browse.png)

1. Do pole **název konfigurace** zadejte název. Tento název musí odpovídat názvu konfigurace, ze které byla zkompilována konfigurace uzlu.
1. Klikněte na **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Import konfigurace uzlu s Azure PowerShell

Pomocí rutiny [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) můžete importovat konfiguraci uzlu do svého účtu Automation.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu v kanálu průběžného nasazování najdete v tématu [Nastavení průběžného nasazování pomocí čokolády](automation-dsc-cd-chocolatey.md).
