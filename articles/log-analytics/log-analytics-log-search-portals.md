---
title: Portály pro vytváření a úpravy dotazů protokolu v Azure Log Analytics | Microsoft Docs
description: Tento článek popisuje portály, které můžete použít v Azure Log Analytics můžete vytvářet a upravovat protokolu hledání.
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
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132866"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portály pro vytváření a úpravy dotazů protokolu v Azure Log Analytics

Protokol hledání v různých místech analýzy protokolů se použít k načtení dat z pracovního prostoru.  Ve skutečnosti vytváření a úpravy dotazů kromě práce interaktivně se vrácená data, když, máte dvě možnosti, které jsou popsány níže.  

## <a name="log-search"></a>Prohledávání protokolů 
Stránka hledání protokolů je dostupný z portálu Azure.  Je vhodné pro vytvoření základní dotazy, které lze vytvořit na jeden řádek.  Hledání protokolů můžete používat bez spuštění externí portál a slouží k provádění různých funkcí s protokolu hledání, včetně vytvoření pravidla výstrah, vytvoření skupin počítačů a export výsledků dotazu.  

Hledání protokolů poskytuje více funkcí pro úpravu dotaz bez nutnosti úplné znalost dotazovacího jazyka.  Zobrazí souhrn těchto funkcí v [vytvořit protokolu hledání v Azure Log Analytics pomocí hledání protokolů](log-analytics-log-search-log-search-portal.md).


![Stránka vyhledávání protokolu](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Pokročilé analýzy portálu
Portálu pro pokročilou analýzu je vyhrazené portál, který poskytuje pokročilé funkce není k dispozici v hledání protokolů z portálu Azure.  Mezi tyto funkce patří možnost upravovat dotazy na několik řádků, selektivní spouštění kódu, funkce IntelliSense závislá na kontextu a inteligentní analýzy.  Portál Advanced Analytics je nejvhodnější pro návrh složitých dotazů, které jsou buď jako vyhledávání protokolu uložit nebo kopírovat a vkládat data do jiných elementů analýzy protokolů.  Spuštění portálu Advanced Analytics z odkaz na stránce hledání protokolů.

![Pokročilé analýzy portálu](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Z důvodu jeho pokročilé funkce obvykle použijete portálu pokročilou analýzu jako svůj primární nástroj pro vytváření a úpravy dotazů.  Jednou jste zjistili, že dotaz funguje podle očekávání, a potom budete zkopírujte a vložte ho jinde například stránka vyhledávání protokolu nebo Návrhář zobrazení.  

### <a name="firewall-requirements"></a>Požadavky na bránu firewall
Váš prohlížeč vyžaduje přístup na následující adresy pro přístup k portálu pokročilé analýzy.  Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, musíte povolit přístup pro tyto adresy.

| URI | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamická | 80,443 |
| api.loganalytics.io    | Dynamická | 80,443 |
| docs.loganalytics.io   | Dynamická | 80,443 |


## <a name="next-steps"></a>Další postup

- Provede kurz k používání [hledání protokolů](log-analytics-tutorial-viewdata.md) Další informace o vytváření dotazů pomocí dotazu jazyka
- Podívejte se [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587) k vytvoření složitých dotazů a použít jako vývojové prostředí pro svoje vyhledávání protokolu.

