---
title: Úvod do Azure Advisoru
description: Pomocí Azure Advisoru optimalizujte nasazení Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443097"
---
# <a name="introduction-to-azure-advisor"></a>Úvod do Azure Advisoru

Seznamte se s klíčovými funkcemi Azure Advisoru a získejte odpovědi na nejčastější dotazy.

## <a name="what-is-advisor"></a>Co je poradce?
Advisor je personalizovaný cloudový konzultant, který vám pomůže dodržovat osvědčené postupy při optimalizaci nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

S poradcem můžete:
* Získat proaktivní přizpůsobená doporučení osvědčených postupů, na jejichž základě můžete jednat. 
* Zlepšete výkon, zabezpečení a vysokou dostupnost svých prostředků, protože identifikujete příležitosti ke snížení celkových výdajů na Azure.
* Získat doporučení s navrhovanými akcemi.

K Advisoru se dostanete prostřednictvím [portálu Azure](https://aka.ms/azureadvisordashboard). Přihlaste se k [portálu](https://portal.azure.com), vyhledejte **poradce** v navigační nabídce nebo ho vyhledejte v nabídce **Všechny služby.**

Řídicí panel Advisor zobrazuje přizpůsobená doporučení pro všechna vaše předplatná.  Filtry můžete použít k zobrazení doporučení pro konkrétní odběry a typy prostředků.  Doporučení se dělí do čtyř kategorií: 

* **Vysoká dostupnost**: Zajištění a zlepšení kontinuity vašich důležitých podnikových aplikací. Další informace naleznete v [tématu Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md).
* **Zabezpečení**: Zjišťování hrozeb a slabých míst, která by mohla vést k narušení zabezpečení. Další informace naleznete v [tématu Doporučení pro zabezpečení poradce](advisor-security-recommendations.md).
* **Výkon**: Chcete-li zvýšit rychlost aplikací. Další informace naleznete v tématu [Doporučení pro výkon poradce](advisor-performance-recommendations.md).
* **Cena**: Optimalizace a snížení celkových výdajů na Azure. Další informace naleznete v [tématu Doporučení nákladů poradce](advisor-cost-recommendations.md).
* **Provozní dokonalost**: Abychom vám pomohli dosáhnout efektivity procesů a pracovních postupů, možností správy zdrojů a osvědčených postupů nasazení. . Další informace naleznete [v doporučeních poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md).

  ![Typy doporučení poradce](./media/advisor-overview/advisor-dashboard.png)

Kliknutím na kategorii můžete zobrazit seznam doporučení v rámci této kategorie a vybrat doporučení, abyste se o ní dozvěděli více.  Můžete se také seznámit s akcemi, které můžete provést, abyste využili příležitosti nebo vyřešili problém.

![Kategorie doporučení poradce](./media/advisor-overview/advisor-ha-category-example.png) 

Vyberte doporučenou akci pro doporučení k implementaci doporučení.  Otevře se jednoduché rozhraní, které umožňuje implementovat doporučení nebo odkazovat na dokumentaci, která vám pomůže s implementací.  Jakmile implementujete doporučení, může to trvat až jeden den, než to Poradce rozpozná.

Pokud nemáte v úmyslu přijmout okamžitá opatření na doporučení, můžete jej odložit o určitou dobu nebo jej zrušit.  Pokud nechcete dostávat doporučení pro konkrétní předplatné nebo skupinu prostředků, můžete nakonfigurovat Poradce generovat pouze doporučení pro zadané odběry a skupiny prostředků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-access-advisor"></a>Jak se dostanu k Poradci?
K Advisoru se dostanete prostřednictvím [portálu Azure](https://aka.ms/azureadvisordashboard). Přihlaste se k [portálu](https://portal.azure.com), vyhledejte **poradce** v navigační nabídce nebo ho vyhledejte v nabídce **Všechny služby.**

Doporučení poradce můžete také zobrazit prostřednictvím rozhraní prostředků virtuálního počítače. Vyberte virtuální počítač a pak přejděte na doporučení poradce v nabídce. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jaká oprávnění potřebuji pro přístup k poradci?
 
K doporučením poradce můžete přistupovat jako *vlastník*, *přispěvatel*nebo *čtenář* předplatného.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Pro jaké zdroje poskytuje poradce doporučení?

Advisor poskytuje doporučení pro aplikační bránu, služby App Services, skupiny dostupnosti, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure public IP adresy, SQL Data Warehouse, SQL servery, účty úložiště, profily Traffic Manageru a virtuální počítače.

Azure Advisor také obsahuje vaše doporučení z [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-recommendations) které mohou obsahovat doporučení pro další typy prostředků.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Mohu doporučení odložit nebo zamítnout?

Chcete-li doporučení odložit nebo zamítnout, klepněte na odkaz **Odložit.** Můžete určit období odložení nebo vybrat **možnost Nikdy** odmítnout doporučení.

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců najdete v tématu:

* [Začínáme s poradcem](advisor-get-started.md)
* [Doporučení poradce pro vysokou dostupnost](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
