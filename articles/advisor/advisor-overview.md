---
title: Seznámení s Azure Advisorem
description: K optimalizaci nasazení Azure použijte Azure Advisor.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 12e56bf44a29a32b2149bca14f7c99f319c9c4ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91405203"
---
# <a name="introduction-to-azure-advisor"></a>Seznámení s Azure Advisorem

Přečtěte si o klíčových funkcích Azure Advisor a Získejte odpovědi na nejčastější dotazy.

## <a name="what-is-advisor"></a>Co je Advisor?
Advisor je přizpůsobený cloudový konzultant, který pomáhá dodržovat osvědčené postupy pro optimalizaci nasazení Azure. Analyzuje svou konfiguraci prostředků a telemetrii využití a potom doporučuje řešení, která vám pomůžou vylepšit efektivitu nákladů, výkon, spolehlivost (dříve označované jako vysoké dostupnosti) a zabezpečení vašich prostředků Azure.

Pomocí Poradce můžete:
* Získat proaktivní přizpůsobená doporučení osvědčených postupů, na jejichž základě můžete jednat. 
* Zvyšte výkon, zabezpečení a spolehlivost svých prostředků, jak identifikujete příležitosti pro snížení celkové útraty Azure.
* Získat doporučení s navrhovanými akcemi.

Ke službě Advisor můžete přistupovat prostřednictvím [Azure Portal](https://aka.ms/azureadvisordashboard). Přihlaste se k [portálu](https://portal.azure.com), vyhledejte **Poradce** v navigační nabídce nebo ho vyhledejte v nabídce **všechny služby** .

Řídicí panel poradce zobrazuje individuální doporučení pro všechna Vaše předplatná.  Filtry můžete použít k zobrazení doporučení pro konkrétní předplatná a typy prostředků.  Doporučení se dělí do pěti kategorií: 

* **Spolehlivost (dříve označovaná jako vysoká dostupnost)**: zajištění a zlepšení kontinuity důležitých podnikových aplikací. Další informace najdete v tématu [doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md).
* **Zabezpečení**: detekuje hrozby a ohrožení zabezpečení, které by mohly vést k porušení zabezpečení. Další informace najdete v tématu [doporučení pro zabezpečení služby Advisor](advisor-security-recommendations.md).
* **Výkon**: pro zlepšení rychlosti aplikací. Další informace najdete v tématu [doporučení k výkonu služby Advisor](advisor-performance-recommendations.md).
* **Náklady**: pro optimalizaci a snížení celkové útraty Azure. Další informace najdete v tématu [doporučení pro náklady na poradce](advisor-cost-recommendations.md).
* **Vynikající provozní** prostředí: vám umožní dosáhnout efektivity procesu a pracovního postupu, osvědčených postupů pro správu prostředků a nasazení. . Další informace najdete v tématu [doporučení pro provozní výkon služby Advisor](advisor-operational-excellence-recommendations.md).

  ![Typy doporučení Advisoru](./media/advisor-overview/advisor-dashboard.png)

Kliknutím na kategorii můžete zobrazit seznam doporučení v rámci této kategorie a vybrat doporučení pro další informace o této kategorii.  Můžete si také přečíst informace o akcích, které můžete provést, abyste mohli využít příležitost nebo vyřešit problém.

![Kategorie doporučení Poradce pro](./media/advisor-overview/advisor-ha-category-example.png) 

Vyberte doporučenou akci pro doporučení k implementaci doporučení.  Otevře se jednoduché rozhraní, které vám umožní implementovat doporučení, nebo se můžete podívat na dokumentaci, která vám pomůže s implementací.  Po implementaci doporučení může poradce trvat až jeden den, než to rozpozná.

Pokud nechcete, aby se doporučení projevila okamžitě, můžete ho po zadaném časovém období odložit nebo zavřít.  Pokud nechcete dostávat doporučení pro konkrétní předplatné nebo skupinu prostředků, můžete službu Advisor nakonfigurovat tak, aby vygenerovala jenom doporučení pro zadaná předplatná a skupiny prostředků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-access-advisor"></a>Návody přístup Advisor?
Ke službě Advisor můžete přistupovat prostřednictvím [Azure Portal](https://aka.ms/azureadvisordashboard). Přihlaste se k [portálu](https://portal.azure.com), vyhledejte **Poradce** v navigační nabídce nebo ho vyhledejte v nabídce **všechny služby** .

Doporučení Poradce můžete zobrazit také prostřednictvím rozhraní prostředku virtuálního počítače. Zvolte virtuální počítač a pak přejděte na doporučení poradce v nabídce. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jaká oprávnění potřebuji pro přístup k Advisoru?
 
K doporučením Advisoru se dostanete jako *vlastník*, *Přispěvatel* nebo *Čtenář* předplatného, skupiny prostředků nebo prostředku.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>K jakým prostředkům poradce poskytuje doporučení?

Advisor poskytuje doporučení pro Application Gateway, App Services, skupiny dostupnosti, Azure cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, veřejné IP adresy Azure, Azure synapse Analytics, SQL servery, účty úložiště, profily Traffic Manager a virtuální počítače.

Azure Advisor také obsahuje doporučení z [Azure Security Center](../security-center/security-center-recommendations.md) , která mohou obsahovat doporučení pro další typy prostředků.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Můžu odložit nebo zrušit doporučení?

Chcete-li odložit nebo zrušit doporučení, klikněte na odkaz **odložit** . Můžete zadat období odložení nebo vybrat možnost **nikdy** nezrušit doporučení.

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:

* [Začínáme se službou Advisor](advisor-get-started.md)
* [Skóre Advisoru](azure-advisor-score.md)
* [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
