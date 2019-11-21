---
title: Přehled Cloudynu v Azure | Microsoft Docs
description: Cloudyn je multicloudové řešení správy nákladů, které pomáhá využívat Azure a další cloudové prostředky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 70951bfdafb9e54a63c2645d4993233fc4b8446c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230014"
---
# <a name="what-is-the-cloudyn-service"></a>What is the Cloudyn service?

Cloudyn, pobočka Microsoftu, umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Google. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. Cloudyn pomáhá optimalizovat výdaje za cloud identifikováním nevyužitých prostředků, které následně můžete spravovat a upravovat.

Pokud chcete zhlédnout úvodní video, podívejte se na video [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Seznámení s Azure Cloudyn).

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](overview-cost-mgt.md).

Watch the [Azure Cost Management and Cloudyn video](https://www.youtube.com/watch?v=PmwFWwSluh8) to see recommendations when you should use either Azure Cost Management or Cloudyn, based on your business needs.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn features moving to Azure Cost Management

Microsoft acquired Cloudyn and is migrating its cost management features from the Cloudyn portal natively into Azure. To use the new features, sign-in to the Azure portal and navigate to [Cost Management and Billing](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) in the list of Azure services. Compared to Cloudyn, the native experience offers improved performance and lower data latency of about eight hours.

Key feature migration for Enterprise Agreement, Pay-As-You-Go, and MSDN offer categories to Azure Cost Management is complete. CSP subscriptions are in the process of being migrated over to Azure Cost Management.

If you have an offer category not yet migrated, you should continue to use the Cloudyn portal. Everyone else can use Azure Cost Management.

| Microsoft Azure offers and features | Recommended cost management service |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Cross-cloud cost analysis support for AWS (in preview) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS recommendations | [Cloudyn](https://azure.cloudyn.com) |

Some of the following features are available in Cloudyn, but all of them are available now in Azure Cost Management.

- Rozhraní API
- Azure compute recommendations
- Azure Reservation recommendations
- Rozpočty
- Analýza nákladů
- Export data to an Azure storage account
- Nižší latence
- Power BI content pack and connector
- Resource tag support

## <a name="monitor-usage-and-spending"></a>Monitorování využití a výdajů

Monitorování využití a výdajů je v případě cloudových infrastruktur mimořádně důležité, protože organizace platí za prostředky, které v průběhu času spotřebují. Když využití překročí prahové hodnoty uvedené ve smlouvě, rychle se můžou začít účtovat neočekávané poplatky za nadlimitní využití. A few important factors can make ad hoc monitoring difficult. Za prvé odhadování nákladů na základě průměrného využití předpokládá konzistentní spotřebu v průběhu daného fakturačního období. Za druhé je důležité, abyste aktivně dostávali upozornění v případě, že se náklady blíží stanovenému rozpočtu nebo ho překročí, abyste mohli upravit své výdaje. Poskytovatelé cloudových služeb navíc nemusejí nabízet sestavy odhadu nákladů oproti prahovým hodnotám nebo porovnání jednotlivých období.

Sestavy pomáhají s monitorováním výdajů za účelem analýzy a sledování využití cloudu, nákladů a trendů. Pomocí sestav v průběhu času můžete detekovat anomálie, které se odchylují od normálních trendů. Nedostatky vašeho cloudového nasazení se zobrazí v sestavách optimalizace. Můžete si také všimnout nedostatků v sestavách analýzy nákladů.

## <a name="manage-costs"></a>Správa nákladů

Historická data můžou pomoct se správou nákladů při analýze využití a nákladů v průběhu času za účelem identifikace trendů. Trendy se následně použijí k předpovědi budoucích výdajů. Cloudyn obsahuje také užitečné sestavy odhadovaných nákladů.

Přidělování nákladů snižuje náklady díky analýze nákladů na základě zásad označování. Přidělování nákladů můžete upřesnit použitím značek na vlastní účty, prostředky a entity. Správce kategorií vaše značky uspořádává a pomáhá tak zajistit další zásady správného řízení. Přidělování nákladů můžete použít také pro metody showback a chargeback. Můžete tak zobrazit využití prostředků a související náklady a ovlivnit chování spotřeby nebo spotřebu účtovat zákazníkům tenanta.

Řízení přístupu pomáhá snižovat náklady tím, že zajišťuje, aby uživatelé a týmy měly přístup pouze k datům správy nákladů, která potřebují. Přístup přiřazujete pomocí struktury entit, správy uživatelů a plánovaných sestav se seznamy příjemců.

Upozorňování pomáhá snižovat náklady tím, že vás automaticky upozorňuje na neobvyklé nebo nadměrné výdaje. Upozornění můžou také automaticky informovat ostatní účastníky na anomálie ve výdajích a rizika nadměrných výdajů. Různé sestavy podporují upozornění na základě rozpočtu a prahových hodnot nákladů. Upozornění se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

## <a name="improve-efficiency"></a>Zlepšení efektivity

Pomocí Cloudynu můžete určit optimální využití virtuálních počítačů a identifikovat nečinné virtuální počítače nebo odebrat nečinné virtuální počítače a nepřipojené disky. Pomocí informací v sestavách optimalizace velikosti a nedostatků můžete vytvořit plán pro zmenšení nebo odebrání nečinných virtuálních počítačů. Sestavy optimalizace se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

Pokud jste zřídili rezervované instance AWS, můžete využití těchto rezervovaných instancí vylepšit pomocí sestav optimalizace, kde můžete zobrazit doporučení k nákupu, upravit nepoužívané rezervace a plánovat zřizování.


## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s Cloudynem, je dalším krokem registrace cloudového prostředí a zahájení prozkoumávání dat.

- [Registrace samostatného předplatného Azure](quick-register-azure-sub.md)
