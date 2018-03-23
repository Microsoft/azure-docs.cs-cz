---
title: Azure Log Analytics dotazu jazyka – tahák | Microsoft Docs
description: Tento článek obsahuje pomoc při přechodu do nové dotazovací jazyk pro analýzu protokolu, pokud jste již obeznámeni s starší verze jazyka.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Přechod na nový dotaz jazyk Azure Log Analytics
Analýzy protokolů nedávno implementována o nový jazyk dotazu.  Tento článek obsahuje pomoc na přechod tento jazyk pro analýzu protokolu, pokud jste již obeznámeni s starší verze jazyka a potřebujete ještě další pomoc.

## <a name="resources"></a>Zdroje a prostředky


## <a name="language-converter"></a>Převaděč jazyk

Pokud jste obeznámeni s starší verze analýzy protokolů dotazovací jazyk, je nejjednodušší způsob, jak vytvořit stejný dotaz v požadovaném jazyce pomocí převaděče jazyk, který je nainstalován na portálu hledání protokolů při převodu pracovního prostoru.  Pomocí převaděč je jednoduché, zadáním v starší verze dotaz top textového pole a pak levým na **převést**.  Můžete buď klikněte na tlačítko Hledat spustit dotaz nebo kopírovat a vložit jej používat někde jinde.

![Převaděč jazyk](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Zdroje a prostředky
[Web dokumentace pro dotazovacího jazyka pro analýzu protokolu](https://docs.loganalytics.io) má všechny prostředky, budete muset pocházet urychlit v požadovaném jazyce.  To zahrnuje kurzy, příklady a odkazem na dokončení jazyk.


## <a name="cheat-sheet"></a>Tahák

Následující tabulka uvádí srovnání mezi celou řadu běžných dotazů na ekvivalentní příkazy mezi novou a starší verze dotazovací jazyk v Azure Log Analytics.

| Popis | Starší verze | novinka |
|:--|:--|:--|
| Vyhledat všechny tabulky      | error | hledání "error" (ne velká a malá písmena) |
| Vyberte data z tabulky | Typ = událostí |  Událost |
|                        | Typ = událostí &#124; vyberte zdroj protokolu událostí, ID události | Událost &#124; projektu zdroj protokolu událostí, ID události |
|                        | Typ = událostí &#124; prvních 100 | Událost &#124; trvat 100 |
| Porovnání řetězců      | Typ = Computer=srv01.contoso.com událostí   | Událost &#124; kde počítač == "srv01.contoso.com" |
|                        | Typ = Computer=contains("contoso") událostí | Událost &#124; kde počítač obsahuje "contoso" (ne velká a malá písmena)<br>Událost &#124; kde počítač contains_cs "Contoso" (malá a velká písmena) |
|                        | Typ = počítač událostí = RegEx ("@contoso@")  | Událost &#124; kde počítače odpovídá regulárnímu výrazu ". *contoso*" |
| Porovnání kalendářních dat        | Typ události TimeGenerated = > nyní 1DAYS | Událost &#124; kde TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Událost &#124; kde TimeGenerated mezi (datetime(2017-05-01)... DATETIME(2017-05-31)) |
| Logická hodnota porovnání     | Type=Heartbeat IsGatewayInstalled=false  | Prezenční signál \| kde IsGatewayInstalled hodnotu false |
| Seřadit                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Událost \| řazení podle počítače asc, EventLevelName asc a desc protokolu událostí |
| Odlišné               | Typ = událostí &#124; odstraňování duplicitních dat počítače \| vyberte počítač | Událost &#124; shrnout počítačem protokolu událostí |
| Rozšíření sloupců         | Typ = výkonu název_čítače = "čas procesoru v %" &#124; rozšíření if(map(CounterValue,0,50,0,1),"HIGH","LOW") jako využití | Výkonu &#124; tam, kde CounterName == "% času procesoru" \| rozšířit využití =, je-li ("Nedostatek" přepočtené > 50, "Vysoká") |
| Agregace            | Typ = událostí &#124; měření count() jako počet počítačem. | Událost &#124; shrnout Count = count() počítačem. |
|                                | Typ = výkonu ObjectName = název_čítače procesoru = "% času procesoru" &#124; měření avg(CounterValue) počítače interval 5 minut | Výkonu &#124; kde ObjectName == "Procesor" a název_čítače == "% času procesoru" &#124; shrnout avg(CounterValue) počítačem bin (TimeGenerated, 5 minut) |
| Agregace s limitem | Typ = událostí &#124; měření count() počítačem &#124; top 10 | Událost &#124; shrnout AggregatedValue = count() počítačem &#124; omezit 10 |
| sjednocení                  | Typ = události nebo typ = Syslog | sjednocení události procesu Syslog |
| Spojit                   | Typ = NetworkMonitoring &#124; připojení vnitřní AgentIP (typ = prezenční signál) ComputerIP | NetworkMonitoring &#124; připojení typ = vnitřní (vyhledávání typu == "Prezenčního signálu") na $left. AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Další postup
- Podívejte se [kurz na zápis dotazů](https://go.microsoft.com/fwlink/?linkid=856078) pomocí nového dotazu jazyka.
- Odkazovat [referenční příručka jazyka dotazů](https://go.microsoft.com/fwlink/?linkid=856079) podrobné informace o příkazu, operátory a funkce pro nové dotazovací jazyk.  
