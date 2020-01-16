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
ms.openlocfilehash: d4a31518dcd6fb5722fc19617b153bafb8a20f68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987653"
---
# <a name="what-is-the-cloudyn-service"></a>Co je služba Cloudyn?

Cloudyn, pobočka Microsoftu, umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Google. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. Cloudyn pomáhá optimalizovat výdaje za cloud identifikováním nevyužitých prostředků, které následně můžete spravovat a upravovat.

Pokud chcete zhlédnout úvodní video, podívejte se na video [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Seznámení s Azure Cloudyn).

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](../cost-management-billing-overview.md).

Podívejte se na [video Azure cost management a Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) , kde můžete zobrazit doporučení v závislosti na vašich obchodních potřebách, pokud používáte buď Azure cost management nebo Cloudyn.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn funkce přesunu na Azure Cost Management

Společnost Microsoft získala Cloudyn a migruje své funkce pro správu nákladů z portálu Cloudyn nativně do Azure. Pokud chcete používat nové funkce, přihlaste se k Azure Portal a v seznamu služeb Azure přejděte na [cost management a fakturace](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) . V porovnání s Cloudyn nabízí nativní prostředí Vylepšený výkon a nižší latenci dat asi osm hodin.

Migrace klíčových funkcí pro smlouva Enterprise, průběžné platby a kategorie nabídek MSDN pro Azure Cost Management je dokončená. Předplatné CSP se v procesu migrují přes Azure Cost Management.

Pokud máte kategorii nabídek ještě nemigrováni, měli byste nadále používat portál Cloudyn. Azure Cost Management může použít kdokoli jiný.

| Microsoft Azure nabídky a funkce | Doporučená služba cost management |
| --- | --- |
| Smlouva Enterprise Azure | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Web Direct pro Azure (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Podpora analýzy nákladů mezi cloudy pro AWS (ve verzi Preview) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Doporučení pro AWS | [Cloudyn](https://azure.cloudyn.com) |

Některé z následujících funkcí jsou k dispozici v Cloudyn, ale všechny jsou nyní k dispozici v Azure Cost Management.

- Rozhraní API
- Doporučení k výpočtům Azure
- Doporučení pro rezervaci Azure
- Rozpočty
- Analýza nákladů
- Export dat do účtu služby Azure Storage
- Nižší latence
- Power BI balíček obsahu a konektor
- Podpora značek prostředků

## <a name="monitor-usage-and-spending"></a>Monitorování využití a výdajů

Monitorování využití a výdajů je v případě cloudových infrastruktur mimořádně důležité, protože organizace platí za prostředky, které v průběhu času spotřebují. Když využití překročí prahové hodnoty uvedené ve smlouvě, rychle se můžou začít účtovat neočekávané poplatky za nadlimitní využití. Několik důležitých faktorů může ztížit monitorování ad hoc. Za prvé odhadování nákladů na základě průměrného využití předpokládá konzistentní spotřebu v průběhu daného fakturačního období. Za druhé je důležité, abyste aktivně dostávali upozornění v případě, že se náklady blíží stanovenému rozpočtu nebo ho překročí, abyste mohli upravit své výdaje. Poskytovatelé cloudových služeb navíc nemusejí nabízet sestavy odhadu nákladů oproti prahovým hodnotám nebo porovnání jednotlivých období.

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
