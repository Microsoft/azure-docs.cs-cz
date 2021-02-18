---
title: Instalace agenta Log Analytics na počítačích s Linuxem
description: Tento článek popisuje, jak propojit počítače se systémem Linux hostované v jiných cloudech nebo v místním prostředí, aby se Azure Monitor s agentem Log Analytics pro Linux.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 9e0d9162c497ff035438b5a65c6f4500ce834860
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609206"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Instalace agenta Log Analytics na počítačích s Linuxem
Tento článek poskytuje podrobné informace o instalaci agenta Log Analytics v počítačích se systémem Linux pomocí následujících metod:

* [Nainstalujte agenta pro Linux pomocí skriptu obálky](#install-the-agent-using-wrapper-script) hostovaného na GitHubu. Toto je doporučená metoda pro instalaci a upgrade agenta, pokud je počítač připojen k Internetu, a to přímo nebo prostřednictvím proxy server.
* [Ručně stáhněte a nainstalujte](#install-the-agent-manually) agenta. To je nutné v případě, že počítač se systémem Linux nemá přístup k Internetu a bude komunikovat s Azure Monitor nebo Azure Automation prostřednictvím [brány Log Analytics](../platform/gateway.md). 

>[!IMPORTANT]
> Metody instalace popsané v tomto článku se obvykle používají pro virtuální počítače v místním prostředí nebo v jiných cloudech. V tématu [Možnosti instalace](../platform/log-analytics-agent.md#installation-options) získáte efektivnější možnosti, které můžete použít pro virtuální počítače Azure.



## <a name="supported-operating-systems"></a>Podporované operační systémy

Seznam distribucí pro Linux podporovaný agentem Log Analytics najdete v tématu [přehled Azure monitor agentů](agents-overview.md#supported-operating-systems) .

>[!NOTE]
>OpenSSL 1.1.0 se podporuje jenom na platformách x86_x64 (64 bitů) a OpenSSL starších než 1. x se na žádné platformě nepodporuje.

>[!NOTE]
>Spuštění agenta Log Analytics Linux v kontejnerech se nepodporuje. Pokud potřebujete monitorovat kontejnery, využijte prosím [řešení monitorování kontejnerů](../insights/containers.md) pro hostitele Docker nebo [Azure monitor pro kontejnery](../insights/container-insights-overview.md) pro Kubernetes.

Od verzí vydaných po srpna 2018 provedeme následující změny modelu podpory:  

* Podporovány jsou pouze verze serveru, nikoli klient.  
* Zaměřte se na podporu kteréhokoli [distribuce schváleného pro Azure Linux](../../virtual-machines/linux/endorsed-distros.md). Všimněte si, že může dojít k prodlevě mezi novou distribuce/verzí, kterou systém Azure Linux schválil, a podporuje se pro agenta Log Analytics Linux.
* Všechny dílčí verze jsou podporovány pro každou hlavní verzi uvedenou v seznamu.
* Verze, které předaly datum ukončení podpory svého výrobce, nejsou podporovány.
* Podporují jenom image virtuálních počítačů. kontejnery, i ty, které jsou odvozeny od oficiálních imagí vydavatelů distribuce, nejsou podporovány.
* Nové verze AMI se nepodporují.  
* Podporují se jenom verze standardu SSL 1. x.

>[!NOTE]
>Pokud používáte distribuce nebo verzi, která není v současné době podporovaná a nerovná se k našemu modelu podpory, doporučujeme, abyste toto úložiště rozpustili a potvrdili, že podpora Microsoftu nebude poskytovat pomoc s rozvětvené verze agenta.

### <a name="python-requirement"></a>Požadavek Pythonu

Od agenta verze 1.13.27 bude agent Linux podporovat Python 2 a 3. Vždycky doporučujeme používat nejnovějšího agenta. 

Pokud používáte starší verzi agenta, musíte mít virtuální počítač ve výchozím nastavení používat Python 2. Pokud váš virtuální počítač používá distribuce, který ve výchozím nastavení neobsahuje Python 2, musíte ho nainstalovat. Následující vzorové příkazy instalují Python 2 v různých distribuce.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Spustitelný soubor python2 musí mít alias na *Python*. Následuje jedna metoda, kterou můžete použít k nastavení tohoto aliasu:

1. Spuštěním následujícího příkazu odeberte všechny existující aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Spusťte následující příkaz pro vytvoření aliasu.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Podporované posílení zabezpečení pro Linux
Agent OMS má podporu vlastního nastavení pro Linux. 

V současné době jsou podporovány následující: 
- Standardů

Níže jsou uvedené aspekty, které se zatím nepodporují:
- SLUŽBY
- SELINUX

Jiné metody posílení zabezpečení a přizpůsobení nejsou pro agenta OMS podporovány ani plánovány.  

## <a name="agent-prerequisites"></a>Předpoklady pro agenta

V následující tabulce jsou vysvětlené balíčky požadované pro [Podporované distribuce Linux](#supported-operating-systems) , na které se agent nainstaluje.

|Požadovaný balíček |Description |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2.5-12 
|Openssl    | Knihovny OpenSSL | 1,0. x nebo 1.1. x |
|Curl | Webový klient s kudrlinkou | 7.15.5 |
|Python | | 2.6 + nebo 3.3 +
|Python – ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Aby bylo možné shromažďovat zprávy syslog, jsou vyžadovány buď rsyslog, nebo syslog-ng. Výchozí démon procesu Syslog verze 5 Red Hat Enterprise Linux, CentOS a verze Oracle Linux (sysklog) není pro shromažďování událostí syslog podporován. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat démona rsyslog a nakonfigurovat tak, aby nahradila sysklog.

## <a name="network-requirements"></a>Požadavky sítě
Požadavky na síť pro agenta pro Linux najdete v tématu [Přehled agenta Log Analytics](../platform/log-analytics-agent.md#network-requirements) .

## <a name="agent-install-package"></a>Balíček pro instalaci agenta

Agent Log Analytics pro Linux se skládá z více balíčků. Soubor verze obsahuje následující balíčky, které jsou k dispozici spuštěním sady prostředků prostředí s `--extract` parametrem:

**Balíček** | **Verze** | **Popis**
----------- | ----------- | --------------
omsagent | 1.13.9 | Agent Log Analytics pro Linux
omsconfig | 1.1.1 | Agent konfigurace pro agenta Log Analytics
OMI | 1.6.4 | Otevřete infrastrukturu pro správu (OMI) – server Lightweight CIM. *Všimněte si, že OMI vyžaduje přístup k hlavnímu adresáři, aby bylo možné spustit úlohu cron potřebnou pro fungování služby.*
SCX | 1.6.4 | OMI poskytovatelé CIM pro metriky výkonu operačního systému
Apache – cimprov | 1.0.1 | Poskytovatel sledování výkonu serveru Apache HTTP pro OMI. Instaluje se jenom v případě, že se zjistí server Apache HTTP.
MySQL – cimprov | 1.0.1 | Poskytovatel sledování výkonu serveru MySQL pro OMI. Instaluje se jenom v případě, že se zjistil server MySQL/MariaDB.
Docker – cimprov | 1.0.0 | Poskytovatel Docker pro OMI. Instaluje se jenom v případě, že se detekuje Docker.

### <a name="agent-installation-details"></a>Podrobnosti o instalaci agenta

Po instalaci agenta Log Analytics pro balíčky pro Linux se aplikují následující další změny konfigurace v rámci systému. Tyto artefakty jsou po odinstalaci balíčku omsagent odebrány.

* Je vytvořen Neprivilegovaný uživatel s názvem: `omsagent` . Démon se spustí pod tímto pověřením. 
* V nástroji  se vytvoří soubor sudoers include `/etc/sudoers.d/omsagent` . Tím se zmocňuje `omsagent` k restartování procesu démona syslog a omsagent. Pokud direktivy *include* sudo nejsou podporovány v nainstalované verzi sudo, budou do nich zapisovány tyto položky `/etc/sudoers` .
* Konfigurace syslog je upravena tak, aby předá podmnožinu událostí agentovi. Další informace najdete v tématu [Konfigurace shromažďování dat SYSLOG](data-sources-syslog.md).

Na monitorovaném počítači se systémem Linux je agent uveden jako `omsagent` . `omsconfig` je agentem Log Analytics agenta pro konfiguraci pro Linux, který vyhledává novou konfiguraci na straně portálu každých 5 minut. Nové a aktualizované konfigurace se aplikují na konfigurační soubory agenta v umístění `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

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

## <a name="upgrade-from-a-previous-release"></a>Upgrade z předchozí verze

Upgrade z předchozí verze, počínaje verzí 1.0.0-47, je podporován v každé vydané verzi. Proveďte instalaci s `--upgrade` parametrem pro upgrade všech komponent agenta na nejnovější verzi.

## <a name="cache-information"></a>Informace o mezipaměti
Data z agenta Log Analytics pro Linux se ukládají do mezipaměti v místním počítači v umístění *% STATE_DIR_WS%/out_oms_common*. Buffer * před odesláním do Azure monitor. Data vlastního protokolu se ukládají do vyrovnávací paměti v *% STATE_DIR_WS%/out_oms_blob*. Buffer *. Tato cesta se může u některých [řešení a datových typů](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)lišit.

Agent se pokusí o nahrání každých 20 sekund. Pokud selže, bude počkat exponenciálně rostoucí dobu, dokud nebude úspěšná: 30 sekund před druhým pokusem, 60 sekund před třetí 120 sekundami... a tak až po dobu maximálně 16 minut mezi opakovanými pokusy, dokud se znovu úspěšně nepřipojí. Před zahozením a přechodem na následující datový blok bude agent opakovat až 6 časů pro danou datovou část. To bude pokračovat, dokud se agent nebude moci znovu úspěšně odeslat. To znamená, že data se můžou ukládat do vyrovnávací paměti přibližně 30 minut, než se zahodí.

Výchozí velikost mezipaměti je 10 MB, ale lze ji upravit v [souboru omsagent. conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Správa a údržba agenta Log Analytics pro systémy Windows a Linux](agent-manage.md) , kde se dozvíte, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Pokud narazíte na problémy při instalaci nebo správě agenta, přečtěte si téma [řešení potíží s agentem pro Linux](agent-linux-troubleshoot.md) .
