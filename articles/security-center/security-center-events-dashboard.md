---
title: Monitorování a zpracování událostí zabezpečení v Azure Security Center | Microsoft Docs
description: Přečtěte si, jak můžete pomocí řídicího panelu události Security Center zobrazit události zabezpečení z virtuálních počítačů Azure a počítačů mimo Azure.
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
ms.openlocfilehash: 389aaee621251890cd3f75744a94b9c9b29c5695
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662395"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorování a zpracování událostí zabezpečení v Azure Security Center
Řídicí panel události poskytuje přehled o počtu událostí zabezpečení shromážděných v průběhu času a seznam důležitých událostí, které mohou vyžadovat vaši pozornost.  

> [!NOTE]
> Řídicí panel událostí zabezpečení byl vyřazen z 31. července 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_events).

## <a name="what-is-a-security-event"></a>Co je událost zabezpečení?
Security Center používá Microsoft Monitoring Agent ke shromažďování různých konfigurací a událostí souvisejících se zabezpečením z vašich počítačů a ukládá tyto události do vašich pracovních prostorů. Příklady takových dat: protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy a události z řešení zabezpečení integrovaných s Security Center. Microsoft Monitoring Agent také do vašich pracovní prostorů zkopíruje soubory se stavem systému.

## <a name="requirements"></a>Požadavky
Aby bylo možné tuto funkci používat, musí váš pracovní prostor běžet Log Analytics verze 2 a musí být na úrovni Standard Security Center. Další informace o úrovni Standard najdete na [stránce s cenami](security-center-pricing.md) Security Center.

## <a name="events-processed-dashboard"></a>Řídicí panel zpracovaných událostí
Přístup k řídicímu panelu **události** získáte v hlavní nabídce Security Center nebo v okně s **přehledem** Security Center.  

![Řídicí panel zpracovaných událostí][1]

Dlaždice **události** v části **Security Center** zobrazuje počet událostí toku do Security Center z vašich virtuálních počítačů Azure a počítačů mimo Azure.

**Řídicí panel události** poskytuje přehled o počtu zpracovaných událostí přesčasu a o seznamu událostí.

 ![Řídicí panel][2]

 Horní polovina řídicího panelu trenduje všechny události zpracované za poslední týden. V dolní polovině řídicího panelu se zobrazují významné události a všechny události podle typu:

 - Mezi **významné události** patří dotazy na události, které Security Center poskytování a dotazy na události, které vytvoříte a přidáte. Řídicí panel také poskytuje rychlý přehled o počtu každé významné události.
 - **Všechny události podle typu** zobrazuje typy událostí, které jsou přijímány, a počet pro každý typ. Příklady typu události jsou SecurityEvent, CommonSecurityLog, WindowsFirewall a W3CIISLog.

> [!NOTE]
> Mezi významné události patří [vyhodnocení standardních hodnot webu](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Cílem vyhodnocování standardních hodnot webu je najít potenciálně ohrožená nastavení webového serveru.

## <a name="view-processed-event-details"></a>Zobrazit podrobnosti o zpracovaných událostech
1. V hlavní nabídce **Security Center** vyberte možnost **události**.
2. Může se otevřít selektor pracovního prostoru **řídicího panelu události** . Pokud máte jenom jeden pracovní prostor, tento selektor pracovního prostoru se nezobrazí. Pokud máte více než jeden pracovní prostor, je nutné vybrat pracovní prostor a zobrazit jeho podrobnosti o jeho zpracovaných událostech. Vyberte pracovní prostor ze seznamu, pokud máte více než jeden pracovní prostor.

   ![Seznam pracovních prostorů][3]

3. Otevře se **řídicí panel události** se zobrazenými podrobnostmi o události pro vybraný pracovní prostor. Můžete zobrazit významné události a všechny události podle typu.  V tomto příkladu jsme vybrali **významné události**.

   ![Významná událost][4]

4. Můžete zadat dotaz na další data v pracovním prostoru tak, že vyberete typ události. V tomto příkladu jsme vybrali **SecurityEvent**.

   ![Výběr typu události][5]

5. **Hledání v protokolu** se otevře s dalšími podrobnostmi o typu události.

   ![Prohledávání protokolů][6]

## <a name="add-a-notable-event"></a>Přidání významné události
Security Center poskytuje okamžité významné události. Pomocí [dotazovacího jazyka Kusto](../log-analytics/log-analytics-search-reference.md)můžete přidat významné události na základě vlastního dotazu. K přidání významné události se vrátíme na **řídicí panel události** .

1. Vyberte **přidat významné události**.

   ![Přidání významné události][7]

2. Otevře se okno **Přidat vlastní významné události** .  Do pole **Zobrazovaný název**zadejte název významné události. V části **vyhledávací dotaz**zadejte dotaz pro událost.

   ![Zadejte dotaz.][8]

4. Vyberte **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Aktualizace pracovního prostoru pro zpracování událostí
Aby bylo možné používat zpracování událostí v Security Center, musí váš pracovní prostor běžet Log Analytics verze 2 a musí být na úrovni Standard Security Center. Selektor pracovního prostoru **řídicí panel události** identifikuje pracovní prostory, které tyto požadavky nesplňují.

![Pracovní prostor nesplňuje požadavky][9]

Pokud řádek pracovního prostoru:

- Obsahuje **vyžaduje aktualizaci** – je potřeba aktualizovat pracovní prostor na Log Analytics verze 2
- Obsahuje **plán upgradu** – je potřeba upgradovat pracovní prostor na úroveň Security Center úrovně Standard.
- Je prázdné – váš pracovní prostor splňuje požadavky a vybere pracovní prostor, který vás přesměruje na řídicí panel.

> [!NOTE]
> V části **řídicí panel události**označuje sloupec **události** množství událostí v každém pracovním prostoru.  Tento sloupec je pro některé pracovní prostory prázdný, protože na tento pracovní prostor se používá úroveň Free Security Center. V rámci bezplatné úrovně bude Security Center shromažďovat události, ale události se neukládají v protokolech Azure Monitor a na řídicím panelu nejsou k dispozici.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aktualizovat pracovní prostor na Log Analytics verze 2
1. Vyberte pracovní prostor, který **vyžaduje aktualizaci**.
2. Otevře se **Upgrade hledání** . Vyberte **Upgrade nyní**.

   ![Upgradovat][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Upgrade na úroveň Standard Security Center
1. Vyberte pracovní prostor s **plánem upgradu**.
2. Otevře se **řídicí panel události** . Vyberte **řídicí panel vyzkoušet události**.

   ![Vyzkoušet řídicí panel][11]

3. V části **připojování k rozšířenému zabezpečení**vyberte pracovní prostor, který upgradujete.
4. V části **ceny**vyberte **Standard**.
5. Vyberte **Uložit**.

   ![Upgradovat na úroveň Standard][12]

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat řídicí panel událostí Security Center. Další informace o tom, jak řídicí panel funguje a jak psát vlastní dotazy na události, najdete v těchto tématech:

- [Co jsou protokoly Azure Monitor?](../log-analytics/log-analytics-overview.md) – Přehled protokolů Azure Monitor
- [Principy prohledávání protokolů v Kusto](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak se používají prohledávání protokolu v Azure monitor protokoly, a poskytuje koncepty, které by se měly chápat před vytvořením prohledávání protokolu.
- [Odkaz na hledání Kusto](../log-analytics/log-analytics-search-reference.md) – Naučte se psát vlastní dotazy na události pomocí dotazovacího jazyka v protokolu.

Další informace o Security Center najdete v těchto tématech:

- [Přehled Security Center](security-center-intro.md) – popisuje klíčové funkce Security Center

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
