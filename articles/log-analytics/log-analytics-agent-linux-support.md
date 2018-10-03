---
title: Řešení potíží s agentem Azure Log Analytics Linux | Dokumentace Microsoftu
description: Popište příznaky, příčiny a řešení běžných problémů s Log Analytics linuxového agenta.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: baa81a52d4b007cd690a2b01df642cd3775f7d6b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044132"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Řešení potíží s agentem Linux ke službě Log Analytics

Tento článek poskytuje nápovědu řešení potíží s chybami setkat s agenta pro Linux ke službě Log Analytics a navrhne řešení k jejich řešení.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problém: Nelze se připojit přes proxy do Log Analytics

### <a name="probable-causes"></a>Možných příčin
* Proxy zadaný během registrace byl nesprávný
* Log Analytics a koncové body služby automatizace Azure nejsou na seznamu povolených ve vašem datovém centru 

### <a name="resolutions"></a>Jejich řešení
1. Reonboard ke službě Log Analytics s agentem OMS pro Linux pomocí následujícího příkazu s parametrem `-v` povolena. To umožňuje podrobný výstup agenta připojeným přes proxy do služby OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Projděte si část [aktualizovat nastavení proxy serveru](log-analytics-agent-manage.md#update-proxy-settings) k ověření aplikace správně nakonfigurována, aby mohli agenti komunikovat přes proxy server.    
* Pečlivě zkontrolujte, že následující koncové body služby Log Analytics jsou povolené:

    |Prostředek agenta| Porty | Směr |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.oms.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.blob.core.windows.net | Port 443| Příchozí a odchozí |  
    |*.azure-automation.net | Port 443| Příchozí a odchozí | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problém: Zobrazí Chyba 403 při pokusu o připojení

### <a name="probable-causes"></a>Možných příčin
* Datum a čas není na serveru s Linuxem 
* ID pracovního prostoru a klíč pracovního prostoru použít nejsou správné

### <a name="resolution"></a>Řešení

1. Zkontrolujte čas na serveru Linux s datem příkazu. Pokud je doba +/-15 minut od aktuálního času, registrace se nezdaří. Pokud chcete správné to aktualizovat datum a časové pásmo serveru Linux. 
2. Ověřte, že máte nainstalovanou nejnovější verzi agenta OMS pro Linux.  Nejnovější verze nyní oznámí, že jste Pokud časového posunu je příčinou selhání připojení.
3. Reonboard pomocí správné ID pracovního prostoru a klíč pracovního prostoru, postupujte podle pokynů instalaci výše v tomto tématu.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problém: Se zobrazí chyba 500 a 404 v souboru protokolu hned po registraci
Jde o známý problém, ke které dojde při prvním uložení dat s Linuxem do pracovního prostoru Log Analytics. Dat odeslaných nebo že služba prostředí to neovlivní.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problém: Se nezobrazují žádná data na webu Azure Portal

### <a name="probable-causes"></a>Možných příčin

- Připojování ke službě Log Analytics se nezdařilo
- Připojení ke službě Log Analytics je blokován.
- Agenta OMS pro Linux data se zálohují.

### <a name="resolutions"></a>Jejich řešení
1. Zkontrolujte, jestli registrace služby Log Analytics byla úspěšná kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Pomocí Reonboard `omsadmin.sh` pokyny příkazového řádku
3. Pokud používáte proxy server, podívejte se na postup řešení proxy server jste zadali dříve.
4. V některých případech když agenta OMS pro Linux nemůže komunikovat se službou, data v agentovi je ve frontě velikost úplné vyrovnávací paměti, což je 50 MB. Spuštěním následujícího příkazu byste měli restartovat agenta OMS pro Linux: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Tento problém je vyřešen 1.1.0-28 verzi agenta a novější.

