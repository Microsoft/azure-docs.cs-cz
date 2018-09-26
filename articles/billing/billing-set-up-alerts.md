---
title: Nastavení upozornění fakturace ani kredit nad rámec pro předplatná Azure | Dokumentace Microsoftu
description: Popisuje, jak můžete nastavit výstrahy na vaší faktuře za Azure tomu se můžete vyhnout fakturační překvapení.
keywords: kredit výstrahy, upozornění fakturace
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
ms.date: 06/15/2018
ms.author: adpick
ms.openlocfilehash: 094bfe041ae04e52b6d998ccfd1d964abf192d6a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095095"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Nastavení upozornění fakturace nebo kredit pro předplatné Microsoft Azure
Pokud jste správcem účtu pro předplatné Azure, služba upozornění fakturace Azure můžete vytvářet přizpůsobená upozornění fakturace, která vám pomůžou sledovat a řídit fakturaci účtů Azure.

Tato služba je ve verzi preview, takže je potřeba povolit na stránce funkce ve verzi Preview.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Nastavte upozornění příjemcům prahové hodnoty a e-mailu
1. Navštivte [stránce funkcí ve verzi Preview](https://account.windowsazure.com/PreviewFeatures) a povolit **služba upozornění fakturace**.

1. Po přijetí e-mailové potvrzení, že je zapnutá fakturace služby pro vaše předplatné, navštivte [na stránce předplatná](https://account.windowsazure.com/Subscriptions) na portálu účtů. Klikněte na předplatné, které chcete monitorovat a potom klikněte na **výstrahy**.

    ![Snímek obrazovky zobrazení předplatných centru účtů Azure s výstrahami zvýrazněnou][Image1]

2. Klepnutím na tlačítko **přidat upozornění** k vytvoření vaší první z nich. Celkový počet pět upozornění fakturace na jedno předplatné, se různé prahové hodnoty a až dva příjemců e-mailu pro každé upozornění můžete nastavit.

    ![Snímek obrazovky zobrazení výstrah, ve kterém můžete přidat výstrahu][Image2]

3. Přidáte-li výstrahu, zadejte pro něj jedinečný název, zvolte útraty prahovou hodnotu a zvolte e-mailové adresy, kam se mají oznámení odesílat. Při nastavování prahovou hodnotu, můžete použít buď **fakturace celkové** nebo **peněžní kredit ve výši** z **výstrah pro** seznamu. Fakturační celkem zobrazí se výstraha při předplatné útraty překročí prahovou hodnotu. U nabídek peněžních kreditů zobrazí se výstraha při peněžní kredity klesne pod limit. Peněžní kredity obvykle vztahovat na předplatná bezplatnou zkušební verzi a sady Visual Studio.

    ![Snímek obrazovky zobrazení výstrah sčítání, kde můžete konfigurovat příjemce][Image3]

Azure podporuje všechny e-mailovou adresu, ale neověřuje, že e-mailové adresy funguje, tak zkontrolujte pro překlepy.

## <a name="check-on-your-alerts"></a>Podívejte se na vaše upozornění
Po nastavení výstrah do centra pro účty jejich seznam a ukazuje, kolik více můžete nastavit. Pro každé upozornění zobrazí datum a čas, kdy byla vyslána ať už jde o výstrahu týkající se fakturace celkové nebo peněžní kredit ve výši a omezení, které nastavíte. Datum je formát rrrr mm-dd formát data a času je 24 hodin koordinovat světový čas (UTC). Klikněte na znaménko plus upozornění v seznamu se ji upravit, nebo klikněte na tlačítko odpadkového koše ho odstranit.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Odstranění upozornění nebo e-mailové adresy z služba upozornění fakturace Azure
Pokud byste někdy potřebovali, odeberte všechny informace ze služby, aktualizujte e-mailovou adresu pro soubor nebo výstrahu zcela odstranit.

   ![Snímek obrazovky zobrazení výstrah odstranění, kde můžete odebrat osobní údaje][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Upozornění fakturace pro zákazníky, kteří Enterprise Agreement (EA)
Tato služba nepodporuje předplatných EA, místo toho zákazníci programu EA si můžete získat upozornění pro každé oddělení v rámci registrace nastavením kvóty útraty. Zobrazit [kvóty útraty oddělení](https://ea.azure.com/helpdocs/departmentSpendingQuotas) na portálu EA, abyste mohli začít.

## <a name="learn-more-about-azure-cost-management"></a>Další informace o službě Azure cost management
- Odhad nákladů s využitím [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/), [celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator), a pokud přidáte službu.
- [Kontrola využití a nákladů pravidelně na webu Azure portal](billing-getting-started.md#costs).
- Zapnout [nákladů doporučení Azure Advisoru](../advisor/advisor-cost-recommendations.md).

Další informace najdete v tématu [Azure cost management pokyny](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
