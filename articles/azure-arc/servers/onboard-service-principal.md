---
title: Připojení hybridních počítačů k Azure ve velkém měřítku
description: V tomto článku se dozvíte, jak připojit počítače k Azure pomocí serverů s podporou ARC Azure pomocí instančního objektu.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831595"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Připojení hybridních počítačů k Azure ve velkém měřítku

Můžete povolit servery s podporou ARC Azure pro více počítačů se systémem Windows nebo Linux ve vašem prostředí s několika flexibilními možnostmi v závislosti na vašich požadavcích. Pomocí skriptu šablony, který poskytujeme, můžete automatizovat všechny kroky instalace, včetně navázání připojení ke službě Azure ARC. Je ale potřeba, abyste tento skript mohli interaktivně spustit pomocí účtu, který má zvýšená oprávnění na cílovém počítači a v Azure.

Pokud chcete počítače připojit k serverům s podporou ARC Azure, můžete místo používání privilegované identity použít [objekt služby](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory, abyste mohli [interaktivně připojit počítač](onboard-portal.md). Instanční objekt je speciální omezená identita pro správu, která je udělována jenom minimálním oprávněním, která jsou nutná pro připojení počítačů k Azure pomocí `azcmagent` příkazu. Je to bezpečnější než použití vyšší privilegovaného účtu, jako je Správce klienta, a postupuje podle osvědčených postupů zabezpečení řízení přístupu. Instanční objekt se používá jenom během připojování. nepoužívá se pro žádný jiný účel.  

Metody instalace pro instalaci a konfiguraci agenta připojeného počítače vyžadují, aby automatizovaná metoda, kterou použijete, měla na počítačích oprávnění správce. V systému Linux pomocí kořenového účtu a ve Windows jako člen místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky. Informace o podporovaných oblastech a dalších souvisejících aspektech najdete v tématu [podporované oblasti Azure](overview.md#supported-regions). Také si přečtěte náš [Průvodce plánováním v rámci škálování](plan-at-scale-deployment.md) , který vám pomůže pochopit kritéria návrhu a nasazení a také naše doporučení pro správu a monitorování.  

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Vytvoření instančního objektu pro registraci ve velkém měřítku

K vytvoření instančního objektu pomocí rutiny [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) můžete použít službu [Azure PowerShell](/powershell/azure/install-az-ps) . Nebo můžete postupovat podle kroků uvedených v části [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) k dokončení této úlohy.

> [!NOTE]
> Před vytvořením instančního objektu musí být váš účet členem role **Správce přístupu uživatelů nebo uživatelů** **v rámci** předplatného, které chcete použít pro registraci. Pokud nemáte dostatečná oprávnění ke konfiguraci přiřazení rolí, může být objekt služby vytvořen, ale nebude moci připojit počítače.
>

Pokud chcete vytvořit instanční objekt pomocí PowerShellu, proveďte následující kroky.

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

Role registrace **počítače připojeného k Azure** obsahuje jenom oprávnění potřebná k připojení počítače. Oprávnění instančního objektu můžete přiřadit, abyste umožnili jeho oboru zahrnutí skupiny prostředků nebo předplatného. Pokud chcete přidat přiřazení role, přečtěte si téma [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md) nebo [přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Vygenerujte instalační skript z Azure Portal

Skript pro automatizaci stahování a instalace a navázání připojení pomocí ARC Azure je dostupný z Azure Portal. K dokončení procesu proveďte následující kroky:

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

1. Na stránce **servery – ARC Azure** vyberte v levém horním rohu **Přidat** .

1. Na stránce **Vyberte metodu** vyberte dlaždici **Přidat více serverů** a pak vyberte **vygenerovat skript**.

1. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kam se budou ukládat metadata počítače. Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.

1. Na stránce **požadované součásti** zkontrolujte informace a pak vyberte **Další: podrobnosti o prostředku**.

1. Na stránce **Podrobnosti o prostředku** zadejte toto:

    1. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků, ze které se bude počítač spravovat.
    1. V rozevíracím seznamu **oblast** vyberte oblast Azure, do které se budou ukládat metadata serverů.
    1. V rozevíracím seznamu **operační systém** vyberte operační systém, na kterém je skript nakonfigurovaný ke spuštění.
    1. Pokud počítač komunikuje prostřednictvím proxy server pro připojení k Internetu, zadejte IP adresu proxy server nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu ve formátu `http://<proxyURL>:<proxyport>` .
    1. Vyberte **Další: ověřování**.

1. Na stránce **ověřování** v rozevíracím seznamu **instanční objekt** vyberte možnost **ARC-pro-servery**.  Pak vyberte, **Další: značky**.

1. Na stránce **značky** zkontrolujte vybrané výchozí **značky fyzického umístění** a zadejte hodnotu nebo zadejte jednu nebo více **vlastních značek** pro podporu standardů.

1. Vyberte **Další: Stáhněte a spusťte skript**.

1. Na stránce **Stáhnout a spustit skript** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete dělat změny, vyberte **Předchozí**.

Pro Windows se zobrazí výzva k uložení `OnboardingScript.ps1` a pro Linux `OnboardingScript.sh` do vašeho počítače.

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

Když jste vytvořili šablonu skriptu dříve, můžete nainstalovat a nakonfigurovat agenta připojeného počítače na více hybridních počítačích se systémem Linux a Windows pomocí preferovaného nástroje pro automatizaci v organizacích. Skript provádí podobný postup popsaný v tématu [připojení hybridních počítačů k Azure z Azure Portal](onboard-portal.md) . Rozdíl je v posledním kroku, kdy vytvoříte připojení k Arc Azure pomocí příkazu, který `azcmagent` používá instanční objekt.

Níže jsou uvedené nastavení, pomocí kterého nakonfigurujete `azcmagent` příkaz pro použití instančního objektu.

* `service-principal-id` : Jedinečný identifikátor (GUID), který představuje ID aplikace instančního objektu.
* `service-principal-secret` | Heslo objektu služby
* `tenant-id` : Jedinečný identifikátor (GUID), který představuje vaši vyhrazenou instanci služby Azure AD.
* `subscription-id` : ID předplatného vašeho předplatného Azure, ve kterém chcete počítače.
* `resource-group` : Název skupiny prostředků, do které chcete připojené počítače patřit.
* `location` : Viz [podporované oblasti Azure](overview.md#supported-regions). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.
* `resource-name` : (*Volitelné*) používá se pro reprezentaci prostředků Azure vašeho místního počítače. Pokud tuto hodnotu nezadáte, použije se název hostitele počítače.

Další informace o `azcmagent` nástroji příkazového řádku najdete v [referenčních](./manage-agent.md)informacích k Azcmagent.

>[!NOTE]
>Skript Windows PowerShellu podporuje jenom běh z 64 verze Windows PowerShellu.
>

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

- Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

- Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověřte, že počítač hlásí do očekávaného pracovního prostoru Log Analytics, umožní monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/vm/vminsights-enable-policy.md)a spoustu dalších věcí.

- Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh pomocí Azure Monitor pro virtuální počítače, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).
