---
title: Připojení hybridních počítačů k Azure z portálu Azure
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítače k Azure pomocí Azure Arc pro servery (náhled) z webu Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366367"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Připojení hybridních počítačů k Azure z portálu Azure

Azure Arc pro servery (náhled) pro jeden nebo malý počet počítačů s Windows nebo Linuxem ve vašem prostředí můžete povolit ručním provedením sady kroků. Nebo můžete použít automatizovanou metodu spuštěním skriptu šablony, který poskytujeme. Tento skript automatizuje stahování a instalaci obou agentů.

Tato metoda vyžaduje, abyste měli oprávnění správce v počítači k instalaci a konfiguraci agenta. V Linuxu, pomocí kořenového účtu a v systému Windows jste členem skupiny Místní správci.

Než začnete, zkontrolujte [požadavky](overview.md#prerequisites) a ověřte, zda vaše předplatné a prostředky splňují požadavky.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generování instalačního skriptu z webu Azure Portal

Skript pro automatizaci stahování a instalace a navázání spojení s Azure Arc je k dispozici na webu Azure Portal. Proces dokončíte takto:

1. V prohlížeči přejděte na [portál Azure](https://aka.ms/hybridmachineportal).

1. Na **stránce Počítače – Azure Arc** vyberte možnost **Přidat**v levém horním rohu nebo možnost Vytvořit počítač – **Oblouk Azure** v dolní části prostředního podokna. 

1. Na stránce **Vybrat metodu** vyberte **položku Přidat počítače pomocí interaktivní** ho skriptu a pak vyberte Generovat **skript**.

1. Na stránce **Generovat skript** vyberte předplatné a skupinu prostředků, kde chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kde se budou uložena metadata počítače.

    >[!NOTE]
    >Azure Arc pro servery (preview) podporuje pouze následující oblasti:
    >- Západní US2
    >- WestEurope
    >- Západní Asie
    >
    >Přečtěte si další aspekty při výběru oblasti [zde](overview.md#supported-regions) v článku Přehled.

1. Na stránce **Generovat skript** vyberte v rozevíracím seznamu **Operační systém** operační systém operační systém, ve které bude skript spuštěn.

1. Pokud počítač komunikuje přes proxy server pro připojení k Internetu, vyberte **další: Proxy server**. 
1. Na kartě **Proxy server** zadejte adresu IP serveru proxy nebo název a číslo portu, které bude počítač používat ke komunikaci se serverem proxy. Zadejte hodnotu `http://<proxyURL>:<proxyport>`ve formátu . 
1. Vyberte **Zkontrolovat + generovat**.

1. Na kartě **Revize + generovat** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete provést změny, vyberte **Předchozí**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalace a ověření agenta v systému Windows

### <a name="install-manually"></a>Ruční instalace

Agenta připojeného počítače můžete nainstalovat ručně spuštěním balíčku Instalační služby systému Windows *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Chcete-li nainstalovat nebo odinstalovat agenta, musíte mít oprávnění *správce.*
> * Nejprve je nutné stáhnout a zkopírovat balíček Instalační služby do složky na cílovém serveru nebo ze sdílené síťové složky. Pokud spustíte balíček Instalační služba bez jakýchkoli možností, spustí se průvodce instalací, který můžete sledovat a nainstalovat agenta interaktivně.

Pokud počítač potřebuje komunikovat prostřednictvím proxy serveru ke službě, po instalaci agenta je třeba spustit příkaz, který je popsán dále v článku. Tím nastavíte proměnnou `https_proxy`prostředí systému proxy serveru .

Pokud nejste obeznámeni s možnostmi příkazového řádku pro balíčky Instalační služby systému Windows, přečtěte si [standardní možnosti příkazového řádku Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) a [možnosti příkazového řádku Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Například spusťte instalační `/?` program s parametrem a zkontrolujte možnost nápovědy a rychléreference. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Chcete-li agenta nainstalovat beze sušárna a vytvořit soubor protokolu instalace ve `C:\Support\Logs` složce, spusťte následující příkaz.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Soubory agenta připojeného počítače jsou ve výchozím nastavení nainstalovány v *aplikaci C:\Program Files\AzureConnectedMachineAgent*. Pokud se agentovi nepodaří spustit po dokončení instalace, zkontrolujte v protokolech podrobné informace o chybě. Adresář protokolu je *%Programfiles%\AzureConnectedMachineAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalace pomocí skriptované metody

1. Přihlaste se k serveru.

1. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.

1. Změňte na složku nebo sdílenou složku, do které jste skript `./OnboardingScript.ps1` zkopírovali, a spusťte jej na serveru spuštěním skriptu.

### <a name="configure-the-agent-proxy-setting"></a>Konfigurace nastavení proxy serveru agenta

Chcete-li nastavit proměnnou prostředí proxy serveru, spusťte následující příkaz:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nepodporuje nastavení ověřování proxy v tomto náhledu.
>

### <a name="configure-agent-communication"></a>Konfigurace komunikace agenta

Po instalaci agenta je třeba nakonfigurovat agenta pro komunikaci se službou Azure Arc spuštěním následujícího příkazu:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalace a ověření agenta na Linuxu

Agent Connected Machine pro Linux je k dispozici v preferovaném formátu balíčku pro distribuci (. rpm nebo . DEB), který je hostován v [úložišti balíčků](https://packages.microsoft.com/)Microsoftu . [Sada skriptů `Install_linux_azcmagent.sh` prostředí](https://aka.ms/azcmagent) provádí následující akce:

- Nakonfiguruje hostitelský počítač tak, aby stáhl balíček agenta z packages.microsoft.com.
- Nainstaluje balíček Hybrid Resource Provider.

Volitelně můžete nakonfigurovat agenta s informacemi `--proxy "{proxy-url}:{proxy-port}"` o serveru proxy zahrnutím parametru.

Skript také obsahuje logiku k identifikaci podporovaných a nepodporovaných distribucí a ověřuje oprávnění, která jsou potřebná k provedení instalace. 

Následující příklad stáhne agenta a nainstaluje ho:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Chcete-li stáhnout a nainstalovat `--proxy` agenta, včetně parametru pro konfiguraci agenta pro komunikaci prostřednictvím serveru proxy, spusťte následující příkazy:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurace komunikace agenta

Po instalaci agenta ho nakonfigurujte tak, aby komunikoval se službou Azure Arc, spuštěním následujícího příkazu:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arc

Po instalaci agenta a nakonfigurajete ho pro připojení k Azure Arc pro servery (preview), přejděte na portál Azure a ověřte, že server byl úspěšně připojen. Zobrazení počítačů na [webu Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak spravovat počítač pomocí [Zásad Azure](../../governance/policy/overview.md), pro takové věci, jako je [konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače , ověření, že počítač hlásí očekávanému pracovnímu prostoru Log Analytics, povolit monitorování pomocí [Azure Monitoru pomocí virtuálních počítačů](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)a mnoho dalšího.

- Další informace o [agentovi Analýzy protokolů](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro Windows a Linux je vyžadován, pokud chcete proaktivně sledovat operační systém a úlohy spuštěné na počítači, spravovat jej pomocí runbooků automation nebo řešení, jako je správa aktualizací, nebo používat jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).
