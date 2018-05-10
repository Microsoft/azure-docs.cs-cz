---
title: Optimalizace prostředí služby Active Directory s Azure Log Analytics | Microsoft Docs
description: Zkontrolujte Active Directory stavu řešení můžete použít k vyhodnocení rizik a stavu prostředí v pravidelných intervalech.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 016b5bafdf6543ad43e36a875e6aa68572b35f97
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimalizace prostředí služby Active Directory s řešením pro kontrolu stavu v Active Directory v analýzy protokolů

![Zkontrolujte stav symbol AD](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Zkontrolujte Active Directory stavu řešení můžete použít k vyhodnocení rizik a stavu serveru prostředí v pravidelných intervalech. Tento článek vám pomůže nainstalovat a použít řešení tak, že lze provádět opravné akce pro potenciální problémy.

Toto řešení poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serverů seřazený podle priority. Doporučení jsou rozdělené mezi čtyři konkrétní oblasti, které vám pomůžou rychle pochopit riziko a provést akci.

Doporučení jsou založené na znalosti a zkušenosti technici Microsoft z tisíce zákazníka návštěvách. Každé doporučení obsahuje informace, proč může pro vás důležitá problém a implementaci navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou důležité pro vaši organizaci a sledovat průběh směrem k spuštění prostředí riziko volné a v pořádku.

Když jste přidali řešení a kontrolu je dokončené, souhrnné informace pro konkrétní oblasti je zobrazena na **AD Health zkontrolujte** řídicí panel infrastruktury ve vašem prostředí. Následující části popisují, jak používat informace na **AD Health zkontrolujte** řídicí panel, kde můžete zobrazit a pak proveďte doporučené akce pro serverové infrastruktury služby Active Directory.  

![Obrázek kontroly stavu AD dlaždice](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![Obrázek AD Health zkontrolujte řídicí panel](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Zkontrolujte Active Directory stavu řešení vyžaduje podporovanou verzi rozhraní .NET Framework 4.5.2 nebo výše nainstalovaný na každém počítači, který má monitorování agenta MMA (Microsoft) nainstalována.  MMA agent používá System Center 2016 - Operations Manager a Operations Manager 2012 R2 a službu analýzy protokolů.
* Řešení podporuje řadiče domény se systémem Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2 a Windows Server 2016.
* Pracovní prostor analýzy protokolů pro přidání zkontrolujte Active Directory stavu řešení z Azure marketplace na portálu Azure.  Není nutná žádná další konfigurace.

  > [!NOTE]
  > Po přidání řešení, soubor AdvisorAssessment.exe je přidán na servery s agenty. Konfigurační data je čtení a pak se odešle službě Analýza protokolů v cloudu pro zpracování. Logika se použije pro přijatá data a cloudové služby zaznamenává data.
  >
  >

Při provádění kontroly stavu u řadičů domény, které jsou členy domény, který se má vyhodnotit vyžadují agenta a připojení k analýze protokolů pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Pokud řadič domény není již monitorován pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovaný pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2 a skupině pro správu není integrované se službou analýzy protokolů, může být řadič domény vícedomé s analýzy protokolů pro shromažďování dat a předávat služby a bude i nadále monitorovat pomocí nástroje Operations Manager.  
3. Jinak, pokud skupině pro správu nástroje Operations Manager je integrovaná se službou, je nutné přidat řadiče domény pro shromažďování dat pomocí služby podle kroků v části [přidat počítače spravované agentem](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) po povolení řešení v pracovním prostoru.  

Agent na řadiči domény, které sestavy ke skupině pro správu nástroje Operations Manager shromažďuje data, předává do serveru pro správu přiřazené, se pak odešlou přímo ze serveru pro správu ke službě Analýza protokolů.  Data není zapsán do databáze nástroje Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory stavu zkontrolujte podrobnosti dat kolekce

Zkontrolujte stav, Active Directory shromažďuje data z následujících zdrojů pomocí agenta, který jste povolili:

- Registr
- LDAP
- .NET Framework
- V protokolu událostí
- Služba Active Directory rozhraní (ADSI)
- Windows PowerShell
- Data souborů
- Windows Management Instrumentation (WMI)
- Nástroj DCDIAG rozhraní API
- Rozhraní API služby (NTFRS) replikace souborů
- Kód vlastní C#

Data jsou shromažďována na řadiči domény a předávat k analýze protokolů každých sedm dní.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Pochopení, jak budou doporučení mít vyšší prioritu
Každé doporučení je zadána hodnota vyvážení, která určuje relativní důležitost doporučení. Jsou zobrazeny pouze 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Jak jsou vypočítávány váhu
Váhy jsou agregovaných hodnot založena na tři klíčové faktory:

* *Pravděpodobnosti* , způsobuje problémy, problém identifikovat. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
* *Dopad* problému na vaší organizaci, pokud ji způsobovat problémy. Vyšší dopad rovná větší celkové skóre pro doporučení.
* *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí rovná menší celkové skóre pro doporučení.

Vyvážení pro každé doporučení je vyjádřený jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast fokus. Například pokud doporučení v oblasti zabezpečení a dodržování předpisů fokus je skóre % 5, implementace tímto doporučením zvyšuje celkové skóre podle 5 % zabezpečení a dodržování předpisů.

### <a name="focus-areas"></a>Konkrétní oblasti
**Zabezpečení a dodržování předpisů** – v tomto poli fokus zobrazí doporučení pro potenciální bezpečnostní hrozby a narušení, podnikové zásady a dodržování předpisů technických, právních i regulačních požadavků.

**Dostupnost a provozní kontinuita** – v tomto poli fokus zobrazí doporučení pro dostupnost služeb, odolnost vaší infrastruktury a obchodní ochrany.

**Výkon a škálovatelnost** – v tomto poli fokus zobrazí doporučení, která pomůžou vaší organizace IT infrastruktury růst, zajistěte, aby vaše IT prostředí splňuje aktuální požadavky na výkon a schopné reagovat na měnící se infrastruktury potřebuje.

**Upgrade, nasazení a migrace** – v tomto poli fokus zobrazí doporučení, která vám pomohou upgradovat, migrace a nasazení služby Active Directory vaší stávající infrastruktuře.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Mají usilovat o stanovení skóre 100 % v každé oblasti fokus?
Ne nutně. Doporučení jsou založené na prostředí, které nebyly získány prostřednictvím Microsoft technici napříč tisíce návštěvy zákazníka a znalostní báze. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší relevantní pro vás. Například může být několik doporučení zabezpečení méně důležité, pokud vaše virtuální počítače nejsou vystaveny v Internetu. Některá doporučení, jaké dostupnosti může být méně důležité pro služby, které poskytují kolekce s nízkou prioritou ad hoc dat a vytváření sestav. Problémy, které jsou důležité, abyste vyspělých firmy může být méně důležité Startup. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak se vaše skóre časem změnit.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Měli byste použít tyto pokyny k vyhodnocení, zda implementace doporučení je vhodné pro vás, daná povaze vašich IT služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použití stavu zkontrolujte fokus oblasti doporučení
Po instalaci, zobrazí se souhrn doporučení pomocí dlaždice stavu zkontrolujte na stránce řešení na portálu Azure.

Zobrazte vyhodnocování souhrnné dodržování předpisů pro infrastrukturu a potom přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Proveďte opravné akce a zobrazit doporučení pro oblastí zájmu
3. Klikněte **přehled** dlaždici pro váš pracovní prostor analýzy protokolů na portálu Azure.
4. Na **přehled** klikněte na tlačítko **Active Directory stavu zkontrolujte** dlaždici.
5. Na **kontroly stavu** zkontrolujte souhrnné informace v jednom z okna oblasti fokus a pak klikněte na jednu zobrazíte doporučení pro tuto oblast fokus.
6. Na všech stránkách oblasti fokus můžete zobrazit seřazený podle priority doporučení, která se pro vaše prostředí. Klikněte na tlačítko doporučení v části **vliv na objekty** Chcete-li zobrazit podrobnosti, proč se provádí doporučení.<br><br> ![Obrázek stavu zkontrolujte doporučení](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Můžete provést nápravné akce navržený v **doporučované akce**. Když položka byla řešit, novější vyhodnocování záznamy, které doporučené akce provedené a zvýší vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předán objekty**.

## <a name="ignore-recommendations"></a>Ignorovat doporučení
Pokud máte doporučení, které chcete ignorovat, můžete vytvořit textový soubor, který analýzy protokolů použije k zabránění doporučení ze storu ve výsledky hodnocení.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>K identifikaci doporučení, které se budou ignorovat.
1. Na stránce pracovní prostor analýzy protokolů pro vaši vybranou pracovní prostor na portálu Azure, klikněte na tlačítko **hledání protokolů** dlaždici.
2. Pro počítače ve vašem prostředí použijte následující dotaz, který seznam doporučení, které selhaly.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Zde je snímek obrazovky zobrazující protokolu vyhledávací dotaz:<br><br> ![doporučení se nezdařilo](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Zvolte doporučení, které chcete ignorovat. Hodnoty pro RecommendationId budete používat v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytváření a používání textový soubor s IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložit nebo zadejte každou RecommendationId pro jednotlivá doporučení, které chcete analýzy protokolů ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači, kam chcete analýzy protokolů ignorovat doporučení.
   * Na počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím nástroje Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent monitorování
   * Na serveru pro správu nástroje Operations Manager 2012 R2 - *SystemDrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu nástroje Operations Manager 2016 - *SystemDrive*: \Program Files\Microsoft Manager\Server 2016\Operations System Center

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že se ignorovat doporučení
Po na další naplánované spuštění kontroly stavu, ve výchozím nastavení každých sedm dní, jsou označené zadanou doporučení *ignorovaná* a nezobrazí se na řídicím panelu.

1. Následující dotazy hledání protokolů můžete použít k zobrazení seznamu všech ignorováno doporučení.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Pokud se později rozhodnete, zda chcete zobrazit ignorováno doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="ad-health-check-solutions-faq"></a>AD Health Kontrola řešení – nejčastější dotazy
*Jak často kontrolu stavu spustit?*

* Kontrola spouští každých sedm dní.

*Existuje způsob, jak nakonfigurovat, jak často se spustí Kontrola stavu?*

* V tuto chvíli to není možné.

*Pokud jiný server pro zjištění po byly přidány řešení kontrolu stavu, bude ho zkontrolovat*

* Ano, jakmile se zjistí, že je zaškrtnuté z pak, každých sedm dní.

*Pokud dojde k deaktivaci serveru, když ho se odebere z kontrolou stavu?*

* Pokud server není odesílání dat 3 týdny, bude odebrán.

*Jaký je název procesu, který nemá shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá na shromáždění dat?*

* Kolekce skutečná data na serveru trvá asi 1 hodina. Může trvat déle na serverech, které mají velký počet servery služby Active Directory.

*Existuje způsob, jak nakonfigurovat, když jsou shromažďována data?*

* V tuto chvíli to není možné.

*Proč zobrazit pouze prvních 10 doporučení?*

* Namísto udělení vyčerpávající čtenáře seznam úloh, doporučujeme můžete soustředit na první adresování seřazený podle priority doporučení. Po jejich řešení, bude k dispozici další doporučení. Pokud ji chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení pomocí hledání protokolů.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, najdete v části [ignorovat doporučení](#ignore-recommendations) část výše.

## <a name="next-steps"></a>Další postup
* Použití [přihlásit analýzy protokolů hledání](log-analytics-log-searches.md) se dozvíte, jak analyzovat podrobné doporučení a údaje o kontrolu stavu AD.
