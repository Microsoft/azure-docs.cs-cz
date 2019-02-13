---
title: Hledání v Centru zabezpečení Azure | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center používá hledání Log Analytics k načtení a analyzovat data zabezpečení.
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
ms.openlocfilehash: c02a9f61a4a8b88f8b6c4d861f1a6cbe904ad70d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110536"
---
# <a name="azure-security-center-search"></a>Hledání v Centru zabezpečení Azure
Azure Security Center používá [hledání Log Analytics](../log-analytics/log-analytics-log-searches.md) načíst a analyzovat data zabezpečení. Log Analytics obsahuje dotazovací jazyk pro rychlé načítání a slučování dat. Ze služby Security Center můžete využít hledání Log Analytics vytvářet dotazy a analyzovat shromážděná data.

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

Zobrazit [dotazovací jazyk Log Analytics](../log-analytics/log-analytics-search-reference.md) Další informace o tom, jak dotaz na data ve vybraném pracovním prostoru.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak získat přístup k vyhledávání ve službě Security Center. Security Center používá hledání Log Analytics. Další informace o hledání Log Analytics najdete v tématu:

- [Co je služba Log Analytics?](../log-analytics/log-analytics-overview.md) – Přehled v Log Analytics
- [Principy prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak se používají prohledávání protokolů v Log Analytics a poskytuje koncepty, které by měl být srozumitelný před vytvořením prohledávání protokolu
- [Vyhledání dat pomocí prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md) – kurz k používání prohledávání protokolů
- [Referenční příručce k vyhledávání log Analytics](../log-analytics/log-analytics-search-reference.md) – popisuje dotazovací jazyk v Log Analytics

Další informace o službě Security Center najdete v tématu:

- [Přehled služby Security Center](security-center-intro.md) – Centrum zabezpečení popisuje klíčové funkce

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
