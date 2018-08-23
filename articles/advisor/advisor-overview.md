---
title: Úvod do Azure Advisor | Dokumentace Microsoftu
description: Použití Azure Advisoru a optimalizovat nasazení Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: e1d7edef304dc91829066f19b6974f0bcf0be0a5
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055635"
---
# <a name="introduction-to-azure-advisor"></a>Úvod do Azure Advisoru

Seznamte se s klíčovými funkcemi Azure Advisoru a získejte odpovědi na nejčastější dotazy.

## <a name="what-is-advisor"></a>Co je Advisor?
Advisor je individuální cloudový konzultant, který pomáhá dodržovat osvědčené postupy a optimalizovat nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

Advisor vám umožní:
* Získejte přehledy, aktivní a individuálních doporučení osvědčených postupů. 
* Zlepšení výkonu, zabezpečení a vysokou dostupnost vašich prostředků, jak identifikovat příležitosti ke snížení celkové Azure výdajů.
* Získejte doporučení pomocí vložených navrhované akce.

Můžete přistupovat prostřednictvím Advisor [webu Azure portal](https://aka.ms/azureadvisordashboard). Přihlaste se k [portál](https://portal.azure.com), vyhledejte **Advisor** v navigační nabídce, nebo vyhledejte ho v **všechny služby** nabídky.

Řídicí panel služby Advisor zobrazí individuální doporučení pro vaše předplatná.  Můžete použít filtry pro zobrazení doporučení pro konkrétní předplatné a typy prostředků.  Doporučení jsou rozděleny do čtyř kategorií: 

* **Vysoká dostupnost**: K zajištění a zlepšování kontinuity důležité podnikové aplikace. Další informace najdete v tématu [doporučení k vysoké dostupnosti služby Advisor](advisor-high-availability-recommendations.md).
* **Zabezpečení**: ke zjištění hrozeb a ohrožení zabezpečení, které by mohly vést k narušení zabezpečení. Další informace najdete v tématu [doporučení Advisoru zabezpečení](advisor-security-recommendations.md).
* **Výkon**: aby se zvýšila rychlost vašich aplikací. Další informace najdete v tématu [doporučení k výkonu služby Advisor](advisor-performance-recommendations.md).
* **Náklady na**: K optimalizaci a snížit vaše celkové útraty Azure. Další informace najdete v tématu [doporučení Advisoru náklady](advisor-cost-recommendations.md).

  ![Typy doporučení advisoru](./media/advisor-overview/advisor-dashboard.png)

Můžete kliknutím na kategorii zobrazíte seznam doporučení v rámci této kategorie a vybrat na doporučení získáte další informace.  Můžete také informace o akcích, které můžete provádět, abyste mohli využít příležitosti nebo řešení problému.

![Kategorie doporučení advisoru](./media/advisor-overview/advisor-ha-category-example.png) 

Vyberte doporučenou akci pro doporučení pro implementaci doporučení.  Jednoduché rozhraní se otevře, která umožňuje implementovat doporučení nebo s odkazem na dokumentaci, která vám pomůže s implementací.  Jakmile implementujete doporučení, to může trvat až jeden den, Poradce pro rozpoznávání, který.

Pokud je nemáte v úmyslu provést okamžitou akci na základě doporučení, můžete odložit za zadané časové období nebo zavřít.  Pokud nechcete dostávat doporučení pro konkrétní předplatné nebo skupinu prostředků, můžete nakonfigurovat Advisor pouze generování doporučení pro zadaná předplatná a skupiny prostředků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-access-advisor"></a>Jak získám přístup do služby Advisor?
Můžete přistupovat prostřednictvím Advisor [webu Azure portal](https://aka.ms/azureadvisordashboard). Přihlaste se k [portál](https://portal.azure.com), vyhledejte **Advisor** v navigační nabídce, nebo vyhledejte ho v **všechny služby** nabídky.

Můžete také zobrazit doporučení Advisoru prostřednictvím rozhraní prostředků virtuálního počítače. Vyberte virtuální počítač a poté přejděte k doporučení Advisoru v nabídce. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jaká oprávnění potřebuji pro přístup k Advisoru?
 
Můžete přistupovat doporučení Advisoru jako *vlastníka*, *Přispěvatel*, nebo *čtečky* předplatného.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Jaké prostředky služby Advisor poskytuje doporučení pro?

Advisor poskytuje doporučení pro virtuální počítače, skupiny dostupnosti, služby application Gateway, App Services, SQL servery a Redis Cache.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Můžete posunout nebo zavřít doporučení?

Posunout nebo zavřít doporučení, klikněte **odložit** odkaz. Můžete zadat odložit období nebo vyberte **nikdy** zavřete doporučení.

## <a name="next-steps"></a>Další postup

Další informace o doporučení Advisoru, naleznete v tématu:

* [Začínáme se službou Advisor](advisor-get-started.md)
* [Poradce doporučení k vysoké dostupnosti](advisor-high-availability-recommendations.md)
* [Bezpečnostní doporučení advisoru](advisor-security-recommendations.md)
* [Poradce doporučení k výkonu](advisor-performance-recommendations.md)
* [Náklady na doporučení advisoru](advisor-cost-recommendations.md)
