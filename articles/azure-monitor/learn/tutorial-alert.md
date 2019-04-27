---
title: Odesílání oznámení z Azure Application Insights | Dokumentace Microsoftu
description: Kurz odesílání výstrah v reakci na chyby ve vaší aplikaci pomocí Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 05285a177827cd0dd1e0e39e779a395ccfdfc0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541004"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorování a upozornění na stav aplikace pomocí Azure Application Insights

Azure Application Insights umožňuje monitorovat své aplikace a pošle vám výstrahy, když je buď nedostupné, ve kterém došlo k selhání, nebo trpí problémy s výkonem.  Tento kurz vás provede procesem vytvoření testů, který bude nepřetržitě kontrolovat dostupnost vaší aplikace.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit test dostupnosti, který bude nepřetržitě kontrolovat odpovědi aplikace
> * Odeslání e-mailu správce, když dojde k potížím

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

Vytvoření [prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Vytvořit test dostupnosti.

Testy dostupnosti ve službě Application Insights umožňují automaticky otestovat aplikaci z různých míst po celém světě.   V tomto kurzu provedete test adresy url k zajištění, že vaše webová aplikace je k dispozici.  Můžete také vytvořit kompletní návod k otestování jeho provozu. 

1. Vyberte **Application Insights** a pak vyberte své předplatné.  

2. Vyberte **dostupnosti** pod **prošetření** nabídky a pak klikněte na tlačítko **vytvořit testovací**.

    ![Přidat test dostupnosti](media/tutorial-alert/add-test-001.png)

3. Zadejte název pro testovací a nechte ostatní výchozí hodnoty.  Tento výběr se aktivuje požadavky pro adresy url aplikace každých 5 minut z pěti různých geografických umístěních.

4. Vyberte **výstrahy** otevřít **výstrahy** rozevíracího seznamu, ve kterém můžete definovat podrobnosti o tom, jak reagovat, pokud se test nezdaří. Zvolte **v téměř reálném čase** a nastavit stav na **povoleno.**

    Typ e-mailovou adresu odeslání, pokud je splněna kritéria výstrahy.  Volitelně můžete zadat adresu z webhooku volat, když je splněna kritéria výstrahy.

    ![Vytvořit test](media/tutorial-alert/create-test-001.png)

5. Vraťte se do panelu testu, vyberte symbol tří teček a upravit upozornění zadejte konfiguraci pro upozornění v téměř reálném čase.

    ![Upravit upozornění](media/tutorial-alert/edit-alert-001.png)

6. Nastavit umístění se na větší než nebo rovna hodnotě 3. Vytvoření [skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) konfigurace, který načte oznámení, když prahové hodnoty výstrahy dojde k porušení zabezpečení.

    ![Uložit upozornění uživatelského rozhraní](media/tutorial-alert/save-alert-001.png)

7. Po konfiguraci upozornění, klikněte na název testu, chcete-li zobrazit podrobnosti o jednotlivých umístěních. Testy lze zobrazit v obou řádku a bodového grafu vykreslení formátu k vizualizaci úspěch nebo selhání pro dané časové rozmezí.

    ![Podrobnosti o testu](media/tutorial-alert/test-details-001.png)

8. Můžete procházet hierarchii k podrobnostem jakéhokoli testu, kliknutím na jeho bod v bodovém grafu. Tím se spustí zobrazení podrobností transakcí začátku do konce. Následující příklad ukazuje podrobnosti pro chybné žádosti.

    ![Výsledek testu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak upozorní na problémy, přejděte k dalšímu kurzu, kde se naučíte, jak analyzovat, jak uživatelé interagují s vaší aplikací.

> [!div class="nextstepaction"]
> [Pochopení uživatelů](../../azure-monitor/learn/tutorial-users.md)