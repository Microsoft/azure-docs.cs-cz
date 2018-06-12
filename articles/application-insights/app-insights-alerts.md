---
title: Nastavit výstrahy ve službě Azure Application Insights | Microsoft Docs
description: Upozorňování o pomalé odezvy, výjimky a další výkonu nebo použití změn ve vaší webové aplikaci.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 87be1a48a6c3320187243e549a8fb8e5ecc9e006
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293599"
---
# <a name="set-alerts-in-application-insights"></a>Nastavit výstrahy ve službě Application Insights
[Azure Application Insights] [ start] vás může upozornit na změny v metriky výkonu a využití ve vaší webové aplikaci. 

Application Insights monitoruje vaše živé aplikace na [širokou škálu platformy] [ platforms] vám pomohou diagnostikovat problémy s výkonem a porozumět různým způsobům využití.

Existují tři typy výstrah:

* **Metriky výstrahy** zjistíte, kdy metriky protne prahovou hodnotu po nějakou dobu – například dobu odezvy, počty výjimky, využití procesoru nebo zobrazení stránky. 
* [**Webové testy** ] [ availability] zjistíte, pokud váš webový server je k dispozici na Internetu nebo odpovídá pomalu. [Další informace][availability].
* [**Proaktivní diagnostiky** ](app-insights-proactive-diagnostics.md) jsou konfigurovány automaticky k upozornění na neobvyklé výkonu vzory.

Zaměříme na metriky výstrahy v tomto článku.

## <a name="set-a-metric-alert"></a>Nastavení metriky výstrahy
Otevřete okno pravidla výstrah a pak pomocí tlačítka Přidat. 

![V okně pravidla výstrah vyberte Přidat výstrahy. Nastavte aplikaci jako prostředek k měření, zadejte název pro výstrahu a vyberte metriky.](./media/app-insights-alerts/01-set-metric.png)

* Nastavte prostředků než ostatní vlastnosti. **Vyberte prostředek, "(součásti)"** Pokud chcete nastavit výstrahy na metriky výkonu a využití.
* Název, který přiřadíte výstrahy musí být jedinečný v rámci skupiny prostředků (ne jenom aplikace).
* Pečlivě si uvědomit a jednotky, ve kterých budete vyzváni k zadání prahovou hodnotu.
* Pokud zaškrtnete políčko "E-mailu vlastníky...", výstrahy se posílají e-mailem pro každého, kdo má přístup do této skupiny prostředků. Rozšířit tato sada osoby, přidejte je do [skupiny prostředků nebo předplatného](app-insights-resources-roles-access-control.md) (ne prostředků).
* Pokud zadáte "Další e-mailů", výstrahy se posílají pro tyto skupiny (jestli je zaškrtnuté políčko "e-mailu vlastníky...") nebo jednotlivce. 
* Nastavte [webhooku adresu](../monitoring-and-diagnostics/insights-webhooks-alerts.md) Pokud jste vytvořili webovou aplikaci, která reaguje na výstrahy. Je volána při aktivaci výstrahy i při vyřešení. (Ale Všimněte si, že se v současné době, parametry dotazu nejsou předána jako vlastnosti webhooku.)
* Můžete zakázat nebo povolit upozornění: zobrazení tlačítek v horní části okna.

*Tlačítko Přidat oznámení se nezobrazí.* 

* Používáte účet organizace? Výstrahy můžete nastavit, pokud máte vlastníka nebo přispěvatele přístup k tomuto prostředku aplikace. Podívejte se na v okně řízení přístupu. [Další informace o řízení přístupu][roles].

> [!NOTE]
> V okně výstrahy zjistíte, že je již sadu výstrah: [proaktivní diagnostiky](app-insights-proactive-failure-diagnostics.md). Automatické výstrahy monitoruje jeden konkrétní metriky, žádost míra selhání. Pokud se rozhodnete zakázat proaktivní výstrahy, nemusíte nastavit vlastní upozornění na selhání rychlost požadavků. 
> 
> 

## <a name="see-your-alerts"></a>Zobrazit oznámení
Zobrazí se e-mailu, když do stavu výstrahy změny mezi neaktivní a aktivní. 

Aktuální stav každé výstraze se zobrazí v okně pravidla výstrah.

Je souhrn poslední aktivitu v upozorněních rozevíracího seznamu:

![Rozevírací seznam výstrah](./media/app-insights-alerts/010-alert-drop.png)

Historie změn stavů, se v protokolu aktivit:

![V okně Přehled klikněte na tlačítko Nastavení, protokoly auditu](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak výstrahy fungují
* Výstraha má tři stavy: "Nikdy neaktivoval", "Aktivované" a "Přeložit". Aktivovaný znamená podmínku, kterou jste zadali byla nastavena hodnota true, pokud bylo naposled vyhodnoceno.
* Oznámení se vygeneruje, když výstrahu změny stavu. (Pokud podmínka upozornění již byla hodnota true, pokud jste vytvořili výstrahu, nemusí získáte oznámení dokud podmínku přejde false.)
* Jednotlivá oznámení vygeneruje e-mailu v případě, že je zaškrtnuté políčko e-mailů, nebo zadat e-mailové adresy. Můžete také zobrazit v rozevíracím seznamu oznámení.
* Výstraha je vyhodnocován pokaždé, když dorazí metriky, ale není jinak.
* Hodnocení agreguje metriku předchozí období a porovná je prahová hodnota, na zjištění nového stavu.
* Dobu, po kterou zvolíte, určuje interval, za které se shromažďují metriky. Nemá vliv, jak často je vyhodnocován výstrahy: to závisí na četnosti příchodem metrik.
* Pokud žádná data doručení pro konkrétní metriku nějakou dobu, mezeru má jiný účinky na výstrahy vyhodnocení a grafy v Průzkumníku metriky. V Průzkumníku metriky Pokud je vidět žádná data po dobu delší než interval vzorkování grafu, graf zobrazuje hodnotu 0. Ale výstrahy založené na stejné metriky není znovu vyhodnocena, a výstrah stavu zůstává beze změny. 
  
    Pokud data nakonec dorazí, grafu přejde zpět na hodnotu nula. Vyhodnotí výstrahy na základě dat po dobu určenou je k dispozici. Nový bod dat je pouze jeden, který je k dispozici v období, agregace je založen na právě na datových bodů.
* Výstrahu můžete často blikat mezi stavy výstrah a v pořádku, i když nastavíte dlouhou dobu. To může dojít, pokud hodnota metriky bude umístěn kolem prahovou hodnotu. Neexistuje žádné hystereze v prahové hodnotě: přechod výstraha se odehrává na stejnou hodnotu jako přechodu na v pořádku.

## <a name="what-are-good-alerts-to-set"></a>Jaké jsou dobrou výstrahy nastavit?
To závisí na vaší aplikace. Abyste mohli začít je nejlepší nastavit příliš mnoho metriky. Věnujte nějaký čas prohlížení vaší metriky grafy, když aplikace běží na podívat, jak se chová normálně. Tento postup vám pomůže najít způsobů, jak zlepšit jeho výkon. Potom nastavte výstrahy říct, kdy metriky výlet za hranice normální zóny. 

Oblíbené výstrahy patří:

* [Prohlížeč metriky][client], zejména prohlížeče **časů načtení stránky**, jsou vhodné pro webové aplikace. Pokud stránka má mnoho skriptů, je vhodné vyhledat **výjimky prohlížeče**. Chcete-li získat tyto metriky a výstrahy, budete muset nastavit [monitorování webové stránky][client].
* **Doba odezvy serveru** pro webové aplikace na straně serveru. A také nastavení výstrah, dohlížet na tato metrika chcete zobrazit, pokud ho se liší podle nepřiměřeně vysoké požadavků: variace, může znamenat, vaše aplikace může být nedostatek prostředků. 
* **Výjimky serveru** – Pokud chcete vidět, budete muset provést některé [další nastavení](app-insights-asp-net-exceptions.md).

Nezapomeňte, že [proaktivní selhání míra diagnostiky](app-insights-proactive-failure-diagnostics.md) automaticky monitorovat rychlost, jakou aplikace reaguje na požadavky s kódy selhání. 

## <a name="automation"></a>Automation
* [Použití Powershellu k automatizaci nastavení výstrah](app-insights-powershell-alerts.md)
* [Pomocí webhooků automatizovat reagovat na výstrahy](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Další informace najdete v tématech
* [Testy dostupnosti webu](app-insights-monitor-web-app-availability.md)
* [Automatizovat nastavení výstrah](app-insights-powershell-alerts.md)
* [Proaktivní diagnostiky](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

