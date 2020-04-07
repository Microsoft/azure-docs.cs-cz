---
title: Kompilace konfigurací v konfiguraci stavu automatizace Azure
description: This article describes how to compile Desired State Configuration (DSC) configurations for Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b366777cce4dd4212a2e3ec0cdcd180e0d7164f4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757188"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilace konfigurací DSC v konfiguraci stavu automatizace Azure

You can compile Desired State Configuration (DSC) configurations in Azure Automation State Configuration in the following ways:

- Služba kompilace konfigurace stavu Azure
  - Metoda pro začátečníky s interaktivním uživatelským rozhraním
   - Snadné sledování stavu úlohy

- Windows PowerShell
  - Volání z prostředí Windows PowerShell na místní pracovní stanici nebo sestavení služby
  - Integrace s testovacím kanálem pro vývoj
  - Zadat komplexní hodnoty parametrů
  - Práce s daty uzlů a dat bez uzlů ve velkém měřítku
  - Výrazné zlepšení výkonu

K nabízení konfigurací virtuálním počítačům Azure můžete taky použít šablony Azure Resource Manager u rozšíření Azure Desired State Configuration (DSC). Rozšíření Azure DSC používá rozhraní agenta virtuálních počítačů Azure k doručování, uzákonění a sestavování konfigurací DSC spuštěných na virtuálních počítačích Azure. Podrobnosti kompilace pomocí šablon Azure Resource Manager najdete v [tématu rozšíření Konfigurace požadovaného stavu se šablonami Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilace konfigurace DSC v konfiguraci stavu Azure

### <a name="portal"></a>Portál

1. V účtu Automatizace klikněte na **stav konfigurace (DSC)**.
1. Klikněte na kartu **Konfigurace** a potom klikněte na název konfigurace, který chcete zkompilovat.
1. Klepněte na tlačítko **Kompilovat**.
1. Pokud konfigurace nemá žádné parametry, budete vyzváni k potvrzení, zda chcete zkompilovat. Pokud má konfigurace parametry, otevře se okno **Konfigurace kompilace,** takže můžete zadat hodnoty parametrů.
1. Stránka Úloha kompilace je otevřena, takže můžete sledovat stav úlohy kompilace. Na této stránce můžete také sledovat konfigurace uzlů (dokumenty konfigurace MOF) umístěné na serveru pro vyžádat konfiguraci stavu Azure Automation.

### <a name="azure-powershell"></a>Azure PowerShell

[Pomocí funkce Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) můžete začít s kompilací pomocí prostředí Windows PowerShell. Následující ukázkový kód zahájí kompilaci konfigurace DSC s názvem SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** vrátí objekt úlohy kompilace, který můžete použít ke sledování stavu úlohy. Potom můžete použít tento objekt úlohy kompilace s [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) k určení stavu úlohy kompilace a [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) pro zobrazení jeho datových proudů (výstup). Následující ukázka spustí kompilaci sampleconfig konfigurace, čeká, dokud nebude dokončena a potom zobrazí jeho datové proudy.

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

Deklarace parametrů v konfiguracích DSC, včetně typů parametrů a vlastností, funguje stejně jako v runbookech Azure Automation. Další informace o parametrech runbooku najdete [v tématu Spuštění runbooku v Azure Automation.](automation-starting-a-runbook.md)

Následující příklad používá dva parametry s názvem *FeatureName* a *IsPresent*, k určení hodnot vlastností v konfiguraci uzlu ParametersExample.sample, které byly generovány během kompilace.

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

Můžete zkompilovat konfigurace DSC, které používají základní parametry na portálu Azure Automation State Configuration portal nebo s Azure PowerShell.

#### <a name="portal"></a>Portál

Na portálu můžete po klepnutí na tlačítko **Kompilovat**zadat hodnoty parametrů.

![Parametry kompilace konfigurace](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Prostředí PowerShell vyžaduje parametry v [hodnotě hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables), kde klíč odpovídá názvu parametru a hodnota se rovná hodnotě parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informace o předávání pscredentials jako parametry, viz [Prostředky pověření](#credential-assets) níže.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilace konfigurací obsahujících složené prostředky v Azure Automation

Funkce **Složené prostředky** umožňuje používat konfigurace DSC jako vnořené prostředky uvnitř konfigurace. Tato funkce umožňuje použití více konfigurací na jeden prostředek. Viz [Složené prostředky: Použití konfigurace DSC jako prostředku,](/powershell/scripting/dsc/resources/authoringresourcecomposite) abyste se dozvěděli další informace o složených prostředcích.

> [!NOTE]
> Aby se konfigurace obsahující složené prostředky zkompilovaly správně, musíte nejprve importovat do Azure Automation všechny prostředky DSC, na které jsou závislé kompozity. Přidání složeného prostředku DSC se nijak neliší od přidání libovolného modulu PowerShellu do Azure Automation. Tento proces je zdokumentován v [spravovat moduly v Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Správa configurationdata při kompilaci konfigurací v Azure Automation

**ConfigurationData** umožňuje oddělit strukturální konfiguraci od libovolné konfigurace specifické pro prostředí při použití prostředí PowerShell DSC. Další informace naleznete [v tématu Oddělení "Co" od "Kde" v Prostředí PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Při kompilaci v azure automation state configuration můžete použít **ConfigurationData** v Azure PowerShellu, ale ne na webu Azure Portal.

Následující příklad konfigurace DSC používá **ConfigurationData** prostřednictvím klíčových slov $ConfigurationData a $AllNodes. V tomto příkladu také potřebujete [modul xWebAdministration.](https://www.powershellgallery.com/packages/xWebAdministration/)

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

Předchozí konfiguraci DSC můžete zkompilovat pomocí prostředí Windows PowerShell. Následující skript přidá do služby vyžádat konfiguraci stavu Azure Automation dva konfigurace uzlů: ConfigurationDataSample.MyVM1 a ConfigurationDataSample.MyVM3.

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

Odkazy na datové zdroje jsou stejné v konfiguraci stavu Azure Automation i v sadách Runbook. Další informace najdete v následujících článcích:

- [Certifikáty](automation-certificates.md)
- [Připojení](automation-connections.md)
- [Přihlašovací údaje](automation-credentials.md)
- [Proměnné](automation-variables.md)

#### <a name="credential-assets"></a>Datové zdroje pověření

Konfigurace DSC v Azure Automation můžou odkazovat na prostředky pověření automatizace pomocí rutiny **Get-AutomationPSCredential.** Pokud konfigurace má parametr, který určuje objekt **PSCredential,** použijte **Get-AutomationPSCredential** předáním název řetězce prostředku pověření Azure Automation rutiny načíst pověření. Potom použijte tento objekt pro parametr, který vyžaduje objekt **PSCredential.** Na pozadí datového zdroje přihlašovacích údajů Azure Automation s tímto názvem se načte a předá do konfigurace. Následující příklad ukazuje tento scénář v akci.

Zabezpečení přihlašovacích údajů v konfiguracích uzlů (konfigurační dokumenty MOF) vyžaduje šifrování pověření v souboru MOF konfigurace uzlu. V současné době je nutné udělit PowerShell DSC oprávnění k výstupu pověření ve formátu prostého textu během konfigurace uzlu MOF generace. Prostředí PowerShell DSC si není vědomo, že Azure Automation šifruje celý soubor MOF po jeho generování prostřednictvím úlohy kompilace.

PowerShell DSC můžete sdělit, že je v pořádku, aby pověření byla vyprosazena ve formátu prostého textu v konfigurovaných mofech konfigurace generovaného uzlu pomocí konfiguračních dat. Měli byste `PSDscAllowPlainTextPassword = $true` předat přes **ConfigurationData** pro každý název bloku uzlu, který se zobrazí v konfiguraci DSC a používá pověření.

Následující příklad ukazuje konfiguraci DSC, která používá prostředek pověření automatizace.

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

Předchozí konfiguraci DSC můžete zkompilovat pomocí prostředí PowerShell. Následující kód Prostředí PowerShell přidá dva konfigurace uzlů na server pro vyžádat konfiguraci stavu Azure Automation: CredentialSample.MyVM1 a CredentialSample.MyVM2.

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
> Po dokončení kompilace se může zobrazit chybová zpráva "Modul Microsoft.PowerShell.Management' nebyl importován, protože modul snap-in Microsoft.PowerShell.Management byl již importován." Tuto zprávu klidně ignorujte.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Kompilace konfigurace DSC v prostředí Windows PowerShell

Můžete také importovat konfigurace uzlů (MOFs), které byly zkompilovány mimo Azure. Import zahrnuje kompilaci z pracovní stanice pro vývojáře nebo ve službě, jako je [Azure DevOps](https://dev.azure.com). Tento přístup má několik výhod, včetně výkonu a spolehlivosti.

Kompilace v prostředí Windows PowerShell také poskytuje možnost podepsat obsah konfigurace. Agent DSC ověří konfiguraci podepsaného uzlu místně na spravovaném uzlu. Ověření zajišťuje, že konfigurace použitá pro uzel pochází z autorizovaného zdroje.

> [!NOTE]
> Konfigurační soubor uzlu nesmí být větší než 1 MB, aby mohl být importován do Azure Automation.

Další informace o podepisování konfigurací uzlů naleznete [v tématu Vylepšení wmf 5.1 - Jak podepsat konfiguraci a modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Kompilace konfigurace DSC

Proces kompilace konfigurací DSC v prostředí Windows PowerShell je součástí dokumentace prostředí PowerShell DSC [Zápis, kompilace a použití konfigurace](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Tento proces můžete spustit z pracovní stanice pro vývojáře nebo v rámci služby sestavení, jako je [například Azure DevOps](https://dev.azure.com). Potom můžete importovat soubory MOF vytvořené kompilací konfigurace do služby Konfigurace stavu Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu na webu Azure Portal

1. V části Správa **konfigurace**klikněte na účet Automation na položku Konfigurace stavu **(DSC).**
1. Na stránce Konfigurace stavu (DSC) klikněte na kartu **Konfigurace** a potom klikněte na **+ Přidat**.
1. Na stránce Import klikněte na ikonu složky vedle textového pole **Konfigurační soubor uzlu** a vyhledejte v místním počítači konfigurační soubor uzlu (MOF).

   ![Vyhledat místní soubor](./media/automation-dsc-compile/import-browse.png)

1. Do pole **Název konfigurace** zadejte název. Tento název se musí shodovat s názvem konfigurace, ze které byla zkompilován konfigurace uzlu.
1. Klikněte na tlačítko **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Import konfigurace uzlu pomocí Azure PowerShellu

Rutina [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) můžete importovat konfiguraci uzlu do účtu Automation.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell najdete [v tématu Rutiny konfigurace stavu azure automatizace](/powershell/module/az.automation).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení do virtuálních počítačů pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
