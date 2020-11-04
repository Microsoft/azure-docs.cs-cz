---
title: Jak vytvořit definice zásad konfigurace hostů z Zásady skupinyho směrného plánu pro Windows
description: Přečtěte si, jak převést Zásady skupiny ze směrného plánu zabezpečení Windows serveru 2019 do definice zásady.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348640"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Jak vytvořit definice zásad konfigurace hostů z Zásady skupinyho směrného plánu pro Windows

Než začnete vytvářet vlastní definice zásad, je vhodné si přečíst informace o koncepčním přehledu v tématu [Azure Policy konfigurace hostů](../concepts/guest-configuration.md). Další informace o vytváření vlastních definic zásad konfigurace hostů pro Linux najdete v tématu [Postup vytvoření zásad konfigurace hostů pro Linux](./guest-configuration-create-linux.md). Další informace o vytváření vlastních definic zásad konfigurace hostů pro Windows najdete v tématu [Postup vytvoření zásad konfigurace hostů pro Windows](./guest-configuration-create.md).

Při auditování Windows konfigurace hosta k vytvoření konfiguračního souboru využívá modul prostředků DSC ([Desired State Configuration](/powershell/scripting/dsc/overview/overview)). Konfigurace DSC definuje stav, ve kterém by počítač měl být. Pokud vyhodnocení konfigurace **nedodržuje předpisy** , aktivuje se *auditIfNotExists* účinek zásad.
[Azure Policy konfigurace hostů](../concepts/guest-configuration.md) jenom auditují nastavení v počítačích.

> [!IMPORTANT]
> Rozšíření konfigurace hosta se vyžaduje k provádění auditů na virtuálních počítačích Azure. Pokud chcete nasadit rozšíření v celém počítači s Windows, přiřaďte následující definice zásad:
> - [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích s Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Nepoužívejte tajné klíče ani důvěrné informace v balíčcích vlastního obsahu.

Komunita DSC publikovala [modul BaselineManagement](https://github.com/microsoft/BaselineManagement) k převedení exportovaných šablon zásady skupiny do formátu DSC. V kombinaci s rutinou GuestConfiguration vytvoří modul BaselineManagement v Zásady skupiny obsahu Azure Policy konfigurační balíček hosta pro Windows. Podrobnosti o používání modulu BaselineManagement naleznete v článku [rychlý Start: převod zásady skupiny do DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

V této příručce Vás provedeme procesem vytvoření Azure Policy konfiguračního balíčku hosta z objektu Zásady skupiny (GPO). I když tento návod popisuje převod směrného plánu zabezpečení Windows serveru 2019, můžete stejný postup použít i u jiných objektů zásad skupiny.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Stáhněte si základní hodnoty zabezpečení Windows serveru 2019 a nainstalujte související moduly PowerShellu.

Instalace rozhraní **DSC** , **GuestConfiguration** , **správy standardních hodnot** a souvisejících modulů Azure v prostředí PowerShell:

1. Z příkazového řádku PowerShellu spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Vytvořte adresář pro a Stáhněte si standardní hodnoty zabezpečení Windows serveru 2019 ze sady nástrojů dodržování předpisů zabezpečení systému Windows.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Odblokuje a rozbalí stažený směrný plán serveru 2019.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Ověřte obsah směrného plánu serveru 2019 pomocí **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Převod z Zásady skupiny na Azure Policy konfiguraci hostů

V dalším kroku převede stažený směrný plán serveru 2019 na konfigurační balíček hosta pomocí modulů konfigurace hosta a základní správy.

1. Pomocí modulu pro správu standardních hodnot převeďte Zásady skupiny na konfiguraci požadovaného stavu.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Než vytvoříte balíček obsahu zásad, přejmenujte, přeformátujte a spusťte převedené skripty.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Vytvořte Azure Policy balíčku obsahu konfigurace hosta.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Vytvořit Azure Policy konfiguraci hosta

1. Dalším krokem je publikování souboru do Azure Blob Storage. Příkaz `Publish-GuestConfigurationPackage` vyžaduje `Az.Storage` modul.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Po vytvoření a nahrání balíčku vlastní zásady konfigurace hosta se vytvoří definice zásady konfigurace hosta. Pomocí `New-GuestConfigurationPolicy` rutiny vytvořte konfiguraci hosta.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Publikujte definice zásad pomocí `Publish-GuestConfigurationPolicy` rutiny. Rutina má pouze parametr **path** , který odkazuje na umístění souborů JSON, které vytvořil `New-GuestConfigurationPolicy` . K provedení příkazu Publikovat budete potřebovat přístup k vytvoření definic zásad v Azure. Konkrétní autorizační požadavky jsou zdokumentovány na stránce [přehled Azure Policy](../overview.md#getting-started) . Nejlepší integrovanou rolí je **Přispěvatel zásad prostředků**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Přiřazení definice zásad konfigurace hostů

V rámci zásad vytvořených v Azure je posledním krokem přiřazení iniciativy. Podívejte se, jak přiřadit iniciativu k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Definice zásad konfigurace hostů se musí **vždy** přiřadit pomocí iniciativy, která kombinuje zásady _AuditIfNotExists_ a _DeployIfNotExists_ . Pokud je přiřazena pouze zásada _AuditIfNotExists_ , požadavky nejsou nasazeny a zásady vždy ukazují, že jsou servery "0" kompatibilní.

Přiřazení definice zásady s _DeployIfNotExists_ účinkem vyžaduje další úroveň přístupu. Chcete-li udělit nejnižší oprávnění, můžete vytvořit vlastní definici role, která rozšiřuje **přispěvatele zásad prostředků**. V následujícím příkladu se vytvoří role s názvem **Přispěvatel zásad prostředků Rewards** s dodatečným oprávněním _Microsoft. Authorization/roleAssignments/Write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](./programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](./get-compliance-data.md).
