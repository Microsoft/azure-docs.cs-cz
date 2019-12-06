---
title: Připojování počítačů pro správu podle konfigurace stavu Azure Automation
description: Postup nastavení počítačů pro správu pomocí konfigurace stavu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 80038cf5fba18eca4fbbe1405df2a76cfc84e2db
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850325"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Připojování počítačů pro správu podle konfigurace stavu Azure Automation

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Proč spravovat počítače s konfigurací stavu Azure Automation?

Konfigurace stavu Azure Automation je služba správy konfigurace pro uzly DSC v jakémkoli cloudovém nebo místním datacentru.
Umožňuje snadnou a snadnou škálovatelnost v tisících počítačů z centrálního a bezpečného umístění.
Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali.
Služba konfigurace stavu Azure Automation je DSC, které Azure Automation Runbooky využívají ke skriptování prostředí PowerShell.
Jinými slovy, a to stejným způsobem, jakým Azure Automation pomáhá spravovat skripty prostředí PowerShell, vám také pomůže spravovat konfigurace DSC.
Další informace o výhodách použití konfigurace stavu Azure Automation najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).

Konfigurace stavu Azure Automation se dá použít ke správě různých počítačů:

- Virtuální počítače Azure
- Virtuální počítače Azure (klasické)
- Instance EC2 Amazon Web Services (AWS)
- Fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure/AWS
- Fyzické nebo virtuální počítače se systémem Linux v místním prostředí, v Azure nebo v jiném cloudu než Azure

Pokud nejste připravení spravovat konfiguraci počítače z cloudu, můžete také použít konfiguraci stavu Azure Automation jako koncový bod pouze sestavy.
Díky tomu můžete nastavit (push) konfigurace prostřednictvím DSC a zobrazit podrobnosti o vytváření sestav v Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure s konfigurací stavu je zahrnuta bez dalších poplatků, pokud je nainstalovaná rozšíření DSC virtuálního počítače větší než 2,70. Další podrobnosti najdete na [**stránce s cenami služby Automation**](https://azure.microsoft.com/pricing/details/automation/) .

V následujících částech najdete informace o tom, jak můžete připojit jednotlivé typy počítačů ke konfiguraci stavu Azure Automation.

## <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Konfigurace stavu Azure Automation umožňuje snadno připojit virtuální počítače Azure ke správě konfigurace pomocí Azure Portal, Azure Resource Managerch šablon nebo PowerShellu. V digestoři a bez správce, který se musí vzdáleně přidružit k virtuálnímu počítači, registruje rozšíření pro konfiguraci požadovaného stavu virtuálního počítače Azure virtuální počítač s konfigurací stavu Azure Automation.
Vzhledem k tomu, že se rozšíření konfigurace požadovaného stavu virtuálního počítače Azure spustí asynchronně, postup sledování jeho průběhu nebo řešení potíží je k dispozici v následujících oddílech [**řešení potíží s virtuálním počítačem Azure**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Portál Azure

V [Azure Portal](https://portal.azure.com/)přejděte na účet Azure Automation, kde chcete připojit virtuální počítače. Na stránce Konfigurace stavu a na kartě **uzly** klikněte na **+ Přidat**.

Vyberte virtuální počítač Azure, který se má připojit.

Pokud na počítači není nainstalovaná požadovaná rozšíření stavu prostředí PowerShell a stav napájení je spuštěno, klikněte na **připojit**.

V části **registrace**zadejte [hodnoty pro místní Configuration Manager PowerShellu DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) , které se vyžadují pro váš případ použití, a volitelně konfiguraci uzlu, která se má k virtuálnímu počítači přiřadit.

![registrace](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Virtuální počítače Azure je možné nasadit a připojit k Azure Automation konfiguraci stavu prostřednictvím šablon Azure Resource Manager. Příklad šablony, která připojí existující virtuální počítač k Azure Automation konfiguraci stavu, najdete v části [Server spravovaný službou pro konfiguraci požadovaného stavu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) .
Pokud spravujete sadu škálování virtuálního počítače, přečtěte si téma příklad šablony [VM Scale set Configuration spravovanou pomocí Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

Rutina [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) se dá použít k připojování virtuálních počítačů v Azure pomocí PowerShellu.
To se ale v tuto chvíli implementuje jenom pro počítače s Windows (rutina aktivuje jenom rozšíření Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrace virtuálních počítačů napříč předplatnými Azure

Nejlepším způsobem, jak registrovat virtuální počítače z jiných předplatných Azure, je použít rozšíření DSC v šabloně nasazení Azure Resource Manager.
Příklady jsou k dispozici v [rozšíření konfigurace požadovaného stavu pomocí šablon Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Pokud chcete najít registrační klíč a registrační adresu URL, které se použijí jako parametry v šabloně, přečtěte si následující část [**Zabezpečená registrace**](#secure-registration) .

## <a name="amazon-web-services-aws-virtual-machines"></a>Virtuální počítače s Amazon Web Services (AWS)

Pomocí sady nástrojů AWS DSC můžete snadno připojit Amazon Web Services virtuálních počítačů ke správě konfigurace pomocí Azure Automation konfigurace stavu. Další informace o sadě nástrojů najdete [tady](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure/AWS

Servery Windows, na kterých běží místně nebo v jiných cloudových prostředích, je taky možné připojit k Azure Automation konfiguraci stavu, pokud mají [odchozí přístup k Azure](automation-dsc-overview.md#network-planning):

1. Ujistěte se, že je na počítačích, které chcete připojit k Azure Automation konfigurace, nainstalovaná nejnovější verze [WMF 5](https://aka.ms/wmf5latest) .
1. Podle pokynů v následujícím oddílu [**generujících DSC metaconfigurations**](#generating-dsc-metaconfigurations) vygenerujte složku obsahující potřebné metaconfigurations DSC.
1. Vzdáleně použijte prostředí PowerShell DSC metaconfiguration na počítače, které chcete připojit. **Počítač, ze kterého se tento příkaz spouští, musí mít nainstalovanou nejnovější verzi [WMF 5](https://aka.ms/wmf5latest)** :

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte složku metaconfigurations z kroku 2 do každého počítače, který chcete připojit. Pak na každém počítači, který chcete připojit, zavolejte **set-DscLocalConfigurationManager** .
1. Pomocí rutiny Azure Portal nebo ověřte, že počítače, které se mají připojit, se nyní zobrazují jako uzly Konfigurace stavu zaregistrované ve vašem účtu Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fyzické nebo virtuální počítače se systémem Linux v místním prostředí nebo v jiném cloudu než Azure

Servery Linux spuštěné místně nebo v jiných cloudových prostředích je možné taky připojit k Azure Automation konfiguraci stavu, pokud mají [odchozí přístup k Azure](automation-dsc-overview.md#network-planning):

1. Ujistěte se, že je na počítačích, které chcete připojit Azure Automation ke konfiguraci konfigurace, nainstalovaná nejnovější verze [Konfigurace požadovaného stavu prostředí PowerShell pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .
1. Pokud se [výchozí nastavení Configuration Manager PowerShellu DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) shodují s vaším případem použití, a chcete připojit počítače tak, aby **obě** konfigurace stavu vyčetly a nahlásily do Azure Automation:

   - V každém počítači se systémem Linux, který se má připojit ke konfiguraci stavu Azure Automation, použijte `Register.py` k připojení pomocí výchozích nastavení v prostředí PowerShell Configuration Manager DSC:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Pokud chcete najít registrační klíč a adresu URL pro registraci k vašemu účtu Automation, přečtěte si následující část [**Zabezpečená registrace**](#secure-registration) .

     Pokud se výchozí nastavení místní Configuration Manager PowerShellu DSC **neshoduje** s vaším případem použití, nebo chcete počítače připojit tak, aby hlásily jenom Azure Automation konfiguraci stavu, postupujte podle kroků 3-6. V opačném případě pokračujte přímo na krok 6.

1. Podle pokynů v následující části [**generování DSC metaconfigurations**](#generating-dsc-metaconfigurations) vygenerujte složku obsahující potřebné metaconfigurations DSC.
1. Vzdáleně použijte prostředí PowerShell DSC metaconfiguration na počítače, které chcete připojit:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Počítač, ze kterého se tento příkaz spouští, musí mít nainstalovanou nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) .

1. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte metaconfiguration odpovídající tomuto počítači ze složky v kroku 5 do počítače se systémem Linux. Potom zavolejte `SetDscLocalConfigurationManager.py` místně na každém počítači se systémem Linux, který chcete připojit ke konfiguraci Azure Automation stavu:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Pomocí rutin Azure Portal nebo můžete zkontrolovat, že počítače, které se mají připojit, se teď zobrazují jako uzly DSC zaregistrované ve vašem účtu Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generování DSC metaconfigurations

Aby bylo možné obecně připojit jakýkoli počítač ke konfiguraci stavu Azure Automation, je možné vygenerovat [DSC metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig) , která agentovi DSC oznamuje, že se má vyžádat od a/nebo hlásit Azure Automation konfiguraci stavu. Metaconfigurations DSC pro konfiguraci stavu Azure Automation můžete vygenerovat buď pomocí konfigurace PowerShellu DSC, nebo rutin Azure Automation PowerShellu.

> [!NOTE]
> DSC metaconfigurations obsahují tajné klíče potřebné k připojení počítače k účtu Automation pro správu. Ujistěte se, že jste správně chránili všechny metaconfigurationsy DSC, které jste vytvořili, nebo je po použití odstraňte.

### <a name="using-a-dsc-configuration"></a>Použití konfigurace DSC

1. Otevřete VSCode (nebo oblíbený editor) jako správce v počítači v místním prostředí. Počítač musí mít nainstalovanou nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) .
1. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci PowerShellu DSC pro vytvoření metaconfigurations a příkaz pro vytvoření metaconfiguration.

> [!NOTE]
> Názvy konfigurací uzlu Konfigurace stavu rozlišují velká a malá písmena na portálu. Pokud se případ neshoduje, uzel se nezobrazí na kartě **uzly** .

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

1. Zadejte registrační klíč a adresu URL pro váš účet Automation a názvy počítačů, které se mají připojit. Všechny ostatní parametry jsou volitelné. Pokud chcete najít registrační klíč a adresu URL pro registraci k vašemu účtu Automation, přečtěte si následující část [**Zabezpečená registrace**](#secure-registration) .
1. Pokud chcete, aby počítače nahlásily informace o stavu DSC do konfigurace stavu Azure Automation, ale ne pro vyžádanou konfiguraci nebo moduly PowerShellu, nastavte parametr **reportOnly** na hodnotu true.
1. Spusťte skript. Teď byste měli mít ve svém pracovním adresáři složku s názvem **DscMetaConfigs** , která obsahuje PowerShell DSC metaconfigurations pro počítače, které se mají připojit (jako správce):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Používání rutin Azure Automation

Pokud se výchozí nastavení Configuration Manager PowerShellu pro DSC shodují s vaším případem použití, a chcete připojit počítače tak, aby načetly ze sestavy Azure Automation stav a nahlásily je, rutiny Azure Automation poskytují zjednodušenou metodu generování. potřebný metaconfigurations DSC:

1. Otevřete konzolu PowerShellu nebo VSCode jako správce v počítači v místním prostředí.
2. Připojení k Azure Resource Manager pomocí `Connect-AzAccount`
3. Stáhněte PowerShell DSC metaconfigurations pro počítače, které chcete připojit, z účtu Automation, na který chcete uzly připojit:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Teď byste měli mít složku s názvem ***DscMetaConfigs***, která obsahuje PowerShell DSC metaconfigurations pro počítače, které se mají připojit (jako správce):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Zabezpečená registrace

Počítače mohou bezpečně připojit Azure Automation k účtu pomocí registračního protokolu WMF 5 DSC, který umožňuje uzlu DSC ověřit u serveru pro vyžádání obsahu PowerShellu DSC nebo pro sestavy (včetně konfigurace stavu Azure Automation). Uzel se registruje na server v **registrační adrese URL**, který se ověřuje pomocí **registračního klíče**. V průběhu registrace vyjednávat uzel DSC a server pro vyžádání obsahu DSC/generování sestav jedinečný certifikát, který bude tento uzel používat k ověřování po registraci serveru. Tento proces znemožňuje uzlům připojit uzly od sebe navzájem, například pokud dojde k ohrožení bezpečnosti uzlu a chování se zlými úmysly. Po registraci se registrační klíč znovu nepoužívá pro ověřování a z uzlu se odstraní.

Informace požadované pro registrační protokol konfigurace stavu můžete získat z **klíčů** v části **nastavení účtu** v Azure Portal. Otevřete toto okno kliknutím na ikonu klíče na panelu **základy** pro účet Automation.

![Klíče a adresa URL služby Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adresa URL registrace je pole Adresa URL v okně Správa klíčů.
- Registrační klíč je primární přístupový klíč nebo sekundární přístupový klíč v okně spravovat klíče. Lze použít buď klíč.

Pro zvýšení zabezpečení můžete primární a sekundární přístupové klíče účtu Automation kdykoli znovu vygenerovat (na stránce **Správa klíčů** ) a zabránit tak budoucí registraci uzlů pomocí předchozích klíčů.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Řešení potíží s připojováním virtuálních počítačů Azure

Konfigurace stavu Azure Automation umožňuje snadno připojit virtuální počítače Azure s Windows ke správě konfigurace. V digestoři se k registraci virtuálního počítače s konfigurací stavu Azure Automation používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure. Vzhledem k tomu, že se rozšíření konfigurace požadovaného stavu virtuálního počítače Azure spustí asynchronně, sledování průběhu a řešení potíží s jeho spuštěním může být důležité.

> [!NOTE]
> Kterákoli z metod připojení virtuálního počítače Azure s Windows k Azure Automation stavu, který používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, může trvat až hodinu, než se uzel zobrazí jako zaregistrované v Azure Automation. Důvodem je instalace rozhraní Windows Management Framework 5,0 na virtuálním počítači pomocí rozšíření Azure VM DSC, které je nutné k připojení virtuálního počítače ke Azure Automation konfigurace stavu.

Pokud chcete vyřešit nebo zobrazit stav rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, přejděte v Azure Portal na virtuální počítač, který se připojuje, a pak klikněte na **rozšíření** v části **Nastavení**. Pak v závislosti na vašem operačním systému klikněte na **DSC** nebo **DSCForLinux** . Pro další podrobnosti můžete kliknout na **zobrazit podrobný stav**.

## <a name="certificate-expiration-and-reregistration"></a>Vypršení platnosti certifikátu a jeho registrace

Po registraci počítače jako uzlu DSC v konfiguraci stavu Azure Automation existuje několik důvodů, proč možná budete muset tento uzel v budoucnu znovu zaregistrovat:

- Pro verze Windows serveru starší než Windows Server 2019 každý uzel automaticky vyjednává jedinečný certifikát pro ověřování, jehož platnost vyprší po jednom roce. V současné době nemůže registrační protokol PowerShellu DSC automaticky obnovovat certifikáty, pokud se blíží vypršení platnosti, takže je potřeba znovu zaregistrovat uzly po roce. Před opětovným registrací zajistěte, aby každý uzel používal rozhraní Windows Management Framework 5,0 RTM. Pokud vyprší platnost certifikátu ověřování uzlu a uzel není znovu zaregistrován, uzel nemůže komunikovat s Azure Automation a je označen jako "neodpovídá". Nová registrace provedla 90 dní nebo méně z času vypršení platnosti certifikátu nebo kdykoli po uplynutí doby vypršení platnosti certifikátu dojde k vygenerování a použití nového certifikátu.  Řešení tohoto problému je součástí Windows serveru 2019 a novějších verzí.
- Chcete-li změnit všechny [hodnoty místního Configuration Manager prostředí PowerShell pro DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) , které byly nastaveny při počáteční registraci uzlu, například ConfigurationMode. V současné době se tyto hodnoty agenta DSC dají změnit jenom prostřednictvím změny registrace. Jedinou výjimkou je konfigurace uzlu přiřazená k uzlu – to se dá změnit přímo v Azure Automation DSC.

Pomocí kterékoli metody registrace popsané v tomto dokumentu můžete provést znovu registraci stejným způsobem jako na prvním zaregistrovaném uzlu. Před opětovnou registrací není nutné zrušit registraci uzlu v konfiguraci stavu Azure Automation.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/az.automation#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
