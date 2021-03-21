---
title: Telemetrie Application Insights ve Visual Studio CodeLens | Dokumentace Microsoftu
description: Přistupujte rychle k požadavkům Application Insights a telemetrii výjimek pomocí CodeLens v sadě Visual Studio.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2cfc4e06dcb5671f34ff98794c9569b6ed5ddae7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94538491"
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Telemetrie Application Insights ve Visual Studio CodeLens
Metody v kódu vaší webové aplikace mohou být opatřeny poznámkami s telemetrií o výjimkách za běhu a časech odezvy na požadavky. Pokud ve své aplikaci nainstalujete [Azure Application Insights](./app-insights-overview.md), telemetrie se zobrazí ve Visual Studio [CodeLens](/visualstudio/ide/find-code-changes-and-other-history-with-codelens?view=vs-2015) s poznámkami v horní části každé funkce, kde jste zvyklí vídat užitečné informace, jako například počet míst, ze kterých se na funkci odkazuje, nebo jméno poslední osoby, která ji upravila.

![CodeLens](./media/visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> Application Insights v CodeLens je k dispozici v sadě Visual Studio 2015 Update 3 a novější nebo s nejnovější verzí [rozšíření Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a). CodeLens je k dispozici v edicích Enterprise a Professional sady Visual Studio.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Kde najít data Application Insights
V indikátorech CodeLens veřejných metod žádostí vaší webové aplikace vyhledejte telemetrii Application Insights. Indikátory CodeLens jsou uvedené nad metodami a dalšími deklaracemi v kódu C# a Visual Basic. Pokud jsou pro metodu k dispozici data Application Insights, zobrazí se indikátory požadavků a výjimek, například „100 požadavků, 1 % neúspěšných“ nebo „10 výjimek“. Další podrobnosti zobrazíte kliknutím na indikátor CodeLens. 

> [!TIP]
> Načtení indikátorů požadavků a výjimek Application Insights může trvat o několik sekund déle než načtení dalších indikátorů CodeLens.
> 
> 

## <a name="exceptions-in-codelens"></a>Výjimky v CodeLens
![Snímek obrazovky zobrazuje výjimky 47 zobrazené v CodeLens.](./media/visual-studio-codelens/codelens-exceptions.png)

Indikátor výjimek CodeLens ukazuje počet výskytů výjimek pro 15 nejčastěji se vyskytujících výjimek ve vaší aplikaci v posledních 24 hodinách během zpracování požadavku, který metoda obsluhuje.

Chcete-li zobrazit podrobnosti, klikněte na indikátor výjimek CodeLens:

* Procentuální změna počtu výjimek z posledních 24 hodin vzhledem k předchozím 24 hodinám.
* Chcete-li přejít do kódu k funkci, která vyvolává výjimku, zvolte **Přejít ke kódu**.
* Chcete-li se dotázat na všechny instance této výjimky, ke kterým došlo v posledních 24 hodinách, zvolte **Vyhledat**.
* Chcete-li zobrazit vizualizaci trendu výskytu této výjimky v posledních 24 hodinách, zvolte **Trend**.
* Chcete-li se dotázat na všechny výjimky, ke kterým došlo v posledních 24 hodinách, zvolte **Zobrazit všechny výjimky v této aplikaci**.
* Chcete-li zobrazit vizualizaci trendu výskytu všech výjimek v posledních 24 hodinách, zvolte **Prozkoumat trendy výjimek**. 

> [!TIP]
> Pokud v CodeLens uvidíte „0 výjimek“, ale víte, že by tam měly být výjimky, zkontrolujte, že jste v CodeLens vybrali správný prostředek Application Insights. Chcete-li vybrat jiný prostředek, klikněte pravým tlačítkem na svůj projekt v Průzkumníku řešení a zvolte **Application Insights > Zvolit zdroj telemetrie**. CodeLens se zobrazuje pouze pro 15 nejčastěji se vyskytujících výjimek ve vaší aplikaci v posledních 24 hodinách, takže pokud nějaká výjimka není mezi 15 nejčastějšími, zobrazí se „0 výjimek“. Výjimky v zobrazeních ASP.NET se nemusí zobrazit v metodách kontroleru, které tato zobrazení vygenerovaly.
> 
> [!TIP]
> Pokud v CodeLens uvidíte „? výjimek“, je nutné přidružit účet Azure k sadě Visual Studio nebo je možné, že vypršely přihlašovací údaje vašeho účtu Azure. V obou případech klikněte na „? výjimek“, zvolte **Přidat účet...** a zadejte své přihlašovací údaje.
> 
> 

## <a name="requests-in-codelens"></a>Požadavky v CodeLens
![Snímek obrazovky zobrazuje podrobnosti o požadavcích 684, včetně 7% selhání.](./media/visual-studio-codelens/codelens-requests.png)

Indikátor požadavků CodeLens ukazuje počet požadavků HTTP, které metoda obsloužila během posledních 24 hodin, a procentuální podíl neúspěšných požadavků.

Chcete-li zobrazit podrobnosti, klikněte na indikátor požadavků CodeLens:

* Absolutní a procentuální změny počtu požadavků, neúspěšných požadavků a průměrná doba odezvy během posledních 24 hodin ve srovnání s předchozími 24 hodinami.
* Spolehlivost metody, vypočítaná jako procento úspěšných požadavků v posledních 24 hodinách.
* Chcete-li se dotázat na všechny požadavky nebo pouze na neúspěšné požadavky, ke kterým došlo v posledních 24 hodinách, zvolte **Vyhledat** (neúspěšné) požadavky.
* Chcete-li zobrazit vizualizaci trendu požadavků, neúspěšných požadavků nebo průměrné doby odezvy v posledních 24 hodinách, zvolte **Trend**.
* Chcete-li změnit, který prostředek je zdrojem dat pro CodeLens, v levém horním rohu zobrazení podrobností CodeLens zvolte název prostředku Application Insights.

## <a name="next-steps"></a><a name="next"></a>Další kroky
* **[Práce s Application Insights v aplikaci Visual Studio](./visual-studio.md)**. Hledejte telemetrii, zobrazujte data v CodeLens a konfigurujte Application Insights. Vše v sadě Visual Studio. 
* **[Práce s portálem Application Insights](./overview-dashboard.md)**. Řídicí panely, výkonné nástroje pro diagnostiku a analýzy, výstrahy, aktivní mapa závislostí vaší aplikace a export telemetrie. 

