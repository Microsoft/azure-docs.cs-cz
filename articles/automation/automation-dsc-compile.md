---
title: Kompilace konfigurací v Azure Automation DSC
description: Tento článek popisuje, jak kompilaci konfigurace Desired State Configuration (DSC) pro Azure Automation.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 897681cda31b2f187fca64e77621b7dc5ed4dfae
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072105"
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Kompilace konfigurací v Azure Automation DSC

Konfigurace Desired State Configuration (DSC) dvě možnosti, jak pomocí Azure Automation můžete zkompilovat: na webu Azure Portal a pomocí Windows Powershellu. Následující tabulka vám pomůže určit, kdy použít jakou metodu na základě charakteristik každé:

### <a name="azure-portal"></a>Azure Portal

* Nejjednodušší způsob s interaktivní uživatelské rozhraní
* Formuláře zadejte jednoduchý parametr hodnoty
* Jednoduše sledovat stav úlohy
* Přístup k ověření pomocí přihlášení Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell
* Mohou být součástí automatizované řešení s více kroky
* Zadejte hodnoty parametrů jednoduché a komplexní
* Sledovat stav úlohy
* Klient potřebné k podpoře rutin prostředí PowerShell
* Předejte jsou konfigurační data
* Kompilace konfigurace, které používají přihlašovací údaje

Až se rozhodnete pro metodu kompilace použijte následující postupy ke spuštění kompilace.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilace konfigurací DSC pomocí webu Azure portal

1. Ve svém účtu Automation, klikněte na tlačítko **konfigurací DSC**.
2. Klikněte na konfigurace a otevřete její okno.
3. Klikněte na tlačítko **kompilaci**.
4. Pokud konfigurace nemá žádné parametry, zobrazí se výzva k potvrzení, zda chcete zkompilovat jej. Pokud v konfiguraci obsahuje parametry, **kompilaci konfigurace** otevře se okno, abyste mohli zadat hodnoty parametrů. Přečtěte si následující [ **základní parametry** ](#basic-parameters) části Další podrobnosti o parametrech.
5. **Úloha kompilace** , aby mohli sledovat stav úlohy kompilace a způsobila umístit na Azure Automation DSC serveru vyžádané replikace konfigurace uzlu (MOF konfigurace dokumenty), otevře se okno.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilace konfigurací DSC pomocí Windows Powershellu

Můžete použít [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) spustit kompilaci pomocí Windows Powershellu. Následující vzorový kód spustí kompilaci konfigurace DSC volá **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` Vrátí objekt úlohy kompilace, který můžete použít ke sledování jeho stavu. Pak můžete použít tento objekt úlohy kompilace s [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) k určení stavu úlohy kompilace a [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) zobrazíte její datové proudy (výstup). Následující vzorový kód spustí kompilace **SampleConfig** konfigurace, čeká na dokončení a potom zobrazí jeho datové proudy.

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

Můžete zkompilovat konfiguraci DSC, používajících základní parametry na portálu Azure Automation DSC, nebo pomocí Azure Powershellu:

### <a name="portal"></a>Portál

Na portálu, můžete zadat hodnoty parametrů po kliknutí na tlačítko **kompilaci**.

![alternativní text](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

Prostředí PowerShell vyžaduje parametry [zatřiďovací tabulky](http://technet.microsoft.com/library/hh847780.aspx) kde klíč odpovídá názvu parametru a hodnota se rovná parametru hodnoty.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Informace o předání PSCredentials jako parametrů najdete v tématu <a href="#credential-assets"> **Assety přihlašovacích údajů** </a> níže.

## <a name="composite-resources"></a>Složené prostředky

**Složené prostředky** vám umožní použít konfigurace DSC jako vnořených prostředků v rámci konfigurace. To umožňuje použití několika konfigurací na jediný prostředek. Zobrazit [složené prostředky: pomocí konfigurace DSC jako prostředek](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) Další informace o **složené prostředky**

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

## <a name="configurationdata"></a>Jsou konfigurační data
**ConfigurationData** umožňuje oddělit strukturální konfiguraci z jakékoli konfigurace specifických pro prostředí při použití prostředí PowerShell DSC. Zobrazit [oddělení "Co" z "Kde" v prostředí PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) Další informace o **ConfigurationData**.

> [!NOTE]
> Můžete použít **ConfigurationData** při kompilaci v Azure Automation DSC pomocí Azure Powershellu, ale není na portálu Azure portal.

Následující příklad DSC konfigurace používá **ConfigurationData** prostřednictvím **$ConfigurationData** a **$AllNodes** klíčová slova. Musíte také [ **xWebAdministration** modulu](https://www.powershellgallery.com/packages/xWebAdministration/) v tomto příkladu:

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

Můžete kompilovat předchozí konfigurace DSC pomocí Powershellu. Následující příkaz Powershellu přidá dvě konfigurace uzlu do Azure Automation DSC serveru vyžádané replikace: **ConfigurationDataSample.MyVM1** a **ConfigurationDataSample.MyVM3**:

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

Odkazy na prostředek jsou stejné v konfiguracích DSC Azure Automation a sady runbook. Si přečtěte následující informace:

* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Proměnné](automation-variables.md)

### <a name="credential-assets"></a>Assety přihlašovacích údajů

Konfigurace DSC ve službě Azure Automation může odkazovat na používání prostředků přihlašovacích údajů Automation `Get-AutomationPSCredential`. Pokud má parametr, který obsahuje konfiguraci **PSCredential** typ, můžete použít `Get-AutomationPSCredential` rutiny předáním název řetězce objektu Azure Automation asset přihlašovacích údajů pro danou rutinu se načíst přihlašovací údaje. Pak můžou použít pak pomocí tohoto objektu pro parametr vyžadování **PSCredential** objektu. Azure Automation asset přihlašovacích údajů s tímto názvem na pozadí, je načten a předané do konfigurace. Následující příklad ukazuje to v akci.

Uchování přihlašovacích údajů zabezpečené při konfiguraci uzlů (MOF konfigurace dokumenty) vyžaduje šifrování přihlašovacích údajů v uzlu konfiguračního souboru MOF. Ale aktuálně je zapotřebí sdělit PowerShell DSC, že je v pořádku, pro přihlašovací údaje, které má obsahovat výstup ve formátu prostého textu při generování MOF konfigurace uzlu, protože prostředí PowerShell DSC neví, že Azure Automation bude možné šifrování celý soubor MOF po jeho vygenerování prostřednictvím úlohy kompilace.

Poznáte, prostředí PowerShell DSC, která je v pořádku, pro přihlašovací údaje, které má obsahovat výstup ve formátu prostého textu v konfiguraci uzlu generované soubory MOF pomocí [ **ConfigurationData**](#configurationdata). Je třeba předat `PSDscAllowPlainTextPassword = $true` prostřednictvím **ConfigurationData** pro každý uzel blok název, který se zobrazí v konfiguraci DSC a mechanismus využívá přihlašovací údaje.

Následující příklad ukazuje konfiguraci DSC, která používá prostředek přihlašovacích údajů Automation.

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

Můžete kompilovat předchozí konfigurace DSC pomocí Powershellu. Následující příkaz Powershellu přidá dvě konfigurace uzlu do Azure Automation DSC serveru vyžádané replikace: **CredentialSample.MyVM1** a **CredentialSample.MyVM2**.

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
> Po dokončení kompilace může zobrazit chybová zpráva: **'Microsoft.PowerShell.Management' modul nebyl importován, protože modul snap-in 'Microsoft.PowerShell.Management' již byl importován.** Toto upozornění můžete ignorovat.

## <a name="importing-node-configurations"></a>Import konfigurace uzlu

Můžete také importovat konfigurace uzlu (soubory MOF), které jste zkompilovali mimo Azure. Jednou z výhod tohoto je, že může být podepsané konfigurace uzlu.
Konfigurace uzlu podepsaný ověřují místně na spravovaných uzlů DSC agenta, zajištění, že konfigurace zavádí uzel pochází z autorizovaná zdrojová.

> [!NOTE]
> Můžete použít import podepsané konfigurace ke svému účtu Azure Automation, ale Azure Automation aktuálně nepodporuje kompilování podepsané konfigurace.

> [!NOTE]
> Konfigurační soubor uzlu nesmí být větší než 1 MB, aby mohla být importován do služby Azure Automation.

Můžete naučit se konfigurace uzlu na https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Import konfigurace uzlu na webu Azure Portal

1. Ve svém účtu Automation, klikněte na tlačítko **konfigurace uzlu DSC** pod **Configuration Management**.

    ![Konfigurace uzlu DSC](./media/automation-dsc-compile/node-config.png)
2. V **konfigurace uzlu DSC** okna, klikněte na tlačítko **přidat NodeConfiguration**.
3. V **Import** okna, klikněte na ikonu složky vedle **konfigurační soubor uzlu** textového pole a vyhledejte uzel konfiguračního souboru (MOF) v místním počítači.

    ![Vyhledat místní soubor](./media/automation-dsc-compile/import-browse.png)
4. Zadejte název do pole **název konfigurace** textového pole. Tento název musí odpovídat názvu konfigurace, ze kterého byl zkompilován konfigurace uzlu.
5. Klikněte na **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Import konfigurace uzlu pomocí Powershellu

Můžete použít [Import AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) rutiny pro import konfigurace uzlu do vašeho účtu automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



