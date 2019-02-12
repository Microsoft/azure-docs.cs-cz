---
title: Ukládání do protokolů IIS ve službě Azure Monitor | Dokumentace Microsoftu
description: Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které můžou shromažďovat službou Azure Monitor.  Tento článek popisuje, jak konfigurovat shromažďování protokolů služby IIS a podrobnosti o záznamy vytvořené ve službě Azure Monitor.
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
ms.openlocfilehash: ca951c813554ae253cbd572e03c53b8687499af9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000161"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Shromažďování protokolů IIS ve službě Azure Monitor
Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které můžete shromážděná službou Azure Monitor a uložená jako [můžete vytvářet protokoly dat](data-collection.md).

![Protokoly IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace služby IIS protokoly
Azure Monitor provádí sběr položky ze souborů protokolů, které jsou vytvořené službou IIS, takže je potřeba [konfigurace služby IIS pro protokolování](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor pouze podporuje uložit ve formátu W3C soubory protokolu služby IIS a nepodporuje vlastní pole nebo rozšířené protokolování internetové informační služby. Neshromažďuje protokoly ve formátu native NCSA nebo služby IIS.

Konfigurace protokolů IIS ve službě Azure Monitor z [nabídky Rozšířené nastavení](agent-data-sources.md#configuring-data-sources).  Není nutná žádná konfigurace jiné než výběrem **soubory protokolů IIS ve formátu W3C shromažďovat**.


## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje z každého agenta pokaždé, když je uzavřen do protokolu a nového vytvoření záznamů protokolu IIS. Tato četnost se řídí **plán změna souboru protokolu** nastavení pro web služby IIS, který je jednou za den ve výchozím nastavení. Například, pokud je nastavení **hodinové**, pak Azure Monitor shromáždí do protokolu každou hodinu.  Pokud je nastavení **denní**, pak Azure Monitor bude shromažďovat protokolu každých 24 hodin.


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
| sIP |IP adresa webového serveru. |
| SourceSystem |OpsMgr |
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
| W3CIISLog &#124; summarize count() by cIP |Počet IIS položky protokolu podle IP adresy klienta. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Počet IIS položky protokolu pomocí adresy URL pro hostitele www.contoso.com. |
| W3CIISLog &#124; shrnout sum(csBytes) počítačem &#124; trvat 500000 |Celkový počet bajtů přijatých jednotlivými počítači IIS. |

## <a name="next-steps"></a>Další postup
* Konfigurace služby Azure Monitor ke shromažďování dalších [zdroje dat](agent-data-sources.md) pro analýzu.
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.