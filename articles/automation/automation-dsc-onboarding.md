---
title: Připojování počítačů pro správu pomocí Azure Automation DSC
description: Jak nastavit počítače pro správu pomocí Azure Automation DSC
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.topic: conceptual
ms.date: 07/20/2018
manager: carmonm
ms.openlocfilehash: 907bb9b998012a5b907c041b3eba061036289cc1
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186981"
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Připojování počítačů pro správu pomocí Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Proč Správa počítačů pomocí Azure Automation DSC?

Stejně jako [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx), Azure Automation Desired State Configuration je jednoduché, ale výkonnou službu pro správu konfigurace uzlů DSC (fyzické a virtuální počítače) v jakémkoli cloudu nebo lokálně datové centrum. Umožňuje škálovatelnost napříč tisíci počítače rychle a snadno z centrální a zabezpečené umístění. Můžete snadno připojit počítače, přiřaďte jim deklarativních konfigurací a zobrazit sestavy zobrazující každý počítač v dodržování předpisů, stavu, který jste zadali. Vrstva správy Azure Automation DSC je DSC, co je vrstva správy služby Azure Automation pro skriptování PowerShell. Jinými slovy stejným způsobem, který Azure Automation pomáhá spravovat skripty prostředí PowerShell, pomáhá také při správě konfigurací DSC. Další informace o výhodách používání DSC Azure Automation najdete v tématu [přehled Azure Automation DSC](automation-dsc-overview.md).

Azure Automation DSC můžete použít ke správě různých počítačů:

* Virtuální počítače Azure (nasazené jak v modelu classic a modelu nasazení Azure Resource Manager)
* Instance EC2 Amazon Web Services (AWS) 
* Windows fyzické nebo virtuální počítače, místně nebo v jiném cloudu než Azure nebo AWS
* Linux fyzické nebo virtuální počítače v místním prostředí, v Azure nebo v jiném cloudu než Azure

Kromě toho pokud si nejste připraveni ke správě konfigurace počítačů z cloudu, Azure Automation DSC může také sloužit jako koncového bodu jen pro sestavy. To umožňuje nastavit (push) požadované konfigurace pomocí DSC v místním a zobrazit bohaté možnosti generování sestav podrobnosti o dodržování předpisů uzlu s požadovaným stavem ve službě Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure pomocí DSC je zahrnutých bez dalších poplatků, pokud je nainstalované rozšíření virtuálního počítače DSC vyšší než 2.70. Odkazovat [ **stránce s cenami služby Automation** ](https://azure.microsoft.com/pricing/details/automation/) další podrobnosti.


Následující oddíly popisují, jak můžete připojit každého typu počítače do Azure Automation DSC.

## <a name="azure-virtual-machines-classic"></a>Virtuální počítače Azure (klasické)

Díky Azure Automation DSC můžete snadno připojit virtuální počítače Azure (classic) pro správu konfigurace pomocí webu Azure portal, nebo prostředí PowerShell. Pod pokličkou a bez nutnosti vzdáleném připojení k virtuálnímu počítači Správce rozšíření virtuálního počítače Azure, Desired State Configuration zaregistruje virtuální počítač s Azure Automation DSC. Rozšíření Azure VM Desired State Configuration běží asynchronně, postup sledovat její průběh nebo vyřešit potíže s jeho jsou k dispozici v následujících [ **připojování virtuálního počítače Azure pro řešení potíží s** ](#troubleshooting-azure-virtual-machine-onboarding) oddílu.

### <a name="azure-portal"></a>Azure Portal

V [webu Azure portal](http://portal.azure.com/), klikněte na tlačítko **Procházet** -> **virtuální počítače (classic)**. Vyberte virtuální počítač Windows, který chcete připojit. V okně řídicím panelu virtuálního počítače, klikněte na **všechna nastavení** -> **rozšíření** -> **přidat** -> **Azure Automatizace DSC** -> **vytvořit**. Zadejte [hodnoty prostředí PowerShell DSC Local Configuration Manageru](https://msdn.microsoft.com/powershell/dsc/metaconfig4) potřebné pro váš případ použití, registrační klíč vašeho účtu Automation a adresa URL pro registraci a volitelně konfigurace uzlu k přiřazení k virtuálnímu počítači.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Adresa URL pro registraci a klíč pro účet služby Automation na připojení počítače, přečtěte si následující [ **zabezpečení registrace** ](#secure-registration) části:

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Konfigurace uzlu DSC názvy jsou malá a velká písmena na portálu. Pokud jsou neodpovídající uzel nezobrazí pod uzly DSC.

## <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Azure Automation DSC umožňuje snadno připojit virtuální počítače Azure pro správu konfigurace pomocí webu Azure portal, šablon Azure Resource Manageru nebo PowerShell. Pod pokličkou a bez nutnosti vzdáleném připojení k virtuálnímu počítači Správce rozšíření virtuálního počítače Azure, Desired State Configuration zaregistruje virtuální počítač s Azure Automation DSC. Rozšíření Azure VM Desired State Configuration běží asynchronně, postup sledovat její průběh nebo vyřešit potíže s jeho jsou k dispozici v následujících [ **připojování virtuálního počítače Azure pro řešení potíží s** ](#troubleshooting-azure-virtual-machine-onboarding) oddílu.

### <a name="azure-portal"></a>Azure Portal

V [webu Azure portal](https://portal.azure.com/), přejděte do účtu Azure Automation, ve které chcete připojit virtuální počítače. Na řídicím panelu účtu Automation, klikněte na tlačítko **uzly DSC** -> **+ přidat virtuální počítač Azure**.

Vyberte virtuální počítač Azure připojit.

Pokud počítač nemá žádné PowerShell desired nainstalované rozšíření stav a stav napájení je spuštěna, klikněte na tlačítko **připojit**.

V části **registrace**, zadejte [hodnoty prostředí PowerShell DSC Local Configuration Manageru](https://msdn.microsoft.com/powershell/dsc/metaconfig4) vyžadované pro vašemu případu použití a volitelně konfigurace uzlu přiřazení k virtuálnímu počítači.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Azure virtual machines můžete nasadit a zapojený do služby Azure Automation DSC prostřednictvím šablon Azure Resource Manageru. Zobrazit [konfigurace virtuálního počítače pomocí rozšíření DSC a Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) pro příklad šablony, který připojí existujícího virtuálního počítače do Azure Automation DSC. Najít registrační klíč a adresa URL pro registraci provést jako vstup do této šablony, přečtěte si následující [ **zabezpečení registrace** ](#secure-registration) oddílu.

### <a name="powershell"></a>PowerShell

[Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) rutinu je možné připojit virtuální počítače na webu Azure Portal přes PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Virtuální počítače služby Amazon Web Services (AWS)

Můžete snadno připojit virtuální počítače služby Amazon Web Services za správu konfigurace pomocí DSC Azure automatizace pomocí nástrojů DSC AWS. Další informace o sadu nástrojů [tady](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Windows fyzické nebo virtuální počítače, místně nebo v jiném cloudu než Azure nebo AWS

Místní počítače Windows a Windows počítače do cloudů mimo Azure (například Amazon Web Services) může být také zapojený do služby Azure Automation DSC, tak dlouho, dokud mají odchozí přístup k Internetu prostřednictvím několika jednoduchými kroky:

1. Ujistěte se, že nejnovější verze [WMF 5](http://aka.ms/wmf5latest) je nainstalován v počítačích, které chcete pro připojení k Azure Automation DSC.
2. Postupujte podle pokynů v následující části [ **generování DSC metaconfigurations** ](#generating-dsc-metaconfigurations) ke generování složku obsahující potřebné metaconfigurations DSC.
3. Vzdáleně se vztahují metaconfiguration DSC Powershellu na počítače, které chcete připojit. **Příkaz spouštíte z počítače musí mít nejnovější verzi [WMF 5](http://aka.ms/wmf5latest) nainstalované**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Pokud nemůžete použít metaconfigurations DSC Powershellu vzdáleně, zkopírujte složku metaconfigurations z kroku 2 na každý počítač připojit. Poté zavolejte **Set-DscLocalConfigurationManager** místně na každém počítači na připojení.
5. Pomocí webu Azure portal nebo rutin, zkontrolujte, jestli počítače k zařazení do se nyní zobrazují jako uzly DSC registrované ve vašem účtu Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Linux fyzické nebo virtuální počítače v místním prostředí, v Azure nebo v jiném cloudu než Azure

Počítače s Linuxem v místním, počítače s Linuxem v Azure a počítačů s Linuxem v cloudech mimo Azure může být také zapojený do služby Azure Automation DSC, tak dlouho, dokud mají odchozí přístup k Internetu prostřednictvím několika jednoduchými kroky:

1. Ujistěte se, že nejnovější verze [PowerShell Desired State Configuration pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) je nainstalován v počítačích, které chcete pro připojení k Azure Automation DSC.
2. Pokud [výchozí hodnoty prostředí PowerShell DSC Local Configuration Manageru](https://msdn.microsoft.com/powershell/dsc/metaconfig4) odpovídat vašemu případu použití a vy chcete připojit počítače, že **obě** načítat a tvorba sestav Azure Automation DSC:

   + Na každém počítači s Linuxem pro připojení k Azure Automation DSC použijte Register.py připojení pomocí výchozího nastavení prostředí PowerShell DSC Local Configuration Manageru:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Registrační klíč a adresa URL pro registraci u vašeho účtu Automation, najdete v tématu následující [ **zabezpečení registrace** ](#secure-registration) oddílu.

     Pokud výchozí nastavení prostředí PowerShell DSC Local Configuration Manageru **proveďte** **není** shoda vašemu případu použití, nebo chcete pro připojení počítačů tak, aby pouze ohlásí Azure Automation DSC, ale nestahuje konfigurace nebo Moduly Powershellu z něj, postupujte podle kroků 3 až 6. V opačném případě přejděte přímo ke kroku 6.

3. Postupujte podle pokynů v následujícím [ **generování DSC metaconfigurations** ](#generating-dsc-metaconfigurations) část k vygenerování složku obsahující potřebné metaconfigurations DSC.
4. Vzdáleně se vztahují metaconfiguration DSC Powershellu na počítače, které chcete připojit:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Příkaz spouštíte z počítače musí mít nejnovější verzi [WMF 5](http://aka.ms/wmf5latest) nainstalované.

1. Pokud nemůžete použít metaconfigurations DSC Powershellu vzdáleně, pro každý počítač s Linuxem připojit, zkopírujte metaconfiguration odpovídající tomuto počítači ze složky v kroku 5 na počítači s Linuxem. Poté zavolejte `SetDscLocalConfigurationManager.py` místně na každém počítači s Linuxem chcete pro připojení k Azure Automation DSC:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Pomocí webu Azure portal nebo rutin, zkontrolujte, jestli počítače k zařazení do se nyní zobrazují jako uzly DSC registrované ve vašem účtu Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generování DSC metaconfigurations

Obecně připojit žádné počítače do Azure Automation DSC, [DSC metaconfiguration](https://msdn.microsoft.com/powershell/dsc/metaconfig) může být vygenerována, při použití, říká agentovi, DSC na počítači za účelem načítat a/nebo sestavy do Azure Automation DSC. Metaconfigurations DSC pro Azure Automation DSC můžete vygenerovat pomocí prostředí PowerShell DSC konfigurace nebo rutiny prostředí PowerShell pro Azure Automation.

> [!NOTE]
> DSC metaconfigurations obsahovat tajné klíče potřebné ke připojit počítače s služby Automation účtu pro správu. Ujistěte se, že dobře chráněna jakékoli metaconfigurations DSC, které vytvoříte, nebo je odstranit za použití.

### <a name="using-a-dsc-configuration"></a>Pomocí konfigurace DSC

1. Otevřete prostředí PowerShell ISE jako správce na počítači v místním prostředí. Počítač musí mít nejnovější verzi [WMF 5](http://aka.ms/wmf5latest) nainstalované.
2. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci DSC Powershellu pro vytvoření metaconfigurations a pusťte se do vytváření metaconfiguration příkazu.

> [!NOTE]
> Konfigurace uzlu DSC názvy jsou malá a velká písmena na portálu. Pokud jsou neodpovídající uzel nezobrazí pod uzly DSC.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Vyplňte registrační klíč a adresu URL pro svůj účet Automation, stejně jako názvy počítačů na připojení. Všechny ostatní parametry jsou volitelné. Registrační klíč a adresa URL pro registraci u vašeho účtu Automation, najdete v tématu následující [ **zabezpečení registrace** ](#secure-registration) oddílu.
4. Pokud chcete, aby počítače hlásit informace o stavu DSC Azure Automation DSC, ale ne o přijetí změn konfigurace nebo moduly Powershellu, nastavte **jen** parametr na hodnotu true.
5. Spusťte skript. Teď byste měli mít složku s názvem **DscMetaConfigs** ve svém pracovním adresáři, který obsahuje metaconfigurations PowerShell DSC pro počítače pro připojení (jako správce):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Pomocí rutin Azure Automation.

Pokud odpovídají vašemu případu použití výchozí hodnoty prostředí PowerShell DSC Local Configuration Manageru a chcete počítače můžete připojit tak, že načítat i sestav Azure Automation DSC, rutiny služby Azure Automation poskytuje zjednodušený způsob generování DSC metaconfigurations potřeby:

1. Otevřete konzolu Powershellu nebo ISE Powershellu jako správce na počítači v místním prostředí.
2. Připojení k Azure Resource Manageru pomocí **Connect-AzureRmAccount**
3. Stáhněte si metaconfigurations PowerShell DSC pro počítače, které chcete připojit z účtu Automation, ke kterému chcete připojit uzly:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Teď byste měli mít složku s názvem ***DscMetaConfigs***, obsahující metaconfigurations PowerShell DSC pro počítače pro připojení (jako správce):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Zabezpečení registrace

Počítače můžete bezpečně připojit k účtu Azure Automation pomocí protokolu registrace DSC WMF 5, který umožňuje uzlu DSC k ověření na serveru prostředí PowerShell DSC o přijetí změn nebo vytváření sestav (včetně Azure Automation DSC). Uzel se registruje na serveru s **adresa URL pro registraci**pomocí **registrační klíč**. Během registrace vyjednávání jedinečný certifikát pro tento uzel, který použijete pro ověřování serveru po registraci uzlu DSC a server pro vyžádání obsahu/sestavy DSC. Tento postup brání připojili uzlů z jednoho jiného, třeba když se uzel dojde k narušení zosobnění a chová závadně. Po registraci registrační klíč se používá k ověřování znovu a odstranění z uzlu.

Umožňuje získat všechny informace potřebné pro registraci protokolu DSC z **klíče** pod **nastavení účtu** na webu Azure Portal. Otevřete toto okno kliknutím na ikonu klíče na **Essentials** panelu pro účet Automation.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* Adresa URL pro registraci je do pole Adresa URL v okně Správa klíčů.
* Registrační klíč je primární přístupový klíč nebo sekundární přístupový klíč v okně Správa klíčů. Dá se ani jeden klíč.

Pro zvýšení zabezpečení, primární a sekundární přístupové klíče účtu služby Automation může být znovu vygenerován v každém okamžiku (na **Správa klíčů** okno) zabránit budoucím uzel registrace pomocí dřívější klíče.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Řešení potíží registrace virtuálních počítačů Azure

Azure Automation DSC umožňuje snadno připojit virtuální počítače Azure Windows pro správu konfigurace. Pod pokličkou rozšíření virtuálního počítače Azure, Desired State Configuration slouží k registraci virtuálního počítače ve službě Azure Automation DSC. Rozšíření Azure VM Desired State Configuration běží asynchronně, může být důležité pro sledování průběhu a řešení potíží s produktem.

> [!NOTE]
> Jakékoli metody objektu připojování virtuálního počítače Windows Azure k DSC Azure Automation, který používá rozšíření Azure VM Desired State Configuration může trvat až hodinu, uzel, který má zobrazit až zaregistrovaný ve službě Azure Automation. To je způsobeno instalace Windows Management Framework 5.0 na virtuálním počítači rozšíření DSC virtuálního počítače Azure, které je potřeba pro připojení virtuálního počítače do Azure Automation DSC.

Řešení potíží nebo zobrazit stav rozšíření Azure VM Desired State Configuration, na webu Azure Portal přejděte k virtuálnímu počítači se připojit a pak klikněte na **rozšíření** pod **nastavení**. Pak klikněte na tlačítko **DSC** nebo **DSCForLinux** v závislosti na operačním systému. Další podrobnosti, můžete kliknout na **zobrazit podrobný stav**.

## <a name="certificate-expiration-and-reregistration"></a>Vypršení platnosti certifikátu a opětovná

Po registraci počítače s jako uzel DSC v Azure Automation DSC, existuje mnoho důvodů, proč je nutné znovu registrovat tento uzel v budoucnosti:

* Po registraci, automatické každý uzel jedinečný certifikát pro ověřování, jejíž platnost vyprší po jednom roce. V současné době protokol registrace DSC Powershellu nelze obnovit automaticky certifikáty při jejich brzy vyprší platnost, takže budete muset znovu registrovat uzlů po dobu za rok. Před nnásledující, ujistěte se, že každý uzel se systémem Windows Management Framework 5.0 RTM. Pokud vyprší platnost certifikátu ověřování uzlu a uzel není přeregistrován, uzel nemůže komunikovat s Azure Automation a je označená "Unresponsive." Opětovná provést 90 dní nebo méně od času vypršení platnosti certifikátu, nebo kdykoli po dobu platnosti certifikátu, výsledkem bude nový certifikát se generovat a používat.
* Chcete-li změnit některý [hodnoty prostředí PowerShell DSC Local Configuration Manageru](https://msdn.microsoft.com/powershell/dsc/metaconfig4) , které byly nastaveny při počáteční registraci uzlu, jako je například ConfigurationMode. V současné době můžete tyto hodnoty agenta DSC změnit jenom prostřednictvím registrace. Jedinou výjimkou je konfigurace uzlu přiřazené k uzlu – to se dá změnit v Azure Automation DSC přímo.

Opětovná lze provádět v stejným způsobem zaregistrovat uzlu na začátku pomocí některé z metod registrace popsané v tomto dokumentu. Zrušení registrace uzlu z Azure Automation DSC před nnásledující ji nepotřebujete.

## <a name="related-articles"></a>Související články

* [Přehled služby Azure Automation DSC](automation-dsc-overview.md)
* [Rutiny Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Ceny za Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
