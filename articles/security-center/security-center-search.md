---
title: Hledání v Centru zabezpečení Azure | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center využívá Azure Monitor protokoly hledání načíst a analyzovat data zabezpečení.
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
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332561"
---
# <a name="azure-security-center-search"></a>Hledání v Centru zabezpečení Azure
Azure Security Center používá [protokoly Azure monitoru hledání](../log-analytics/log-analytics-log-searches.md) načíst a analyzovat data zabezpečení. Protokoly Azure monitoru poskytuje dotazovací jazyk k rychlému načítání a slučování dat. Ze služby Security Center můžete využít vyhledávání protokoly Azure monitoru vytvářet dotazy a analyzovat shromážděná data.

Vyhledávání je dostupné úrovni Free a úroveň Standard služby Security Center.  Aby byla data dostupná v rámci vaší prohledávání protokolů je závislá na úroveň vrstvy použitý pro váš pracovní prostor.  Zobrazit Security Center [stránce s cenami](../security-center/security-center-pricing.md) Další informace.


> [!NOTE]
> Security Center nedojde k uložení dat zabezpečení pro pracovní prostor v bezplatné úrovni. Různé protokoly můžete odeslat do pracovního prostoru v rámci úrovně Free a hledání na těchto datech, ale výsledky hledání neobsahují data ze služby Security Center. Security Center jenom uloží data do pracovního prostoru v části na úrovni Standard.
>
>

## <a name="access-search"></a>Přístup k vyhledávání
1. V hlavní nabídce služby Security Center, vyberte **hledání**.

   ![Vyberte prohledávání protokolů][1]

2. Security Center obsahuje seznam všech pracovních prostorů v rámci vašich předplatných Azure. Vyberte pracovní prostor. (Pokud máte jenom jeden pracovní prostor, tento selektor pracovního prostoru se nezobrazí.)

   ![Vybrat pracovní prostor][2]

3. **Prohledávání protokolů** otevře. K dotazování na další data v rámci vybraného pracovního prostoru, zadejte tento příklad dotazu:

   SecurityEvent | kde EventID == 4625 | shrnutí count() by TargetAccount

   Výsledek se zobrazí všechny účty, které se nezdařilo přihlášení (událost 4625).

   ![Výsledky hledání][3]

Zobrazit [Kusto dotazovací jazyk](../log-analytics/log-analytics-search-reference.md) Další informace o tom, jak dotaz na data ve vybraném pracovním prostoru.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak získat přístup k vyhledávání ve službě Security Center. Security Center používá službu search protokoly Azure monitoru. Další informace o vyhledávání protokoly Azure monitoru, najdete v tématech:

- [Co je Azure Monitor protokoly?](../log-analytics/log-analytics-overview.md) – Přehled na protokoly Azure monitoru
- [Principy prohledávání protokolů v protokoly Azure monitoru](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak prohledávání protokolů jsou v protokolech Azure Monitor a poskytuje koncepty, které by měl být srozumitelný před vytvořením prohledávání protokolu
- [Vyhledání dat pomocí prohledávání protokolů v protokoly Azure monitoru](../log-analytics/log-analytics-log-searches.md) – kurz k používání prohledávání protokolů
- [Reference ke službě search Kusto](../log-analytics/log-analytics-search-reference.md) – popisuje dotazovací jazyk v protokoly Azure monitoru

Další informace o službě Security Center najdete v tématu:

- [Přehled služby Security Center](security-center-intro.md) – Centrum zabezpečení popisuje klíčové funkce

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
