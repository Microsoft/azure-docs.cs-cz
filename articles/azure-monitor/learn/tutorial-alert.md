---
title: Poslat výstrahy z Azure Application Insights | Microsoft Docs
description: Kurz pro posílání výstrah v reakci na chyby v aplikaci pomocí Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656258"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorování a upozornění na stav aplikace pomocí Azure Application Insights

Azure Application Insights umožňuje monitorovat vaši aplikaci a odesílat výstrahy, pokud jsou buď nedostupné, dochází k chybám, nebo trpí problémy s výkonem.  Tento kurz vás provede procesem vytváření testů pro nepřetržitou kontrolu dostupnosti aplikace.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit test dostupnosti pro nepřetržitou kontrolu odezvy aplikace
> * Při výskytu problému poslat e-mail správcům

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu je potřeba:

Vytvořte [prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Vytvořit test dostupnosti

Testy dostupnosti v Application Insights umožňují automaticky testovat aplikaci z různých míst po celém světě.   V tomto kurzu provedete test adresy URL, abyste zajistili, že vaše webová aplikace bude k dispozici.  Můžete také vytvořit kompletní návod k otestování jeho podrobné operace. 

1. Vyberte **Application Insights** a pak vyberte své předplatné.  

2. V nabídce **prozkoumat** vyberte **dostupnost** a pak klikněte na **vytvořit test**.

    ![Přidat test dostupnosti](media/tutorial-alert/add-test-001.png)

3. Zadejte název testu a nechte ostatní výchozí hodnoty.  Tento výběr spustí žádosti o adresu URL aplikace každých 5 minut z pěti různých geografických umístění.

4. Výběrem **výstrahy** otevřete rozevírací seznam **výstrahy** , kde můžete zadat podrobnosti o tom, jak reagovat, pokud se test nezdařil. Vyberte **prakticky v reálném čase** a nastavte stav na **povoleno.**

    Zadejte e-mailovou adresu, která se odešle při splnění kritérií výstrahy.  Volitelně můžete zadat adresu Webhooku, který se má zavolat při splnění kritérií výstrahy.

    ![Vytvořit test](media/tutorial-alert/create-test-001.png)

5. Vraťte se na panel testu, vyberte tři tečky a upravte výstrahu a zadejte konfiguraci pro upozornění téměř v reálném čase.

    ![Upravit upozornění](media/tutorial-alert/edit-alert-001.png)

6. Nastavte umístění, která selhala, aby byla větší než nebo rovna 3. Vytvořte [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) pro konfiguraci, kdo obdrží oznámení, když dojde k porušení prahové hodnoty upozornění.

    ![Uložit uživatelské rozhraní výstrahy](media/tutorial-alert/save-alert-001.png)

7. Po nakonfigurování výstrahy klikněte na název testu, abyste zobrazili podrobnosti z každého umístění. Testy lze zobrazit ve formátu spojnicového a bodového grafu, aby bylo možné vizualizovat úspěšnost a selhání pro daný časový rozsah.

    ![Podrobnosti testu](media/tutorial-alert/test-details-001.png)

8. Můžete přejít k podrobnostem libovolného testu kliknutím na jeho tečku v bodovém grafu. Tím se spustí zobrazení Podrobnosti o koncovém konci transakce. V následujícím příkladu jsou uvedeny podrobnosti o neúspěšné žádosti.

    ![Výsledek testu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak upozornit na problémy, přejděte k dalšímu kurzu, kde se dozvíte, jak analyzovat způsob interakce uživatelů s vaší aplikací.

> [!div class="nextstepaction"]
> [Pochopení uživatelů](../../azure-monitor/learn/tutorial-users.md)
