---
title: Připojení hybridních počítačů k Azure z Azure Portal
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítače k Azure pomocí serverů s podporou ARC Azure z Azure Portal.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: ca3c08acdef1b2a1f7c3774f5755967d472c93ed
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398024"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Připojení hybridních počítačů k Azure z Azure Portal

Můžete povolit servery s podporou ARC Azure pro jeden nebo malý počet počítačů s Windows nebo Linux ve vašem prostředí provedením sady kroků ručně. Nebo můžete použít automatizovanou metodu spuštěním skriptu šablony, který poskytujeme. Tento skript automatizuje stahování a instalaci obou agentů.

Tato metoda vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, jste členem místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky. Informace o podporovaných oblastech a dalších souvisejících aspektech najdete v tématu [podporované oblasti Azure](overview.md#supported-regions).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Vygenerujte instalační skript z Azure Portal

Skript pro automatizaci stahování a instalace a navázání připojení pomocí ARC Azure je dostupný z Azure Portal. Chcete-li dokončit proces, postupujte následovně:

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

1. Na stránce **servery – ARC Azure** vyberte v levém horním rohu **Přidat** .

1. Na stránce **Vyberte metodu** vyberte dlaždici **Přidat servery pomocí interaktivního skriptu** a pak vyberte **vygenerovat skript**.

1. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kam se budou ukládat metadata počítače. Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.

1. Na stránce **požadované součásti** zkontrolujte informace a pak vyberte **Další: podrobnosti o prostředku**.

1. Na stránce **Podrobnosti o prostředku** zadejte toto:

    1. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků, ze které se bude počítač spravovat.
    1. V rozevíracím seznamu **oblast** vyberte oblast Azure, do které se budou ukládat metadata serverů.
    1. V rozevíracím seznamu **operační systém** vyberte operační systém, na kterém je skript nakonfigurovaný ke spuštění.
    1. Pokud počítač komunikuje prostřednictvím proxy server pro připojení k Internetu, zadejte IP adresu proxy server nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu ve formátu `http://<proxyURL>:<proxyport>` .
    1. Vyberte **Další: značky**.

1. Na stránce **značky** zkontrolujte vybrané výchozí **značky fyzického umístění** a zadejte hodnotu nebo zadejte jednu nebo více **vlastních značek** pro podporu standardů.

1. Vyberte **Další: Stáhněte a spusťte skript**.

1. Na stránce **Stáhnout a spustit skript** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete dělat změny, vyberte **Předchozí**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalace a ověření agenta ve Windows

### <a name="install-manually"></a>Ruční instalace

Agenta připojeného počítače můžete nainstalovat ručně spuštěním balíčku Instalační služba systému Windows *AzureConnectedMachineAgent.msi*. Nejnovější verzi [balíčku Windows agent Instalační služba systému Windows](https://aka.ms/AzureConnectedMachineAgent) můžete stáhnout z webu služby Stažení softwaru.

>[!NOTE]
>* Chcete-li nainstalovat nebo odinstalovat agenta, je nutné mít oprávnění *správce* .
>* Nejdřív musíte stáhnout a zkopírovat instalační balíček do složky na cílovém serveru nebo ze sdílené síťové složky. Pokud spustíte instalační balíček bez jakýchkoli možností, spustí se Průvodce instalací nástroje, který můžete použít k interaktivní instalaci agenta.

Pokud počítač potřebuje komunikovat prostřednictvím proxy server ke službě, po instalaci agenta musíte spustit příkaz, který je popsaný v následujících krocích. Tento příkaz nastaví proměnnou prostředí proxy server systému `https_proxy` .

Pokud neznáte možnosti příkazového řádku pro Instalační služba systému Windows balíčky, Projděte si možnosti příkazového řádku nástroje [msiexec Standard](/windows/win32/msi/standard-installer-command-line-options) a [Možnosti příkazového](/windows/win32/msi/command-line-options)řádku programu Msiexec.

Například spusťte instalační program s `/?` parametrem, abyste si mohli prohlédnout možnost nápovědu a rychlé reference.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Chcete-li nainstalovat agenta tiše a vytvořit soubor protokolu instalace ve `C:\Support\Logs` složce, která existuje, spusťte následující příkaz.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Adresář protokolu je *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Pokud počítač potřebuje komunikovat prostřednictvím proxy server, nastavte proměnnou prostředí proxy server spuštěním následujícího příkazu:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >Agent nepodporuje nastavení ověřování proxy serveru v této verzi Preview.
    >

3. Po instalaci agenta je třeba ho nakonfigurovat, aby komunikoval se službou Azure ARC, a to spuštěním tohoto příkazu:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Instalace pomocí skriptované metody

1. Přihlaste se k serveru.

1. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.

    >[!NOTE]
    >Skript podporuje běh jenom z 64 verze Windows PowerShellu.
    >

1. Přejděte do složky nebo sdílené složky, do které jste zkopírovali skript, a spusťte ho na serveru spuštěním `./OnboardingScript.ps1` skriptu.

Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Adresář protokolu je *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>Instalace a ověření agenta v systému Linux

Agent připojeného počítače pro Linux je k dispozici v preferovaném formátu balíčku pro distribuci (. Ot./min. nebo. DEB), která je hostovaná v [úložišti balíčků](https://packages.microsoft.com/)Microsoftu. [ `Install_linux_azcmagent.sh` Sada skriptu prostředí](https://aka.ms/azcmagent) provádí následující akce:

* Nakonfiguruje hostitelský počítač ke stažení balíčku agenta z packages.microsoft.com.

* Nainstaluje balíček hybridního poskytovatele prostředků.

Volitelně můžete agenta nakonfigurovat s vašimi informacemi o proxy serveru, včetně `--proxy "{proxy-url}:{proxy-port}"` parametru.

Skript obsahuje také logiku pro identifikaci podporovaných a nepodporovaných distribucí a ověřuje oprávnění, která jsou nutná k provedení instalace.

Následující příklad stáhne agenta a nainstaluje ho:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. Chcete-li stáhnout a nainstalovat agenta, včetně `--proxy` parametru pro konfiguraci agenta pro komunikaci prostřednictvím proxy server, spusťte následující příkazy:

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Po instalaci agenta je třeba ho nakonfigurovat, aby komunikoval se službou Azure ARC, a to spuštěním tohoto příkazu:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Instalace pomocí skriptované metody

1. Přihlaste se k serveru pomocí účtu, který má root Access.

1. Přejděte do složky nebo sdílené složky, do které jste zkopírovali skript, a spusťte ho na serveru spuštěním `./OnboardingScript.sh` skriptu.

Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Adresář protokolu je *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).