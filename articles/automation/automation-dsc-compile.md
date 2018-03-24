---
title: Kompilování konfigurace v Azure Automation DSC.
description: Tento článek popisuje způsob kompilace konfigurace konfigurace požadovaného stavu (DSC) automatizace Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 04a8a8970df26638f2c19171864a3c4095ac7769
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Kompilování konfigurace v Azure Automation DSC.

Zkompilujete konfigurace konfigurace požadovaného stavu (DSC) s Azure Automation dvěma způsoby: na portálu Azure a pomocí prostředí Windows PowerShell. Následující tabulka vám pomůže určit, kdy použít jakou metodu na základě charakteristik jednotlivých:

### <a name="azure-portal"></a>Azure Portal

* Nejjednodušším způsobem s interaktivní uživatelské rozhraní
* Formuláře zadat jednoduchý parametr hodnoty
* Snadno sledovat stav úlohy
* Přístup k ověření pomocí přihlášení Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell
* Můžou být součástí automatizované řešení s více kroků
* Zadejte parametr jednoduché a komplexní hodnoty
* Sledování stavu úlohy
* Klienta, které jsou potřebné k podpoře rutiny prostředí PowerShell
* Předat ConfigurationData
* Zkompilování konfigurace, které používají pověření

Jakmile jste se rozhodli metodě kompilace, pomocí následujících postupů spusťte kompilace.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilaci konfigurace DSC pomocí portálu Azure

1. Z vašeho účtu Automation, klikněte na tlačítko **konfigurace DSC**.
2. Klikněte na konfiguraci, kterou chcete otevřete její okno.
3. Klikněte na tlačítko **zkompilovat**.
4. Pokud konfigurace nemá žádné parametry, zobrazí se výzva k potvrzení, zda chcete jej zkompilovat. Pokud konfigurace obsahuje parametry, **zkompilování konfigurace** otevře se okno, můžete zadat hodnoty parametrů. Najdete zde [ **základních parametrů** ](#basic-parameters) části Další podrobnosti o parametrech.
5. **Úloha kompilace** otevře se okno, takže můžete sledovat stav úlohy kompilace a konfigurace uzlu (MOF konfigurace dokumenty) se nezdařila z důvodu má být umístěn do Azure Automation DSC pro vyžádání obsahu serveru.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilaci konfigurace DSC pomocí prostředí Windows PowerShell

Můžete použít [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) spuštění kompilace pomocí prostředí Windows PowerShell. Následující vzorový kód spustí kompilaci konfigurace DSC názvem **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` Vrátí objekt úlohy kompilace, která můžete použít ke sledování jeho stavu. Pak můžete použít tento objekt úlohy kompilace s [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) k určení stavu úlohy kompilace a [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) zobrazíte její datové proudy (výstup). Následující vzorový kód spustí kompilaci **SampleConfig** konfigurace, čeká na dokončení a potom zobrazí jeho datové proudy.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Základní parametry
Deklarace parametru v konfiguracích DSC, včetně typy parametrů a vlastnosti, funguje stejně jako v Azure Automation runbook. V tématu [spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md) Další informace o parametry runbooku.

Následující příklad používá dva parametry s názvem **FeatureName** a **IsPresent**, jak určit hodnoty vlastností v **ParametersExample.sample** uzlu Konfigurace vygenerovaných během kompilace.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Můžete zkompilovat konfigurace DSC, které používají základní parametry, na portálu Azure Automation DSC, nebo pomocí prostředí Azure PowerShell:

### <a name="portal"></a>Portál

Na portálu, můžete zadat hodnoty parametrů po kliknutí na **zkompilovat**.

![alternativní text](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

Vyžaduje parametry v prostředí PowerShell [zatřiďovací tabulky](http://technet.microsoft.com/library/hh847780.aspx) kde klíč odpovídá názvu parametru, a hodnota se rovná hodnotě parametru.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Informace o předávání PSCredentials jako parametry najdete v tématu <a href="#credential-assets"> **prostředků přihlašovacích údajů** </a> níže.

## <a name="composite-resources"></a>Složené prostředky

**Složené prostředky** vám umožní použít konfigurace DSC jako vnořených prostředků v rámci konfigurace. To umožňuje použít více konfigurací pro jediný zdroj. V tématu [složené prostředků: pomocí konfigurace DSC jako prostředek](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) Další informace o **složené prostředky**

> [!NOTE]
> Aby **složené prostředky** pro správnou kompilaci, musíte napřed zajistit, aby veškeré prostředky DSC, které složené spoléhá na prvním nainstalování v úložišti moduly účet Azure Automation nebo není správně importovat.

Chcete-li přidat DSC **složené prostředků**, modul prostředků je třeba přidat do archivu (* .zip). Přejděte do úložiště modulů na váš účet Azure Automation. Klikněte na tlačítko "Přidat modul".

![Přidání modulu](./media/automation-dsc-compile/add_module.png)

Přejděte do adresáře, kde se nachází vašem archivu. Vyberte soubor archivu a klikněte na tlačítko OK.

![Vyberte modul](./media/automation-dsc-compile/select_dscresource.png)

Budete přesměrováni zpět do adresáře modulů, kde můžete sledovat stav vaší **složené prostředků** při se rozbalí a zaregistruje se Azure Automation.

![Import složené prostředků](./media/automation-dsc-compile/register_composite_resource.png)

Jakmile je registrovaný modul, můžete pak kliknutím na ho chcete ověřit, že **složené prostředky** jsou nyní k dispozici pro použití v konfiguraci.

![Ověření složeného prostředků](./media/automation-dsc-compile/validate_composite_resource.png)

Potom můžete volat **složené prostředků** do vaší konfigurace takto:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** umožňuje oddělit strukturální konfigurace z jakékoli konkrétní prostředí konfigurace při používání DSC prostředí PowerShell. V tématu [oddělení "Co" z "Kde" v prostředí PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) Další informace o **ConfigurationData**.

> [!NOTE]
> Můžete použít **ConfigurationData** při kompilaci v Azure Automation DSC pomocí Azure PowerShell, ale není v portálu Azure.

Následující příklad DSC konfigurace používá **ConfigurationData** prostřednictvím **$ConfigurationData** a **$AllNodes** klíčová slova. Musíte taky [ **xWebAdministration** modulu](https://www.powershellgallery.com/packages/xWebAdministration/) v tomto příkladu:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Můžete zkompilovat předchozí konfigurace DSC pomocí prostředí PowerShell. Následující PowerShell přidá dvě konfigurace uzlu DSC pro vyžádání obsahu server Azure Automation: **ConfigurationDataSample.MyVM1** a **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Prostředky

Asset odkazy jsou stejné ve sady runbook a konfigurace Azure Automation DSC. Najdete následující informace:

* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Proměnné](automation-variables.md)

### <a name="credential-assets"></a>Prostředků přihlašovacích údajů

Během konfigurace DSC ve službě Azure Automation, můžete odkazovat pomocí prostředků přihlašovacích údajů **Get-AutomationPSCredential**, prostředků přihlašovacích údajů můžete také být předán prostřednictvím parametrů, v případě potřeby. Pokud konfigurace přebírá parametr **PSCredential** typ, je třeba předat název řetězce objektu prostředek přihlašovacích údajů Azure Automation jako hodnota tohoto parametru, a nikoli objekt PSCredential. Na pozadí je prostředek přihlašovacích údajů Azure Automation s tímto názvem načíst a předána do konfigurace.

Zachování pověření zabezpečení v uzlu Konfigurace (configuration dokumenty MOF) vyžaduje šifrování přihlašovacích údajů v souboru MOF konfigurace uzlu. Služby Azure Automation trvá tento krok jeden další a šifruje celý soubor MOF. Ale právě se musí zjistit DSC Powershellu, že je to v pořádku pro přihlašovací údaje k výstupu v prostém textu v průběhu generování MOF konfigurace uzlu, protože prostředí PowerShell DSC neví, že Azure Automation bude možné šifrování celý soubor MOF po jeho generaci prostřednictvím úlohu kompilace.

DSC Powershellu, které je to v pořádku pro přihlašovací údaje k výstupu v prostém textu v konfiguraci uzlu generované soubory MOF zjistíte pomocí [ **ConfigurationData**](#configurationdata). By měla předávat `PSDscAllowPlainTextPassword = $true` prostřednictvím **ConfigurationData** pro každý uzel blok název, který se zobrazí v konfigurace DSC a použije přihlašovací údaje.

Následující příklad ukazuje konfigurace DSC, který používá prostředek přihlašovacích údajů automatizace.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Můžete zkompilovat předchozí konfigurace DSC pomocí prostředí PowerShell. Následující PowerShell přidá dvě konfigurace uzlu DSC pro vyžádání obsahu server Azure Automation: **CredentialSample.MyVM1** a **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> Po dokončení kompilace může zobrazit chybová zpráva: **modulu 'Microsoft.PowerShell.Management' nebyl importován, protože modul snap-in 'Microsoft.PowerShell.Management' byla již byla naimportována.** Toto upozornění můžete ignorovat.

## <a name="importing-node-configurations"></a>Import konfigurace uzlu

Můžete také importovat konfigurace uzlu (soubory MOF), které jste sestavili mimo Azure. Jednou z výhod to je, že může být podepsané konfigurace uzlu.
Konfigurace podepsaný uzlu je ověřováno místně na spravovaný uzel DSC agenta, zajistíte, že konfigurace aplikované na uzlu pochází z autorizovaná zdrojová.

> [!NOTE]
> Můžete importovat podepsané konfigurace do účtu Azure Automation, ale automatizace Azure aktuálně nepodporuje kompilování podepsaný konfigurace.

> [!NOTE]
> Soubor konfigurace uzlu musí být větší než 1 MB tak, aby ji bylo možné importovat do Azure Automation.

Zjistěte, jak podepsat konfigurace uzlu v https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu na portálu Azure

1. Z vašeho účtu Automation, klikněte na tlačítko **konfigurace uzlu DSC** pod **Configuration Management**.

    ![Konfigurace uzlu DSC](./media/automation-dsc-compile/node-config.png)
2. V **konfigurace uzlu DSC** okně klikněte na tlačítko **přidat NodeConfiguration**.
3. V **Import** okně klikněte na ikonu složky vedle **uzlu konfigurační soubor** textové pole a vyhledejte soubor konfigurace uzlu (MOF) v místním počítači.

    ![Procházením vyhledejte místního souboru](./media/automation-dsc-compile/import-browse.png)
4. Zadejte název do pole **název konfigurace** textové pole. Tento název musí odpovídat názvu konfigurace, ze kterého byl kompilován konfigurace uzlu.
5. Klikněte na **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Import konfigurace uzlu pomocí prostředí PowerShell

Můžete použít [Import AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) rutiny Import konfigurace uzlu do vašeho účtu automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



