---
title: Ukládání do protokolů IIS ve službě Log Analytics | Dokumentace Microsoftu
description: Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které můžete shromážděná službou Log Analytics.  Tento článek popisuje, jak konfigurovat shromažďování protokolů služby IIS a podrobnosti o záznamy vytvořené v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: cd63c63344f322f7d761a2907f52e97f1009e3b8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101951"
---
# <a name="collect-iis-logs-in-log-analytics"></a>Shromažďování protokolů IIS ve službě Log Analytics
Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které mohou být shromážděná službou Log Analytics a uloží jako [můžete vytvářet protokoly dat](data-collection.md).

![Protokoly IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace služby IIS protokoly
Log Analytics shromažďuje položky z soubory protokolů vytvořené službou IIS, takže je potřeba [konfigurace služby IIS pro protokolování](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics pouze podporuje uložit ve formátu W3C soubory protokolu služby IIS a nepodporuje vlastní pole nebo rozšířené protokolování internetové informační služby. Neshromažďuje protokoly ve formátu native NCSA nebo služby IIS.

Konfigurace služby IIS protokolů v Log Analytics [nabídky Rozšířené nastavení](agent-data-sources.md#configuring-data-sources).  Není nutná žádná konfigurace jiné než výběrem **soubory protokolů IIS ve formátu W3C shromažďovat**.


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

## <a name="log-queries-with-iis-logs"></a>Protokol dotazů s protokoly IIS
Následující tabulka obsahuje příklady různých dotazů na protokoly, které načítají záznamy protokolu IIS.

| Dotaz | Popis |
|:--- |:--- |
| W3CIISLog |Všechny záznamy protokolu IIS. |
| W3CIISLog &#124; kde scStatus == 500 |Všechny záznamy protokolu IIS s návratový stav 500. |
| W3CIISLog &#124; vytvořit souhrn count() podle cIP |Počet IIS položky protokolu podle IP adresy klienta. |
| W3CIISLog &#124; kde csHost == "www.contoso.com" &#124; vytvořit souhrn count() podle csUriStem |Počet IIS položky protokolu pomocí adresy URL pro hostitele www.contoso.com. |
| W3CIISLog &#124; shrnout sum(csBytes) počítačem &#124; trvat 500000 |Celkový počet bajtů přijatých jednotlivými počítači IIS. |

## <a name="next-steps"></a>Další postup
* Konfigurace Log Analytics ke shromažďování dalších [zdroje dat](agent-data-sources.md) pro analýzu.
* Další informace o [protokolu dotazy](../../log-analytics/log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.
