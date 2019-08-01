---
title: Hledání Azure Security Center | Microsoft Docs
description: Přečtěte si, jak Azure Security Center používá hledání Azure Monitor protokolů k načtení a analýze dat zabezpečení.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662916"
---
# <a name="azure-security-center-search-retired"></a>Hledání Azure Security Center (vyřazeno)

> [!NOTE]
> Řídicí panel hledání Security Center byl vyřazen 31 2019. července. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_search).

Azure Security Center používá [hledání Azure monitor protokolů](../log-analytics/log-analytics-log-searches.md) k načtení a analýze dat zabezpečení. Protokoly Azure Monitor obsahují dotazovací jazyk pro rychlé načítání a slučování dat. Z Security Center můžete využít hledání Azure Monitorch protokolů k vytváření dotazů a analýze shromážděných dat.

Hledání je dostupné jak na úrovni Free, tak na úrovni Standard Security Center.  Data, která jsou k dispozici v hledání v protokolu, jsou závislá na úrovni vrstvy použité pro váš pracovní prostor.  Další informace najdete na [stránce s cenami](../security-center/security-center-pricing.md) Security Center.


> [!NOTE]
> Security Center neukládá data zabezpečení pro pracovní prostor v bezplatné úrovni. Do pracovního prostoru můžete na úrovni Free poslat celou řadu protokolů a vyhledat tato data, ale výsledky hledání neobsahují data z Security Center. Security Center ukládá pouze data do pracovního prostoru v rámci úrovně Standard.
>
>

## <a name="access-search"></a>Hledání přístupu
1. V hlavní nabídce Security Center vyberte **Hledat**.

   ![Výběr prohledávání protokolu][1]

2. Security Center uvádí všechny pracovní prostory v rámci předplatných Azure. Vyberte pracovní prostor. (Pokud máte jenom jeden pracovní prostor, výběr tohoto pracovního prostoru se nezobrazí.)

   ![Vybrat pracovní prostor][2]

3. Otevře se **prohledávání protokolu** . Pokud chcete zadat dotaz na další data ve vybraném pracovním prostoru, zadejte tento ukázkový dotaz:

   SecurityEvent | kde ID události = = 4625 | Shrnutí počtu () podle TargetAccount

   Výsledek zobrazí všechny účty, které se nepodařilo přihlásit (událost 4625).

   ![Výsledky vyhledávání][3]

Další informace o tom, jak se dotazovat na data ve vybraném pracovním prostoru, najdete v tématu [dotazovací jazyk Kusto](../log-analytics/log-analytics-search-reference.md) .

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak získat přístup k hledání v Security Center. Security Center používá hledání Azure Monitorch protokolů. Další informace o vyhledávání protokolů Azure Monitor najdete tady:

- [Co jsou protokoly Azure Monitor?](../log-analytics/log-analytics-overview.md) – Přehled protokolů Azure Monitor
- [Principy prohledávání protokolů v](../log-analytics/log-analytics-log-search-new.md) protokolech Azure monitor – popisuje, jak se v protokolech Azure monitor používají prohledávání protokolů, a poskytuje koncepty, které by se měly chápat před vytvořením prohledávání protokolu.
- [Hledání dat pomocí hledání v protokolu v](../log-analytics/log-analytics-log-searches.md) protokolech Azure monitor – kurz použití prohledávání protokolů
- [Odkaz na hledání Kusto](../log-analytics/log-analytics-search-reference.md) – popisuje dotazovací jazyk v protokolech Azure monitor

Další informace o Security Center najdete v těchto tématech:

- [Přehled Security Center](security-center-intro.md) – popisuje klíčové funkce Security Center

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
