---
title: Připojování počítačů pro správu podle konfigurace stavu služby Azure Automation
description: Jak nastavit počítače pro správu pomocí Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 8a505e88ff92c5227d3b42da2adaf1dce58e6fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441499"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Připojování počítačů pro správu podle konfigurace stavu služby Azure Automation

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Proč spravovat počítače pomocí Azure Automation State Configuration?

Konfigurace stavu Azure Automation je služba pro správu konfigurace uzlů DSC v jakémkoli cloudu nebo v místním datovém centru.
Umožňuje škálovatelnost napříč tisíci počítače rychle a snadno z centrální a zabezpečené umístění.
Můžete snadno připojit počítače, přiřaďte jim deklarativních konfigurací a zobrazit sestavy zobrazující každý počítač v dodržování předpisů, stavu, který jste zadali.
Služba Azure Automation stavu konfigurace je DSC, jaké jsou runbooky Azure Automation pro skriptování PowerShell.
Jinými slovy stejným způsobem, který Azure Automation pomáhá spravovat skripty prostředí PowerShell, pomáhá také při správě konfigurací DSC.
Další informace o výhodách používání konfigurace stavu služby Azure Automation najdete v tématu [přehled Azure Automation stavu konfigurace](automation-dsc-overview.md).

Konfigurace stavu Azure Automation můžete použít ke správě různých počítačů:

- Virtuální počítače Azure
- Virtuální počítače Azure (klasické)
- Amazon Web Services (AWS) EC2 instances
- Windows fyzické nebo virtuální počítače, místně nebo v jiném cloudu než Azure nebo AWS
- Linux fyzické nebo virtuální počítače v místním prostředí, v Azure nebo v jiném cloudu než Azure

Kromě toho pokud si nejste připraveni ke správě konfigurace počítačů z cloudu, konfigurace stavu služby Azure Automation může také sloužit jako koncového bodu jen pro sestavy.
To umožňuje nastavit (push) konfigurace pomocí DSC a zobrazení podrobností ve službě Azure Automation generování sestav.

> [!NOTE]
> Správa virtuálních počítačů Azure pomocí konfigurace stavu je zahrnutých bez dalších poplatků, pokud je nainstalované rozšíření virtuálního počítače DSC vyšší než 2.70. Odkazovat [ **stránce s cenami služby Automation** ](https://azure.microsoft.com/pricing/details/automation/) další podrobnosti.

Následující oddíly popisují, jak můžete připojit každého typu počítače do Azure Automation State Configuration.

## <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Konfigurace stavu Azure Automation vám umožní snadno připojit virtuální počítače Azure pro správu konfigurace pomocí webu Azure portal, šablon Azure Resource Manageru nebo PowerShell. Pod pokličkou a bez nutnosti vzdáleném připojení k virtuálnímu počítači Správce rozšíření virtuálního počítače Azure, Desired State Configuration zaregistruje virtuálního počítače pomocí Azure Automation State Configuration.
Rozšíření Azure VM Desired State Configuration běží asynchronně, postup sledovat její průběh nebo vyřešit potíže s jeho jsou k dispozici v následujících [ **připojování virtuálního počítače Azure pro řešení potíží s** ](#troubleshooting-azure-virtual-machine-onboarding) oddílu.

### <a name="azure-portal"></a>portál Azure

V [webu Azure portal](https://portal.azure.com/), přejděte do účtu Azure Automation, ve které chcete připojit virtuální počítače. Na stránce konfigurace stavu a **uzly** klikněte na tlačítko **+ přidat**.

Vyberte virtuální počítač Azure připojit.

Pokud počítač nemá žádné PowerShell desired nainstalované rozšíření stav a stav napájení je spuštěna, klikněte na tlačítko **připojit**.

V části **registrace**, zadejte [hodnoty prostředí PowerShell DSC Local Configuration Manageru](/powershell/dsc/metaconfig4) vyžadované pro vašemu případu použití a volitelně konfigurace uzlu přiřazení k virtuálnímu počítači.

![Registrace](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Azure virtual machines můžete nasadit a zapojený do služby Azure Automation stav konfigurace prostřednictvím šablon Azure Resource Manageru. Zobrazit [serveru spravované službou Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) pro příklad šablony, který připojí existujícího virtuálního počítače do Azure Automation stavu konfigurace.
Pokud spravujete Škálovací sady virtuálních počítačů, naleznete v tématu Ukázková šablona [nastavení konfigurace škálování virtuálního počítače spravované službou Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

[Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) rutinu je možné připojit virtuální počítače na webu Azure Portal přes PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrace virtuálních počítačů mezi předplatnými Azure

Nejlepší způsob, jak zaregistrovat virtuální počítače z jiných předplatných Azure je použít rozšíření DSC v nasazení šablony Azure Resource Manageru.
Příklady jsou uvedeny v [Desired State Configuration rozšíření pomocí šablon Azure Resource Manageru](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Registrační klíč a adresa URL pro registraci k použití jako parametry v šabloně najdete v tématu následující [ **zabezpečení registrace** ](#secure-registration) oddílu.

## <a name="amazon-web-services-aws-virtual-machines"></a>Virtuální počítače služby Amazon Web Services (AWS)

Můžete snadno připojit virtuální počítače služby Amazon Web Services za správu konfigurace pomocí Azure Automation stavu konfigurace pomocí AWS DSC Toolkit. Další informace o sadu nástrojů [tady](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Windows fyzické nebo virtuální počítače, místně nebo v jiném cloudu než Azure nebo AWS

Windows servery s místně nebo v jiných cloudových prostředích může být také zapojený do služby Azure Automation stav konfigurace, za předpokladu, že mají [odchozí přístup k Azure](automation-dsc-overview.md#network-planning):

1. Ujistěte se, že nejnovější verze [WMF 5](https://aka.ms/wmf5latest) je nainstalován v počítačích, které chcete pro připojení k Azure Automation stav konfigurace.
1. Postupujte podle pokynů v následující části [ **generování DSC metaconfigurations** ](#generating-dsc-metaconfigurations) ke generování složku obsahující potřebné metaconfigurations DSC.
1. Vzdáleně se vztahují metaconfiguration DSC Powershellu na počítače, které chcete připojit. **Příkaz spouštíte z počítače musí mít nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) nainstalované**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nemůžete použít metaconfigurations DSC Powershellu vzdáleně, zkopírujte složku metaconfigurations z kroku 2 na každý počítač připojit. Poté zavolejte **Set-DscLocalConfigurationManager** místně na každém počítači na připojení.
1. Pomocí webu Azure portal nebo rutin, zkontrolujte, jestli počítače k zařazení do se nyní zobrazují jako stav konfigurace uzlů registrované ve vašem účtu Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Linux fyzické nebo virtuální počítače na místní nebo v jiném cloudu než Azure

Servery s Linuxem s místně nebo v jiných cloudových prostředích může být také zapojený do služby Azure Automation stav konfigurace, za předpokladu, že mají [odchozí přístup k Azure](automation-dsc-overview.md#network-planning):

1. Ujistěte se, že nejnovější verze [PowerShell Desired State Configuration pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) je nainstalován v počítačích, které chcete pro připojení k Azure Automation stav konfigurace.
1. Pokud [výchozí hodnoty prostředí PowerShell DSC Local Configuration Manageru](/powershell/dsc/metaconfig4) odpovídat vašemu případu použití a vy chcete připojení těchto počítačů, kterou **obě** načítat a hlásí stav konfigurace Azure Automation:

   - Na každém počítači s Linuxem pro připojení k Azure Automation stav konfigurace, použijte `Register.py` pro připojení pomocí výchozího nastavení prostředí PowerShell DSC Local Configuration Manageru:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Registrační klíč a adresa URL pro registraci u vašeho účtu Automation, najdete v tématu následující [ **zabezpečení registrace** ](#secure-registration) oddílu.

     Pokud výchozí nastavení prostředí PowerShell DSC Local Configuration Manageru **nejsou** odpovídat vašemu případu použití, nebo můžete chtít připojit počítače, tak, aby pouze ohlásí konfigurace stavu služby Azure Automation, postupujte podle kroků 3 až 6. V opačném případě přejděte přímo ke kroku 6.

1. Postupujte podle pokynů v následujícím [ **generování DSC metaconfigurations** ](#generating-dsc-metaconfigurations) část k vygenerování složku obsahující potřebné metaconfigurations DSC.
1. Vzdáleně se vztahují metaconfiguration DSC Powershellu na počítače, které chcete připojit:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Příkaz spouštíte z počítače musí mít nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) nainstalované.

1. Pokud nemůžete použít metaconfigurations DSC Powershellu vzdáleně, zkopírujte metaconfiguration odpovídající tomuto počítači ze složky v kroku 5 na počítači s Linuxem. Poté zavolejte `SetDscLocalConfigurationManager.py` místně na každém počítači s Linuxem chcete pro připojení k Azure Automation stav konfigurace:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Pomocí webu Azure portal nebo rutin, zkontrolujte, jestli počítače k zařazení do se nyní zobrazují jako uzly DSC registrované ve vašem účtu Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generování DSC metaconfigurations

Obecně připojit žádné počítače do Azure Automation stav konfigurace, [DSC metaconfiguration](/powershell/dsc/metaconfig) lze generovat, který říká agentovi DSC, aby načítat a/nebo sestavy do konfigurace stavu služby Azure Automation. Metaconfigurations DSC Azure automatizace stavu konfigurace můžete vygenerovat pomocí prostředí PowerShell DSC konfigurace nebo rutiny prostředí PowerShell pro Azure Automation.

> [!NOTE]
> DSC metaconfigurations obsahovat tajné klíče potřebné ke připojit počítače s služby Automation účtu pro správu. Ujistěte se, že dobře chráněna jakékoli metaconfigurations DSC, které vytvoříte, nebo je odstranit za použití.

### <a name="using-a-dsc-configuration"></a>Pomocí konfigurace DSC

1. Otevřete VSCode (nebo svém oblíbeném editoru) jako správce na počítači v místním prostředí. Počítač musí mít nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) nainstalované.
1. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci DSC Powershellu pro vytvoření metaconfigurations a pusťte se do vytváření metaconfiguration příkazu.

> [!NOTE]
> Stav konfigurace uzlu názvy jsou malá a velká písmena na portálu. Pokud jsou neodpovídající uzel nezobrazí v části **uzly** kartu.

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

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
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

1. Vyplňte registrační klíč a adresu URL pro svůj účet Automation, stejně jako názvy počítačů na připojení. Všechny ostatní parametry jsou volitelné. Registrační klíč a adresa URL pro registraci u vašeho účtu Automation, najdete v tématu následující [ **zabezpečení registrace** ](#secure-registration) oddílu.
1. Pokud chcete, aby počítače hlásit DSC informace o stavu konfigurace stavu služby Azure Automation, ale ne o přijetí změn konfigurace nebo moduly Powershellu, nastavte **jen** parametr na hodnotu true.
1. Spusťte skript. Teď byste měli mít složku s názvem **DscMetaConfigs** ve svém pracovním adresáři, který obsahuje metaconfigurations PowerShell DSC pro počítače pro připojení (jako správce):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Pomocí rutin Azure Automation.

Pokud odpovídají vašemu případu použití výchozí hodnoty prostředí PowerShell DSC Local Configuration Manageru a chcete počítače můžete připojit tak, aby se načítat i zprávy do konfigurace stavu služby Azure Automation, rutiny služby Azure Automation poskytuje zjednodušený způsob generování metaconfigurations DSC, třeba:

1. Otevřete konzolu Powershellu nebo VSCode jako správce na počítači v místním prostředí.
2. Připojte se k Azure Resource Manageru pomocí `Connect-AzureRmAccount`
3. Stáhněte si metaconfigurations PowerShell DSC pro počítače, které chcete připojit z účtu Automation, ke kterému chcete připojit uzly:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Teď byste měli mít složku s názvem ***DscMetaConfigs***, obsahující metaconfigurations PowerShell DSC pro počítače pro připojení (jako správce):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Zabezpečení registrace

Počítače můžete bezpečně připojit k účtu Azure Automation pomocí protokolu registrace DSC WMF 5, který umožňuje uzlu DSC k ověření na serveru prostředí PowerShell DSC o přijetí změn nebo vytváření sestav (včetně Azure Automation stav konfigurace). Uzel se registruje na serveru s **adresa URL pro registraci**pomocí **registrační klíč**. Během registrace vyjednávání jedinečný certifikát pro tento uzel, který použijete pro ověřování serveru po registraci uzlu DSC a server pro vyžádání obsahu/sestavy DSC. Tento postup brání připojili uzlů z jednoho jiného, třeba když se uzel dojde k narušení zosobnění a chová závadně. Po registraci registrační klíč se používá k ověřování znovu a odstranění z uzlu.

Umožňuje získat všechny informace potřebné pro registraci protokolu konfigurace stavu z **klíče** pod **nastavení účtu** na webu Azure Portal. Otevřete toto okno kliknutím na ikonu klíče na **Essentials** panelu pro účet Automation.

![Azure automation klíče a adresy URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adresa URL pro registraci je do pole Adresa URL v okně Správa klíčů.
- Registrační klíč je primární přístupový klíč nebo sekundární přístupový klíč v okně Správa klíčů. Dá se ani jeden klíč.

Pro zvýšení zabezpečení, primární a sekundární přístupové klíče účtu služby Automation může být znovu vygenerován v každém okamžiku (na **Správa klíčů** stránky) zabránit budoucím uzel registrace pomocí dřívější klíče.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Řešení potíží registrace virtuálních počítačů Azure

Konfigurace stavu Azure Automation vám umožní snadno připojit virtuální počítače Azure Windows pro správu konfigurace. Pod pokličkou rozšíření virtuálního počítače Azure, Desired State Configuration slouží k registraci virtuálního počítače pomocí Azure Automation State Configuration. Rozšíření Azure VM Desired State Configuration běží asynchronně, může být důležité pro sledování průběhu a řešení potíží s produktem.

> [!NOTE]
> Jakékoli metody objektu připojování virtuálního počítače Azure s Windows do Azure Automation stav konfigurace, který používá rozšíření Azure VM Desired State Configuration může trvat až hodinu, uzel, který má zobrazit až zaregistrovaný ve službě Azure Automation. To je způsobeno instalace Windows Management Framework 5.0 na virtuálním počítači rozšíření DSC virtuálního počítače Azure, které je potřeba pro připojení virtuálních počítačů do Azure Automation stavu konfigurace.

Řešení potíží nebo zobrazit stav rozšíření Azure VM Desired State Configuration, na webu Azure Portal přejděte k virtuálnímu počítači se připojit a pak klikněte na **rozšíření** pod **nastavení**. Pak klikněte na tlačítko **DSC** nebo **DSCForLinux** v závislosti na operačním systému. Další podrobnosti, můžete kliknout na **zobrazit podrobný stav**.

## <a name="certificate-expiration-and-reregistration"></a>Vypršení platnosti certifikátu a opětovná

Po registraci počítače s jako uzel v konfiguraci stavu služby Azure Automation DSC, jsou z několika důvodů, proč je nutné znovu registrovat tento uzel v budoucnosti:

- Po registraci, automatické každý uzel jedinečný certifikát pro ověřování, jejíž platnost vyprší po jednom roce. V současné době protokol registrace DSC Powershellu nelze obnovit automaticky certifikáty při jejich brzy vyprší platnost, takže budete muset znovu registrovat uzlů po dobu za rok. Před nnásledující, ujistěte se, že každý uzel se systémem Windows Management Framework 5.0 RTM. Pokud vyprší platnost certifikátu ověřování uzlu a uzel není přeregistrován, uzel nemůže komunikovat s Azure Automation a je označená "Unresponsive." Opětovná provést 90 dní nebo méně od času vypršení platnosti certifikátu, nebo kdykoli po dobu platnosti certifikátu, výsledkem bude nový certifikát se generovat a používat.
- Chcete-li změnit některý [hodnoty prostředí PowerShell DSC Local Configuration Manageru](/powershell/dsc/metaconfig4) , které byly nastaveny při počáteční registraci uzlu, jako je například ConfigurationMode. V současné době můžete tyto hodnoty agenta DSC změnit jenom prostřednictvím registrace. Jedinou výjimkou je konfigurace uzlu přiřazené k uzlu – to se dá změnit v Azure Automation DSC přímo.

Opětovná lze provádět v stejným způsobem zaregistrovat uzlu na začátku pomocí některé z metod registrace popsané v tomto dokumentu. Zrušení registrace uzlu z konfigurace služby Azure Automation stavu před nnásledující ji nepotřebujete.

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)
