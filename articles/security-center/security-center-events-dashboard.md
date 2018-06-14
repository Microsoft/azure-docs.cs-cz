---
title: Monitorování a zpracování události zabezpečení v Azure Security Center | Microsoft Docs
description: Zjistěte, jak můžete řídicím panelu Security Center událostí zobrazíte události zabezpečení z počítače mimo Azure a virtuální počítače Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926526"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorování a zpracování události zabezpečení v Azure Security Center
Řídicí panel události poskytuje přehled počet shromažďovaných přes čas a seznam významné události, které mohou vyžadovat vaši pozornost událostí zabezpečení.  

> [!NOTE]
> Chcete-li tuto funkci používat, musí pracovního prostoru spuštěná analýzy protokolů verze 2 a být na úrovni Standard Security Center. Najdete v Centru zabezpečení [stránce s cenami](security-center-pricing.md) Další informace o na plán úrovně Standard.
>
>

## <a name="what-is-a-security-event"></a>Co je událostí zabezpečení?
Security Center používá Microsoft Monitoring Agent shromažďovat různé zabezpečení související s konfigurací a události z vašeho počítače a uloží tyto události do vašeho pracovních prostorů. Příkladem takových dat jsou: zpracovává operační protokoly systému (protokol událostí systému Windows), spuštění a události z řešení zabezpečení integraci s Security Center. Microsoft Monitoring Agent také do vašich pracovní prostorů zkopíruje soubory se stavem systému.

## <a name="events-processed-dashboard"></a>Řídicí panel zpracovaných událostí
Přístup **události** řídicí panel z hlavní nabídky Security Center nebo Security Center **přehled** okno.  

![Řídicí panel zpracovaných událostí][1]

**Události** dlaždici v části **Security Center** zobrazí počet událostí odesílaných do Security Center z počítače mimo Azure a virtuální počítače Azure.

**Řídicí panel události** poskytuje přehled o počtu zpracovaných událostí přesčasová a seznam událostí.

 ![Řídicí panel][2]

 V horní polovině řídicího panelu trendů všechny události, které jsou zpracovány během posledního týdne. Dolní polovinu řídicí panel uvádí upozorňují na důležité události a všechny události podle typu:

 - **Významné události** zahrnují dotazy na události, které poskytuje Security Center a dotazy na události, které můžete vytvořit a přidat. Řídicí panel poskytuje také rychlý pohled na počet každé upozorňují na důležité události.
 - **Všechny události podle typu** zobrazuje typy událostí, které se přijímají a počet pro každý typ. Příklady typu události: SecurityEvent, CommonSecurityLog, WindowsFirewall a W3CIISLog.

> [!NOTE]
> Významné události zahrnují [webové směrného plánu vyhodnocení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Cílem vyhodnocování standardních hodnot webu je najít potenciálně ohrožená nastavení webového serveru.

## <a name="view-processed-event-details"></a>Zobrazit podrobnosti o zpracování událostí
1. V části **Security Center** hlavní nabídky, vyberte **události**.
2. **Řídicí panel události** může otevřete pracovní prostor selektor. Pokud máte jenom jeden pracovní prostor, tento pracovní prostor selektor se nezobrazí. Pokud máte více než jednoho pracovního prostoru, je nutné vybrat prostoru zobrazíte její podrobnosti zpracování událostí. Pokud máte více než jednoho pracovního prostoru, vyberte ze seznamu pracovního prostoru.

  ![Pracovní prostor seznamu][3]

3. **Řídicí panel události** se otevře zobrazující podrobnosti události pro vybraný pracovní prostor. Můžete zobrazit upozorňují na důležité události a všechny události podle typu.  V tomto příkladu jsme vybrali **upozorňují na důležité události**.

  ![Významné události][4]

4. Pro další data v pracovním prostoru můžete dotazovat tak, že vyberete typ události. V tomto příkladu jsme vybrali **SecurityEvent**.

  ![Vyberte typ události][5]

5. **Přihlaste se vyhledávání** otevře se další podrobnosti o typu události.

  ![Prohledávání protokolů][6]

## <a name="add-a-notable-event"></a>Přidat významné událost
Security Center nabízí významné události se na pole. Můžete přidat významné události, které jsou založené na použití vlastního dotazu [analýzy protokolů dotazu jazyka](../log-analytics/log-analytics-search-reference.md). Vrátíme k **řídicí panel události** přidat upozorňují na důležité události.

1. Vyberte **přidat významné událost**.

  ![Přidat významné událost][7]

2. **Přidat vlastní upozorňují na důležité události** otevře.  V části **zobrazovaný název**, zadejte název vaší upozorňují na důležité události. V části **vyhledávací dotaz**, zadejte dotaz pro událost.

  ![Zadejte dotaz.][8]

4. Vyberte **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Aktualizace pracovního prostoru pro zpracování událostí
Pracovní prostor musí být spuštěné analýzy protokolů verze 2 a být na úrovni Standard Security Center používat zpracování událostí ve službě Security Center. **Řídicí panel události** prostoru selektor identifikuje pracovní prostory, které tyto požadavky nesplňují.

![Pracovní prostor nesplňuje požadavky][9]

Pokud pracovní prostor řádek:

- Obsahuje **vyžaduje aktualizaci** -budete muset aktualizovat pracovní prostor analýzy protokolů verze 2
- Obsahuje **upgradovat plán** – je třeba upgradovat pracovního prostoru Security Center úrovně Standard
- Prázdné - pracovního prostoru splňuje požadavky a výběrem pracovního prostoru přejdete na řídicí panel

> [!NOTE]
> V části **řídicí panel události**, **události** sloupec zobrazuje objem událostí v každém pracovním prostoru.  Tento sloupec je prázdný u některých pracovních prostorů, protože do tohoto pracovního prostoru se používá úroveň Free Security Center. V části úroveň Free Security Center bude shromažďovat události ale události nejsou uloženy ve analýzy protokolů a nejsou k dispozici v řídicím panelu.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Aktualizujte pracovní prostor analýzy protokolů verze 2
1. Vyberte pracovní prostor, **vyžaduje aktualizaci**.
2. **Hledání Upgrade** otevře. Vyberte **upgradovat nyní**.

  ![Upgradovat nyní][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Upgradujte na plán úrovně Standard Security Center
1. Vyberte pracovní prostor s **upgradovat plán**.
2. **Řídicí panel události** otevře. Vyberte **řídicí panel zkuste události**.

  ![Zkuste řídicí panel][11]

3. V části **registrace k pokročilým zabezpečením**, vyberte pracovní prostor, který upgradujete.
4. V části **cenová**, vyberte **standardní**.
5. Vyberte **Uložit**.

  ![Upgradujte na plán úrovně Standard][12]

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat řídicího panelu Security Center událostí. Další informace o tom, jak funguje řídicího panelu a vytvářet vlastní dotazy na události, najdete v části:

- [Co je služba Log Analytics?](../log-analytics/log-analytics-overview.md) – Přehled na analýzy protokolů
- [Principy protokolu vyhledá v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md) – popisuje, jak se používají protokol hledání v analýzy protokolů a poskytuje koncepty, které by se měl chápat před vytvořením vyhledávání protokolu
- [Analýzy protokolů hledání odkaz](../log-analytics/log-analytics-search-reference.md) – zjistěte, jak psát vlastní dotazy na události v protokolu pomocí dotazu jazyka

Další informace o službě Security Center najdete v tématu:

- [Přehled Security Center](security-center-intro.md) – klíčové funkce popisuje Security Center

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
