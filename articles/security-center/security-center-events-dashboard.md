---
title: Sledování a zpracování událostí zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak můžete využít řídicí panel Security Center události zobrazíte události zabezpečení z virtuálních počítačů Azure a počítače mimo Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: f1a3268fcacd4083b767a3fe89d6ab9b41b6cceb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114055"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Sledování a zpracování událostí zabezpečení v Azure Security Center
Řídicí panel události poskytuje přehled o počet událostí zabezpečení, které jsou shromážděné v průběhu času a seznam významné události, které mohou vyžadovat vaši pozornost.  

> [!NOTE]
> Pro tuto funkci používat, musí váš pracovní prostor v nich běžet Log Analytics verze 2 a je na Security Center úrovně Standard. Zobrazit Security Center [stránce s cenami](security-center-pricing.md) Další informace o úrovni Standard.
>
>

## <a name="what-is-a-security-event"></a>Co je událost zabezpečení?
Security Center používá agenta Microsoft Monitoring Agent shromažďovat různé zabezpečení související s konfigurací a události z vašeho počítače a uloží tyto události v pracovních prostorech. Příkladem takových dat jsou: protokoly operačního systému (protokoly událostí Windows), spuštění procesů a události z řešení zabezpečení integrované pomocí služby Security Center. Microsoft Monitoring Agent také do vašich pracovní prostorů zkopíruje soubory se stavem systému.

## <a name="events-processed-dashboard"></a>Řídicí panel události byly zpracovány
Přístup **události** řídicího panelu z hlavní nabídky služby Security Center nebo Security Center **přehled** okno.  

![Řídicí panel události byly zpracovány][1]

**Události** dlaždici v části **Security Center** zobrazí počet událostí odesílaných do služby Security Center z počítače mimo Azure a virtuální počítače Azure.

**Řídicí panel události** poskytuje přehled o počtu zpracovaných událostí přesčas a seznam událostí.

 ![Řídicí panel][2]

 V horní polovině řídicího panelu trendů všechny události byly zpracovány za poslední týden. Dolní polovinu řídicího panelu jsou uvedené významné události a všechny události podle typu:

 - **Významné události** zahrnují dotazy na události, které poskytuje Security Center a dotazy na události, které vytvoříte a přidáte. Řídicí panel také poskytuje rychlý přehled o počtu každou významnou událost.
 - **Všechny události podle typu** ukazuje typy událostí, které jsou přijímány a počet pro jednotlivé typy. Příklady typu události: SecurityEvent, CommonSecurityLog, WindowsFirewall a W3CIISLog.

> [!NOTE]
> Významné události zahrnout [vyhodnocování standardních hodnot webu](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Cílem vyhodnocování standardních hodnot webu je najít potenciálně ohrožená nastavení webového serveru.

## <a name="view-processed-event-details"></a>Zobrazit podrobnosti o zpracování událostí
1. V části **Security Center** hlavní nabídky, vyberte **události**.
2. **Řídicí panel události** selektor pracovního prostoru může otevřít. Pokud máte jenom jeden pracovní prostor, nezobrazí se tento selektor pracovního prostoru. Pokud máte více než jeden pracovní prostor, musíte vybrat pracovní prostor zobrazíte její podrobnosti zpracovaných událostí. V seznamu vyberete pracovní prostor, pokud máte více než jednomu pracovnímu prostoru.

  ![Seznam pracovních prostorů][3]

3. **Řídicí panel události** otevře se zobrazí detaily události pro vybraný pracovní prostor. Můžete zobrazit všechny události podle typu a významné události.  V tomto příkladu jsme vybrali **významné události**.

  ![Významná událost][4]

4. Můžete zadat dotaz na další data v části pracovní prostor tak, že vyberete typ události. V tomto příkladu jsme vybrali **SecurityEvent**.

  ![Vyberte typ události][5]

5. **Prohledávání protokolů** otevře se další podrobnosti o typ události.

  ![Prohledávání protokolů][6]

## <a name="add-a-notable-event"></a>Přidat významnou událost
Security Center poskytuje out-of-the-box významné události. Můžete přidat významné události podle vlastní pomocí dotazu [dotazovací jazyk Log Analytics](../log-analytics/log-analytics-search-reference.md). Vrátí na **řídicí panel události** přidat významnou událost.

1. Vyberte **přidat významnou událost**.

  ![Přidat významnou událost][7]

2. **Přidejte vlastní významnou událost** otevře.  V části **zobrazovaný název**, zadejte název pro významnou událost. V části **vyhledávací dotaz**, zadejte dotaz. pro událost.

  ![Zadejte dotaz.][8]

4. Vyberte **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Aktualizujte svůj pracovní prostor pro zpracování událostí
Váš pracovní prostor musí být spuštěn Log Analytics verze 2 a je na Security Center úrovně Standard použít zpracování událostí ve službě Security Center. **Řídicí panel události** identifikuje selektor pracovního prostoru pracovní prostory, které tyto požadavky nesplňují.

![Pracovní prostor nesplňuje požadavky][9]

Pokud řádek pracovního prostoru:

- Obsahuje **vyžaduje aktualizaci** – je potřeba aktualizovat pracovní prostor Log Analytics verze 2
- Obsahuje **UPGRADUJTE plán** – je potřeba upgradovat pracovního prostoru na Security Center úrovně Standard
- Prázdné – pracovní prostor splňuje požadavky a vyberete pracovní prostor přejdete na řídicí panel

> [!NOTE]
> V části **řídicí panel události**, **události** sloupci určuje objem událostí v každém pracovním prostoru.  Tento sloupec je prázdný u některých pracovních prostorů, protože Security Center úrovně Free se použije pro tento pracovní prostor. V části na úrovni Free Security Center bude shromažďovat události ale události nejsou uloženy ve službě Log Analytics a nejsou k dispozici na řídicím panelu.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aktualizovat pracovní prostor Log Analytics verze 2
1. Vyberte pracovní prostor, který **vyžaduje aktualizaci**.
2. **Hledat Upgrade** otevře. Vyberte **upgradovat**.

  ![Upgradovat][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Upgrade na Security Center úrovně Standard
1. Vyberte pracovní prostor s **UPGRADUJTE plán**.
2. **Řídicí panel události** otevře. Vyberte **řídicí panel události zkuste**.

  ![Vyzkoušet řídicí panel][11]

3. V části **připojení k rozšířenému zabezpečení**, vyberte pracovní prostor, kterou upgradujete.
4. V části **ceny**vyberte **standardní**.
5. Vyberte **Uložit**.

  ![Upgradujte na úroveň Standard][12]

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat řídicí panel Security Center událostí. Další informace o tom, jak funguje řídicí panel a psát dotazy, vlastní událost, najdete v tématu:

- [Co je služba Log Analytics?](../log-analytics/log-analytics-overview.md) – Přehled v Log Analytics
- [Principy prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak se používají prohledávání protokolů v Log Analytics a poskytuje koncepty, které by měl být srozumitelný před vytvořením prohledávání protokolu
- [Referenční příručce k vyhledávání log Analytics](../log-analytics/log-analytics-search-reference.md) – zjistěte, jak psát své vlastní dotazy události v protokolu pomocí dotazovacího jazyka

Další informace o službě Security Center najdete v tématu:

- [Přehled služby Security Center](security-center-intro.md) – Centrum zabezpečení popisuje klíčové funkce

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
