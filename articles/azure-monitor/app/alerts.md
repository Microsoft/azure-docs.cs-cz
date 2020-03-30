---
title: Nastavení výstrah v přehledech aplikací Azure
description: Získejte upozornění na pomalé doby odezvy, výjimky a další změny výkonu nebo využití ve vaší webové aplikaci.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295083"
---
# <a name="set-alerts-in-application-insights"></a>Nastavení výstrah v přehledech aplikací

[Azure Application Insights][start] vás může upozornit na změny v metrikách výkonu nebo využití ve vaší webové aplikaci. 

Application Insights monitoruje vaši živou aplikaci na [celé řadě platforem,][platforms] které vám pomohou diagnostikovat problémy s výkonem a porozumět vzorcům využití.

Existuje několik typů výstrah:

* [**Upozornění na metriky**](../../azure-monitor/platform/alerts-metric-overview.md) vás upozorní, když metrika propojí prahovou hodnotu pro určité období – například doby odezvy, počty výjimek, využití procesoru nebo zobrazení stránek.
* [**Výstrahy protokolu**](../../azure-monitor/platform/alerts-unified-log.md) se používají k popisu výstrah, kde je výstražný signál založen na vlastním dotazu Kusto.
* [**Webové testy**][availability] vám řeknou, kdy je váš web na internetu nedostupný nebo že reaguje pomalu. [Další informace][availability].
* [**Proaktivní diagnostika**](../../azure-monitor/app/proactive-diagnostics.md) je konfigurována automaticky, aby vás upozorňovala na neobvyklé vzorce výkonu.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak nastavit výstrahu výjimky pomocí vlastního vyhledávání protokolu

V této části projdeme, jak nastavit výstrahu výjimky založené na dotazu. V tomto příkladu řekněme, že chceme výstrahu, když je míra selhání větší než 10 % za posledních 24 hodin.

1. Přejděte na svůj prostředek Application Insight na webu Azure Portal.
2. Na levé straně, v rámci konfigurovat klikněte na **Upozornit**.

    ![Vlevo v části Konfigurovat upozornění na kliknutí](./media/alerts/1appinsightalert.png)

3. V horní části karty upozornění vyberte **Nové pravidlo výstrahy**.

     ![V horní části karty upozornění klikněte na nové pravidlo výstrahy](./media/alerts/2createalert.png)

4. Zdroj by měl být automaticky vybrán. Chcete-li nastavit podmínku, klepněte na tlačítko **Přidat podmínku**.

    ![Klikněte na přidat podmínku](./media/alerts/3addcondition.png)

5. Na kartě Konfigurovat logiku signálu vyberte **Vlastní vyhledávání protokolů**

    ![Klikněte na vlastní vyhledávání v protokolu.](./media/alerts/4customlogsearch.png)

6. Na kartě hledání vlastního protokolu zadejte dotaz do pole Vyhledávací dotaz. V tomto příkladu použijeme níže uvedený dotaz Kusto.
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

    ![Zadaný dotaz do pole vyhledávacího dotazu](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Tyto kroky můžete použít také u jiných typů výstrah založených na dotazech. Další informace o dotazovacím jazyce Kusto naleznete v tomto [dokumentu Kusto, který začíná jako doc,](https://docs.microsoft.com/azure/kusto/concepts/) nebo o tomto [cheatsheetu SQL to Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. V části "Logika výstrahy" zvolte, zda je založen álný na počtu výsledků nebo metrických měřeních. Pak vyberte podmínku (větší než, rovná se, menší než) a prahovou hodnotu. Při změně těchto hodnot si můžete všimnout změny věty náhledu podmínky. V tomto příkladu používáme "equal to".

    ![V části Vagéřská logika vyberte z možností stanovených na základě a podmínek a zadejte prahovou hodnotu.](./media/alerts/6alertlogic.png)

8. V části "Vyhodnoceno na základě" nastavte období a frekvenci. Období zde musí odpovídat hodnotě, kterou jsme vložili pro období ve výše uvedeném dotazu. Poté klepněte na **tlačítko Hotovo**.

    ![Nastavte tečku a frekvenci v dolní části a pak klikněte na hotovo](./media/alerts/7evaluate.png)

9. Nyní vidíme stav, který jsme vytvořili s odhadovanými měsíčními náklady. Níže v části ["Akční skupiny"](../platform/action-groups.md) můžete vytvořit novou skupinu nebo vybrat existující. Pokud chcete, můžete akce přizpůsobit.

    ![klikněte na vybrat nebo vytvořit tlačítka v rámci skupiny akcí](./media/alerts/8actiongroup.png)

10. Nakonec přidejte podrobnosti o výstraze (název pravidla výstrahy, popis, závažnost). Až budete hotovi, klikněte dole na **Vytvořit pravidlo výstrahy.**

    ![V části Podrobnosti výstrahy zadejte název pravidla výstrahy, napište popis a vyberte závažnost.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak se odhlásit z klasických upozornění e-mailových upozornění

Tato část se vztahuje na **klasické výstrahy dostupnosti**, **klasické výstrahy metrik Application Insights**a klasické **výstrahy anomálií selhání**.

Pokud platí některá z následujících platí, obdržíte e-mailová oznámení pro tato klasická upozornění:

* Vaše e-mailová adresa je uvedena v poli Oznámení příjemcům e-mailu v nastavení pravidla výstrahy.

* Možnost odesílat e-mailová oznámení uživatelům, kteří mají určité role pro předplatné, je aktivována a máte příslušnou roli pro konkrétní předplatné Azure.

![Snímek obrazovky s upozorněním](./media/alerts/alert-notification.png)

Chcete-li lépe kontrolovat zabezpečení a ochranu osobních údajů, obecně doporučujeme explicitně zadat příjemce oznámení pro klasické výstrahy v poli **Příjemci e-mailu s oznámením.** Možnost upozornit všechny uživatele, kteří mají určité role, je k dispozici pro zpětnou kompatibilitu.

Chcete-li se odhlásit z odběru e-mailových oznámení generovaných určitým pravidlem výstrahy, odeberte e-mailovou adresu z pole **Příjemci e-mailu s oznámením.**

Pokud vaše e-mailová adresa není výslovně uvedena, doporučujeme zakázat možnost automaticky upozornit všechny členy určitých rolí a místo toho uvést všechny e-maily uživatelů, kteří potřebují dostávat oznámení pro toto pravidlo výstrahy v e-mailu s oznámením pole příjemců.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo obdrží (klasická) upozornění?

Tato část se vztahuje pouze na klasická upozornění a pomůže vám optimalizovat upozornění tak, aby oznámení dostávali pouze požadovaní příjemci. Další informace o rozdílu mezi [klasickými výstrahami](../platform/alerts-classic.overview.md) a novým prostředím výstrah naleznete v [článku s přehledem výstrah](../platform/alerts-overview.md). Chcete-li v novém prostředí výstrah řídit upozornění, použijte [skupiny akcí](../platform/action-groups.md).

* Doporučujeme používat konkrétní příjemce pro klasická upozornění.

* Pro výstrahy na všechny metriky Application Insights (včetně metrik dostupnosti), **možnost hromadného/skupinového** zaškrtnutí, pokud je povolena, odesílá uživatelům s rolí vlastníka, přispěvatele nebo čtenáře v předplatném. Ve skutečnosti _všichni_ uživatelé s přístupem k předplatnému prostředku Application Insights jsou v oboru a budou dostávat oznámení.

> [!NOTE]
> Pokud aktuálně používáte možnost **hromadného/skupinového** zaškrtávacího políčka a zakážete ji, nebudete moci změnu vrátit zpět.

Pokud potřebujete upozornit uživatele na základě jejich rolí, použijte nové prostředí výstrah y/výstrahy téměř v reálném čase. Pomocí [skupin akcí](../platform/action-groups.md)můžete nakonfigurovat e-mailová oznámení uživatelům s některou z rolí přispěvatele/vlastníka/čtenáře (není kombinováno dohromady jako jedna možnost).

## <a name="automation"></a>Automatizace
* [Automatizace výstrah při nastavení pomocí PowerShellu](../../azure-monitor/app/powershell-alerts.md)
* [Automatizace odpovídání na výstrahy pomocí webhooků](../../azure-monitor/platform/alerts-webhooks.md)

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

