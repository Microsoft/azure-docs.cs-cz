---
title: Odesílání upozornění z Přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Kurz pro odesílání výstrah v reakci na chyby ve vaší aplikaci pomocí Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77656258"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorování stavu aplikací a výstrahy na azure application insights

Azure Application Insights umožňuje sledovat vaši aplikaci a odesílat upozornění, když je buď k dispozici, dochází k selhání nebo trpí problémy s výkonem.  Tento kurz vás provede procesem vytváření testů průběžně kontrolovat dostupnost vaší aplikace.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit test dostupnosti průběžně kontrolovat odpověď aplikace
> * Odeslání pošty správcům v případě problému

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

Vytvořte [prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-availability-test"></a>Vytvoření testu dostupnosti

Testy dostupnosti v Application Insights umožňují automaticky testovat vaši aplikaci z různých míst po celém světě.   V tomto kurzu provedete test adresy URL, abyste zajistili, že vaše webová aplikace je k dispozici.  Můžete také vytvořit úplný návod k testování jeho podrobné operace. 

1. Vyberte **Application Insights** a pak vyberte své předplatné.  

2. V nabídce **Investigate** vyberte **Availability** a potom klepněte na tlačítko **Vytvořit test**.

    ![Přidat test dostupnosti](media/tutorial-alert/add-test-001.png)

3. Zadejte název testu a ostatní výchozí hodnoty ponechte.  Tento výběr spustí požadavky na adresu URL aplikace každých 5 minut z pěti různých zeměpisných umístění.

4. Výběrem **možnosti Výstrahy** otevřete rozevírací soubor **Výstrahy,** kde můžete definovat podrobnosti o tom, jak reagovat, pokud se test nezdaří. Zvolte **Téměř v reálném čase** a nastavte stav na **Povoleno.**

    Zadejte e-mailovou adresu, kterou chcete odeslat, když jsou splněna kritéria výstrahy.  Volitelně můžete zadat adresu webhooku, který chcete volat, když jsou splněna kritéria výstrahy.

    ![Vytvořit test](media/tutorial-alert/create-test-001.png)

5. Vraťte se na testovací panel, vyberte tři tečky a upravte výstrahu pro zadání konfigurace výstrahy téměř v reálném čase.

    ![Upravit výstrahu](media/tutorial-alert/edit-alert-001.png)

6. Nastavte neúspěšná umístění na větší nebo rovna 3. Vytvořte [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) a nakonfigurujte, kdo bude upozorněn, když dojde k porušení prahové hodnoty výstrahy.

    ![Uložit uzdu upozornění](media/tutorial-alert/save-alert-001.png)

7. Po konfiguraci výstrahy klikněte na název testu a zobrazte podrobnosti z každého umístění. Testy lze zobrazit ve formátu čárového grafu i grafu scatter, aby bylo možné vizualizovat úspěch/neúspěchy pro daný časový rozsah.

    ![Podrobnosti testu](media/tutorial-alert/test-details-001.png)

8. Kliknutím na jeho tečku v bodovém grafu můžete přejít k podrobnostem každého testu. Tím se spustí zobrazení podrobností o transakcích od konce. Následující příklad ukazuje podrobnosti o neúspěšném požadavku.

    ![Výsledek zkoušky](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak upozornit na problémy, přejdete na další kurz a dozvíte se, jak analyzovat, jak uživatelé interagují s vaší aplikací.

> [!div class="nextstepaction"]
> [Pochopení uživatelů](../../azure-monitor/learn/tutorial-users.md)
