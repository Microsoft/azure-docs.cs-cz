---
title: Přehled Cloudynu v Azure | Microsoft Docs
description: Cloudyn je multicloudové řešení správy nákladů, které pomáhá využívat Azure a další cloudové prostředky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 08a1980a8855fc119a5066e058e9bb4d2df6646e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998871"
---
# <a name="what-is-the-cloudyn-service"></a>Co je služba Cloudyn?

Cloudyn, pobočka Microsoftu, umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Google. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. Cloudyn pomáhá optimalizovat výdaje za cloud identifikováním nevyužitých prostředků, které následně můžete spravovat a upravovat.

Pokud chcete zhlédnout úvodní video, podívejte se na video [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Seznámení s Azure Cloudyn).

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](overview-cost-mgt.md).

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Funkce Cloudyn přesunutí do služby Azure Cost Management

Společnost Microsoft získali Cloudyn a migruje jeho funkce pro správu nákladů na portálu Cloudyn nativně do Azure. Pokud chcete používat nové funkce, přihlásit se k webu Azure portal a přejděte do [Cost Management a fakturace](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) v seznamu služeb Azure. Ve srovnání s Cloudyn, nativního prostředí nabízí lepší výkon a nižší latenci a data o osmi hodin.

Dokončení migrace klíčovou funkcí pro smlouvu Enterprise s průběžnými platbami a MSDN kategorie nabídky do služby Azure Cost Management. Předplatná CSP se právě migruje přes do služby Azure Cost Management.

Pokud máte kategorii nabídka ještě nebyly migrovány, by měla dál používat na portálu Cloudyn. Všichni ostatní můžete použít Azure Cost Management.

| Nabídky Microsoft Azure a funkce | Služba správy doporučené náklady |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (průběžné platby/MSDN.) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Cloudu nákladů podporu analýzy pro AWS (ve verzi preview) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Doporučení AWS | [Cloudyn](https://azure.cloudyn.com) |

Některé z následujících funkcí jsou k dispozici ve službě Cloudyn, ale všechny z nich jsou teď dostupné ve službě Azure Cost Management.

- Rozhraní API
- Doporučení pro výpočetní prostředky Azure
- Doporučení Azure rezervace
- Rozpočty
- Analýza nákladů
- Export dat do účtu služby Azure storage
- Nižší latence
- Balíček obsahu Power BI a konektoru
- Podpora značek prostředků

## <a name="monitor-usage-and-spending"></a>Monitorování využití a výdajů

Monitorování využití a výdajů je v případě cloudových infrastruktur mimořádně důležité, protože organizace platí za prostředky, které v průběhu času spotřebují. Když využití překročí prahové hodnoty uvedené ve smlouvě, rychle se můžou začít účtovat neočekávané poplatky za nadlimitní využití. Několik důležitých faktorů může ztěžovat ad hoc monitorování. Za prvé odhadování nákladů na základě průměrného využití předpokládá konzistentní spotřebu v průběhu daného fakturačního období. Za druhé je důležité, abyste aktivně dostávali upozornění v případě, že se náklady blíží stanovenému rozpočtu nebo ho překročí, abyste mohli upravit své výdaje. Poskytovatelé cloudových služeb navíc nemusejí nabízet sestavy odhadu nákladů oproti prahovým hodnotám nebo porovnání jednotlivých období.

Sestavy pomáhají s monitorováním výdajů za účelem analýzy a sledování využití cloudu, nákladů a trendů. Pomocí sestav v průběhu času můžete detekovat anomálie, které se odchylují od normálních trendů. Nedostatky vašeho cloudového nasazení se zobrazí v sestavách optimalizace. Můžete si také všimnout nedostatků v sestavách analýzy nákladů.

## <a name="manage-costs"></a>Správa nákladů

Historická data můžou pomoct se správou nákladů při analýze využití a nákladů v průběhu času za účelem identifikace trendů. Trendy se následně použijí k předpovědi budoucích výdajů. Cloudyn obsahuje také užitečné sestavy odhadovaných nákladů.

Přidělování nákladů snižuje náklady díky analýze nákladů na základě zásad označování. Přidělování nákladů můžete upřesnit použitím značek na vlastní účty, prostředky a entity. Správce kategorií vaše značky uspořádává a pomáhá tak zajistit další zásady správného řízení. Přidělování nákladů můžete použít také pro metody showback a chargeback. Můžete tak zobrazit využití prostředků a související náklady a ovlivnit chování spotřeby nebo spotřebu účtovat zákazníkům tenanta.

Řízení přístupu pomáhá snižovat náklady tím, že zajišťuje, aby uživatelé a týmy měly přístup pouze k datům správy nákladů, která potřebují. Přístup přiřazujete pomocí struktury entit, správy uživatelů a plánovaných sestav se seznamy příjemců.

Upozorňování pomáhá snižovat náklady tím, že vás automaticky upozorňuje na neobvyklé nebo nadměrné výdaje. Upozornění můžou také automaticky informovat ostatní účastníky na anomálie ve výdajích a rizika nadměrných výdajů. Různé sestavy podporují upozornění na základě rozpočtu a prahových hodnot nákladů. Upozornění se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

## <a name="improve-efficiency"></a>Zlepšení efektivity

Pomocí Cloudynu můžete určit optimální využití virtuálních počítačů a identifikovat nečinné virtuální počítače nebo odebrat nečinné virtuální počítače a nepřipojené disky. Pomocí informací v sestavách optimalizace velikosti a nedostatků můžete vytvořit plán pro zmenšení nebo odebrání nečinných virtuálních počítačů. Sestavy optimalizace se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

Pokud jste zřídili rezervované instance AWS, můžete využití těchto rezervovaných instancí vylepšit pomocí sestav optimalizace, kde můžete zobrazit doporučení k nákupu, upravit nepoužívané rezervace a plánovat zřizování.


## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s Cloudynem, je dalším krokem registrace cloudového prostředí a zahájení prozkoumávání dat.

- [Registrace samostatného předplatného Azure](quick-register-azure-sub.md)
