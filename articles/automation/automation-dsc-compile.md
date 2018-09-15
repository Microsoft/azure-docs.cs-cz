---
title: Kompilace konfigurací v konfiguraci stavu služby Azure Automation
description: Tento článek popisuje, jak kompilaci konfigurace Desired State Configuration (DSC) pro Azure Automation.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fae415d158a9fced0c63078cd09c0cc070c88372
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629997"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilace konfigurací DSC v konfiguraci stavu služby Azure Automation

Konfigurace Desired State Configuration (DSC) v dva způsoby, jak pomocí Azure Automation State Configuration můžete zkompilovat: na webu Azure Portal a pomocí Windows Powershellu. Následující tabulka vám pomůže určit, kdy použít jakou metodu na základě charakteristik každé:

**Azure Portal**

- Nejjednodušší způsob s interaktivní uživatelské rozhraní
- Formuláře zadejte jednoduchý parametr hodnoty
- Jednoduše sledovat stav úlohy
- Přístup k ověření pomocí přihlášení Azure

**Windows PowerShell**

- Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell
- Mohou být součástí automatizované řešení s více kroky
- Zadejte hodnoty parametrů jednoduché a komplexní
- Sledovat stav úlohy
- Klient potřebné k podpoře rutin prostředí PowerShell
- Předejte jsou konfigurační data
- Kompilace konfigurace, které používají přihlašovací údaje

Až se rozhodnete pro metodu kompilace použijte následující postupy ke spuštění kompilace.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilace konfigurací DSC pomocí webu Azure portal

1. Ve svém účtu Automation, klikněte na tlačítko **konfigurace stavu (DSC)**.
1. Klikněte na **konfigurace** kartu a potom klikněte na název konfigurace pro kompilaci.
1. Klikněte na tlačítko **kompilaci**.
1. Pokud konfigurace nemá žádné parametry, zobrazí se výzva k potvrzení, zda chcete zkompilovat jej. Pokud v konfiguraci obsahuje parametry, **kompilaci konfigurace** otevře se okno, abyste mohli zadat hodnoty parametrů. Přečtěte si následující [ **základní parametry** ](#basic-parameters) části Další podrobnosti o parametrech.
1. **Úloha kompilace** stránka se otevře, aby mohli sledovat stav úlohy kompilace a způsobila umístit na Azure Automation stav konfigurace serveru vyžádané replikace konfigurace uzlu (dokumenty konfigurace MOF).

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilace konfigurací DSC pomocí Windows Powershellu

Můžete použít [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) spustit kompilaci pomocí Windows Powershellu. Následující vzorový kód spustí kompilaci konfigurace DSC volá **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` Vrátí objekt úlohy kompilace, který můžete použít ke sledování jeho stavu. Pak můžete použít tento objekt úlohy kompilace s [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
k určení stavu úlohy kompilace a [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
Chcete-li zobrazit její datové proudy (výstup). Následující vzorový kód spustí kompilace **SampleConfig** konfigurace, čeká na dokončení a potom zobrazí jeho datové proudy.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Základní parametry

Deklarace parametru v konfiguracích DSC, včetně typy parametrů a vlastnosti, funguje stejně jako v runboocích Azure Automation. Zobrazit [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md) získat další informace o parametry runbooku.

Následující příklad používá dva parametry s názvem **FeatureName** a **IsPresent**, jak určit hodnoty vlastností v **ParametersExample.sample** uzlu Konfigurace vygenerované při kompilaci.

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

Můžete zkompilovat konfiguraci DSC, používajících základní parametry v konfiguraci stavu automatizace Azure portal nebo pomocí Azure Powershellu:

### <a name="portal"></a>Portál

Na portálu, můžete zadat hodnoty parametrů po kliknutí na tlačítko **kompilaci**.

![Parametry kompilace konfigurace](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

Prostředí PowerShell vyžaduje parametry [zatřiďovací tabulky](/powershell/module/microsoft.powershell.core/about/about_hash_tables) kde klíč odpovídá názvu parametru a hodnota se rovná parametru hodnoty.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informace o předání PSCredentials jako parametrů najdete v tématu [Assety přihlašovacích údajů](#credential-assets) níže.

## <a name="composite-resources"></a>Složené prostředky

**Složené prostředky** vám umožní použít konfigurace DSC jako vnořených prostředků v rámci konfigurace. To umožňuje použití několika konfigurací na jediný prostředek. Zobrazit [složené prostředky: pomocí konfigurace DSC jako prostředek](/powershell/dsc/authoringresourcecomposite) Další informace o **složené prostředky**.

> [!NOTE]
> Aby **složené prostředky** pro kompilaci správně, je nutné nejdříve zkontrolovat, že veškeré prostředky DSC, které složeného spoléhá na prvním nainstalování v úložišti moduly účet Azure Automation, nebo není správně importovat.

Chcete-li přidat DSC **složený prostředek**, modulu prostředků je třeba přidat do archivu (* .zip). Přejděte do úložiště modulů ve vašem účtu Azure Automation. Potom klikněte na tlačítko "Přidat modul".

![Přidat modul](./media/automation-dsc-compile/add_module.png)

Přejděte do adresáře, kde se nachází vaše archivu. Vyberte soubor archivu a klikněte na tlačítko OK.

![Výběr modulu](./media/automation-dsc-compile/select_dscresource.png)

Budete přesměrováni zpět do adresáře modulů, kde můžete sledovat stav vašich **složený prostředek** rozbalí a registry pomocí Azure Automation.

![Importovat složené prostředky](./media/automation-dsc-compile/register_composite_resource.png)

Jakmile je registrovaný modul, můžete pak na ni kliknete ověření, zda **složené prostředky** jsou teď dostupné pro použití v konfiguraci.

![Ověření složeného prostředků](./media/automation-dsc-compile/validate_composite_resource.png)

Pak můžete volat **složený prostředek** do vaší konfigurace takto:

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** umožňuje oddělit strukturální konfiguraci z jakékoli konfigurace specifických pro prostředí při použití prostředí PowerShell DSC. Zobrazit [oddělení "Co" z "Kde" v prostředí PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) Další informace o **ConfigurationData**.

> [!NOTE]
> Můžete použít **ConfigurationData** při kompilaci konfigurace stavu automatizace Azure pomocí Azure Powershellu, ale ne na webu Azure portal.

Následující příklad DSC konfigurace používá **ConfigurationData** prostřednictvím **$ConfigurationData** a **$AllNodes** klíčová slova. Musíte také [ **xWebAdministration** modulu](https://www.powershellgallery.com/packages/xWebAdministration/) v tomto příkladu:

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

Můžete kompilovat předchozí konfigurace DSC pomocí Powershellu. Následující příkaz Powershellu přidá dvě konfigurace uzlu do Azure Automation stav konfigurace serveru vyžádané replikace: **ConfigurationDataSample.MyVM1** a **ConfigurationDataSample.MyVM3**:

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>Prostředky

Odkazy na prostředek jsou stejné v konfiguraci stavu Azure Automation a sady runbook. Si přečtěte následující informace:

- [Certifikáty](automation-certificates.md)
- [Připojení](automation-connections.md)
- [Přihlašovací údaje](automation-credentials.md)
- [Proměnné](automation-variables.md)

### <a name="credential-assets"></a>Assety přihlašovacích údajů

Konfigurace DSC ve službě Azure Automation může odkazovat na používání prostředků přihlašovacích údajů Automation `Get-AutomationPSCredential` rutiny. Pokud má parametr, který obsahuje konfiguraci **PSCredential** typ, můžete použít `Get-AutomationPSCredential` rutiny předáním název řetězce objektu Azure Automation asset přihlašovacích údajů pro danou rutinu se načíst přihlašovací údaje. Pak můžete použít tento objekt pro vyžadování parametr **PSCredential** objektu. Azure Automation asset přihlašovacích údajů s tímto názvem na pozadí, je načten a předané do konfigurace. Následující příklad ukazuje to v akci.

Uchování přihlašovacích údajů zabezpečené při konfiguraci uzlů (MOF konfigurace dokumenty) vyžaduje šifrování přihlašovacích údajů v uzlu konfiguračního souboru MOF. Ale aktuálně je zapotřebí sdělit PowerShell DSC, že je v pořádku, pro přihlašovací údaje, které má obsahovat výstup ve formátu prostého textu při generování MOF konfigurace uzlu, protože prostředí PowerShell DSC neví, že Azure Automation bude možné šifrování celý soubor MOF po jeho vygenerování prostřednictvím úlohy kompilace.

Poznáte, prostředí PowerShell DSC, která je v pořádku, pro přihlašovací údaje, které má obsahovat výstup ve formátu prostého textu v konfiguraci uzlu generované soubory MOF pomocí [ **ConfigurationData**](#configurationdata). Je třeba předat `PSDscAllowPlainTextPassword = $true` prostřednictvím **ConfigurationData** pro každý uzel blok název, který se zobrazí v konfiguraci DSC a mechanismus využívá přihlašovací údaje.

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

Můžete kompilovat předchozí konfigurace DSC pomocí Powershellu. Následující příkaz Powershellu přidá dvě konfigurace uzlu do Azure Automation stav konfigurace serveru vyžádané replikace: **CredentialSample.MyVM1** a **CredentialSample.MyVM2**.

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Po dokončení kompilace může zobrazit chybová zpráva: **'Microsoft.PowerShell.Management' modul nebyl importován, protože modul snap-in 'Microsoft.PowerShell.Management' již byl importován.** Toto upozornění můžete ignorovat.

## <a name="importing-node-configurations"></a>Import konfigurace uzlu

Můžete také importovat konfigurace uzlu (soubory MOF), které již byly zkompilovány mimo Azure. Jednou z výhod tohoto je, že může být podepsané konfigurace uzlu. Konfigurace uzlu podepsaný ověřují místně na spravovaných uzlů DSC agenta, zajištění, že konfigurace zavádí uzel pochází z autorizovaná zdrojová.

> [!NOTE]
> Můžete použít import podepsané konfigurace ke svému účtu Azure Automation, ale Azure Automation aktuálně nepodporuje kompilování podepsané konfigurace.

> [!NOTE]
> Konfigurační soubor uzlu nesmí být větší než 1 MB, aby mohla být importován do služby Azure Automation.

Další informace o tom, jak podepsat konfigurace uzlů najdete v tématu [vylepšení ve WMF 5.1. postup konfigurace přihlašování a modul](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu na webu Azure Portal

1. Ve svém účtu Automation, klikněte na tlačítko **konfigurace stavu (DSC)** pod **Configuration Management**.
1. V **konfigurace stavu (DSC)** stránky, klikněte na **konfigurace** kartu a potom klikněte na **+ přidat**.
1. V **Import** vedle klikněte na ikonu složky **konfigurační soubor uzlu** textového pole a vyhledejte uzel konfiguračního souboru (MOF) v místním počítači.

   ![Vyhledat místní soubor](./media/automation-dsc-compile/import-browse.png)

1. Zadejte název do pole **název konfigurace** textového pole. Tento název musí odpovídat názvu konfigurace, ze kterého byl zkompilován konfigurace uzlu.
1. Klikněte na **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Import konfigurace uzlu pomocí Powershellu

Můžete použít [Import AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) rutiny pro import konfigurace uzlu do vašeho účtu automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)