---
title: Instalace agenta připojeného počítače pomocí Windows PowerShell DSC
description: V tomto článku se dozvíte, jak připojit počítače k Azure pomocí serverů s podporou Azure ARC (Preview) pomocí Windows PowerShell DSC.
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 675258ff95829c2dc9922571db5014b2ba93d336
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565816"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Postup instalace agenta připojeného počítače pomocí Windows PowerShell DSC

Pomocí [Konfigurace požadovaného stavu Windows PowerShellu](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) můžete automatizovat instalaci a konfiguraci softwaru pro počítač se systémem Windows. Tento článek popisuje, jak pomocí DSC nainstalovat do hybridních počítačů s Windows agenta připojeného počítače (Preview) s podporou Azure ARC.

## <a name="requirements"></a>Požadavky

- Windows PowerShell verze 4,0 nebo vyšší

- Modul [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC

- Instanční objekt pro připojení počítačů k serverům s podporou Azure ARC (Preview) bez interaktivně. Pokud jste ještě nevytvořili instanční objekt pro servery s podporou ARC (Preview), postupujte podle kroků v části [Vytvoření instančního objektu pro škálování ve velkém měřítku](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) .

## <a name="install-the-connectedmachine-dsc-module"></a>Instalace modulu ConnectedMachine DSC

1. Chcete-li ručně nainstalovat modul, Stáhněte zdrojový kód a rozbalte obsah adresáře projektu do `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Nebo spusťte následující příkaz, který nainstalujete z Galerie prostředí PowerShell pomocí PowerShellGet (v PowerShellu 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Pokud chcete potvrdit instalaci, spusťte následující příkaz a ujistěte se, že jsou dostupné prostředky DSC počítače připojené k Azure.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Ve výstupu by se mělo zobrazit něco podobného jako v následujícím příkladu:

   ![Příklad potvrzení instalace modulu připojení počítače DSC](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

Prostředky v tomto modulu jsou navržené tak, aby spravovaly konfiguraci agenta počítače připojeného k Azure. Součástí je také skript prostředí PowerShell `AzureConnectedMachineAgent.ps1` , který se nachází ve `AzureConnectedMachineDsc\examples` složce. Používá komunitní zdroje k automatizaci stahování a instalace a navázání připojení pomocí ARC Azure. Tento skript provádí podobné kroky popsané v tématu [připojení hybridních počítačů k Azure z Azure Portal](onboard-portal.md) .

Pokud počítač potřebuje komunikovat prostřednictvím proxy server ke službě, po instalaci agenta musíte spustit příkaz, který je [zde](manage-agent.md#update-or-remove-proxy-settings)popsán. Tím se nastaví proměnná prostředí proxy server systému `https_proxy` . Místo ručního spuštění příkazu můžete tento krok provést s DSC pomocí modulu [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) .

>[!NOTE]
>Aby bylo možné spustit DSC, musí být systém Windows nakonfigurovaný tak, aby přijímal vzdálené příkazy PowerShellu i v případě, že používáte konfiguraci localhost. Pokud chcete prostředí snadno nakonfigurovat správně, stačí spustit `Set-WsManQuickConfig -Force` v terminálu PowerShellu se zvýšenými oprávněními.
>

Konfigurační dokumenty (soubory MOF) lze použít v počítači pomocí `Start-DscConfiguration` rutiny.

Níže jsou uvedené parametry, které předáte skriptu PowerShellu, který se má použít.

- `TenantId`: Jedinečný identifikátor (GUID), který představuje vaši vyhrazenou instanci služby Azure AD.

- `SubscriptionId`: ID předplatného vašeho předplatného Azure, ve kterém chcete počítače.

- `ResourceGroup`: Název skupiny prostředků, do které chcete připojené počítače patřit.

- `Location`: Viz [podporované oblasti Azure](overview.md#supported-regions). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.

- `Tags`: Pole řetězců značek, které by měly být aplikovány na prostředek připojeného počítače.

- `Credential`: Objekt přihlašovacích údajů PowerShellu s **ApplicationId** a **heslem** , který se používá k registraci počítačů ve velkém rozsahu pomocí [instančního objektu](onboard-service-principal.md). 

1. V konzole PowerShellu přejděte do složky, kam jste `.ps1` soubor uložili.

2. Spusťte následující příkazy PowerShellu pro zkompilování dokumentu MOF (informace o kompilaci konfigurací DSC najdete v tématu [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations?view=powershell-7)):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Tím se vytvoří `localhost.mof file` Nová složka s názvem `C:\dsc` .

Po nainstalování agenta a jeho konfiguraci pro připojení k serverům s podporou Azure ARC (verze Preview) přejdete na Azure Portal a ověříte tak, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Přidávání do stávajících konfigurací

Tento prostředek se dá přidat do stávajících konfigurací DSC, které budou představovat ucelenou konfiguraci pro počítač. Můžete například chtít přidat tento prostředek do konfigurace, která nastaví zabezpečené nastavení operačního systému.

Modul [CompositeResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) z Galerie prostředí PowerShell lze použít k vytvoření [složeného prostředku](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) ukázkové konfigurace, aby bylo možné dále zjednodušit kombinování konfigurací.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

- Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).
