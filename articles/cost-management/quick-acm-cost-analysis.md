---
title: Rychlý start – Prozkoumání nákladů na Azure pomocí analýzy nákladů | Microsoft Docs
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/10/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 12b7a605350b07565660e9e4d1334b286aa5ac00
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079102"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý start: Prozkoumání a analýza nákladů pomocí analýzy nákladů

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Je také užitečné znát, kolik vás stojí služby a podpora prostředí a systémů. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Výdajová schémata mohou sloužit k prosazování mechanismů řízení nákladů, jako jsou rozpočty.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Kontrola nákladů v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Požadavky

Analýza nákladů je dostupná pro všechny zákazníky se [smlouvou Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Abyste mohli zobrazit data nákladů, musíte mít alespoň přístup pro čtení k nejméně jednomu z následujících oborů.

- Obor *fakturační účet* je definován v https://ea.azure.com a vyžaduje přístup podnikového správce. Není požadováno žádné nastavení EA. Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechna předplatná ve smlouvě Enterprise. Fakturační účet se často označuje jako *smlouva Enterprise* nebo *registrace*.

- Obor *oddělení* je definován v https://ea.azure.com a vyžaduje přístup správce oddělení. Je požadováno, aby na portálu EA bylo povoleno nastavení **DA view charges** (Správce oddělení může zobrazit náklady). Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechna předplatná patřící do registračního účtu spojeného s oddělením.

- Rozsah *registrační účet* je definován v https://ea.azure.com a vyžaduje přístup vlastníka účtu. Je požadováno, aby na portálu EA bylo povoleno nastavení **AO view charges** (Vlastník účtu může zobrazit náklady). Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechna předplatná patřící do daného registračního účtu. Registrační účet s se často označuje jako *vlastník účtu*.

- Obor *skupina pro správu* je definován v https://portal.azure.com a vyžaduje přístup ke čtení správy nákladů (nebo Čtenář). Je požadováno, aby na portálu EA bylo povoleno nastavení **AO view charges** (Vlastník účtu může zobrazit náklady). Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechna předplatná pod skupinou pro správu.

- Obor *předplatné* je definován v https://portal.azure.com a vyžaduje přístup ke čtení správy nákladů (nebo oprávnění Čtenář). Je požadováno, aby na portálu EA bylo povoleno nastavení **AO view charges** (Vlastník účtu může zobrazit náklady). Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechny prostředky a skupiny prostředků v předplatném.

- Obor *skupina prostředků* je definován v https://portal.azure.com a vyžaduje přístup ke čtení správy nákladů (nebo oprávnění Čtenář). Je požadováno, aby na portálu EA bylo povoleno nastavení **AO view charges** (Vlastník účtu může zobrazit náklady). Informace o fakturaci jsou v analýze nákladů konsolidovány pro všechny prostředky ve skupině prostředků.



Další informace o konfiguraci nastavení **DA view charges** a **AO view charges** najdete v článku [Povolení přístupu k nákladům](../billing/billing-enterprise-mgmt-grp-troubleshoot-cost-view.md#enabling-access-to-costs).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Pokud chcete zkontrolovat náklady pomocí analýzy nákladů, přejděte na webu Azure Portal na **Správa nákladů a fakturace** &gt; **Správa nákladů** &gt; **Změnit obor**, zvolte obor a klikněte na **Vybrat**.

Vybraný obor se použije v celé službě Cost Management k poskytování konsolidace dat a k řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho vyberete rozsáhlejší obor, který zahrnuje ostatní obory, a potom vyfiltrujete přesně to, co potřebujete. To je důležité vědět, protože někteří uživatelé nemusí mít přístup k nadřazenému oboru, který zahrnuje data z podřízených oborů.

Klikněte na **Otevřít analýzu nákladů**.

Počáteční zobrazení analýzy nákladů zahrnuje tyto oblasti:

**Celkem** – zobrazuje celkové náklady za aktuální měsíc.

**Rozpočet** – zobrazuje plánovaný limit útraty pro vybraný obor (pokud je k dispozici).

**Kumulované náklady** – zobrazuje celkovou denní útratu od začátku měsíce. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Kontingenční (prstencové) grafy** – poskytují dynamické pivoty a rozdělení nákladů podle společné sady standardních vlastností. Zobrazují nejaktuálnější náklady pro aktuální měsíc. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou standardně zařazeny do těchto kategorií: služba (kategorie měřiče), umístění (oblast) a podřízený obor. Například registrační účty spadají pod fakturační účty, skupiny prostředků pod předplatná a prostředky pod skupiny prostředků.

![Počáteční zobrazení analýzy nákladů](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Přizpůsobení zobrazení nákladů

Výchozí zobrazení poskytuje rychlé odpovědi na běžné otázky, jako jsou:

- Kolik jsem utratil(a)?
- Dodržím rozpočet?

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí výběru data můžete rychle přepnout na minulý měsíc, tento měsíc, toto kalendářní čtvrtletí, tento kalendářní rok, ale můžete i vybrat vlastní rozsah kalendářní dat. Výběr minulého měsíce představuje nejrychlejší způsob analýzy nejnovější faktury Azure a snadného odsouhlasení poplatků. Možnosti aktuálního čtvrtletí a roku usnadňují sledování nákladů oproti dlouhodobým rozpočtům. Můžete také vybrat jiný rozsah kalendářních dat. Můžete například vybrat jeden den, posledních sedm dnů nebo jakékoli období až rok do minulosti od aktuálního měsíce.

![Výběr kalendářního data](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Kumulované náklady zahrnují všechny náklady za každý den plus předchozí dny pro neustále rostoucí zobrazení denně generovaných nákladů. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočet, denní zobrazení také zobrazuje odhad toho, jak může vypadat váš denní rozpočet. Pokud vaše denní náklady neustále překračují odhadovaný denní rozpočet, můžete očekávat, že překročíte měsíční rozpočet. Odhadovaný denní rozpočet jednoduše představuje prostředek, který vám pomůže vizualizovat rozpočet na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

![Denní zobrazení](./media/quick-acm-cost-analysis/daily-view.png)

Výběrem možnosti **Seskupit podle** můžete vybrat kategorii skupiny a změnit data zobrazená v horním celkovém plošném grafu. Seskupení vám umožňuje rychle zobrazit, jak jsou vaše útraty kategorizovány podle typu prostředku. Tady je zobrazení nákladů na služby Azure pro zobrazení minulého měsíce.

![Seskupené denní kumulované zobrazení](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Kontingenční grafy pod horním celkovým zobrazením znázorňují zobrazení pro různé kategorie seskupení a filtrování. Když vyberete kteroukoli kategorii seskupení, zobrazí se v dolní části zobrazení úplná sada dat pro celkové zobrazení. Tady je příklad skupin prostředků.

![Úplná data pro aktuální zobrazení](./media/quick-acm-cost-analysis/full-data-set.png)

Na předchozím obrázku jsou znázorněny názvy skupin prostředků. Zobrazení značek prostředků není dostupné v žádném zobrazení, filtrech ani seskupeních analýzy nákladů.

Při seskupování nákladů podle konkrétního atributu je zobrazeno deset nejvýznamnějších přispěvatelů k nákladům, od nejvyššího k nejnižšímu. Pokud je skupin více než deset, je zobrazeno devět horních přispěvatelů a potom skupina **Ostatní**, která pokrývá všechny zbývající skupiny dohromady.

Prostředky virtuálních počítačů, sítě a úložiště *Classic* (Azure Service Management nebo ASM) nesdílí podrobná data o fakturaci. Ta jsou při seskupování nákladů sloučena do položky **Classic services**.


## <a name="download-cost-analysis-data"></a>Stažení dat analýzy nákladů

Informace z analýzy dat si můžete **stáhnout** a vygenerovat tak soubor CSV pro veškerá data aktuálně zobrazená na webu Azure Portal. Všechny použité filtry a seskupení jsou v souboru zahrnuty. Do souboru CSV se zahrnou i podkladová data pro horní celkový graf, která nejsou aktivně zobrazena.

## <a name="next-steps"></a>Další kroky

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
