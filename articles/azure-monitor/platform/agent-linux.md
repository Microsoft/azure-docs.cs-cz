---
title: Připojení počítačů S Linuxem k Azure Monitoru | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak připojit počítače SIP hostované v jiných cloudech nebo místně k Azure Monitoru s agentem Log Analytics pro Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668702"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Připojení počítačů S Linuxem k Azure Monitoru

Chcete-li sledovat a spravovat virtuální počítače nebo fyzické počítače v místním datovém centru nebo jiném cloudovém prostředí pomocí Služby Azure Monitor, musíte nasadit agenta Log Analytics a nakonfigurovat ho tak, aby se hlásil do pracovního prostoru Log Analytics. Agent také podporuje roli hybridního pracovníka runbooku pro Azure Automation.

Agent a log Analytics pro Linux lze nainstalovat pomocí jedné z následujících metod. Podrobnosti o použití jednotlivých metod jsou uvedeny dále v článku.

* [Ručně stáhněte a nainstalujte](#install-the-agent-manually) agenta. To je vyžadováno, když počítač s Linuxem nemá přístup k Internetu a bude komunikovat s Azure Monitor nebo Azure Automation prostřednictvím [brány Log Analytics](gateway.md). 
* [Nainstalujte agenta pro Linux pomocí obálkového skriptu hostovaného](#install-the-agent-using-wrapper-script) na GitHubu. Toto je doporučená metoda instalace a inovace agenta, pokud je počítač připojen k Internetu, přímo nebo prostřednictvím proxy serveru.

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Linux](log-analytics-agent.md#supported-linux-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>Agenta Log Analytics pro Linux není možné nakonfigurovat tak, aby se hlásil více než jednomu pracovnímu prostoru služby Log Analytics. Lze jej nakonfigurovat pouze tak, aby se zhodnocovalo skupině pro správu nástroje System Center Operations Manager a pracovního prostoru Log Analytics současně nebo samostatně.

## <a name="agent-install-package"></a>Instalační balíček agenta

Agent Log Analytics pro Linux se skládá z více balíčků. Vydání souboru obsahuje následující balíčky, které jsou k `--extract` dispozici spuštěním shell svazek s parametrem:

**Balíček** | **Verze** | **Popis**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agent analýzy protokolů pro Linux
omsconfig | 1.1.1 | Konfigurační agent pro agenta analýzy protokolů
Omi | 1.6.3 | Open Management Infrastructure (OMI) -- lehký CIM Server. *Všimněte si, že OMI vyžaduje root přístup ke spuštění cron úlohy nezbytné pro fungování služby*
Scx | 1.6.3 | Poskytovatelé CIM OMI pro metriky výkonu operačního systému
apache-cimprov | 1.0.1 | Apache HTTP Server sledování výkonu poskytovatele pro OMI. Nainstalován pouze v případě, že je zjištěn apache http server.
mysql-cimprov | 1.0.1 | MySQL Server sledování výkonu poskytovatele pro OMI. Nainstalován pouze v případě, že je zjištěn server MySQL/MariaDB.
docker-cimprov | 1.0.0 | Poskytovatel Dockeru pro OMI. Nainstalováno pouze v případě, že je zjištěn Docker.

### <a name="agent-installation-details"></a>Podrobnosti o instalaci agenta

Po instalaci agenta Log Analytics pro balíčky Linuxu jsou použity následující další změny konfigurace celého systému. Tyto artefakty jsou odebrány při odinstalaci balíčku omsagent.

* Neprivilegovaný uživatel s `omsagent` názvem: je vytvořen. Daemon běží pod tímto pověřením. 
* Sudoers *zahrnout* soubor je `/etc/sudoers.d/omsagent`vytvořen v aplikaci . Tím se `omsagent` povoluje restartování daemonů syslogu a omsagent. Pokud *direktivy* sudo include nejsou ve nainstalované verzi sudo podporovány, budou tyto položky zapsány do aplikace `/etc/sudoers`.
* Konfigurace syslogu je upravena tak, aby předala podmnožinu událostí agentovi. Další informace naleznete v [tématu Konfigurace shromažďování dat syslogu](data-sources-syslog.md).

Na monitorovaném počítači s Linuxem je agent uveden jako `omsagent`. `omsconfig`je agent Log Analytics pro konfigurační agent Linuxu, který každých 5 minut hledá novou konfiguraci na straně portálu. Nová a aktualizovaná konfigurace je použita pro konfigurační soubory agenta umístěné na adrese `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru

Před instalací agenta Log Analytics pro Linux potřebujete ID a klíč vašeho pracovního prostoru služby Log Analytics. Tyto informace jsou vyžadovány během instalace agenta správně nakonfigurovat a zajistit, že může úspěšně komunikovat s Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. V levém horním rohu portálu Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte **Log Analytics**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **pracovní prostory Analýzy protokolů**.

2. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor, který jste vytvořili dříve. (Je možné, že jste jej pojmenovali **DefaultLAWorkspace**.)

3. Vyberte **Upřesnit nastavení**:

    ![Nabídka Pokročilá nastavení pro analýzu protokolů na webu Azure Portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Vyberte **Připojené zdroje** a pak **Servery s Linuxem**.

5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

## <a name="install-the-agent-manually"></a>Ruční instalace agenta

Agent Log Analytics pro Linux je k dispozici v samorozbalovací a instalovatelné shell skript udatný balíček. Tento balíček obsahuje balíčky Debianu a RPM pro každou součást agenta a může být instalován přímo nebo extrahován pro načtení jednotlivých balíčků. Jeden balíček je k dispozici pro x64 a jeden pro architektury x86. 

> [!NOTE]
> Pro virtuální počítače Azure doporučujeme nainstalovat agenta na ně pomocí [rozšíření virtuálního počítače Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) pro Linux. 

1. [Stáhněte](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) a přeneste příslušný balíček (x64 nebo x86) do virtuálního počítače s Linuxem nebo do fyzického počítače pomocí scp/sftp.

2. Nainstalujte balíček `--install` pomocí argumentu. Chcete-li na palubě do pracovního prostoru `-w <WorkspaceID>` `-s <workspaceKey>` Log Analytics během instalace, zadejte a parametry zkopírované dříve.

    >[!NOTE]
    >Je třeba použít `--upgrade` argument, pokud jsou nainstalovány všechny závislé balíčky, jako je omi, scx, omsconfig nebo jejich starší verze, jako by tomu bylo v případě, že systém Center Operations Manager agent pro Linux je již nainstalován. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Chcete-li nakonfigurovat agenta Linuxu tak, aby instaloval a připojoval se k pracovnímu prostoru Log Analytics prostřednictvím brány Log Analytics, spusťte následující příkaz poskytující proxy server, ID pracovního prostoru a klíčové parametry pracovního prostoru. Tuto konfiguraci lze zadat na `-p [protocol://][user:password@]proxyhost[:port]`příkazovém řádku zahrnutím . Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu serveru brány Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Pokud je vyžadováno ověření, je třeba zadat uživatelské jméno a heslo. Například: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Chcete-li nakonfigurovat počítač s Linuxem pro připojení k pracovnímu prostoru Log Analytics v cloudu Azure Government, spusťte následující příkaz poskytující ID pracovního prostoru a primární klíč zkopírovaný dříve.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Pokud chcete nainstalovat balíčky agentů a nakonfigurovat je tak, aby se později hlásily konkrétnímu pracovnímu prostoru Analýzy protokolů, spusťte následující příkaz:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Pokud chcete extrahovat balíčky agenta ze svazku bez instalace agenta, spusťte následující příkaz:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instalace agenta pomocí obálkového skriptu

Následující kroky nakonfigurují nastavení agenta pro Analýzu protokolů v cloudu Azure a Azure Government pomocí obálkového skriptu pro počítače s Linuxem, které můžou komunikovat přímo nebo prostřednictvím proxy serveru, aby si stáhli agenta hostovaného na GitHubu a nainstalovali agenta.  

Pokud váš počítač s Linuxem potřebuje komunikovat prostřednictvím proxy serveru do log analytics, lze tuto konfiguraci zadat na příkazovém řádku zahrnutím `-p [protocol://][user:password@]proxyhost[:port]`. Vlastnost *protokolu* přijímá `http` `https`nebo a vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo ADRESU IP proxy serveru. 

Příklad: `https://proxy01.contoso.com:30443`

Pokud je vyžadováno ověření v obou případech, je třeba zadat uživatelské jméno a heslo. Příklad: `https://user01:password@proxy01.contoso.com:30443`

1. Chcete-li nakonfigurovat počítač s Linuxem tak, aby se připojil k pracovnímu prostoru Log Analytics, spusťte následující příkaz poskytující ID pracovního prostoru a primární klíč. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, pokud je ověření vyžadováno serverem proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Chcete-li nakonfigurovat počítač s Linuxem pro připojení k pracovnímu prostoru Log Analytics v cloudu Azure Government, spusťte následující příkaz poskytující ID pracovního prostoru a primární klíč zkopírovaný dříve. Tento příkaz stáhne agenta, ověří jeho kontrolní součet a nainstaluje ho. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Následující příkaz obsahuje `-p` parametr proxy a ukázkovou syntaxi, pokud je ověření vyžadováno serverem proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Upgrade z předchozí verze

Inovace z předchozí verze, počínaje verzí 1.0.0-47, je podporována v každé verzi. Proveďte instalaci `--upgrade` s parametrem a upgradujte všechny součásti agenta na nejnovější verzi.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [správu a údržbu agenta Log Analytics pro Windows a Linux,](agent-manage.md) abyste zjistili, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Zkontrolujte [řešení potíží s agentem Linuxu,](agent-linux-troubleshoot.md) pokud narazíte na problémy při instalaci nebo správě agenta.
