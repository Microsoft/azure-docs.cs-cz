---
title: Zobrazení a analýza dat v Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje portály, které můžete v Azure Log Analytics můžete vytvářet a upravovat prohledávání protokolů.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 996502ffe5a31fcfa1b73dab9a041c336c4ea98f
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45602627"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Zobrazení a analýza dat v Log Analytics
Existují dvě možnosti k dispozici na webu Azure Portal pro analýzu dat uložených ve službě Log analytics a k vytváření dotazů ad hoc analýzy. Pro další funkce, jako je například výstrahy a řídicí panely je možné dotazy, které vytvoříte pomocí těchto portálů.

## <a name="log-analytics-page"></a>Stránka log Analytics
Otevřete stránku Log Analytics z **protokoly** v nabídce Log Analytics. Jedná se o nové prostředí pro práci s daty protokolů a vytváření dotazů. Můžete získat Úvod k tomuto portálu a zkontrolujte jeho funkce v [Začínáme s Log Analytics stránky na webu Azure Portal](query-language/get-started-analytics-portal.md).

Na stránce Log Analytics nabízí následující vylepšení v porovnání [prohledávání protokolů](#log-search) prostředí.

* Více karet – vytvoření samostatných kartách pro práci s více dotazy.
* Přehledné vizualizace – různé možnosti grafu.
* Vylepšená technologie Intellisense a jazyk automatického dokončování.
* Zvýraznění syntaxe – zlepšuje čitelnost dotazů. 
* Průzkumník dotazů – přístup k uložení dotazů a funkce.
* Schéma – zobrazení zkontrolujte strukturu vašich dat, které pomáhají při psaní dotazů.
* Sdílet – vytvořit odkazy na dotazy nebo dotazy připnout na řídicí panel žádné sdílené Azure.
* Inteligentní analýza – identifikuje poraďte se špičkami grafů a rychlá Analýza příčiny.
* Výběr sloupce – řazení a seskupení sloupců ve výsledcích dotazu.

> [!NOTE]
> Na stránce Log Analytics obsahuje stejné funkce jako portál pro pokročilou analýzu, což je externího nástroje mimo na webu Azure portal. Je stále k dispozici portálu pro pokročilou analýzu, ale s touto novou stránkou se nahrazují odkazy a odkazy na ni na portálu Azure portal.

![Rozšířený portál Analytics](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Požadavky na bránu firewall
Váš prohlížeč vyžaduje přístup k následující adresy, které mají přístup ke stránce Log Analytics a portál pro pokročilou analýzu.  Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, je třeba povolit přístup pro tyto adresy.

| URI | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamická | 80,443 |
| api.loganalytics.io    | Dynamická | 80,443 |
| docs.loganalytics.io   | Dynamická | 80,443 |


## <a name="log-search-classic"></a>Prohledávání protokolu (Classic)
Otevřete stránku vyhledávání protokolu z **protokoly (Classic)** v nabídce Log Analytics nebo z **Log Analytics** v nabídce Azure Monitor. Toto je vhodný pro analýzu dat protokolu pomocí základní dotazy. Poskytuje několik funkcí pro úpravy dotazů bez nutnosti úplné znalost dotazovacího jazyka.  Můžete se souhrnné informace o těchto funkcích v [vytvořit prohledávání protokolů v Azure Log Analytics s využitím prohledávání protokolů](log-analytics-log-search-log-search-portal.md). 


![Stránka hledání protokolů](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Další postup

- Projít [kurz pomocí prohledávání protokolů](log-analytics-tutorial-viewdata.md) Další informace o vytváření dotazů pomocí dotazovacího jazyka
- Projít [lekce pomocí portálu pro pokročilou analýzu](query-language/get-started-analytics-portal.md) poskytující stejné prostředí jako stránce Log Analytics.

