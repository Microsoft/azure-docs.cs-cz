---
title: Ukládání do protokolů IIS ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které můžete shromážděná službou Log Analytics.  Tento článek popisuje, jak konfigurovat shromažďování protokolů služby IIS a podrobnosti o záznamy, které vytvářejí v pracovním prostoru Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: ''
ms.openlocfilehash: 52d81e4f4070d8d8e13497ad39d5adf1b99470de
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007735"
---
# <a name="iis-logs-in-log-analytics"></a>Ukládání do protokolů IIS ve službě Log Analytics
Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které můžete shromážděná službou Log Analytics.  

![Protokoly IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace služby IIS protokoly
Log Analytics shromažďuje položky z soubory protokolů vytvořené službou IIS, takže je potřeba [konfigurace služby IIS pro protokolování](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics pouze podporuje uložit ve formátu W3C soubory protokolu služby IIS a nepodporuje vlastní pole nebo rozšířené protokolování internetové informační služby.  
Log Analytics neshromažďuje protokoly ve formátu native NCSA nebo služby IIS.

Konfigurace služby IIS protokolů v Log Analytics [dat nabídky v nastavení analýzy protokolů](log-analytics-data-sources.md#configuring-data-sources).  Není nutná žádná konfigurace jiné než výběrem **soubory protokolů IIS ve formátu W3C shromažďovat**.


## <a name="data-collection"></a>Shromažďování dat
Log Analytics shromažďuje z každého agenta, se vytvoří pokaždé, když je uzavřen do protokolu a nové záznamy protokolu IIS. Tato četnost se řídí **plán změna souboru protokolu** nastavení pro web služby IIS, který je jednou za den ve výchozím nastavení. Například, pokud je nastavení **hodinové**, pak Log Analytics shromažďovat do protokolu každou hodinu.  Pokud je nastavení **denní**, pak Log Analytics shromažďovat protokolu každých 24 hodin.


## <a name="iis-log-record-properties"></a>Vlastnosti záznamů protokolu IIS
Záznamy protokolu IIS mají typ **W3CIISLog** a mít vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, který událost byla shromážděna z. |
| cIP |IP adresa klienta. |
| csMethod |Metoda požadavku, například GET nebo POST. |
| csReferer |Lokalita, uživatele a potom odkaz z aktuální lokality. |
| csUserAgent |Typ prohlížeče klienta. |
| csUserName |Jméno ověřeného uživatele, který přistupuje k serveru. Anonymní uživatelé se označují pomlčkou. |
| csUriStem |Cíl požadavku, například na webové stránce. |
| csUriQuery |Dotaz, pokud existuje, že se klient pokoušel vykonat. |
| ManagementGroupName |Název skupiny pro správu pro agenty nástroje Operations Manager.  Pro ostatní agenty to je AOI -\<ID pracovního prostoru\> |
| RemoteIPCountry |Země IP adresu klienta. |
| RemoteIPLatitude |Zeměpisná šířka IP adresu klienta. |
| RemoteIPLongitude |Zeměpisná délka IP adresu klienta. |
| scStatus |Stavový kód HTTP. |
| scSubStatus |Podřízený stavový kód chyby. |
| scWin32Status |Stavový kód Windows. |
| sIP. |IP adresa webového serveru. |
| SourceSystem |Nástroj OpsMgr |
| sPort |Port na serveru klient připojen. |
| sSiteName |Název webu služby IIS. |
| TimeGenerated |Datum a čas, kdy byla položka zapsána. |
| timeTaken |Dlouhá doba zpracování požadavku v milisekundách. |

## <a name="log-searches-with-iis-logs"></a>Hledání v protokolu s protokoly IIS
Následující tabulka obsahuje příklady různých dotazů na protokoly, které načítají záznamy protokolu IIS.

| Dotaz | Popis |
|:--- |:--- |
| W3CIISLog |Všechny záznamy protokolu IIS. |
| W3CIISLog &#124; kde scStatus == 500 |Všechny záznamy protokolu IIS s návratový stav 500. |
| W3CIISLog &#124; vytvořit souhrn count() podle cIP |Počet IIS položky protokolu podle IP adresy klienta. |
| W3CIISLog &#124; kde csHost == "www.contoso.com" &#124; vytvořit souhrn count() podle csUriStem |Počet IIS položky protokolu pomocí adresy URL pro hostitele www.contoso.com. |
| W3CIISLog &#124; shrnout sum(csBytes) počítačem &#124; trvat 500000 |Celkový počet bajtů přijatých jednotlivými počítači IIS. |

## <a name="next-steps"></a>Další postup
* Konfigurace Log Analytics ke shromažďování dalších [zdroje dat](log-analytics-data-sources.md) pro analýzu.
* Další informace o [prohledávání protokolů](log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.
* Konfigurace upozornění v Log Analytics můžete zajistit aktivní upozorňování důležité podmínek najít v protokolech služby IIS.
