---
title: Přehled služby Azure Cost Management | Microsoft Docs
description: Azure Cost Management je multicloudové řešení správy nákladů, které pomáhá využívat Azure a další cloudové prostředky.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e2f12a989eb275e645c8b3bde3494928c5cbdb47
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="what-is-azure-cost-management"></a>Co je Azure Cost Management?

Služba Azure Cost Management, kterou licencuje pobočka Microsoftu Cloudyn, umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Google. Snadno pochopitelné sestavy řídicího panelu pomáhají s přidělováním nákladů i metodami showback a chargeback. Cost Management pomáhá optimalizovat výdaje za cloud díky identifikaci nevyužitých prostředků, které následně můžete spravovat a upravovat.

Pokud chcete zhlédnout úvodní video, podívejte se na video [Introduction to Azure Cost Management](https://youtu.be/NWIRny6Wpsk) (Seznámení se službou Azure Cost Management).

## <a name="monitor-usage-and-spending"></a>Monitorování využití a výdajů

Monitorování využití a výdajů je v případě cloudových infrastruktur mimořádně důležité, protože organizace platí za prostředky, které v průběhu času spotřebují. Když využití překročí prahové hodnoty uvedené ve smlouvě, rychle se můžou začít účtovat neočekávané poplatky za nadlimitní využití. Ad hoc monitorování může ztěžovat několik důležitých faktorů. Za prvé odhadování nákladů na základě průměrného využití předpokládá konzistentní spotřebu v průběhu daného fakturačního období. Za druhé je důležité, abyste aktivně dostávali upozornění v případě, že se náklady blíží stanovenému rozpočtu nebo ho překročí, abyste mohli upravit své výdaje. Poskytovatelé cloudových služeb navíc nemusejí nabízet sestavy odhadu nákladů oproti prahovým hodnotám nebo porovnání jednotlivých období.

Sestavy pomáhají s monitorováním výdajů za účelem analýzy a sledování využití cloudu, nákladů a trendů. Pomocí sestav v průběhu času můžete detekovat anomálie, které se odchylují od normálních trendů. Nedostatky vašeho cloudového nasazení se zobrazí v sestavách optimalizace. Můžete si také všimnout nedostatků v sestavách analýzy nákladů.

![Sestava nákladů v průběhu času](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Správa nákladů

Historická data můžou pomoct se správou nákladů při analýze využití a nákladů v průběhu času za účelem identifikace trendů. Trendy se následně použijí k předpovědi budoucích výdajů. Cost Management obsahuje také užitečné sestavy odhadovaných nákladů.

Přidělování nákladů snižuje náklady díky analýze nákladů na základě zásad označování. Přidělování nákladů můžete upřesnit použitím značek na vlastní účty, prostředky a entity. Správce kategorií vaše značky uspořádává a pomáhá tak zajistit další zásady správného řízení. Přidělování nákladů můžete použít také pro metody showback a chargeback. Můžete tak zobrazit využití prostředků a související náklady a ovlivnit chování spotřeby nebo spotřebu účtovat zákazníkům tenanta.

Řízení přístupu pomáhá snižovat náklady tím, že zajišťuje, aby uživatelé a týmy měly přístup pouze k datům správy nákladů, která potřebují. Přístup přiřazujete pomocí struktury entit, správy uživatelů a plánovaných sestav se seznamy příjemců.

Upozorňování pomáhá snižovat náklady tím, že vás automaticky upozorňuje na neobvyklé nebo nadměrné výdaje. Upozornění můžou také automaticky informovat ostatní účastníky na anomálie ve výdajích a rizika nadměrných výdajů. Různé sestavy podporují upozornění na základě rozpočtu a prahových hodnot nákladů. Upozornění se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

## <a name="improve-efficiency"></a>Zlepšení efektivity

Pomocí služby Cost Management můžete určit optimální využití virtuálních počítačů a identifikovat nečinné virtuální počítače nebo odebrat nečinné virtuální počítače a nepřipojené disky. Pomocí informací v sestavách optimalizace velikosti a nedostatků můžete vytvořit plán pro zmenšení nebo odebrání nečinných virtuálních počítačů. Sestavy optimalizace se však aktuálně nepodporují pro partnerské účty ani předplatná poskytovatele CSP.

![Doporučení pro určování velikostí](.\media\overview\sizing.png)

Pokud jste zřídili rezervované instance AWS, můžete využití těchto rezervovaných instancí vylepšit pomocí sestav optimalizace, kde můžete zobrazit doporučení k nákupu, upravit nepoužívané rezervace a plánovat zřizování.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se službou Cost Management, je dalším krokem registrace cloudového prostředí a zahájení prozkoumávání dat.

- [Registrace samostatného předplatného Azure](quick-register-azure-sub.md)
