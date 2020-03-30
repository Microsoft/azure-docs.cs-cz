---
title: Instalace agenta připojeného počítače pomocí prostředí Windows PowerShell DSC
description: V tomto článku se dozvíte, jak připojit počítače k Azure pomocí Azure Arc pro servery (náhled) pomocí Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164680"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Instalace agenta připojeného počítače pomocí prostředí Windows PowerShell DSC

Pomocí [konfigurace požadovaného stavu prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) můžete automatizovat instalaci a konfiguraci softwaru pro počítač se systémem Windows. Tento článek popisuje, jak pomocí DSC nainstalovat Azure Arc pro servery connected machine agent na hybridních počítačích s Windows.

## <a name="requirements"></a>Požadavky

- Prostředí Windows PowerShell verze 4.0 nebo vyšší

- Modul [DSC AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Instanční objekt pro připojení počítačů k Azure Arc pro servery neinteraktivně. Postupujte podle kroků v části [Vytvoření instančního objektu pro přihlašování ve velkém měřítku,](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) pokud jste již nevytvořili instanční objekt pro Arc pro servery.

## <a name="install-the-connectedmachine-dsc-module"></a>Instalace modulu ConnectedMachine DSC

1. Chcete-li modul nainstalovat ručně, stáhněte zdrojový kód a rozbalte `$env:ProgramFiles\WindowsPowerShell\Modules folder`obsah adresáře projektu do . Nebo spusťte následující příkaz pro instalaci z galerie PowerShellpomocí PowerShellGet (v Prostředí PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Chcete-li potvrdit instalaci, spusťte následující příkaz a ujistěte se, že se zobrazí prostředky DSC připojeného počítače Azure, které jsou k dispozici.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Ve výstupu byste měli vidět něco podobného následujícímu:

   ![Příklad instalace modulu Connected Machine DSC](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

Prostředky v tomto modulu jsou navržené pro správu konfigurace Azure Connected Machine Agent. Součástí je také skript `AzureConnectedMachineAgent.ps1`prostředí PowerShell , který se nachází ve `AzureConnectedMachineDsc\examples` složce. Využívá komunitní prostředky k automatizaci stahování a instalace a navázání připojení k Azure Arc. Tento skript provádí podobné kroky popsané v připojení hybridních počítačů do Azure z článku [portálu Azure.](onboard-portal.md)

Pokud počítač potřebuje komunikovat prostřednictvím proxy serveru ke službě, po instalaci agenta je třeba spustit příkaz, který je popsán [zde](onboard-portal.md#configure-the-agent-proxy-setting). Tím nastavíte proměnnou `https_proxy`prostředí systému proxy serveru . Namísto ručního spuštění příkazu můžete tento krok provést pomocí dsc pomocí modulu [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Chcete-li povolit spuštění dsc, systém Windows musí být nakonfigurován pro příjem vzdálených příkazů prostředí PowerShell, i když používáte konfiguraci localhost. Chcete-li snadno správně nakonfigurovat prostředí, stačí spustit `Set-WsManQuickConfig -Force` v terminálu powershellu se zvýšenými oprávněními.
>

Konfigurační dokumenty (soubory MOF) lze `Start-DscConfiguration` použít na zařízení pomocí rutiny.

Níže jsou uvedeny parametry, které předáte skriptu Prostředí PowerShell, který chcete použít.

- `TenantId`: Jedinečný identifikátor (GUID), který představuje vyhrazenou instanci Azure AD.

- `SubscriptionId`: ID předplatného (GUID) vašeho předplatného Azure, ve kterém chcete počítače.

- `ResourceGroup`: Název skupiny prostředků, do kterého chcete, aby připojené počítače patřily.

- `Location`: Viz [podporované oblasti Azure](overview.md#supported-regions). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.

- `Tags`: Pole řetězec značek, které by měly být použity na prostředek připojeného počítače.

- `Credential`: Objekt pověření prostředí PowerShell s **Identifikátorem aplikace** a **heslem** používaným k registraci počítačů ve velkém měřítku pomocí [instančního objektu](onboard-service-principal.md). 

1. V konzole PowerShell přejděte do složky, do které jste soubor uložili. `.ps1`

2. Spusťte následující příkazy prostředí PowerShell pro kompilaci dokumentu MOF (informace o kompilaci konfigurací DSC naleznete v [tématu Konfigurace DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Tím se `localhost.mof file` vytvoří v nové `C:\dsc`složce s názvem .

Po instalaci agenta a nakonfigurajete ho pro připojení k Azure Arc pro servery (preview), přejděte na portál Azure a ověřte, že server byl úspěšně připojen. Zobrazení počítačů na [webu Azure Portal](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Přidání do existujících konfigurací

Tento prostředek lze přidat do existujícíkonfigurace DSC představují konfiguraci end-to-end pro počítač. Můžete například přidat tento prostředek do konfigurace, která nastaví nastavení zabezpečeného operačního systému.

Modul [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) z Galerie prostředí PowerShell lze použít k vytvoření [složeného prostředku](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) ukázkové konfigurace, aby se dále zjednodušilo kombinování konfigurací.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak spravovat počítač pomocí [Zásad Azure](../../governance/policy/overview.md), pro takové věci, jako je [konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače , ověření, že počítač hlásí očekávanému pracovnímu prostoru Log Analytics, povolit monitorování pomocí [Azure Monitoru pomocí virtuálních počítačů](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)a mnoho dalšího.

- Další informace o [agentovi Analýzy protokolů](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro Windows a Linux je vyžadován, pokud chcete proaktivně sledovat operační systém a úlohy spuštěné na počítači, spravovat jej pomocí runbooků automation nebo řešení, jako je správa aktualizací, nebo používat jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).