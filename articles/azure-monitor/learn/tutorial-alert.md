---
title: Odesílání oznámení z Azure Application Insights | Dokumentace Microsoftu
description: Kurz odesílání výstrah v reakci na chyby ve vaší aplikaci pomocí Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: af002035bb0ab56a985e38d02ad374b23d177583
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081022"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorování a upozornění na stav aplikace pomocí Azure Application Insights

Azure Application Insights umožňuje monitorovat své aplikace a pošle vám výstrahy, když je buď nedostupné, ve kterém došlo k selhání, nebo trpí problémy s výkonem.  Tento kurz vás provede procesem vytvoření testů, který bude nepřetržitě kontrolovat dostupnost vaší aplikace a odesílat různé druhy výstrah v reakci na zjištěné problémy.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit test dostupnosti, který bude nepřetržitě kontrolovat odpovědi aplikace
> * Odeslání e-mailu správce, když dojde k potížím
> * Vytvoření výstrah na základě metrik výkonu 
> * Pomocí aplikace logiky k odesílání telemetrických dat souhrnný podle plánu.


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

- Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
    - Nasadit aplikaci .NET do Azure a [povolit sadu Application Insights SDK](../../azure-monitor/app/asp-net.md). 


## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Vytvořit test dostupnosti.
Testy dostupnosti ve službě Application Insights umožňují automaticky otestovat aplikaci z různých míst po celém světě.   V tomto kurzu provedete jednoduchý test a ujistěte se, že aplikace je k dispozici.  Můžete také vytvořit kompletní návod k otestování jeho provozu. 

1. Vyberte **Application Insights** a pak vyberte své předplatné.  
1. Vyberte **dostupnosti** pod **prošetření** nabídky a pak klikněte na tlačítko **testu přidat**.
 
    ![Přidat test dostupnosti](media/tutorial-alert/add-test.png)

2. Zadejte název pro testovací a nechte ostatní výchozí hodnoty.  To vyžaduje domovskou stránku aplikace každých 5 minut z 5 různých geografických umístěních. 
3. Vyberte **výstrahy** otevřít **výstrahy** panel, ve kterém můžete definovat podrobnosti o tom, jak reagovat, pokud se test nezdaří. Typ e-mailovou adresu odeslání, pokud je splněna kritéria výstrahy.  Volitelně můžete zadat adresu z webhooku volat, když je splněna kritéria výstrahy.

    ![Vytvořit test](media/tutorial-alert/create-test.png)
 
4. Vraťte se do panelu testu a po několika minutách se začnou zobrazovat výsledky testu dostupnosti.  Klikněte na název testu, chcete-li zobrazit podrobnosti o jednotlivých umístěních.  Bodový graf ukazuje úspěšnosti a doba trvání jednotlivých testovacích.

    ![Podrobnosti o testu](media/tutorial-alert/test-details.png)

5.  Můžete procházet hierarchii podrobné informace o všech určitého testu kliknutím na jeho bod v bodovém grafu.  Následující příklad ukazuje podrobnosti pro chybné žádosti.

    ![Výsledek testu](media/tutorial-alert/test-result.png)
  
6. Při splnění kritérií výstrah e-mailu podobně jako následující. na té posílá na adresu, kterou jste zadali.

    ![Upozornění e-mailu](media/tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Vytvořte výstrahu z metrik
Kromě odesílání oznámení z test dostupnosti, můžete vytvořit výstrahy ze všech metrik výkonu, která se shromažďují pro vaši aplikaci.

2. Vyberte **výstrahy** z **konfigurovat** nabídky.  Otevře se panel Azure Alerts.  Můžou existovat další pravidla upozornění nakonfigurované pro jiné služby.
3. Klikněte na tlačítko **přidat upozornění metriky**.  Otevře se panel Chcete-li vytvořit nové pravidlo upozornění.

    ![Přidat upozornění metriky](media/tutorial-alert/add-metric-alert.png)

4. Zadejte **název** výstrahy pravidlo a vyberte svou aplikaci v rozevírací nabídce pro **prostředků**.
5. Vyberte **metrika** vzorků.  Označuje hodnotu této žádosti za posledních 24 hodin se zobrazí graf.  To vám usnadní nastavení podmínky pro metriku.

    ![Přidání pravidla výstrahy](media/tutorial-alert/add-alert-01.png)

6. Zadejte **podmínku** a **prahová hodnota** výstrahy. Toto je počet případů, kdy musí být překročen metriku pro výstrahu, který se má vytvořit. 
6. V části **upozorňovat prostřednictvím** zkontrolujte **e-mailu vlastníci, přispěvatelé a čtenáři** pro odeslání e-mailu pro tyto uživatele, když se vyskytl výstražný stav je splněna a přidání e-mailové adresy všech dalších příjemců, kterým.  Můžete také zadat webhook nebo aplikaci logiky tady, která se spustí, když je splněna podmínka.  Toto může pokusit zmírnit zjištěný problém nebo 

    ![Přidání pravidla výstrahy](media/tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktivně odeslat informace
Upozornění se vytvářejí v reakci na konkrétní sadu problémy zjištěné ve vaší aplikaci a obvykle rezervovat výstrahy pro kritické podmínky, které vyžadují okamžitou pozornost.  Proaktivně můžete získat informace o vaší aplikaci s využitím aplikace logiky, která se spouští automaticky podle plánu.  Můžete například mít e-mailu odesílat správcům každý den se souhrnnými informacemi, které vyžaduje další hodnocení.

Podrobnosti týkající se vytvoření aplikace logiky s využitím Application Insights najdete v tématu [automatizovat Application Insights procesů pomocí Logic Apps](../../azure-monitor/app/automate-with-logic-apps.md)

## <a name="next-steps"></a>Další postup
Teď, když jste zjistili, jak upozorní na problémy, přejděte k dalšímu kurzu, kde se naučíte, jak analyzovat, jak uživatelé interagují s vaší aplikací.

> [!div class="nextstepaction"]
> [Pochopení uživatelů](../../application-insights/app-insights-tutorial-users.md)