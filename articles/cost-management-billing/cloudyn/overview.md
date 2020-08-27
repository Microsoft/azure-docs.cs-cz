---
title: Přehled Cloudynu v Azure
description: Cloudyn je multicloudové řešení správy nákladů, které pomáhá využívat Azure a další cloudové prostředky.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 3acc13ca535808f14cb01d50e38f6bd4d12902fc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684435"
---
# <a name="what-is-the-cloudyn-service"></a>Co je služba Cloudyn?

Cloudyn, pobočka Microsoftu, umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Google. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. Cloudyn pomáhá optimalizovat výdaje za cloud identifikováním nevyužitých prostředků, které následně můžete spravovat a upravovat.

Pokud chcete zhlédnout úvodní video, podívejte se na video [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/) (Seznámení s Azure Cloudyn).
 
Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Podívejte se na [video o službách Azure Cost Management a Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM), ve kterém najdete doporučení, kdy byste v závislosti na potřebách vaší firmy měli používat Azure Cost Management nebo Cloudyn.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>Monitorování využití a výdajů

Monitorování využití a výdajů je v případě cloudových infrastruktur mimořádně důležité, protože organizace platí za prostředky, které v průběhu času spotřebují. Když využití překročí prahové hodnoty uvedené ve smlouvě, rychle se můžou začít účtovat neočekávané poplatky za nadlimitní využití. Ad hoc monitorování může ztěžovat několik důležitých faktorů. Za prvé odhadování nákladů na základě průměrného využití předpokládá konzistentní spotřebu v průběhu daného fakturačního období. Za druhé je důležité, abyste aktivně dostávali upozornění v případě, že se náklady blíží stanovenému rozpočtu nebo ho překročí, abyste mohli upravit své výdaje. Poskytovatelé cloudových služeb navíc nemusejí nabízet sestavy odhadu nákladů oproti prahovým hodnotám nebo porovnání jednotlivých období.

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

- [Registrace do partnerského programu CSP a zobrazení informací o nákladech](quick-register-csp.md)
