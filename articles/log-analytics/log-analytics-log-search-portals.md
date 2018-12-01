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
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8fdf1bed0b75111abef4579565698f0c48b5d843
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724141"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Zobrazení a analýza dat v Log Analytics
Existují dvě možnosti k dispozici na webu Azure Portal pro analýzu dat uložených ve službě Log analytics a k vytváření dotazů ad hoc analýzy. Pro další funkce, jako je například výstrahy a řídicí panely je možné dotazy, které vytvoříte pomocí těchto portálů.

## <a name="log-analytics-page"></a>Stránka log Analytics
Otevřete stránku Log Analytics z **protokoly** v nabídce Log Analytics. Jedná se o nové prostředí pro práci s daty protokolů a vytváření dotazů. Můžete získat Úvod k tomuto portálu a zkontrolujte jeho funkce v [Začínáme s Log Analytics stránky na webu Azure Portal](query-language/get-started-analytics-portal.md).

Na stránce Log Analytics nabízí v porovnání s následujícími vylepšeními [prohledávání protokolu (classic)](#log-search-classic) prostředí.

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

### <a name="resource-logs"></a>Protokoly prostředku
Nové prostředí Log Analytics se integruje se s různými prostředky Azure, jako jsou virtuální počítače. To znamená, že můžete otevřít stránku Log Analytics přímo prostřednictvím nabídky monitorování prostředku bez přechodu k Azure Monitor nebo Log Analytics a ztráty kontextu prostředků. **Protokoly** ještě nepovolila pro všechny prostředky Azure, ale to se začnou zobrazovat v nabídce portálu pro různé prostředky typy.

Když otevřete Log Analytics z konkrétního prostředku, je automaticky vymezí na protokolování záznamů pouze tento prostředek.   Pokud chcete vytvořit dotaz, který obsahuje další záznamy, pak by muset otevřít z nabídky Log Analytics nebo Azure Monitor.

Následující možnosti nejsou k dispozici prostřednictvím zobrazení prostředků služby Log Analytics:

- Uložení
- Nastavení upozornění
- Průzkumník dotazů
- Přepnutí na jiný pracovní prostor/resource (aktuálně nejsou plánované)


### <a name="firewall-requirements"></a>Požadavky na bránu firewall
Váš prohlížeč vyžaduje přístup k následující adresy, které mají přístup ke stránce Log Analytics a portál pro pokročilou analýzu.  Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, je třeba povolit přístup pro tyto adresy.

| URI | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamická | 80,443 |
| api.loganalytics.io    | Dynamická | 80,443 |
| docs.loganalytics.io   | Dynamická | 80,443 |


## <a name="log-search-classic"></a>Prohledávání protokolu (classic)
Otevřete stránku vyhledávání protokolu z **protokoly (Classic)** v nabídce Log Analytics nebo z **Log Analytics** v nabídce Azure Monitor. Toto je stránka classic používané pro práci s dotazy Log Analytics, které nejsou uvedeny další funkce [stránce Log Analytics](#log-analytics-page) uvedené výše.



![Stránka hledání protokolů](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Další postup

- Projít [kurz pomocí prohledávání protokolů](log-analytics-tutorial-viewdata.md) Další informace o vytváření dotazů pomocí dotazovacího jazyka
- Projít [lekce pomocí portálu pro pokročilou analýzu](query-language/get-started-analytics-portal.md) poskytující stejné prostředí jako stránce Log Analytics.

