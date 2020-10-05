---
title: Připojení hybridních počítačů k Azure ve velkém měřítku
description: V tomto článku se dozvíte, jak připojit počítače k Azure pomocí serverů s podporou ARC Azure pomocí instančního objektu.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: dc92b1cb96b61caa17f141ca9a78fb10fe59a2a9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713414"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Připojení hybridních počítačů k Azure ve velkém měřítku

Můžete povolit servery s podporou ARC Azure pro více počítačů se systémem Windows nebo Linux ve vašem prostředí s několika flexibilními možnostmi v závislosti na vašich požadavcích. Pomocí skriptu šablony, který poskytujeme, můžete automatizovat všechny kroky instalace, včetně navázání připojení ke službě Azure ARC. Je ale potřeba, abyste tento skript mohli interaktivně spustit pomocí účtu, který má zvýšená oprávnění na cílovém počítači a v Azure. Pokud chcete počítače připojit k serverům s podporou ARC Azure, můžete místo používání privilegované identity použít [objekt služby](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory, abyste mohli [interaktivně připojit počítač](onboard-portal.md). Instanční objekt je speciální omezená identita pro správu, která je udělována jenom minimálním oprávněním, která jsou nutná pro připojení počítačů k Azure pomocí `azcmagent` příkazu. Je to bezpečnější než použití vyšší privilegovaného účtu, jako je Správce klienta, a postupuje podle osvědčených postupů zabezpečení řízení přístupu. Instanční objekt se používá jenom během připojování. nepoužívá se pro žádný jiný účel.  

Metody instalace pro instalaci a konfiguraci agenta připojeného počítače vyžadují, aby automatizovaná metoda, kterou použijete, měla na počítačích oprávnění správce. V systému Linux pomocí kořenového účtu a ve Windows jako člen místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Na konci tohoto procesu se vaše hybridní počítače úspěšně připojí k serverům s podporou ARC Azure.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Vytvoření instančního objektu pro registraci ve velkém měřítku

K vytvoření instančního objektu pomocí rutiny [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) můžete použít službu [Azure PowerShell](/powershell/azure/install-az-ps) . Nebo můžete postupovat podle kroků uvedených v části [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) k dokončení této úlohy.

> [!NOTE]
> Při vytváření instančního objektu musí být váš účet vlastníkem nebo správcem přístupu uživatele v rámci předplatného, které chcete použít pro registraci. Pokud nemáte dostatečná oprávnění k vytváření přiřazení rolí, může být objekt služby vytvořen, ale nebude moci připojit počítače.
>

Pokud chcete vytvořit instanční objekt pomocí PowerShellu, udělejte toto:

1. Spusťte následující příkaz. Výstup rutiny je nutné uložit [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) do proměnné nebo nebudete moci načíst heslo potřebné v pozdějším kroku.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Chcete-li načíst heslo uložené v `$sp` proměnné, spusťte následující příkaz:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Ve výstupu vyhledejte v poli **heslo** pole hodnotu heslo a zkopírujte ji. Vyhledejte také hodnotu v poli **ApplicationId** pole a zkopírujte také. Uložte je pro pozdější použití na bezpečném místě. Pokud zapomenete nebo ztratíte heslo instančního objektu služby, můžete ho resetovat pomocí [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) rutiny.

Hodnoty z následujících vlastností jsou použity s parametry předaných do `azcmagent` :

* Hodnota vlastnosti **ApplicationId** se používá pro `--service-principal-id` hodnotu parametru.
* Hodnota vlastnosti **Password** se používá pro  `--service-principal-secret` parametr použitý k připojení agenta.

> [!NOTE]
> Nezapomeňte použít vlastnost **ApplicationId** instančního objektu, nikoli vlastnost **ID** .
>

Role registrace **počítače připojeného k Azure** obsahuje jenom oprávnění potřebná k připojení počítače. Oprávnění instančního objektu můžete přiřadit, abyste umožnili jeho oboru zahrnutí skupiny prostředků nebo předplatného. Pokud chcete přidat přiřazení role, přečtěte si téma [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md) nebo [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

Následující postup nainstaluje a nakonfiguruje agenta připojeného počítače na hybridních počítačích pomocí šablony skriptu, která provádí podobný postup popsaný v tématu [připojení hybridních počítačů k Azure z Azure Portal](onboard-portal.md) . Rozdíl je v posledním kroku, kdy navážete připojení ke službě Azure ARC pomocí příkazu, který `azcmagent` používá objekt služby.

Níže jsou uvedené nastavení, pomocí kterého nakonfigurujete `azcmagent` příkaz pro použití instančního objektu.

* `tenant-id` : Jedinečný identifikátor (GUID), který představuje vaši vyhrazenou instanci služby Azure AD.
* `subscription-id` : ID předplatného vašeho předplatného Azure, ve kterém chcete počítače.
* `resource-group` : Název skupiny prostředků, do které chcete připojené počítače patřit.
* `location` : Viz [podporované oblasti Azure](overview.md#supported-regions). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.
* `resource-name` : (*Volitelné*) používá se pro reprezentaci prostředků Azure vašeho místního počítače. Pokud tuto hodnotu nezadáte, použije se název hostitele počítače.

Další informace o `azcmagent` nástroji příkazového řádku najdete v [referenčních](./manage-agent.md)informacích k Azcmagent.

### <a name="windows-installation-script"></a>Instalační skript Windows

Následuje příklad skriptu připojeného agenta pro instalaci systému Windows, který byl změněn tak, aby používal instanční objekt k podpoře plně automatizované, neinteraktivní instalace agenta.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>Skript podporuje běh jenom z 64 verze Windows PowerShellu.
>

### <a name="linux-installation-script"></a>Instalační skript pro Linux

Následuje příklad instalačního skriptu připojeného počítače pro Linux, který byl změněn tak, aby používal instanční objekt k podpoře plně automatizované, neinteraktivní instalace agenta.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>Aby bylo možné spustit **azcmagent**, musíte mít oprávnění *root* Access pro počítače se systémem Linux.

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

- Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

- Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).