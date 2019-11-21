---
title: Přehled služby Azure Cost Management | Microsoft Docs
description: Azure Cost Management je řešení pro správu nákladů, které pomáhá monitorovat a kontrolovat útratu za Azure a optimalizovat využití prostředků.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management-billing
manager: benshy
ms.custom: ''
ms.openlocfilehash: 90d2646aa554a20a823d29cde06537d05415b603
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230026"
---
# <a name="what-is-azure-cost-management"></a>Co je Azure Cost Management?

Správa nákladů je proces, při kterém efektivně plánujete a řídíte náklady své firmy. Úlohy správy nákladů obvykle provádějí finanční týmy, týmy správy účtů a týmy aplikací. Azure Cost Management helps organizations plan with cost in mind. It also helps to analyze costs effectively and take action to optimize cloud spending. Další informace o tom, jak organizace mohou využít správu nákladů, najdete v článku, který se věnuje [osvědčeným postupům pro Azure Cost Management](cost-mgt-best-practices.md).

Watch the [Azure Cost Management overview video](https://www.youtube.com/watch?v=el4yN5cHsJ0) for a quick overview about how Azure Cost Management can help you save money in Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Přestože spolu souvisejí, fakturace se od správy nákladů liší. Fakturace je proces, při kterém vystavujete faktury za zboží a služby zákazníkům a spravujete komerční vztahy.  O fakturační úlohy se obvykle starají zásobovací a finanční týmy.

Pomocí pokročilých analýz služba Cost Management zobrazuje schémata nákladů a využití na úrovni organizace. Reports in Cost Management show the usage-based costs consumed by Azure services and third-party Marketplace offerings. Costs are based on negotiated prices and factor in reservation and Azure Hybrid Benefit discounts. Společně tyto sestavy zobrazují interní a externí náklady na využití a poplatky za Azure Marketplace. Ostatní poplatky, například za nákupy rezervací, podporu a daně, se zatím v sestavách nezobrazují. Tyto sestavy vám pomohou vyznat se v útratách a využití prostředků a zjistit neobvyklé výdaje. K dispozici máte také prediktivní analýzu. Služba Cost Management využívá skupiny pro správu, rozpočty a doporučení Azure, aby přehledně zobrazila, jak máte uspořádány výdaje a jak byste mohli snížit náklady.

K automatizaci exportu můžete využít web Azure Portal nebo různá rozhraní API, abyste mohli integrovat data nákladů s externími systémy a procesy. K dispozici máte také automatizovaný export dat fakturace a naplánované sestavy.

## <a name="plan-and-control-expenses"></a>Plánování a řízení nákladů

Způsoby, kterými služba Cost Management usnadňuje plánování a řízení nákladů, zahrnují analýzu nákladů, rozpočty, doporučení a export dat správy nákladů.

Prostřednictvím analýzy nákladů můžete prozkoumat a analyzovat výdaje organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit souhrnné náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu.

Rozpočty pomáhají plánovat a plnit finanční odpovědnost v organizaci. Pomáhají vám, abyste nepřekročili mezní hodnoty nebo limity nákladů. Prostřednictvím rozpočtů také můžete informovat ostatní o jejich útratách a proaktivně tak spravovat náklady. Díky nim se také můžete podívat na to, jak se útraty během času vyvíjejí.

Doporučení znázorňují, jak můžete optimalizovat a zvýšit efektivitu pomocí identifikace nečinných a nedostatečně využitých prostředků. Mohou také zobrazit levnější možnosti prostředků. Pokud se budete řídit doporučeními, můžete změnit způsob, jakým využíváte prostředky, a ušetřit tak peníze. Pokud chcete postupovat podle doporučení, nejprve si zobrazte doporučení k optimalizaci nákladů a podívejte se na možné neefektivní využití prostředků. Dále změňte využití prostředku Azure na nákladově efektivnější možnost. Potom ověřte akci, abyste měli jistotu, že provedená změna je úspěšná.

Pokud pro přístup k datům správy nákladů nebo jejich kontrole používáte externí systémy, můžete z Azure data snadno exportovat. Můžete také naplánovat denní export ve formátu CSV a soubory dat ukládat v úložišti Azure. Potom budete mít přístup k datům v externím systému.

## <a name="consider-cloudyn"></a>Zvážení využití služby Cloudyn

[Cloudyn](overview.md) je služba Azure, která souvisí se službou Cost Management. Pomocí služby Cloudyn můžete sledovat využití cloudu a výdaje na prostředky Azure. Podporuje také další poskytovatele cloudu, včetně AWS a Googlu. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. V současné době služba Cost Management nepodporuje metody showback a chargeback ani jiné poskytovatele cloudových služeb. Cloudyn je však možnost, která je _podporuje_. Currently, Cost Management doesn't support Microsoft Cloud Service Provider (CSP) accounts but Cloudyn does. If you have CSP accounts or if you want to use showback/chargeback, you can use Cloudyn to help manage your costs.

Watch the [Azure Cost Management and Cloudyn video](https://www.youtube.com/watch?v=PmwFWwSluh8) to see recommendations when you should use either Azure Cost Management or Cloudyn, based on your business needs.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="additional-azure-tools"></a>Další nástroje Azure

Azure má další nástroje, které nejsou součástí sady funkcí Azure Cost Management. However, they play an important role in the cost management process. Další informace o těchto nástrojích si můžete přečíst po kliknutí na následující odkazy.

- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) – tento nástroj slouží k odhadování počátečních nákladů na cloud.
- [Azure Migrate](../migrate/migrate-overview.md) – umožňuje zhodnotit aktuální úlohy datacentra a zjistit, co je třeba využít z náhradního řešení Azure.
- [Azure Advisor](../advisor/advisor-overview.md) – umožňuje identifikovat nevyužité virtuální počítače a získat doporučení k nákupům rezervovaných instancí Azure.
- [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) – umožňuje využít aktuální místní licence k Windows Serveru nebo SQL Serveru pro virtuální počítače v Azure a ušetřit tak peníze.


## <a name="next-steps"></a>Další kroky

Nyní už víte, co služba Cost Management umí, a můžete ji začít využívat.

- Začněte s použitím služby Cost Management k [analýze nákladů](quick-acm-cost-analysis.md).
- Můžete si také přečíst další informace o [osvědčených postupech pro Azure Cost Management](cost-mgt-best-practices.md).
