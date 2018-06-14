---
title: Řešení potíží s agenta systému Linux Azure Log Analytics | Microsoft Docs
description: Popisují příznaky, příčiny a řešení nejběžnějších problémů s agentem Log Analytics Linux.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 80d7e39b284554ebfa8cac4488e1663b3e3648e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936471"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Řešení potíží s agentem systému Linux pro analýzy protokolů

Tento článek obsahuje nápovědu k odstraňování potíží chyby setkat s agentem systému Linux pro analýzy protokolů a navrhne možná řešení jejich řešení.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problém: Nelze se připojit prostřednictvím proxy serveru k analýze protokolů

### <a name="probable-causes"></a>Možných příčin
* Zadaný během registrace proxy serveru byl nesprávný
* Analýzy protokolů a koncové body služby Azure Automation nejsou povolené ve vašem datovém centru 

### <a name="resolutions"></a>Řešení
1. Reonboard ke službě Analýza protokolů s agentem OMS pro Linux pomocí následujícího příkazu s parametrem `-v` povolena. To umožňuje podrobný výstup agenta připojení prostřednictvím proxy serveru pro službu OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Projděte si část [aktualizovat nastavení proxy serveru](log-analytics-agent-manage.md#update-proxy-settings) ověření jste správně nakonfigurovali agenta pro komunikaci přes proxy server.    
* Double zkontrolujte, jestli jsou následující koncové body služby analýzy protokolů seznam povolených adres:

    |Prostředek agenta| Porty | Směr |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.oms.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.blob.core.windows.net | Port 443| Příchozí a odchozí |  
    |*.azure-automation.net | Port 443| Příchozí a odchozí | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problém: Obdržíte 403 chybu při pokusu o zařadit do provozu

### <a name="probable-causes"></a>Možných příčin
* Datum a čas je nesprávný na Linux Server 
* ID pracovního prostoru a klíč pracovního prostoru používá nejsou správné

### <a name="resolution"></a>Řešení

1. Zkontrolujte čas na serveru Linux s datem příkaz. Pokud je doba +/-15 minut od aktuálního času, registrace se nezdaří. Správné to aktualizujte datum a časové pásmo serveru Linux. 
2. Ověřte, jestli že je nainstalovaná nejnovější verze agenta OMS pro Linux.  Nejnovější verze nyní upozorní, že jste Pokud časového posunu způsobuje selhání registrace.
3. Reonboard pomocí správné ID pracovního prostoru a klíč pracovního prostoru pokynů instalace dříve v tomto tématu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problém: Zobrazí 500 a 404 Chyba v souboru protokolu hned po registraci
Jde o známý problém, který se nachází na první nahrávání dat Linux do pracovního prostoru analýzy protokolů. To nemá vliv dat probíhá odeslané nebo služby.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problém: Nevidíte všechna data na portálu Azure

### <a name="probable-causes"></a>Možných příčin

- Připojování ke službě Analýza protokolů se nezdařilo
- Připojení ke službě Analýza protokolů je blokovaný.
- Vytvoření zálohy OMS agenta pro Linux data

### <a name="resolutions"></a>Řešení
1. Zkontrolujte, pokud registrace služby analýzy protokolů po úspěšné kontrole, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Pomocí Reonboard `omsadmin.sh` příkazového řádku pokyny
3. Pokud používáte proxy server, podívejte se na proxy řešení kroků uvedených výše.
4. V některých případech pokud Agent OMS pro Linux nemůže komunikovat se službou, data na agenta je zařazených do fronty pro velikost vyrovnávací paměti úplná, což je 50 MB. Spuštěním následujícího příkazu by měla být restartována OMS agenta pro Linux: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Tento problém vyřešen v 1.1.0-28 verze agenta nebo novější.

