---
title: Připojení hybridních počítačů k Azure ve velkém měřítku
description: V tomto článku se dozvíte, jak připojit počítače k Azure pomocí Azure Arc pro servery (náhled) pomocí instančního objektu.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192269"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Připojení hybridních počítačů k Azure ve velkém měřítku

Azure Arc pro servery (preview) můžete povolit pro více počítačů s Windows nebo Linuxem ve vašem prostředí s několika flexibilními možnostmi v závislosti na vašich požadavcích. Pomocí šablony skriptu, který poskytujeme, můžete automatizovat každý krok instalace, včetně navázání připojení k Azure Arc. Je však nutné interaktivně spustit tento skript s účtem, který má zvýšená oprávnění na cílovém počítači a v Azure. Chcete-li připojit počítače k Azure Arc pro servery, můžete použít [správce služby](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory namísto použití privilegované identity k [interaktivnímu připojení počítače](onboard-portal.md). Instanční objekt je zvláštní omezená identita správy, která je udělena `azcmagent` pouze minimální oprávnění potřebná k připojení počítačů k Azure pomocí příkazu. To je bezpečnější než použití vyšší privilegovaného účtu, jako je správce klienta a řídí se našimi doporučenými postupy zabezpečení řízení přístupu. Instanční objekt se používá pouze při nástupu, není používán k žádnému jinému účelu.  

Metody instalace k instalaci a konfiguraci agenta připojeného počítače vyžadují, aby automatická metoda, kterou používáte, měla oprávnění správce počítačů. Na Linuxu, pomocí kořenového účtu a windows, jako člen skupiny Místní správci.

Než začnete, zkontrolujte [požadavky](overview.md#prerequisites) a ověřte, zda vaše předplatné a prostředky splňují požadavky.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Na konci tohoto procesu budete úspěšně připojeni hybridní počítače k Azure Arc pro servery.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Vytvoření instančního objektu pro připisování ve velkém měřítku

[Azure PowerShell](/powershell/azure/install-az-ps) můžete použít k vytvoření instančního objektu s [rutinou New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Nebo můžete postupovat podle kroků uvedených v části [Vytvořit instanční objekt pomocí portálu Azure](../../active-directory/develop/howto-create-service-principal-portal.md) k dokončení tohoto úkolu.

> [!NOTE]
> Při vytváření instančního objektu musí být váš účet vlastníkem nebo správcem přístupu uživatelů v předplatném, které chcete použít pro připojení. Pokud nemáte dostatečná oprávnění k vytvoření přiřazení rolí, může být vytvořen instanční objekt, ale nebude moci napalubě počítačů.
>

Chcete-li vytvořit instanční objekt pomocí prostředí PowerShell, proveďte následující kroky.

1. Spusťte následující příkaz. Výstup [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) rutiny je nutné uložit do proměnné, jinak nebudete moci načíst heslo potřebné v pozdějším kroku.

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

2. Chcete-li načíst `$sp` heslo uložené v proměnné, spusťte následující příkaz:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Ve výstupu najděte hodnotu hesla pod **heslem** pole a zkopírujte ji. Také najít hodnotu pod pole **ApplicationId** a zkopírujte také. Uložte je na později na bezpečném místě. Pokud zapomenete nebo ztratíte heslo hlavního povinného [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) servisu, můžete ho obnovit pomocí rutiny.

Hodnoty z následujících vlastností se používají `azcmagent`s parametry předanými do :

* Hodnota z **ApplicationId** vlastnost se `--service-principal-id` používá pro hodnotu parametru
* Hodnota z vlastnosti **password** se `--service-principal-secret` používá pro parametr použitý k připojení agenta.

> [!NOTE]
> Ujistěte se, že používáte vlastnost **ApplicationId** instančního objektu, nikoli **id** vlastnost.
>

Role **Připojení k počítači Azure** obsahuje jenom oprávnění vyžadované k připojení počítače. Můžete přiřadit oprávnění instančního objektu, který umožní, aby jeho obor zahrnoval skupinu prostředků nebo předplatné. Pokud chcete přidat přiřazení rolí, přečtěte si informace o [přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md) nebo [přidejte nebo odeberte přiřazení rolí pomocí Azure RBAC a Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

Následující kroky nainstalují a nakonfigurují agenta připojeného počítače na hybridních počítačích pomocí šablony skriptu, která provádí podobné kroky popsané v [hybridních počítačích Connect do Azure z článku portálu Azure.](onboard-portal.md) Rozdíl je v posledním kroku, kde navážete `azcmagent` připojení k Azure Arc pomocí příkazu pomocí instančního objektu. 

Následují nastavení, která nakonfigurujete `azcmagent` příkaz pro instanční objekt.

* `tenant-id`: Jedinečný identifikátor (GUID), který představuje vyhrazenou instanci Azure AD.
* `subscription-id`: ID předplatného (GUID) vašeho předplatného Azure, ve kterém chcete počítače.
* `resource-group`: Název skupiny prostředků, do kterého chcete, aby připojené počítače patřily.
* `location`: Viz [podporované oblasti Azure](overview.md#supported-regions). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.
* `resource-name`: (*Volitelné)* Používá se pro reprezentaci prostředků Azure vašeho místního počítače. Pokud tuto hodnotu nezadáte, použije se název hostitele počítače.

Další informace o `azcmagent` nástroji příkazového řádku získáte v recenzi na [odkaz azcmagenta](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Instalační skript systému Windows

Následuje příklad agenta připojeného počítače pro instalační skript systému Windows, který byl upraven tak, aby používal instanční objekt pro podporu plně automatizované neinteraktivní instalace agenta.

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

### <a name="linux-installation-script"></a>Instalační skript pro Linux

Následuje příklad instalačního skriptu connected machine agenta pro Linux, který byl upraven tak, aby používal instanční objekt pro podporu plně automatizované neinteraktivní instalace agenta.

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

Po instalaci agenta a nakonfigurajete ho pro připojení k Azure Arc pro servery (preview), přejděte na portál Azure a ověřte, že server byl úspěšně připojen. Zobrazení počítačů na [webu Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak spravovat počítač pomocí [Zásad Azure](../../governance/policy/overview.md), pro takové věci, jako je [konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače , ověření, že počítač hlásí očekávanému pracovnímu prostoru Log Analytics, povolit monitorování pomocí [Azure Monitoru pomocí virtuálních počítačů](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)a mnoho dalšího.

- Další informace o [agentovi Analýzy protokolů](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro Windows a Linux je vyžadován, pokud chcete proaktivně sledovat operační systém a úlohy spuštěné na počítači, spravovat jej pomocí runbooků automation nebo řešení, jako je správa aktualizací, nebo používat jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).
