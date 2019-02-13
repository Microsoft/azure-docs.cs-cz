---
title: Zobrazení a analýza dat protokolu ve službě Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje, používat službu Log Analytics na portálu Azure portal můžete vytvářet a upravovat dotazy protokolu ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/22/2018
ms.author: bwren
ms.openlocfilehash: 6e84344e1c0229d15891bd15a512880da6e20cfe
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110757"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Zobrazení a analýza dat protokolu ve službě Azure Monitor
Log Analytics je primární prostředí pro práci s daty protokolů a vytváření dotazů ve službě Azure Monitor. Otevřete Log Analytics z **protokoly** v **Azure Monitor** nabídky. Můžete získat Úvod k tomuto portálu a zkontrolujte jeho funkce v [Začínáme se službou Log Analytics na portálu Azure portal](get-started-portal.md).

Log Analytics poskytuje následující funkce pro práci s dotazů na protokoly.

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
> Log Analytics má stejné funkce jako portál pro pokročilou analýzu, což je externího nástroje mimo na webu Azure portal. Je stále k dispozici portálu pro pokročilou analýzu, ale s touto novou stránkou se nahrazují odkazy a odkazy na ni na portálu Azure portal.

![Log Analytics](media/portals/log-analytics.png)

### <a name="resource-logs"></a>Protokoly prostředku
Log Analytics se integruje se s různými prostředky Azure, jako jsou virtuální počítače. To znamená, že můžete otevřít Log Analytics přímo prostřednictvím nabídky monitorování prostředku bez přepnutí do Azure monitoru a ztráty kontextu prostředků. **Protokoly** ještě nepovolila pro všechny prostředky Azure, ale to se začnou zobrazovat v nabídce portálu pro různé prostředky typy.

Když otevřete Log Analytics z konkrétního prostředku, je automaticky vymezí na protokolování záznamů pouze tento prostředek.   Pokud chcete vytvořit dotaz, který obsahuje další záznamy, pak by muset otevřít z nabídky Azure Monitor.

Následující možnosti nejsou k dispozici prostřednictvím zobrazení prostředků služby Log Analytics:

- Uložení
- Nastavení upozornění
- Průzkumník dotazů
- Přepnutí na jiný pracovní prostor/resource (aktuálně nejsou plánované)


### <a name="firewall-requirements"></a>Požadavky na bránu firewall
Váš prohlížeč vyžaduje přístup na následující adresy pro přístup k Log Analytics.  Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, je třeba povolit přístup pro tyto adresy.

| URI | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamická | 80,443 |
| api.loganalytics.io    | Dynamická | 80,443 |
| docs.loganalytics.io   | Dynamická | 80,443 |


## <a name="log-search-classic"></a>Prohledávání protokolu (classic)
Hledání v protokolu je starší verze prostředí na webu Azure Portal pro dotazování a analýze dat protokolů ve službě Azure Monitor. Brzy skončí, ale aktuálně je stále k dispozici. Otevřete prohledávání protokolů z **protokoly (Classic)** v nabídce Log Analytics.



![Prohledávání protokolů](media/portals/log-search-portal.png)


## <a name="next-steps"></a>Další postup

- Projít [kurz pomocí Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Projít [kurz pomocí prohledávání protokolů](../../azure-monitor/learn/tutorial-viewdata.md).

