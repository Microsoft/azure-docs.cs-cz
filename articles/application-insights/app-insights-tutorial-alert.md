---
title: Odesílání oznámení z Azure Application Insights | Microsoft Docs
description: Kurz k odesílání upozornění v reakci na chyby do vaší aplikace pomocí služby Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2017
ms.locfileid: "23947225"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorování a výstraha na stav aplikací pomocí služby Azure Application Insights

Azure Application Insights umožňuje monitorovat aplikace a odesílání vám upozornění, pokud je buď nedostupný, má selhání nebo trpí problémy s výkonem.  Tento kurz vás provede procesem vytvoření testy nepřetržitě zkontrolovat dostupnost vaší aplikace a odeslat různé druhy výstrah v reakci na zjištěné problémy.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření testu dostupnosti nepřetržitě zkontrolujte odpovědi aplikace
> * Odeslat e-mailu správce, pokud dojde k potížím
> * Vytvářet výstrahy podle metrik výkonu. 
> * Pomocí aplikace logiky odesílat souhrnné telemetrie podle plánu.


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

- Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
    - Nasazení aplikace .NET do Azure a [povolit Application Insights SDK](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k portálu Azure v [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Vytvoření testu dostupnosti
Testy dostupnosti ve službě Application Insights umožňují automaticky testování vaší aplikace z různých míst po celém světě.   V tomto kurzu provedete jednoduchá testovací zajistit, že aplikace je k dispozici.  Můžete také vytvořit kompletní a podrobný postup k testování její podrobné operací. 

1. Vyberte **Application Insights** a potom vyberte své předplatné.  
1. Vyberte **dostupnosti** pod **prošetření** nabídce a pak klikněte na tlačítko **přidat test**.
 
    ![Přidat test dostupnosti](media/app-insights-tutorial-alert/add-test.png)

2. Zadejte název pro test a nechte ostatní výchozí hodnoty.  To vyžaduje domovské stránce aplikace každých 5 minut z 5 různá geografická umístění. 
3. Vyberte **výstrahy** otevřete **výstrahy** panel, kde můžete definovat podrobnosti o tom, jak reagovat, pokud se test nezdaří. Typ v e-mailovou adresu k odeslání, pokud je splněna kritéria výstrahy.  Volitelně můžete zadat v adrese z webhooku pro volání při splnění kritérií pro výstrahy.

    ![Vytvoření testu](media/app-insights-tutorial-alert/create-test.png)
 
4. Vrátí do testovací panelu a po několika minutách byste měli začít, zobrazuje výsledky testu dostupnosti.  Klikněte na název testovacího zobrazení podrobností z každé umístění.  Bodový graf zobrazuje úspěšnosti a doba trvání testu.

    ![Podrobnosti o testu](media/app-insights-tutorial-alert/test-details.png)

5.  Můžete k podrobnostem a podrobnosti o žádné konkrétní test kliknutím na jeho tečky v bodového grafu.  Následující příklad ukazuje podrobnosti pro chybné žádosti.

    ![Výsledek testu](media/app-insights-tutorial-alert/test-result.png)
  
6. Pokud je splněna kritéria výstrahy, zpráva podobné níže je odeslána na adresu, kterou jste zadali.

    ![Výstrahy e-mailu](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Vytvořit výstrahu z metriky
Kromě odesílání oznámení z test dostupnosti, můžete vytvořit výstrahu z jakékoli metriky výkonu, které jsou shromažďovány pro vaši aplikaci.

2. Vyberte **výstrahy** z **konfigurace** nabídky.  Otevře se panel Azure výstrahy.  Mohou existovat další pravidla výstrah nakonfigurované zde pro jiné služby.
3. Klikněte na tlačítko **přidat metriky upozornění**.  Otevře se panel, který chcete vytvořit nové pravidlo výstrahy.

    ![Přidání metriky upozornění](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Zadejte **název** výstrahy pravidlo a vyberte svou aplikaci v rozevírací nabídce pro **prostředků**.
5. Vyberte **metrika** ukázce.  Graf, který se zobrazí hodnota této žádosti za posledních 24 hodin.  To vám usnadní nastavení podmínky pro metriku.

    ![Přidání pravidla výstrahy](media/app-insights-tutorial-alert/add-alert-01.png)

6. Zadejte **podmínku** a **prahová hodnota** pro výstrahu. Toto je počet pokusů, které musí být překročen Metrika pro výstrahu, který se má vytvořit. 
6. V části **oznámit prostřednictvím** zkontrolujte **e-mailu vlastníci, přispěvatelé a čtenáři** políčko Odeslat e-mail uživatelům, když je splněna podmínka upozornění a přidat e-mailovou adresu žádné další příjemce.  Můžete také zadat webhook, jehož nebo aplikace logiky tady která se spouští při splnění této podmínky.  To může pokusit zmírnit zjištěný problém nebo 

    ![Přidání pravidla výstrahy](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktivní odeslat informace
Výstrahy jsou vytvářeny v reakci na konkrétní sadu problémy zjištěné v aplikaci a obvykle rezervovat výstrahy pro kritické podmínky, které vyžadují okamžitou pozornost.  Můžete proaktivně získáte informace o vaší aplikace pomocí aplikace logiky, která je spouštěna automaticky podle plánu.  Například můžete mít e-maily odeslané na správci denně s souhrnné informace, které vyžaduje další vyhodnocení.

Podrobnosti o vytvoření aplikace logiky s Application Insights najdete v tématu [automatizovat Application Insights zpracuje pomocí Logic Apps](automate-with-logic-apps.md)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili postup výstraha na problémy, přechodu na dalším kurzu se dozvíte, jak analyzovat, jak jsou uživatelé interakci s vaší aplikací.

> [!div class="nextstepaction"]
> [Pochopení uživatelů](app-insights-tutorial-users.md)