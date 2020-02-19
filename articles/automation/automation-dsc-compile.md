---
title: Kompilace konfigurací v konfiguraci stavu Azure Automation
description: Tento článek popisuje, jak zkompilovat konfigurace požadovaných stavů (DSC) pro Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462118"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilace konfigurací DSC v konfiguraci stavu Azure Automation

Konfigurace Konfigurace požadovaných stavů (DSC) můžete zkompilovat dvěma způsoby pomocí konfigurace stavu Azure Automation: v Azure a ve Windows PowerShellu. Následující tabulka vám pomůže určit, kdy použít tuto metodu na základě charakteristik jednotlivých vlastností:

- Služba kompilace konfigurace stavu Azure
  - Metoda začátečník s interaktivním uživatelským rozhraním
   - Snadné sledování stavu úlohy

- Windows PowerShell
  - Volání z Windows PowerShellu na místní pracovní stanici nebo službu sestavení
  - Integrace s vývojovým testovacím kanálem
  - Zadejte komplexní hodnoty parametrů
  - Práce se škálováním uzlů a dat mimo uzel
  - Výrazné zlepšení výkonu

Podrobnosti o kompilaci najdete v tématu [Konfigurace požadovaného stavu pomocí šablon Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilace konfigurace DSC v konfiguraci stavu Azure

### <a name="portal"></a>Portál

1. V účtu Automation klikněte na **Konfigurace stavu (DSC)** .
1. Klikněte na kartu **Konfigurace** a potom klikněte na název konfigurace pro zkompilování.
1. Klikněte na **kompilovat**.
1. Pokud konfigurace nemá žádné parametry, zobrazí se výzva k potvrzení, že ji chcete zkompilovat. Pokud má konfigurace parametry, otevře se okno **Konfigurace kompilace** , ve kterém můžete zadat hodnoty parametrů.
1. Stránka úlohy kompilace je otevřená, abyste mohli sledovat stav úlohy kompilace. Tuto stránku můžete také použít ke sledování konfigurací uzlů (konfigurační dokumenty MOF), které jsou umístěny na serveru Pull konfigurace stavu Azure Automation.

### <a name="azure-powershell"></a>Azure PowerShell

K zahájení kompilace prostředí Windows PowerShell můžete použít rutinu [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) . Následující vzorový kód zahájí kompilaci konfigurace DSC s názvem SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** vrátí objekt úlohy kompilace, který můžete použít ke sledování jeho stavu. Pak můžete použít tento objekt úlohy kompilace s příkazem [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) k určení stavu úlohy kompilace a s příkazem [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) k zobrazení datových proudů (výstup). Následující vzorový kód spustí kompilaci konfigurace SampleConfig, počká, až se dokončí, a pak zobrazí své streamy.

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

Deklarace parametru v konfiguracích DSC, včetně typů parametrů a vlastností, funguje stejně jako v Azure Automation Runbooky. Další informace o parametrech Runbooku najdete [v tématu Spuštění Runbooku v Azure Automation](automation-starting-a-runbook.md) .

Následující příklad používá dva parametry **s názvem název** **vlastnosti a k**určení hodnot vlastností v konfiguraci uzlu **ParametersExample. Sample** , vygenerované při kompilaci.

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

Konfigurace DSC, které používají základní parametry, můžete zkompilovat na portálu konfigurace stavu Azure Automation nebo pomocí Azure PowerShell:

#### <a name="portal"></a>Portál

Na portálu můžete zadat hodnoty parametrů po kliknutí na **kompilovat**.

![Parametry kompilace konfigurace](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Prostředí PowerShell vyžaduje parametry v [zatřiďovací](/powershell/module/microsoft.powershell.core/about/about_hash_tables) tabulce, kde klíč odpovídá názvu parametru, a hodnota se rovná hodnotě parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informace o předávání PSCredentials jako parametrů najdete v části [assety s přihlašovacími](#credential-assets) údaji níže.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilovat konfigurace obsahující složené prostředky v Azure Automation

Funkce **kompozitních prostředků** umožňuje používat konfigurace DSC jako vnořené prostředky v rámci konfigurace. To umožňuje použít pro jeden prostředek více konfigurací. Další informace o složených prostředcích najdete v tématu [složené prostředky: použití konfigurace DSC jako prostředku](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> V případě konfigurací, které obsahují složené prostředky ke správnému kompilování, je třeba nejprve zajistit, aby všechny prostředky DSC, na kterých se kompozitní spoléhá, byly importovány do Azure Automation.

Přidání složeného prostředku DSC se neliší od přidání modulu PowerShellu do Azure Automation. Tento proces je popsán v části [Správa modulů v Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Správa ConfigurationData při kompilování konfigurací v Azure Automation

Funkce **ConfigurationData** umožňuje oddělit strukturální konfiguraci z libovolné konfigurace specifické pro prostředí při použití prostředí PowerShell DSC. Další informace o ConfigurationData najdete [v tématu oddělení "co" od "Where" v prostředí POWERSHELL DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) .

> [!NOTE]
> ConfigurationData můžete použít při kompilaci v konfiguraci stavu Azure Automation pomocí Azure PowerShell, ale ne v Azure Portal.

Následující příklad konfigurace DSC používá ConfigurationData pomocí klíčových slov $ConfigurationData a $AllNodes. Pro tento příklad potřebujete také [modul xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) :

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

Předchozí konfiguraci DSC můžete zkompilovat pomocí prostředí Windows PowerShell. Následující skript přidá dvě konfigurace uzlů do služby vyžádané konfigurace stavu Azure Automation: ConfigurationDataSample. MyVM1 a ConfigurationDataSample. MyVM3.

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

Odkazy na prostředky jsou stejné v konfiguraci Azure Automation stavech i v sadách Runbook. Další informace najdete v následujících tématech:

- [Certifikáty](automation-certificates.md)
- [Připojení](automation-connections.md)
- [Přihlašovací údaje](automation-credentials.md)
- [Proměnné](automation-variables.md)

#### <a name="credential-assets"></a>Assety přihlašovacích údajů

Konfigurace DSC v Azure Automation můžou odkazovat na assety přihlašovacích údajů automatizace pomocí rutiny **Get-AutomationPSCredential** . Pokud má konfigurace parametr, který má typ PSCredential, pak můžete použít **Get-AutomationPSCredential** předáním názvu řetězce Azure Automation prostředku přihlašovacích údajů, abyste mohli přihlašovací údaje načíst. Pak můžete použít tento objekt pro parametr, který vyžaduje objekt PSCredential. Na pozadí se Azure Automation Asset přihlašovacích údajů s tímto názvem načte a předává do konfigurace. Následující příklad ukazuje tuto operaci v akci.

Zachování zabezpečení přihlašovacích údajů v konfiguraci uzlu vyžaduje šifrování přihlašovacích údajů v souboru MOF konfigurace uzlu. Je potřeba informovat prostředí PowerShell DSC konkrétně, že má oprávnění k vytvoření výstupních přihlašovacích údajů v prostém textu během generování konfigurace MOF uzlu. Prostředí PowerShell DSC neví, že Azure Automation zašifruje celý soubor MOF po jeho generování prostřednictvím úlohy kompilace.

Pokud chcete udělit oprávnění PowerShellu DSC pro výstup přihlašovacích údajů v nešifrovaném textu v konfiguraci vygenerovaného uzlu soubory MOF pomocí konfiguračních dat, předejte `PSDscAllowPlainTextPassword = $true`. Tyto informace můžete předat prostřednictvím ConfigurationData pro každý název bloku uzlu, který se zobrazí v konfiguraci DSC a používá přihlašovací údaje.

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

Předchozí konfiguraci DSC můžete zkompilovat pomocí PowerShellu. 

Následující prostředí PowerShell přidá dvě konfigurace uzlů do konfigurace stavu Azure Automation vyžádaného serveru: CredentialSample. MyVM1 a CredentialSample. MyVM2.

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

>[!NOTE]
>Po dokončení kompilace se může zobrazit chyba oznamující, že **modul Microsoft. PowerShell. Management nebyl naimportován, protože modul snap-in Microsoft. PowerShell. Management už je naimportovaný.** Toto upozornění můžete ignorovat.

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>Kompilace konfigurace DSC ve Windows PowerShellu

Proces kompilace konfigurací DSC ve Windows PowerShellu je zahrnutý v dokumentaci k PowerShellu DSC [zápis, kompilace a použití konfigurace](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Proces můžete spustit z pracovní stanice pro vývojáře nebo ze služby sestavení, jako je například [Azure DevOps](https://dev.azure.com).
Pak můžete importovat soubory MOF pro výsledné konfigurace uzlů přímo do služby Azure State Configuration. 

>[!NOTE]
>Konfigurační soubor uzlu nesmí být větší než 1 MB, aby bylo možné ho importovat do Azure Automation.

Můžete také importovat konfigurace uzlů (soubory MOF), které byly zkompilovány mimo Azure. Tento přístup má několik výhod, včetně výkonu a spolehlivosti.

Kompilace v prostředí Windows PowerShell poskytuje možnost podepisování obsahu konfigurace s agentem DSC ověřujícím místní konfiguraci podepsaného uzlu na spravovaném uzlu. Ověřování zajišťuje, že konfigurace, která se aplikuje na uzel, pochází z autorizovaného zdroje. Další informace o konfiguracích podpisových uzlů najdete v tématu [vylepšení v WMF 5,1 – jak podepsat konfiguraci a modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu do Azure Portal

1. V účtu Automation klikněte v části **Správa konfigurace**na **Konfigurace stavu (DSC)** .
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a pak klikněte na **+ Přidat**.
1. Na stránce Import klikněte na ikonu složky vedle textového pole **konfigurační soubor uzlu** a vyhledejte v místním počítači konfigurační soubor uzlu (MOF).

   ![Vyhledat místní soubor](./media/automation-dsc-compile/import-browse.png)

1. Do textového pole **název konfigurace** zadejte název. Tento název musí odpovídat názvu konfigurace, ze které byla zkompilována konfigurace uzlu.
1. Klikněte na tlačítko **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Import konfigurace uzlu s Azure PowerShell

Pomocí rutiny [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) můžete importovat konfiguraci uzlu do svého účtu Automation.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/az.automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí konfigurace Azure Automation stavu a čokolády](automation-dsc-cd-chocolatey.md).
