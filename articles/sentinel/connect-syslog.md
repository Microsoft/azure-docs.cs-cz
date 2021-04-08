---
title: Připojit data syslogu ke službě Azure Sentinel | Microsoft Docs
description: Připojte libovolný počítač nebo zařízení, které podporuje syslog, do Azure Sentinel pomocí agenta na počítači se systémem Linux mezi zařízením a službou Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d35a97b0008a7ce3069185dd557a60221776b0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595468"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Shromažďování dat ze zdrojů se systémem Linux pomocí protokolu syslog

Pomocí agenta Log Analytics pro Linux (dřív označovaného jako agent OMS) můžete streamovat události z počítačů nebo zařízení s podporou systému Linux, které podporují protokol syslog, do Azure Sentinel. To můžete provést pro libovolný počítač, který umožňuje nainstalovat agenta Log Analytics přímo na počítači. Nativní démon procesu Syslog počítače bude shromažďovat místní události zadaných typů a přesměrovat je místně do agenta, který je bude zasílat do vašeho pracovního prostoru Log Analytics.

> [!NOTE]
> - Pokud vaše zařízení podporuje **CEF (Common Event Format) přes SYSLOG**, shromáždí se více kompletních datových sad a data se analyzují v kolekci. Tuto možnost byste měli zvolit a postupovat podle pokynů v tématu [připojení vašich externích řešení pomocí CEF](connect-common-event-format.md).
>
> - Log Analytics podporuje shromažďování zpráv odesílaných démony **rsyslog** nebo **syslog-ng** , kde je výchozí rsyslog. Výchozí démon procesu Syslog ve verzi 5 Red Hat Enterprise Linux (RHEL), CentOS a verze Oracle Linux (**sysklog**) není pro shromažďování událostí syslog podporován. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat démona rsyslog a nakonfigurovat tak, aby nahradila sysklog.

## <a name="how-it-works"></a>Jak to funguje

**SYSLOG** je protokol protokolování událostí, který je společný pro Linux. Když je na VIRTUÁLNÍm počítači nebo zařízení nainstalovaný **agent Log Analytics pro Linux** , rutina instalace nakonfiguruje místní démon syslog, aby předal zprávy agentovi na portu TCP 25224. Agent pak pošle zprávu do vašeho pracovního prostoru Log Analytics přes HTTPS, kde se analyzuje do položky protokolu událostí v tabulce syslog v **protokolech služby Azure Sentinel >**.

Další informace najdete v tématu [zdroje dat syslog v Azure monitor](../azure-monitor/agents/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Konfigurovat shromažďování syslog

### <a name="configure-your-linux-machine-or-appliance"></a>Konfigurace počítače se systémem Linux nebo zařízení

1. V Azure Sentinel vyberte **datové konektory** a pak vyberte konektor **syslog** .

1. V okně **syslog** vyberte **otevřít stránku konektoru**.

1. Nainstalujte agenta pro Linux. V části **zvolit umístění pro instalaci agenta:**
    
    **Pro virtuální počítač Azure Linux:**
      
    1. Vyberte **nainstalovat agenta na virtuálním počítači Azure Linux**.
    
    1. Klikněte na tlačítko **stáhnout & nainstalovat agenta pro virtuální počítače se systémem Azure Linux >** odkaz. 
    
    1. V okně **virtuální počítače** klikněte na virtuální počítač, na který chcete nainstalovat agenta, a pak klikněte na **připojit**. Tento krok opakujte pro každý virtuální počítač, ke kterému se chcete připojit.
    
    **Pro jakýkoli jiný počítač se systémem Linux:**

    1. Výběr **instalace agenta na počítači, který není na platformě Azure**

    1. Klikněte na **stáhnout & instalovat agenta pro počítače, které nejsou na platformě Azure Linux >** odkaz. 

    1. V okně **Správa agentů** klikněte na kartu **servery se systémem Linux** a potom zkopírujte příkaz pro **Stažení a zprovoznění agenta pro Linux** a spusťte jej na počítači se systémem Linux. 
    
   > [!NOTE]
   > Ujistěte se, že pro tyto počítače nakonfigurujete nastavení zabezpečení podle zásad zabezpečení vaší organizace. Můžete třeba nakonfigurovat nastavení sítě tak, aby odpovídalo zásadám zabezpečení sítě ve vaší organizaci, a změnit porty a protokoly v procesu démona tak, aby odpovídaly požadavkům na zabezpečení.

### <a name="configure-the-log-analytics-agent"></a>Konfigurace agenta Log Analytics

1. V dolní části okna konektoru syslog klikněte na odkaz **otevřít konfiguraci agentů pracovního prostoru >** .

1. V okně **Konfigurace agentů** vyberte kartu **syslog** . Pak přidejte zařízení, aby se konektor mohl shromažďovat. Vyberte **Přidat zařízení** a zvolte možnost z rozevíracího seznamu zařízení.
    
    - Přidejte do svých hlaviček protokolů zařízení, která vaše zařízení syslog zahrnuje. 
    
    - Pokud chcete použít zjišťování přihlášení neobvyklé SSH s daty, která shromáždíte, přidejte **auth** a **authpriv**. Další podrobnosti najdete v [následující části](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

1. Po přidání všech zařízení, která chcete monitorovat, ověřte, zda jsou označena zaškrtávací políčka pro všechny požadované závažnost.

1. Vyberte **Použít**. 

1. Na svém VIRTUÁLNÍm počítači nebo zařízení se ujistěte, že posíláte zařízení, která jste zadali.

1. Chcete-li zadat dotaz na data protokolu syslog v **protokolech**, zadejte `Syslog` do okna dotazu.

1. Pomocí parametrů dotazu popsaných v tématu [použití funkcí v Azure Monitorch](../azure-monitor/logs/functions.md) dotazech protokolu můžete analyzovat zprávy syslog. Dotaz pak můžete uložit jako novou funkci Log Analytics a použít ji jako nový datový typ.

> [!NOTE]
> **Použití stejného počítače pro přeposílání prostých zpráv syslog *a* CEF**
>
> Existující [počítač pro přeposílání protokolů CEF](connect-cef-agent.md) můžete použít ke shromažďování a posílání protokolů z jednoduchých zdrojů syslog. K tomu, abyste se vyhnuli posílání událostí v obou formátech do služby Azure Sentinel, je nutné provést následující kroky, protože výsledkem bude duplikace událostí.
>
>    Již jste nastavili [shromažďování dat z vašich CEF zdrojů](connect-common-event-format.md)a nakonfigurovali jste agenta Log Analytics, jak je uvedeno výše:
>
> 1. V každém počítači, který odesílá protokoly ve formátu CEF, je nutné upravit konfigurační soubor syslog a odebrat tak zařízení, která se používají k odesílání zpráv CEF. Zařízení, která jsou odesílána v CEF, nebudou také odesílána ve službě syslog. Podrobné pokyny k tomu, jak to udělat, najdete v tématu [Konfigurace protokolu syslog v agentovi Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Pokud chcete zakázat synchronizaci agenta s konfigurací syslog v Azure Sentinel, musíte na těchto počítačích spustit následující příkaz. Tím se zajistí, že se změna konfigurace, kterou jste provedli v předchozím kroku, nepřepíše.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurace konektoru syslog pro detekci přihlášení neobvyklé SSH

> [!IMPORTANT]
> Zjišťování přihlášení SSH neobvyklé je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel může použít Machine Learning (ML) na data syslog k identifikaci přihlašovací aktivity neobvyklé Secure Shell (SSH). Mezi scénáře patří:

- Nemožná cesta – když dojde k dvěma událostem úspěšného přihlášení ze dvou umístění, která nejsou v rámci časového rámce dvou přihlašovacích událostí možná dosažitelná.
- Neočekávané umístění – došlo k podezření na místo, kde došlo k úspěšné události přihlášení. Například umístění se nezobrazilo v poslední době.
 
Tato detekce vyžaduje specifickou konfiguraci konektoru dat syslog: 

1. V kroku 2 v části [Konfigurace agenta Log Analytics](#configure-the-log-analytics-agent) výše se ujistěte, že je vybraná možnost **ověřování** a **authpriv** jako zařízení, která se mají monitorovat, a že jsou vybrané všechny závažnosti. 

2. Umožněte shromažďování informací syslogu dostatek času. Pak přejděte do části **Azure Sentinel-logs** a zkopírujte a vložte následující dotaz:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    V případě potřeby změňte **časový rozsah** a vyberte **Spustit**.
    
    Pokud je výsledný počet nula, potvrďte konfiguraci konektoru a monitorované počítače mají po dobu, kterou jste zadali pro dotaz, aktivitu úspěšného přihlášení.
    
    Pokud je výsledný počet větší než nula, data syslogu jsou vhodná pro detekci přihlášení neobvyklé SSH. Toto zjišťování povolíte pomocí   >   **šablon pravidel** analýz  >  **(Preview) neobvyklé zjišťování přihlášení SSH**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit místní zařízení syslog ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

