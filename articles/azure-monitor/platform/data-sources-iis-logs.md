---
title: Protokoly služby IIS ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Internetová informační služba (IIS) ukládá aktivitu uživatelů v souborech protokolu, které mohou být shromažďovány službou Azure Monitor.  Tento článek popisuje, jak nakonfigurovat kolekci protokolů služby IIS a podrobnosti o záznamech, které vytvářejí v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670572"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Shromažďování protokolů služby IIS ve službě Azure Monitor
Internetová informační služba (IIS) ukládá aktivitu uživatelů v souborech protokolu, které mohou být shromažďovány službou Azure Monitor a uloženy jako [data protokolu](data-platform.md).

![Protokoly IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurace protokolů služby IIS
Služba Azure Monitor shromažďuje položky ze souborů protokolu vytvořených službou IIS, takže je nutné [nakonfigurovat službu IIS pro protokolování](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor podporuje pouze soubory protokolu služby IIS uložené ve formátu W3C a nepodporuje vlastní pole nebo rozšířené protokolování služby IIS. Neshromažďuje protokoly v nativním formátu NCSA nebo IIS.

Nakonfigurujte protokoly služby IIS v programu Azure Monitor z [nabídky Upřesnit nastavení](agent-data-sources.md#configuring-data-sources).  Není nutná žádná konfigurace kromě výběru **souborů protokolu služby IIS ve formátu Collect W3C**.


## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje položky protokolu služby IIS od každého agenta při každé změně časového razítka protokolu. Protokol se čte každých **5 minut**. Pokud z nějakého důvodu služba IIS neaktualizuje časové razítko před časem přechodu při vytvoření nového souboru, budou po vytvoření nového souboru shromážděny položky. Četnost vytváření nových souborů je řízena nastavením **Plán přechodu souborů protokolu** pro web iis, které je ve výchozím nastavení jednou denně. Pokud je nastavení **hodinové**, Azure Monitor shromažďuje protokol každou hodinu. Pokud je nastavení **Denně**, Azure Monitor shromažďuje protokol každých 24 hodin.


## <a name="iis-log-record-properties"></a>Vlastnosti záznamu protokolu služby IIS
Záznamy protokolu služby IIS mají typ **W3CIISLog** a mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, ze kterého byla událost shromážděna. |
| Cip |IP adresa tohoto klienta |
| csMetoda |Metoda požadavku, například GET nebo POST. |
| csReferer |Web, ze kterého uživatel sledoval odkaz na aktuální web. |
| csUserAgent |Typ prohlížeče klienta. |
| csUserName |Jméno ověřeného uživatele, který přistupoval k serveru. Anonymní uživatelé se označují pomlčkou. |
| csUriStem |Cíl požadavku, například webové stránky. |
| csUriQuery |Dotaz, pokud existuje, že klient se snažil provést. |
| ManagementGroupName |Název skupiny pro správu pro agenty nástroje Operations Manager.  Pro ostatní agenty se jedná\<o ID pracovního prostoru AOI.\> |
| RemoteIPCountry |Země nebo oblast IP adresy klienta. |
| RemoteIPLatitude |Zeměpisná šířka IP adresy klienta. |
| RemoteIPLongitude |Zeměpisná a dlouhá písmena ip adresy klienta. |
| scStatus |Stavový kód HTTP. |
| scSubStatus |Kód chyby dílčího stavu. |
| scWin32Stav |Stavový kód systému Windows. |
| Sip |IP adresa webového serveru. |
| SourceSystem |OpsMgr |
| Sport |Port na serveru, ke serveru, ke které je klient připojen. |
| název_webu |Název webu iis. |
| TimeGenerated |Datum a čas záznamu položky. |
| TimeTaken |Doba zpracování požadavku v milisekundách. |

## <a name="log-queries-with-iis-logs"></a>Protokolovat dotazy pomocí protokolů iis
Následující tabulka obsahuje různé příklady dotazů protokolu, které načítají záznamy protokolu iis.

| Dotaz | Popis |
|:--- |:--- |
| W3CIISLog |Všechny záznamy protokolu iis. |
| W3CIISLog &#124; kde scStatus==500 |Všechny záznamy protokolu iis se stavem vrácení 500. |
| W3CIISLog &#124; sumarizovat count() podle cIP |Počet položek protokolu služby IIS podle IP adresy klienta. |
| W3CIISLog &#124; kde csHost=="www\.contoso.com" &#124; shrnout count() od csUriStem |Počet položek protokolu služby IIS\.podle adresy URL hostitele www contoso.com. |
| W3CIISLog &#124; shrnout součet (csBytes) podle počítače &#124; trvat 500000 |Celkový počet bajtů přijatých každým počítačem se systémem IIS. |

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte Azure Monitor tak, aby shromažďoval další [zdroje dat](agent-data-sources.md) pro analýzu.
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.
