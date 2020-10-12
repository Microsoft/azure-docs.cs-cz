---
title: Protokol IIS v Azure Monitor | Microsoft Docs
description: Internetová informační služba (IIS) ukládá činnost uživatelů do souborů protokolu, které lze shromažďovat pomocí Azure Monitor.  Tento článek popisuje, jak nakonfigurovat kolekci protokolů služby IIS a podrobnosti o záznamech, které vytvoří v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 0bca809d6c25594c1c614f694e71e39a4f61e2a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87008179"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Shromažďovat protokoly IIS v Azure Monitor
Internetová informační služba (IIS) ukládá činnost uživatelů do souborů protokolu, které lze shromažďovat pomocí Azure Monitor a uložených jako [data protokolu](data-platform.md).

![Protokoly IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace protokolů IIS
Azure Monitor shromažďuje záznamy ze souborů protokolu vytvořených službou IIS, takže musíte [nakonfigurovat službu IIS pro protokolování](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Azure Monitor podporuje jenom soubory protokolu IIS uložené ve formátu W3C a nepodporuje vlastní pole ani rozšířené protokolování služby IIS. Neshromažďuje protokoly ve formátu NCSA nebo IIS Native.

Nakonfigurujte protokoly IIS v Azure Monitor z [nabídky Upřesnit nastavení](agent-data-sources.md#configuring-data-sources).  Není vyžadována žádná konfigurace kromě výběru možnosti **shromažďovat soubory protokolů IIS ve formátu W3C**.


## <a name="data-collection"></a>Shromažďování dat
Při každém změně časového razítka protokolu Azure Monitor shromažďuje položky protokolu služby IIS od každého agenta. Protokol je čten každých **5 minut**. Pokud z jakéhokoli důvodu služba IIS neaktualizuje časové razítko před časem přechodu při vytvoření nového souboru, budou záznamy shromážděny po vytvoření nového souboru. Frekvence nového vytváření souborů je řízena nastavením plánu změny **souboru protokolu** pro web služby IIS, který je ve výchozím nastavení jednou denně. Pokud je nastavení každou **hodinu**, Azure monitor shromáždí protokol každou hodinu. Pokud je nastavení **denně**, Azure monitor shromáždí protokol každých 24 hodin.


## <a name="iis-log-record-properties"></a>Vlastnosti záznamu protokolu IIS
Záznamy protokolu služby IIS mají typ **W3CIISLog** a mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, ze kterého byla událost shromážděna. |
| cIP |IP adresa tohoto klienta |
| csMethod |Metoda požadavku, například GET nebo POST. |
| csReferer |Lokalita, ze které uživatel následoval odkaz z na aktuální web. |
| csUserAgent |Typ prohlížeče klienta. |
| csUserName |Jméno ověřeného uživatele, který se připojil k serveru. Anonymní uživatelé se označují pomlčkou. |
| csUriStem |Cíl žádosti, jako je například webová stránka. |
| csUriQuery |Dotaz (pokud existuje), který klient prováděl při pokusu o provedení. |
| ManagementGroupName |Název skupiny pro správu pro agenty Operations Manager.  Pro jiné agenty je to AOI-\<workspace ID\> |
| RemoteIPCountry |Země nebo oblast IP adresy klienta. |
| RemoteIPLatitude |Zeměpisná šířka IP adresy klienta. |
| RemoteIPLongitude |Zeměpisná délka klientské IP adresy. |
| scStatus |Stavový kód HTTP. |
| scSubStatus |Kód chyby dílčího stavu |
| scWin32Status |Stavový kód Windows. |
| Adresa |IP adresa webového serveru. |
| SourceSystem |Jedn |
| Sportu |Port na serveru, ke kterému je klient připojen. |
| sSiteName |Název webu služby IIS. |
| TimeGenerated |Datum a čas, kdy byla položka zaznamenána. |
| TimeTaken |Doba zpracování žádosti v milisekundách |

## <a name="log-queries-with-iis-logs"></a>Protokolování dotazů s protokoly služby IIS
Následující tabulka uvádí různé příklady dotazů protokolu, které načítají záznamy protokolu služby IIS.

| Dotaz | Description |
|:--- |:--- |
| W3CIISLog |Všechny záznamy protokolu služby IIS. |
| W3CIISLog &#124; WHERE scStatus = = 500 |Všechny záznamy protokolu služby IIS se stavem vrácenými na 500. |
| W3CIISLog &#124; souhrnu počtu () podle cIP |Počet záznamů protokolu IIS podle IP adresy klienta |
| W3CIISLog &#124; WHERE csHost = = "www \. contoso.com" &#124; sumarizace Count () od csUriStem |Počet záznamů protokolu IIS podle adresy URL pro hostitele www \. contoso.com |
| W3CIISLog &#124; Shrnutí součtu (csBytes) podle počítače &#124; Vezměte 500000 |Celkový počet bajtů přijatých jednotlivými počítači IIS. |

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte Azure Monitor pro shromažďování dalších [zdrojů dat](agent-data-sources.md) pro účely analýzy.
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.
