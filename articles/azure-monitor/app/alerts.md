---
title: Nastavení upozornění v Azure Application Insights
description: Získejte oznámení o pomalých časech odezvy, výjimkách a dalších změnách výkonu nebo využití ve vaší webové aplikaci.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295083"
---
# <a name="set-alerts-in-application-insights"></a>Nastavení výstrah v Application Insights

[Azure Application Insights][start] vás může upozorňovat na změny v metrikách výkonu nebo využití ve vaší webové aplikaci. 

Application Insights monitoruje svou živou aplikaci na nejrůznějších [platformách][platforms] , které vám pomůžou diagnostikovat problémy s výkonem a pochopit vzorce používání.

Existuje několik typů výstrah:

* [**Výstrahy metriky upozorňují**](../../azure-monitor/platform/alerts-metric-overview.md) na to, že metrika překročí prahovou hodnotu pro určitou dobu – například doba odezvy, počty výjimek, využití procesoru nebo zobrazení stránek.
* [**Výstrahy protokolu**](../../azure-monitor/platform/alerts-unified-log.md) se používají k popisu výstrah, ve kterých je signál výstrahy založen na vlastním dotazu Kusto.
* [**Webové testy**][availability] vás informují, že váš web není dostupný na internetu, nebo reaguje pomalu. [Další informace][availability].
* [**Proaktivní Diagnostika**](../../azure-monitor/app/proactive-diagnostics.md) jsou nakonfigurovány automaticky, aby vás upozornily na neobvyklé vzorce výkonu.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Nastavení upozornění na výjimku pomocí vlastního prohledávání protokolu

V této části se naučíme, jak nastavit výstrahu pro výjimku založenou na dotazech. V tomto příkladu řekněme, že chceme, aby byla výstraha, když je neúspěšná míra větší než 10% za posledních 24 hodin.

1. V Azure Portal přejdete do svého prostředku Application Insight.
2. Vlevo v části konfigurovat klikněte na **výstrahu**.

    ![Vlevo v části konfigurace výstrahy kliknutí](./media/alerts/1appinsightalert.png)

3. V horní části karty výstraha vyberte **nové pravidlo výstrahy**.

     ![V horní části karty výstraha klikněte na nové pravidlo výstrahy.](./media/alerts/2createalert.png)

4. Váš prostředek by měl být vybraný automaticky. Pokud chcete nastavit podmínku, klikněte na **Přidat podmínku**.

    ![Klikněte na Přidat podmínku.](./media/alerts/3addcondition.png)

5. Na kartě Konfigurace logiky signálu vyberte **vlastní prohledávání protokolu** .

    ![Kliknout na vlastní prohledávání protokolu](./media/alerts/4customlogsearch.png)

6. Na kartě vlastní hledání protokolu zadejte dotaz do pole vyhledávací dotaz. V tomto příkladu použijeme následující dotaz Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Zadejte dotaz do pole vyhledávací dotaz](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Tyto kroky můžete použít i na jiné typy výstrah založených na dotazech. Další informace o dotazovacím jazyku Kusto najdete v [dokumentu Začínáme s Kusto](https://docs.microsoft.com/azure/kusto/concepts/) nebo tento dokument [SQL pro Kusto tahák](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet) .

7. V části logika výstrahy vyberte, jestli je založená na počtu výsledků, nebo měření metriky. Pak vyberte podmínku (větší než, rovná se, je menší než) a prahovou hodnotu. Když měníte tyto hodnoty, můžete si všimnout změny podmínky verze Preview. V tomto příkladu používáme "EQUAL to".

    ![V části logika výstrahy zvolte jednu z možností na základě podmínky a a pak zadejte prahovou hodnotu.](./media/alerts/6alertlogic.png)

8. V části "vyhodnocené na základě" nastavte období a četnost. Toto období se musí shodovat s hodnotou, kterou zadáte pro období výše v dotazu. Pak klikněte na **Hotovo**.

    ![Nastavte tečku a četnost v dolní části a potom klikněte na Hotovo.](./media/alerts/7evaluate.png)

9. Nyní se zobrazí podmínka, kterou jsme vytvořili, a Odhadované měsíční náklady. Pod položkou ["skupiny akcí"](../platform/action-groups.md) můžete vytvořit novou skupinu nebo vybrat některou z existujících. Pokud chcete, můžete akce přizpůsobit.

    ![v části Skupina akcí klikněte na tlačítka Vybrat nebo vytvořit.](./media/alerts/8actiongroup.png)

10. Nakonec přidejte podrobnosti výstrahy (název pravidla výstrahy, popis, závažnost). Až skončíte, klikněte dole na **vytvořit pravidlo výstrahy** .

    ![V části Podrobnosti výstrahy zadejte název pravidla upozornění, napište popis a vyberte závažnost.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Zrušení odběru oznámení z klasického e-mailového upozornění

Tato část se vztahuje na **klasické výstrahy dostupnosti**, **výstrahy klasického Application Insights metriky**a na klasické výstrahy týkající se **anomálií selhání**.

Pokud platí některá z následujících možností, dostanete e-mailová oznámení pro tyto klasické výstrahy:

* Vaše e-mailová adresa je uvedená v poli příjemci e-mailu s oznámením v nastavení pravidla výstrahy.

* Aktivuje se možnost odesílání e-mailových oznámení uživatelům, kteří mají určité role pro předplatné, a u tohoto konkrétního předplatného Azure udržujete příslušnou roli.

![Snímek obrazovky s oznámením o výstrahách](./media/alerts/alert-notification.png)

Pro lepší kontrolu zabezpečení a ochrany osobních údajů obecně doporučujeme, abyste příjemcům oznámení pro klasické výstrahy explicitně zadali v poli **příjemci e-mailových oznámení** . Možnost upozorňování všech uživatelů, kteří drží určité role, je poskytována z důvodu zpětné kompatibility.

Pokud chcete odhlásit odběr e-mailových oznámení vytvořených pomocí určitého pravidla výstrahy, odeberte e-mailovou adresu z pole **příjemci e-mailu s oznámením** .

Pokud vaše e-mailová adresa není výslovně uvedena, doporučujeme zakázat možnost oznamovat všem členům určitých rolí automaticky a místo toho uvést všechny e-maily uživatelů, kteří potřebují dostávat oznámení pro toto pravidlo výstrahy v poli příjemci oznámení e-mailu.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo obdrží oznámení o výstrahách (Classic)?

Tato část platí jenom pro klasické výstrahy a pomůže vám optimalizovat oznámení o výstrahách, abyste zajistili, že oznámení budou dostávat jenom vaši dožádaný příjemce. Pokud chcete získat další informace o rozdílu mezi [klasickými výstrahami](../platform/alerts-classic.overview.md) a novými výstrahami, přečtěte si [článek Přehled výstrah](../platform/alerts-overview.md). Chcete-li řídit oznamování výstrah v nové možnosti upozornění, použijte [skupiny akcí](../platform/action-groups.md).

* Pro klasická oznámení o výstrahách doporučujeme používat konkrétní příjemce.

* Pro výstrahy týkající se jakékoli metriky Application Insights (včetně metrik dostupnosti), pokud **je tato možnost** povolena, odesílá uživatelům v rámci předplatného role vlastník, přispěvatel nebo čtenář. V důsledku toho budou mít _Všichni_ uživatelé s přístupem k předplatnému Application Insights prostředek v oboru a budou dostávat oznámení.

> [!NOTE]
> Pokud v tuto chvíli používáte možnost **hromadného a skupinového** zaškrtávacího políčka a zakážete ji, nebudete moct změnu vrátit.

Pokud potřebujete upozornit uživatele na základě jejich rolí, použijte nové výstrahy Výstrahy a možnosti téměř v reálném čase. Pomocí [skupin akcí](../platform/action-groups.md)můžete nakonfigurovat e-mailová oznámení uživatelům pomocí kterékoli role Přispěvatel/vlastník/čtenář (bez kombinace společně s jednou možností).

## <a name="automation"></a>Automation
* [Použití PowerShellu k automatizaci nastavení výstrah](../../azure-monitor/app/powershell-alerts.md)
* [Použití webhooků k automatizaci reakce na výstrahy](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Viz také
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizace nastavení výstrah](../../azure-monitor/app/powershell-alerts.md)
* [Proaktivní diagnostika](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

