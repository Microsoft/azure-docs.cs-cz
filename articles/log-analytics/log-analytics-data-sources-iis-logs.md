---
title: Služba IIS přihlásí Azure Log Analytics | Microsoft Docs
description: Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které může shromáždit analýzy protokolů.  Tento článek popisuje, jak nakonfigurovat kolekce protokoly služby IIS a podrobnosti o záznamy, které vytvoří v pracovním prostoru analýzy protokolů.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>Služba IIS přihlásí analýzy protokolů
Internetové informační služby (IIS) ukládá aktivity uživatelů v souborech protokolů, které může shromáždit analýzy protokolů.  

![Protokoly IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace služby IIS protokoly
Analýzy protokolů shromáždí položky z soubory protokolů vytvořené službou IIS, takže musíte [konfigurace IIS pro protokolování](https://technet.microsoft.com/library/hh831775.aspx).

Analýzy protokolů pouze podporuje uložený ve formátu W3C souborech protokolu služby IIS a nepodporuje vlastní pole nebo rozšířené protokolování internetové informační služby.  
Analýzy protokolů neshromažďuje protokoly ve formátu NCSA nebo IIS nativní.

Konfigurace služby IIS protokoly v analýzy protokolů z [nabídce Data v nastavení analýzy protokolů](log-analytics-data-sources.md#configuring-data-sources).  Není nutná žádná konfigurace, jiné než vyberete **souborů protokolu W3C shromažďovat formátu služby IIS**.

Doporučujeme vám, že pokud povolíte kolekce protokolu služby IIS, by měl nakonfigurovat nastavení výměny protokolu služby IIS na každém serveru.

## <a name="data-collection"></a>Shromažďování dat
Analýzy protokolů shromažďuje položky protokolu služby IIS z každého připojeného zdroje přibližně každých 15 minut.  Agent zaznamenává jeho místo v každém protokolu událostí, shromažďující z.  Pokud agenta přejde do režimu offline, pak analýzy protokolů shromažďuje události od poslední místa vypnout, i v případě, že tyto události byly vytvořeny v době, kdy agent offline.

## <a name="iis-log-record-properties"></a>Vlastnosti záznamu protokolu služby IIS
Záznamy protokolu služby IIS mít typ **W3CIISLog** a mít vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, který událost nebyla shromážděna z. |
| cIP |IP adresa klienta. |
| csMethod |Metoda požadavku, jako například GET nebo POST. |
| csReferer |Lokalita, že uživatel udělali odkaz z aktuální web. |
| csUserAgent |Typ prohlížeče klienta. |
| csUserName |Jméno ověřeného uživatele, který se připojil k serveru. Anonymní uživatelé se označují pomlčkou. |
| csUriStem |Cíl žádosti například na webové stránce. |
| csUriQuery |Dotaz, pokud existuje, že se klient pokusil provést. |
| ManagementGroupName |Název skupiny pro správu pro agenty nástroje Operations Manager.  Pro jiné agenty jde AOI -\<ID pracovního prostoru\> |
| RemoteIPCountry |Země IP adresu klienta. |
| RemoteIPLatitude |Zeměpisnou šířku IP adresu klienta. |
| RemoteIPLongitude |Zeměpisná délka IP adresy klienta. |
| scStatus |Stavový kód HTTP. |
| scSubStatus |Druhotný stavový kód chyby. |
| scWin32Status |Stavový kód Windows. |
| sIP |IP adresa webového serveru. |
| SourceSystem |Nástroj OpsMgr |
| sPort |Port na serveru klient připojen. |
| sSiteName |Název webu služby IIS. |
| TimeGenerated |Datum a čas, kdy byla zapsána na položku. |
| timeTaken |Časový interval pro zpracování požadavku v milisekundách. |

## <a name="log-searches-with-iis-logs"></a>Protokol hledání s protokoly služby IIS
Následující tabulka obsahuje příklady různých dotazů protokolu, které načtení záznamů protokolu služby IIS.

| Dotaz | Popis |
|:--- |:--- |
| W3CIISLog |Všechny záznamy protokolu služby IIS. |
| W3CIISLog &#124; kde scStatus == 500 |Všechny záznamy protokolu služby IIS s návratový stav 500. |
| W3CIISLog &#124; shrnout count() podle cIP |Počet služby IIS protokolu položky podle IP adresy klienta. |
| W3CIISLog &#124; kde csHost == "www.contoso.com" &#124; shrnout count() podle csUriStem |Počet z protokolu služby IIS položky podle adresy URL pro www.contoso.com hostitele. |
| W3CIISLog &#124; shrnout sum(csBytes) podle počítače &#124; trvat 500000 |Celkový počet přijatých bajtů na každém počítači služby IIS. |

## <a name="next-steps"></a>Další postup
* Konfigurace analýzy protokolů ke shromažďování dalších [zdroje dat](log-analytics-data-sources.md) pro analýzu.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení.
* Konfigurace výstrah v analýzy protokolů pro proaktivní upozornění důležité podmínek najít v protokolech služby IIS.
