---
title: Hledání Azure Security Center | Microsoft Docs
description: Zjistěte, jak Azure Security Center využívá analýzy protokolů hledání načíst a analyzovat data zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866398"
---
# <a name="azure-security-center-search"></a>Azure Security Center vyhledávání
Azure Security Center používá [analýzy protokolů hledání](../log-analytics/log-analytics-log-searches.md) načíst a analyzovat data zabezpečení. Log Analytics obsahuje dotazovací jazyk rychle načíst a konsolidovat data. Ze služby Security Center můžete využít analýzy protokolů hledání vytvořit dotazy a analyzovat shromážděná data.

Hledání je k dispozici v úrovni Free a úroveň Standard služby Security Center.  Data k dispozici v protokolu hledání je závislá na úrovni vrstvy použít do pracovního prostoru.  Najdete v Centru zabezpečení [stránce s cenami](../security-center/security-center-pricing.md) Další informace.


> [!NOTE]
> Security Center neukládá data zabezpečení pro pracovní prostor pod úroveň Free. Různé protokoly můžete odeslat do pracovního prostoru pod úroveň Free a vyhledávání na data, ale výsledky hledání tak nebudou obsahovat data ze služby Security Center. Data Security Center jenom uloží do pracovního prostoru v části na plán úrovně Standard.
>
>

## <a name="access-search"></a>Přístup k vyhledávání
1. V části v hlavní nabídce Security Center, vyberte **vyhledávání**.

  ![Vyberte protokol hledání][1]

2. Security Center uvádí všechny pracovní prostory v rámci vašich předplatných Azure. Vyberte pracovní prostor. (Pokud máte jenom jeden pracovní prostor, tento pracovní prostor selektor nezobrazí.)

  ![Vyberte pracovní prostor][2]

3. **Přihlaste se vyhledávání** otevře. Dotaz pro další data v rámci vybraný pracovní prostor, zadejte tento příklad dotazu:

  SecurityEvent | kde EventID == 4625 | shrnout count() podle TargetAccount

  Výsledek zobrazuje všechny účty, které se nepodařilo přihlášení (událost 4625).

  ![Výsledky hledání][3]

V tématu [analýzy protokolů dotazu jazyka](../log-analytics/log-analytics-search-reference.md) Další informace o tom, jak dotaz na data v rámci vybraný pracovní prostor.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak pro přístup k vyhledávání ve službě Security Center. Security Center používá analýzy protokolů hledání. Další informace o hledání analýzy protokolů najdete v tématu:

- [Co je služba Log Analytics?](../log-analytics/log-analytics-overview.md) – Přehled na analýzy protokolů
- [Principy protokolu vyhledá v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak se používají protokol hledání v analýzy protokolů a poskytuje koncepty, které by se měl chápat před vytvořením vyhledávání protokolu
- [Najít data pomocí protokolu hledání v analýzy protokolů](../log-analytics/log-analytics-log-searches.md) – kurz na používání protokolu vyhledávání
- [Analýzy protokolů hledání odkaz](../log-analytics/log-analytics-search-reference.md) – popisuje dotazovací jazyk v analýzy protokolů

Další informace o službě Security Center najdete v tématu:

- [Přehled Security Center](security-center-intro.md) – klíčové funkce popisuje Security Center

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
