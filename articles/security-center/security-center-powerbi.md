---
title: Přehledné znázornění dat z Azure Security Center v řešení Power BI | Dokumentace Microsoftu
description: Balíček obsahu Azure Security Center Power BI usnadňuje hledání výstrah zabezpečení, doporučení, prostředků vystavených útoku a trendů na základě datové sady vytvořené pro účely generování sestav.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: 0ded6bc7-52e8-43b4-8940-0bee137526e3
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: yurid
ms.openlocfilehash: 10f7b8f20cc41a5ebb1b1376e2bf17be02600ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22715824"
---
# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Přehledné znázornění dat z Azure Security Center v řešení Power BI
[Řídicí panel Power BI](http://aka.ms/azure-security-center-power-bi) pro Azure Security Center umožňuje zobrazovat, analyzovat a filtrovat doporučení a výstrahy zabezpečení odkudkoli, třeba i z vašeho mobilního zařízení. Pomocí řídicího panelu Power BI můžete odhalovat trendy a vzorce útoků – stačí zobrazit výstrahy zabezpečení podle prostředku nebo zdrojové IP adresy a nevyřešená rizika zabezpečení podle prostředku nebo stáří.

Taky můžete doporučení a výstrahy zabezpečení ze služby Security Center zajímavě zkombinovat s jinými daty, třeba pomocí dat z [protokolů auditu Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) a [auditů Azure SQL Database](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Oboje poskytuje řídicí panely Power BI a vy můžete tato data exportovat do Excelu, kde jde snadno vytvářet sestavy stavu zabezpečení cloudových prostředků.

## <a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Přístup k řešení Power BI pomocí řídicího panelu Azure Security Center
K sestavám Power BI můžete získat přístup také pomocí řídicího panelu Azure Security Center. To provedete pomocí tohoto postupu:

1. Na řídicím panelu **Azure Security Center** klikněte na tlačítko **Power BI**.

    ![Připojení ke službě Azure Security Center pomocí Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-1-newUI-2017.png)
2. Napravo se otevře okno **Power BI**, jak vidíte na následující obrazovce:

    ![Připojení ke službě Azure Security Center pomocí Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new11-2017.png)
3. Pokud vytváříte řídicí panel Power BI poprvé, můžete si v okně **Prozkoumání v Power BI** vybrat některou z následujících možností:

   * **Řídicí panel přehledů o zabezpečení**: Tuto možnost si vyberte, pokud chcete vytvořit řídicí panel, který obsahuje stav zabezpečení, hrozby a zjištěné výstrahy. Tuto možnost obvykle používají osoby s rolí DevOps, které zodpovídají za analýzu stavu ochrany a zjištěných výstrah v rámci předplatných.
   * **Policy management dashboard** (Řídicí panel správy zásad): Tuto možnost vyberte, pokud chcete prozkoumat zásadu správy a vynucování pravidel.  Tuto zásadu obvykle používají osoby z centrálního oddělení IT, které se zaměřují na řízení. Pomocí tohoto řídicího panelu můžou získat přehled o dodržování zásad zabezpečení v rámci organizace.
   * Pokud už nějaký řídicí panel Power BI máte, klikněte na **Go to your current Power BI dashboard** (Přejít na aktuální řídicí panel Power BI).
4. Pro účely tohoto příkladu klikněte na možnost **Řídicí panel přehledů o zabezpečení**. Pokud je to poprvé, co vytváříte řídicí panel Power BI pro Security Center, budete vyzváni k instalaci balíčku obsahu. V okně **Balíčky obsahu pro Power BI**klikněte na tlačítko **Získat**, jak je znázorněno na následující obrazovce:

    ![Přehledný řídicí panel zabezpečení Azure Security Center](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)
5. Zobrazí se okno **Connect to Azure Security Center Security Insights** (Připojit k přehledům o zabezpečení Azure Security Center). Ujistěte se, že je u položky **Authentication method** (Metoda ověřování) nastavená možnost **oAuth2**, jak je zobrazeno dál, a klikněte na tlačítko **Sign in** (Přihlásit).

    ![Authentication](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)
6. Můžete být vyzváni k opakovanému ověření pomocí přihlašovacích údajů Azure. Po ověření se vytvoří řídicí panel. Po vytvoření řídicího panelu se zobrazí sestava s podobnou strukturou, jak je znázorněno na následující obrazovce:

    ![Řídicí panel Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)

> [!NOTE]
> Podle plánu se má každý den provádět aktualizace této sestavy. Pokud aktualizace neprobíhá, získáte další informace o odstraňování potíží v článku [Možné problémy s aktualizací Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/).
>
>

Uvidíte tady řadu výstrah zabezpečení a doporučení a také řadu virtuálních počítačů, databází SQL Azure a síťových prostředků sledovaných pomocí služby Azure Security Center.

Odkaz na Azure Security Center vás přesměruje na portál Azure. Grafy usnadňují znázornění informací o doporučeních zabezpečení a výstrahách, včetně těchto informací:

* Resource Security State (Stav zabezpečení prostředků)
* Pending Recommendations (Nevyřízená doporučení)
* Doporučení pro virtuální počítače
* Alerts over Time (Výstrahy v průběhu času)
* Attacked Resources (Napadené prostředky)
* Attacked IPs (Napadené IP adresy)

Za každým grafem najdete další statistiky. Další informace zobrazíte výběrem příslušné dlaždice. Třeba dlaždice **Resource Security State** (Stav zabezpečení prostředků) obsahuje další podrobnosti o nevyřízených doporučeních podle prostředků, jak vidíte na následující obrazovce:

![Doporučení](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Pokud kliknete na kterýkoli řádek v tomto grafu, ostatní řádky se zobrazí šedě a vy se zaměříte jenom na ten vybraný. Pokud se chcete vrátit na řídicí panel, klikněte v levém podokně této stránky na **Azure Security Center** pod možností **Dashboards** (Řídicí panely).

> [!NOTE]
> Pokud chcete sestavy přizpůsobit a přidat do nich další pole nebo změnit stávající vizuální prvky, můžete sestavu upravit. Další informace najdete v článku [Interakce se sestavou v zobrazení pro úpravy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/).
>
>

Dlaždice **Alerts over Time, Attacked Resources** (Výstrahy v průběhu času, Napadené prostředky) a **Attacker IPs** (IP adresy útočníků) mají podobný výstup, když na ně kliknete. Je to proto, že sestava shromáždí informace týkající se všech těchto tří proměnných, a nazve je **Resources under Attack** (Napadené prostředky), jak vidíte na následující obrazovce:

![Napadené prostředky](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Teď si můžete uložit kopii sestavy, vytisknout ji nebo ji zveřejnit na webu pomocí možností dostupných v nabídce **File** (Soubor).

![Nabídka Soubor](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Prozkoumání dat z Azure Security Center pomocí služeb Power BI
V Power BI se připojte ke službám [Power BI Content Pack Services](https://msit.powerbi.com/groups/me/getdata/services) a proveďte následující kroky:

1. V okně **Content Pack for Power BI** (Balíček obsahu pro Power BI) uvidíte dvě možnosti, jak znázorňuje následující obrázek.

    ![Balíček obsahu pro Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

   > [!NOTE]
   > Pokud jste už provedli první část tohoto článku, uvidíte jenom jednu možnost, kterou je Azure Security Center Policy Management (Správa zásad Azure Security Center).
   >
   >
2. Pro účely tohoto příkladu klikněte na dlaždici **Azure Security Center Policy Management** (Správa zásad Azure Security Center) na **Get** (Načíst).
3. V okně **Connect to Azure Security Center Policy Management** (Připojit se ke správě zásad Azure Security Center) vyberte v rozevíracím seznamu **Authentication Method** (Metoda ověřování) možnost **oAuth2**, jak vidíte na následujícím obrázku, a klikněte na tlačítko **Sign in** (Přihlásit).

    ![Okno správy zásad](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)
4. Přejdete na stránku ověřování, kde byste měli zadat přihlašovací údaje, pomocí kterých se přihlašujete do služby Azure Security Center. Po dokončení procesu ověřování začne Power BI importovat data pro vytvoření vašich sestav. Během toho se může v pravém rohu prohlížeče zobrazovat následující zpráva:

    ![Připojení ke službě Azure Security Center pomocí Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

   > [!NOTE]
   > První vytváření řídicího panelu může trvat o něco delší dobu, hlavně v situacích, kdy máte víc předplatných.
   >
   >
5. Po dokončení procesu váš řídicí panel Azure Security Center Power BI načte sestavu **Policy Management** (Správa zásad), podobnou následující:

    ![Řídicí panel správy zásad](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Viz také
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center používat řešení Power BI. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat přechod na Azure Security Center.
* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak se v Azure Security Center konfigurují zásady zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
