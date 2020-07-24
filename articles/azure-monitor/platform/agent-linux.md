---
title: Připojit počítače se systémem Linux k Azure Monitor | Microsoft Docs
description: Tento článek popisuje, jak propojit počítače se systémem Linux hostované v jiných cloudech nebo v místním prostředí, aby se Azure Monitor s agentem Log Analytics pro Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: accd7c3ad82853c1f2af0b632326b2798f85b36b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073659"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Připojení počítačů se systémem Linux k Azure Monitor

Aby bylo možné monitorovat a spravovat virtuální počítače nebo fyzické počítače v místním datovém centru nebo jiném cloudovém prostředí pomocí Azure Monitor, je nutné nasadit agenta Log Analytics a nakonfigurovat ho tak, aby se nahlásil do Log Analyticsho pracovního prostoru. Agent také podporuje Hybrid Runbook Worker role pro Azure Automation.

Agenta Log Analytics pro Linux lze nainstalovat pomocí jedné z následujících metod. Podrobnosti o použití jednotlivých metod jsou uvedené dále v článku.

* [Ručně stáhněte a nainstalujte](#install-the-agent-manually) agenta. To je nutné v případě, že počítač se systémem Linux nemá přístup k Internetu a bude komunikovat s Azure Monitor nebo Azure Automation prostřednictvím [brány Log Analytics](gateway.md). 
* [Nainstalujte agenta pro Linux pomocí skriptu obálky](#install-the-agent-using-wrapper-script) hostovaného na GitHubu. Toto je doporučená metoda pro instalaci a upgrade agenta, pokud je počítač připojen k Internetu, a to přímo nebo prostřednictvím proxy server.

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Linux](log-analytics-agent.md#supported-linux-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent.md#network-requirements).

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics. Dá se nakonfigurovat jenom pro System Center Operations Manager skupinu pro správu i pro pracovní prostor Log Analytics a samostatně.

## <a name="agent-install-package"></a>Balíček pro instalaci agenta

Agent Log Analytics pro Linux se skládá z více balíčků. Soubor verze obsahuje následující balíčky, které jsou k dispozici spuštěním sady prostředků prostředí s `--extract` parametrem:

**Balíček** | **Verze** | **Popis**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agent Log Analytics pro Linux
omsconfig | 1.1.1 | Agent konfigurace pro agenta Log Analytics
OMI | 1.6.3 | Otevřete infrastrukturu pro správu (OMI) – server Lightweight CIM. *Všimněte si, že OMI vyžaduje přístup k hlavnímu adresáři, aby bylo možné spustit úlohu cron potřebnou pro fungování služby.*
SCX | 1.6.3 | OMI poskytovatelé CIM pro metriky výkonu operačního systému
Apache – cimprov | 1.0.1 | Poskytovatel sledování výkonu serveru Apache HTTP pro OMI. Instaluje se jenom v případě, že se zjistí server Apache HTTP.
MySQL – cimprov | 1.0.1 | Poskytovatel sledování výkonu serveru MySQL pro OMI. Instaluje se jenom v případě, že se zjistil server MySQL/MariaDB.
Docker – cimprov | 1.0.0 | Poskytovatel Docker pro OMI. Instaluje se jenom v případě, že se detekuje Docker.

### <a name="agent-installation-details"></a>Podrobnosti o instalaci agenta

Po instalaci agenta Log Analytics pro balíčky pro Linux se aplikují následující další změny konfigurace v rámci systému. Tyto artefakty jsou po odinstalaci balíčku omsagent odebrány.

* Je vytvořen Neprivilegovaný uživatel s názvem: `omsagent` . Démon se spustí pod tímto pověřením. 
* V nástroji *include* se vytvoří soubor sudoers include `/etc/sudoers.d/omsagent` . Tím se zmocňuje `omsagent` k restartování procesu démona syslog a omsagent. Pokud direktivy *include* sudo nejsou podporovány v nainstalované verzi sudo, budou do nich zapisovány tyto položky `/etc/sudoers` .
* Konfigurace syslog je upravena tak, aby předá podmnožinu událostí agentovi. Další informace najdete v tématu [Konfigurace shromažďování dat SYSLOG](data-sources-syslog.md).

Na monitorovaném počítači se systémem Linux je agent uveden jako `omsagent` . `omsconfig`je agentem Log Analytics agenta pro konfiguraci pro Linux, který vyhledává novou konfiguraci na straně portálu každých 5 minut. Nové a aktualizované konfigurace se aplikují na konfigurační soubory agenta v umístění `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Log Analytics pro Linux potřebujete ID a klíč vašeho pracovního prostoru služby Log Analytics. Tyto informace jsou požadovány během instalace agenta, aby byly správně nakonfigurovány a zajištěny, že mohou úspěšně komunikovat s Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. V levém horním rohu Azure Portal vyberte **všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Log Analytics pracovní prostory**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste nastavili název **DefaultLAWorkspace**.)

3. Vybrat **upřesňující nastavení**:

    ![Nabídka Pokročilá nastavení pro Log Analytics v Azure Portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Vyberte **Připojené zdroje** a pak **Servery s Linuxem**.

5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

## <a name="install-the-agent-manually"></a>Ruční instalace agenta

Agent Log Analytics pro Linux je k dispozici v balíčku skriptu pro samorozbalovací a instalovatelný prostředí. Tato sada obsahuje balíčky Debian a ot./min. pro každou součást agenta a je možné ji nainstalovat přímo nebo extrahovat a načíst jednotlivé balíčky. Jeden svazek je k dispozici pro x64 a jeden pro architektury x86. 

> [!NOTE]
> Pro virtuální počítače Azure doporučujeme nainstalovat agenta na těchto počítačích pomocí [rozšíření virtuálního počítače azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) pro Linux. 

1. [Stáhněte](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) a přeneste příslušnou sadu prostředků (x64 nebo x86) na svůj virtuální počítač nebo fyzický počítač se systémem Linux pomocí spojovacího bodu služby nebo SFTP.

2. Nainstalujte sadu prostředků pomocí `--install` argumentu. Chcete-li se připojit k pracovnímu prostoru Log Analytics během instalace, zadejte parametry a, které jste `-w <WorkspaceID>` `-s <workspaceKey>` zkopírovali dříve.

    >[!NOTE]
    >`--upgrade`Pokud jsou nainstalované všechny závislé balíčky, jako je OMI, SCX, omsconfig nebo jejich starší verze, je třeba použít argument, jako by to byl případ, kdy je již nainstalován agent služby System Center Operations Manager pro Linux. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Chcete-li nakonfigurovat agenta pro Linux k instalaci a připojení k Log Analyticsmu pracovnímu prostoru prostřednictvím brány Log Analytics, spusťte následující příkaz, který poskytuje proxy, ID pracovního prostoru a parametry klíče pracovního prostoru. Tuto konfiguraci můžete zadat na příkazovém řádku zahrnutím `-p [protocol://][user:password@]proxyhost[:port]` . Vlastnost *ProxyHost* přijímá plně kvalifikovaný název domény nebo IP adresu serveru brány Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Pokud je vyžadováno ověření, je nutné zadat uživatelské jméno a heslo. Příklad: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Pokud chcete počítač se systémem Linux nakonfigurovat tak, aby se připojil k pracovnímu prostoru Log Analytics v cloudu Azure Government, spusťte následující příkaz, který zadává dříve zkopírovaný ID pracovního prostoru a primární klíč.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Pokud chcete nainstalovat balíčky agentů a nakonfigurovat je tak, aby se později nahlásily na konkrétní Log Analytics pracovní prostor, spusťte následující příkaz:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Pokud chcete extrahovat balíčky agenta ze sady bez instalace agenta, spusťte následující příkaz:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instalace agenta pomocí skriptu obálky

Následující postup slouží ke konfiguraci nastavení agenta pro Log Analytics v Azure a Azure Government cloudu pomocí skriptu obálky pro počítače se systémem Linux, které mohou komunikovat přímo nebo prostřednictvím proxy server ke stažení agenta hostovaného na GitHubu a instalaci agenta.  

Pokud počítač se systémem Linux potřebuje komunikovat prostřednictvím proxy server k Log Analytics, lze tuto konfiguraci zadat na příkazovém řádku zahrnutím `-p [protocol://][user:password@]proxyhost[:port]` . Vlastnost *protokolu* přijímá `http` nebo `https` a vlastnost *ProxyHost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy server. 

Příklad: `https://proxy01.contoso.com:30443`

Pokud je v obou případech vyžadováno ověřování, je nutné zadat uživatelské jméno a heslo. Příklad: `https://user01:password@proxy01.contoso.com:30443`

1. Chcete-li počítač se systémem Linux nakonfigurovat pro připojení k Log Analyticsmu pracovnímu prostoru, spusťte následující příkaz, který poskytuje ID pracovního prostoru a primární klíč. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, když proxy server vyžaduje ověření:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pokud chcete počítač se systémem Linux nakonfigurovat tak, aby se připojil k Log Analytics pracovnímu prostoru v Azure Government cloudu, spusťte následující příkaz, který zadává dříve zkopírovaný ID a primární klíč pracovního prostoru. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, když proxy server vyžaduje ověření:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="supported-linux-hardening"></a>Podporované posílení zabezpečení pro Linux
Agent OMS má podporu vlastního nastavení pro Linux. 

V současné době jsou podporovány následující: 
- Standardů

Následující jsou plánovány, ale nejsou dosud podporovány:
- CIS – SELINUX

Jiné metody posílení zabezpečení a přizpůsobení nejsou pro agenta OMS podporovány ani plánovány.  


## <a name="upgrade-from-a-previous-release"></a>Upgrade z předchozí verze

Upgrade z předchozí verze, počínaje verzí 1.0.0-47, je podporován v každé vydané verzi. Proveďte instalaci s `--upgrade` parametrem pro upgrade všech komponent agenta na nejnovější verzi.

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Správa a údržba agenta Log Analytics pro systémy Windows a Linux](agent-manage.md) , kde se dozvíte, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Pokud narazíte na problémy při instalaci nebo správě agenta, přečtěte si téma [řešení potíží s agentem pro Linux](agent-linux-troubleshoot.md) .
