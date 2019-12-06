---
title: Rychlý Start – připojení počítačů k Azure pomocí ARC Azure pro servery – PowerShell
description: V tomto rychlém startu se dozvíte, jak připojit počítače k Azure pomocí ARC Azure pro servery pomocí PowerShellu.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní a integrovaný
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: b5299f49663fbf2e828f76d9c240a86fdccb2ff2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872685"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Rychlý Start: připojení počítačů k Azure pomocí ARC Azure pro servery – PowerShell

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Přečtěte si téma Podporované klienty a požadovaná konfigurace sítě v článku [Přehled služby Azure ARC pro servery](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Vytvoření instančního objektu pro registraci ve velkém měřítku

Instanční objekt je speciální omezená identita pro správu, která je udělována jenom minimálním oprávněním, která jsou nutná pro připojení počítačů k Azure. Je to bezpečnější než použití výkonnějšího účtu, jako je Správce klienta. Instanční objekt se používá jenom během připojování. Instanční objekt můžete po připojení požadovaných serverů bezpečně odstranit.

> [!NOTE]
> Tento krok se doporučuje, ale není povinný.

### <a name="steps-to-create-the-service-principal"></a>Postup vytvoření instančního objektu

V tomto příkladu použijeme [Azure PowerShell](/powershell/azure/install-az-ps) k vytvoření hlavního názvu služby (SPN). Případně můžete postupovat podle kroků uvedených v části [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) pro tuto úlohu.

Role `Azure Connected Machine Onboarding` obsahuje pouze oprávnění požadovaná pro registraci. Můžete definovat oprávnění hlavního názvu služby (SPN), abyste povolili její obor pro pokrytí skupiny prostředků nebo předplatného.

Je nutné uložit výstup rutiny [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) , nebo nebudete moci načíst heslo k použití v pozdějším kroku.

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

> [!NOTE] 
> Vaše oprávnění hlavního názvu služby (SPN) si může nějakou dobu trvat, než se správně naplní. Pokud chcete nastavit oprávnění mnohem rychleji, spusťte následující přiřazení role.
> ``` PowerShell
> New-AzRoleAssignment -RoleDefinitionName "Azure Connected Machine Onboarding" -ServicePrincipalName $sp.ApplicationId
> ```

Teď načtěte heslo pomocí PowerShellu.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Z výstupu zkopírujte **heslo** a **ApplicationId** (z předchozího kroku) a uložte je pro pozdější použití na bezpečném místě, jako je úložiště tajného kódu pro nástroj Konfigurace serveru. Pokud zapomenete nebo ztratíte heslo hlavního názvu služby, můžete ho resetovat pomocí rutiny [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

V instalačním skriptu instalace agenta:

* Vlastnost **ApplicationId** se používá pro parametr `--service-principal-id`, který se používá při instalaci agenta.
* Vlastnost **Password** se používá pro parametr `--service-principal-secret` v instalačním agentovi.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Ruční instalace agenta a připojení k Azure

Následující průvodce vám umožní připojit počítač k Azure přihlášením k počítači a provedením kroků. Počítače můžete [z portálu](quickstart-onboard-portal.md)taky připojit k Azure.

### <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

Instalace balíčku agenta vyžaduje na cílovém serveru přístup root nebo místního správce, ale nemá přístup k Azure.

#### <a name="linux"></a>Linux

U serverů se **systémem Linux** je agent distribuován prostřednictvím [úložiště balíčků od společnosti Microsoft](https://packages.microsoft.com) s použitím preferovaného formátu balíčku pro distribuci (. Ot./min. nebo. DEB).

> [!NOTE]
> Během Public Preview byl vydán pouze jeden balíček, který je vhodný pro Ubuntu 16,04 nebo 18,04.

<!-- What about this aks? -->
Nejjednodušší možností je zaregistrovat úložiště balíčků a pak balíček nainstalovat pomocí Správce balíčků distribuce.
Skript bash umístěný v [https://aka.ms/azcmagent](https://aka.ms/azcmagent) provádí následující akce:

1. Nakonfiguruje hostitelský počítač, který se má stáhnout z `packages.microsoft.com`.
2. Nainstaluje balíček hybridního poskytovatele prostředků.
3. Volitelně nakonfiguruje agenta pro operaci proxy serveru, pokud zadáte `--proxy`.

Skript také obsahuje kontroly podporovaných a nepodporovaných distribucí a zjišťování požadovaných oprávnění pro instalaci.

Následující příklad stáhne agenta a nainstaluje jej bez jakýchkoli podmíněných kontrol.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Pokud nechcete odkazovat na úložiště balíčků od Microsoftu, můžete soubor balíčku zkopírovat z něj do interního úložiště.

#### <a name="windows"></a>Windows

V případě **systému Windows**je agent zabalen do souboru Instalační služba systému Windows (`.MSI`) a lze jej stáhnout z [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), která je hostována na [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> V systému Linux se znovu spustí instalační skript, který bude automaticky upgradován na nejnovější verzi. V systému Windows je před opětovným spuštěním instalačního programu pro upgrade nutné odinstalovat agenta připojeného počítače Azure.

### <a name="connecting-to-azure"></a>Připojování k Azure

Po instalaci můžete agenta spravovat a konfigurovat pomocí nástroje příkazového řádku s názvem `azcmagent.exe`. Agent je umístěný v části `/opt/azcmagent/bin` v systému Linux a `$env:programfiles\AzureConnectedMachineAgent` ve Windows.

Ve Windows otevřete PowerShell jako správce na cílovém uzlu a spusťte:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

V systému Linux otevřete prostředí a spusťte příkaz.

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parametry:

* `tenant-id`: identifikátor GUID klienta. Můžete ji najít v Azure Portal tak, že vyberete **vlastnosti** **služby Azure Active Directory** ->  -> **ID adresáře**.
* `subscription-id`: identifikátor GUID předplatného v Azure, kde se má počítač připojit.
* `resource-group`: Skupina prostředků, do které chcete počítač připojit.
* `location`: Přečtěte si téma [oblasti a umístění Azure](https://azure.microsoft.com/global-infrastructure/regions/). Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků. Ve verzi Public Preview se služba podporuje v **WestUS2** a **západní Evropa**.
* `resource-name`: (*volitelné*) se používá pro reprezentaci prostředků Azure vašeho místního počítače. Pokud tuto hodnotu nezadáte, použije se název hostitele počítače.

Další informace o nástroji ' azcmagent ' najdete v [referenčních](azcmagent-reference.md)informacích k azcmagent.
<!-- Isn't this still needed to view machines? -->

Po úspěšném dokončení se Váš počítač připojí k Azure. Počítač můžete zobrazit v Azure Portal tím, že navštívíte [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Úspěšné zprovoznění](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Konfigurace proxy serveru

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

V případě systému **Linux**, pokud server vyžaduje proxy server, můžete provést jednu z těchto akcí:

* Spusťte skript `install_linux_hybrid_agent.sh` z výše uvedeného oddílu [instalace agenta](#download-and-install-the-agent) s `--proxy`.
* Pokud jste již agenta nainstalovali, spusťte příkaz `/opt/azcmagent/bin/hybridrp_proxy add http://{proxy-url}:{proxy-port}`, který nakonfiguruje proxy server a restartuje agenta.

#### <a name="windows"></a>Windows

Pokud pro **Windows**server vyžaduje proxy server pro přístup k internetovým prostředkům, měli byste spuštěním následujícího příkazu nastavit proměnnou prostředí proxy server. Agent tak může použít proxy server pro přístup k Internetu.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Pro Public Preview se nepodporují ověřené proxy servery.

## <a name="clean-up"></a>Vyčištění

Pokud chcete odpojit počítač od Arc Azure pro servery, musíte provést dva kroky.

1. Vyberte počítač na [portálu](https://aka.ms/hybridmachineportal), klikněte na tlačítko se třemi tečkami (`...`) a vyberte **Odstranit**.
1. Odinstalujte agenta z počítače.

   V systému Windows můžete k odinstalaci agenta použít ovládací panel aplikace & funkce.
  
  ![Aplikace & funkce](./media/quickstart-onboard/apps-and-features.png)

   Pokud chcete odinstalaci skriptovat, můžete použít následující příklad, který načte **PackageId** a odinstaluje agenta pomocí `msiexec /X`.

   Podívejte se do klíče registru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` a vyhledejte **PackageId**. Pak můžete agenta odinstalovat pomocí `msiexec`.

   Následující příklad ukazuje odinstalaci agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   V systému Linux spusťte následující příkaz k odinstalaci agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přiřazení zásady k připojeným počítačům](../../governance/policy/assign-policy-portal.md)
