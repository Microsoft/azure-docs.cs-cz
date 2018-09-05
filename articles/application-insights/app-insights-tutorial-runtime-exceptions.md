---
title: Diagnostika výjimek za běhu pomocí Azure Application Insights | Dokumentace Microsoftu
description: Kurz popisující, jak v aplikaci vyhledat a diagnostikovat výjimky za běhu pomocí Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 7c2e67605cd2489f2c8d9da5ac80386056464afa
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815109"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Vyhledání a diagnostika výjimek za běhu pomocí Azure Application Insights

Azure Application Insights shromažďuje telemetrii z vaší aplikace, kterou můžete použít k identifikaci a diagnostice výjimek za běhu.  Tento kurz vás provede tímto procesem s vaší aplikací.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Úprava projektu, aby umožňoval sledování výjimek
> * Identifikace výjimek v různých součástech aplikace
> * Zobrazení podrobností o výjimce
> * Stažení snímku výjimky do Visual Studia pro ladění
> * Analýza podrobností o nezdařených požadavcích pomocí dotazovacího jazyka
> * Vytvoření nové pracovní položky pro opravu chybného kódu


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

- Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure
- Stáhnout a nainstalovat [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger).
- Povolit [Visual Studio Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger).
- Nasadit aplikaci .NET do Azure a [povolit sadu Application Insights SDK](app-insights-asp-net.md). 
- Kurz popisuje identifikaci výjimky ve vaší aplikaci, proto upravte kód ve vašem prostředí vývoj nebo testování, aby generoval výjimku. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analýza selhání
Application Insights shromažďuje informace o selháních v aplikaci a umožňuje zobrazit jejich četnost pro různé operace, abyste se mohli zaměřit na ty s nejvyšším dopadem.  Potom můžete přejít k podrobnostem a identifikovat hlavní příčinu selhání.   

1. Vyberte **Application Insights** a pak své předplatné.  
2. Otevřete panel **Selhání** buď výběrem možnosti **Selhání** v nabídce **Prozkoumat**, nebo kliknutím na graf **Neúspěšné požadavky**.

    ![Neúspěšné požadavky](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. Panel **Neúspěšné požadavky** ukazuje počet neúspěšných požadavků a počet ovlivněných uživatelů pro každou operaci v aplikaci.  Seřazením těchto informací podle počtu uživatelů můžete identifikovat selhání, která uživatele ovlivňují nejvíc.  V tomto příkladu jsou hlavními kandidáty na bližší zkoumání operace **GET Employees/Create** a **GET Customers/Details**, protože selhávají nejčastěji a ovlivňují nejvíc uživatelů.  Výběrem operace zobrazíte v pravém panelu další informace.

    ![Panel Neúspěšné požadavky](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. Zmenšením časového okna si přibližte období, kde má míra selhání špičku.

    ![Okno Neúspěšné požadavky](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. Kliknutím na tlačítko s počtem vyfiltrovaných výsledků zobrazíte související ukázky. „Navrhované“ ukázky obsahují související telemetrii ze všech komponent, a to i v případě, že v některých z nich bylo aktivní vzorkování. Kliknutím na výsledek hledání zobrazíte podrobnosti o selhání.

    ![Ukázky neúspěšných požadavků](media/app-insights-tutorial-runtime-exceptions/failed-requests-search.png)

6. Podrobnosti o neúspěšném požadavku se zobrazí v Ganttově grafu, který ukazuje, že v této transakci došlo ke dvěma chybám závislostí, které také přispěly k více než 50 % celkové doby trvání transakce. V tomto prostředí se zobrazuje veškerá telemetrie ze všech komponent distribuované aplikace, které souvisejí s tímto ID operace. [Další informace o novém prostředí](app-insights-transaction-diagnostics.md). Výběrem libovolné položky můžete na pravé straně zobrazit její podrobnosti. 

    ![Podrobnosti neúspěšné žádosti](media/app-insights-tutorial-runtime-exceptions/failed-request-details.png)

7. Podrobnosti o operaci zahrnují také výjimku FormatException, která pravděpodobné způsobila selhání.  Uvidíte, že je z důvodu neplatného PSČ. V sadě Visual Studio můžete otevřít snímek ladění a zobrazit informace o ladění na úrovni kódu.

    ![Podrobnosti výjimky](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Identifikace chyb kódu
Snapshot Debugger shromažďuje snímky nejčastěji se vyskytujících výjimek v aplikaci, aby vám pomohl při diagnostice jejich hlavní příčiny v produkčním prostředí.  Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Zdrojový kód pak můžete ladit stažením snímku a jeho otevřením v aplikaci Visual Studio 2017.

1. Ve vlastnostech výjimky klikněte na tlačítko **Otevřít snímek ladění**.
2. Otevře se panel **Snímek Ladění** se zásobníkem volání pro daný požadavek.  Kliknutím na kteroukoli metodu můžete zobrazit hodnoty všech místních proměnných v době požadavku.  Počínaje hlavní metodou v tomto příkladu vidíte místní proměnné, které nemají žádnou hodnotu.

    ![Snímek ladění](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. První volání, která má platné hodnoty, je **ValidZipCode**, a my vidíme, že směrovací číslo bylo zadáno s písmeny, takže ho není možné převést na celé číslo.  To vypadá jako chyba v kódu, kterou je třeba opravit.

    ![Snímek ladění](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Chcete-li stáhnout tento snímek do aplikace Visual Studio a najít tak příslušný kód, který je třeba opravit, klikněte na **Stáhnout snímek**.
6. Snímek se načte do aplikace Visual Studio.
7. Teď můžete spustit v aplikaci Visual Studio relaci ladění, která rychle najde řádek kódu, který výjimku způsobil.

    ![Výjimka v kódu](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Použití analytických dat
Všechna data shromážděná pomocí Application Insights jsou uložená ve službě Azure Log Analytics, která nabízí plnohodnotný dotazovací jazyk pro analýzu dat mnoha různými způsoby.  Tato data můžete použít k analýze požadavků, které generují výjimku, kterou zkoumáme. 

8. Kliknutím na informace CodeLens nad kódem zobrazíte telemetrii poskytovanou službou Application Insights.

    ![Kód](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Kliknutím na **Analyzovat dopad** otevřete analýzu Application Insights Analytics.  Otevře se s několika dotazy, které poskytují podrobné informace o neúspěšných požadavcích, například počet ovlivněných uživatelů, prohlížeče a oblasti.<br><br>![Analýzy](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili, jak identifikovat výjimky za běhu, můžete přejít k dalšímu kurzu, ve kterém zjistíte, jak identifikovat a diagnostikovat potíže s výkonem.

> [!div class="nextstepaction"]
> [Identifikace potíží s výkonem](app-insights-tutorial-performance.md)