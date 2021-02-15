---
title: Vizualizace pro analýzu změn aplikace – Azure Monitor
description: Naučte se používat vizualizace při analýze změn aplikací v Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 643645eb3b361cd68def1177ba7e8af89f1963bc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520969"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Vizualizace pro analýzu změn aplikace (Preview)

## <a name="standalone-ui"></a>Samostatné uživatelské rozhraní

V Azure Monitor je k dispozici samostatné podokno pro analýzu změn pro zobrazení všech změn s přehledem o závislostech a prostředcích aplikací.

Pokud chcete spustit prostředí, vyhledejte na panelu hledání možnost analýza změn Azure Portal.

![Snímek obrazovky s vyhledáváním analýzy změn v Azure Portal](./media/change-analysis/search-change-analysis.png)

Všechny prostředky v rámci vybraného předplatného se zobrazí se změnami za posledních 24 hodin. Za účelem optimalizace výkonu načítání stránek služba zobrazuje 10 prostředků najednou. Kliknutím na další stránku zobrazíte další prostředky. Pracujeme na odebrání tohoto omezení.

![Snímek obrazovky okna pro změnu analýzy v Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Kliknutím na prostředek zobrazíte všechny jeho změny. V případě potřeby přejděte k podrobnostem o změně a zobrazte si podrobnosti o změně formátu JSON a přehledy.

![Snímek obrazovky s podrobnostmi o změně](./media/change-analysis/change-details.png)

U jakékoli zpětné vazby použijte tlačítko Odeslat zpětnou vazbu nebo e-mail changeanalysisteam@microsoft.com .

![Snímek obrazovky s tlačítkem pro zpětnou vazbu na kartě změna analýzy](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Podpora více předplatných

Uživatelské rozhraní podporuje výběr více předplatných pro zobrazení změn prostředků. Použijte filtr předplatného:

![Snímek obrazovky s filtrem předplatným, který podporuje výběr více předplatných](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnostika a řešení problémů webové aplikace

V Azure Monitor je analýza změn integrovaná i v prostředí pro samoobslužnou **diagnostiku a řešení problémů** . K tomuto prostředí se dostanete ze stránky **Přehled** vaší aplikace App Service.

![Snímek obrazovky s tlačítkem "Přehled" a "diagnostikovat a řešit problémy"](./media/change-analysis/change-analysis.png)

## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analýza změn aplikace v nástroji Diagnostika a řešení problémů

Analýza změn aplikace je samostatný detektor ve webové aplikaci diagnostikuje a řeší problémy nástroje. Je také agregovaná v případě **chyb aplikací** a **detektorů pro webové aplikace**. Když zadáte nástroj Diagnostika a řešení problémů, poskytovatel prostředků **Microsoft. ChangeAnalysis** se automaticky zaregistruje. Postupujte podle těchto pokynů a povolte sledování změn v hostovaném webovém aplikaci.

1. Vyberte **dostupnost a výkon**.

    ![Snímek možností Poradce při potížích s dostupností a výkonem](./media/change-analysis/availability-and-performance.png)

2. Vyberte **změny aplikace**. Funkce je také k dispozici v případě **chyb aplikace**.

   ![Snímek obrazovky s tlačítkem pro zhroucení aplikace](./media/change-analysis/application-changes.png)

3. Odkaz má za následek vymezení uživatelského rozhraní analýzy změn aplikace v rámci webové aplikace. Pokud není zapnuté sledování změn ve webové aplikaci, použijte k získání změn nastavení souborů a aplikací informační zprávu.

   ![Snímek obrazovky s možnostmi zhroucení aplikací](./media/change-analysis/enable-changeanalysis.png)

4. Zapněte **analýzu změn** a vyberte **Uložit**. Tento nástroj zobrazí všechny webové aplikace v rámci plánu App Service. Pomocí přepínače úroveň plánu můžete zapnout analýzu změn pro všechny webové aplikace v rámci plánu.

    ![Snímek obrazovky s uživatelským rozhraním povolit analýzu změn](./media/change-analysis/change-analysis-on.png)

5. Možnost změnit data je také k dispozici v rozevíracích selektorech pro výběr **webové aplikace** a při **selhání aplikace** . Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách. Ve výchozím nastavení se zobrazí změny za posledních 24 hodin, které vám pomůžou s okamžitými problémy.

     ![Snímek obrazovky se zobrazením rozdílů změn](./media/change-analysis/change-view.png)

## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostika a řešení problémů s virtuálním počítačem

Přejít na nástroj Diagnostika a řešení problémů pro virtuální počítač.  Přejděte na **Nástroje pro řešení potíží**, přejděte na stránku a vyberte **analyzovat poslední změny** a zobrazte změny na virtuálním počítači.

![Snímek obrazovky s diagnostikou a řešením problémů s virtuálním počítačem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analyzátor změn v nástrojích pro řešení potíží](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Historie změn protokolu aktivit

Funkce [Zobrazit historii změn](../platform/activity-log.md#view-change-history) v protokolu aktivit volá back-end služby Analysis Service v aplikaci, aby se získaly změny přidružené k operaci. **Historii změn** , která se používá k přímému volání [Azure Resource graphu](../../governance/resource-graph/overview.md) , ale přeměnila back-end na volání analýzy změn aplikace, takže vrácené změny budou zahrnovat změny úrovně prostředků z [Azure Resource graphu](../../governance/resource-graph/overview.md), vlastnosti prostředku z [Azure Resource Manager](../../azure-resource-manager/management/overview.md)a změny v hostu z PaaS Services, jako je App Services webová aplikace. Aby mohla služba Analysis Services pro změny aplikace kontrolovat změny v předplatných uživatelů, musí být zaregistrovaný poskytovatel prostředků. Při prvním zadání karty **historie změn** se nástroj automaticky spustí, aby zaregistroval poskytovatele prostředků **Microsoft. ChangeAnalysis** . Po registraci budou změny z **Azure Resource Graph** k dispozici okamžitě a budou zahrnovat posledních 14 dní. Změny z jiných zdrojů budou k dispozici po přibližně 4 hodinách po zprovoznění předplatného.

![Integrace historie změn protokolu aktivit](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integrace se službou VM Insights

Uživatelé s povoleným virtuálním [počítačem Insights](../insights/vminsights-overview.md) můžou zobrazit, co se změnilo ve svých virtuálních počítačích, které by mohly způsobovat nějaké špičky v grafu metrik, jako je například procesor nebo paměť. Data změny jsou integrovaná v navigačním panelu na straně virtuálních počítačů Insights. Uživatel může zobrazit, jestli na virtuálním počítači došlo ke změnám, a vybrat **prozkoumat změny** a zobrazit podrobnosti o změně v uživatelském rozhraní pro samostatné analýzy změn aplikace.

[![Integrace se službou VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Naučte se [řešit problémy při změně analýzy](change-analysis-troubleshoot.md) .
