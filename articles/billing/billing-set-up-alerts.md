---
title: Nastavení výstrah fakturace nebo platební pro předplatná Azure | Microsoft Docs
description: Popisuje, jak můžete na můžete nastavit výstrahy faktury Azure, se můžete vyhnout fakturace výskyt nečekaných událostí.
keywords: platební výstraze, fakturace výstrahu
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: 981cb1153e0268e6572207f8d2401edb23485863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607799"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Nastavení výstrah fakturace nebo platební pro vaše předplatné Microsoft Azure
Pokud jste správce účtů pro předplatné Azure, můžete použít Azure fakturace služby oznámení. Chcete-li vytvořit vlastní fakturace výstrahy, které vám pomůžou monitorovat a spravovat fakturace aktivity pro vaše účty Azure.

Tato služba je ve verzi preview, takže je nutné povolit na stránce funkce verze Preview.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Nastavit výstrahy příjemce prahovou hodnotu a e-mailu
1. Navštivte [stránce funkce verze Preview](https://account.windowsazure.com/PreviewFeatures) a povolte **fakturace výstrah služby**.

1. Po přijetí e-mailu potvrzení, že je zapnutý fakturace služby pro vaše předplatné, navštivte [stránce předplatných](https://account.windowsazure.com/Subscriptions) na portálu účtů. Klikněte na předplatné, které chcete monitorovat a pak klikněte na tlačítko **výstrahy**.

    ![Snímek obrazovky zobrazení odběry z centra účtů Azure, se zvýrazněnou výstrahy][Image1]

2. Klikněte na tlačítko **přidat výstrahy** k vytvoření vaší první z nich. Můžete nastavit celkem pět fakturace výstrahy na předplatné s různé prahové hodnoty a až dvě příjemců e-mailu pro každou výstrahu.

    ![Snímek obrazovky zobrazení výstrahy, ve kterém můžete přidat výstrahy][Image2]

3. Přidáte-li výstrahu, můžete zadat jedinečný název, zvolte útraty prahovou hodnotu a zvolte e-mailové adresy, které jsou odesílány výstrahy. Při nastavování prahovou hodnotu, můžete buď **fakturace celkový** nebo **peněžního kreditu, který** z **výstrahy pro** seznamu. Fakturace celkem zobrazí se výstraha při předplatné výdaje překročí prahovou hodnotu. Pro peněžního kreditu zobrazí se výstraha při peněžní kredity neklesla pod limit. Peněžní kredity obvykle platí pro odběry bezplatné zkušební verze a Visual Studio.

    ![Snímek obrazovky zobrazení výstrah přidání, kde můžete konfigurovat příjemce][Image3]

Azure podporuje všechny e-mailovou adresu, ale není ověřte, že prací e-mailovou adresu, proto tuto možnost pro překlepům.

## <a name="check-on-your-alerts"></a>Zkontrolujte na upozornění
Po nastavení výstrahy centra účtů je uvádí a ukazuje, kolik více můžete nastavit. Pro každou výstrahu zobrazí datum a čas, který byl odeslán, jestli je výstraha fakturace celkový nebo peněžního kreditu, který a omezení, které vytvořili. Datum je formát rrrr mm-dd formát data a času je 24 hodin koordinaci světový čas (UTC). Kliknutím na znaménko plus pro výstrahu v seznamu, pokud ho chcete upravit, nebo klikněte na odpadkový ho odstranit.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Odstranit výstrahy nebo e-mailové adresy z Azure fakturace služby oznámení.
Pokud byste někdy potřebovali odebrat jakékoli informace ze služby, aktualizujte e-mailovou adresu na soubor nebo zcela odstranit výstrahu.

   ![Snímek obrazovky zobrazení výstrah odstranění, kde můžete odebrat osobní údaje.][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Výstrahy fakturace pro zákazníky Enterprise Agreement (EA)
EA předplatných nejsou podporované touto službou, místo toho EA zákazníkům můžete získat výstrahy pro každé oddělení v zápisu nastavením výdaje kvóty. V tématu [oddělení výdaje kvóty](https://ea.azure.com/helpdocs/departmentSpendingQuotas) na portálu EA začít pracovat.

## <a name="learn-more-about-azure-cost-management"></a>Další informace o Azure náklady na správu
- Odhad náklady na používání [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/), [celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator), a když přidáte službu.
- [Zkontrolujte využití a náklady na portálu Azure pravidelně](billing-getting-started.md#costs).
- Zapnout [Azure Advisor náklady doporučení](../advisor/advisor-cost-recommendations.md).

Další informace najdete v tématu [Azure náklady správy pokyny](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
