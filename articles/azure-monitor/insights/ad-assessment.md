---
title: Optimalizace prostředí služby Active Directory s Azure Log Analytics | Dokumentace Microsoftu
description: Kontrola stavu služby Active Directory řešení můžete použít k vyhodnocení v pravidelných intervalech rizika a stav různých prostředí.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 063cedc679c3365e6352549e78c75ecff903cae7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193004"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimalizace prostředí služby Active Directory s řešením Kontrola stavu služby Active Directory v Log Analytics

![Symbol kontroly stavu AD](./media/ad-assessment/ad-assessment-symbol.png)

Kontrola stavu služby Active Directory řešení můžete použít k vyhodnocení v pravidelných intervalech rizika a stav různých prostředí serveru. Tento článek vám pomůže nainstalovat a mohli řešení používat, takže můžete provést opravné akce pro potenciální problémy.

Toto řešení poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury. Doporučení jsou rozdělené mezi čtyři oblasti zaměření, které vám pomohou rychle vědomi rizika a provést akci.

Doporučení jsou založeny na znalosti a zkušenosti získané z návštěvě tisíců zákaznických odborníky z Microsoftu. Každé doporučení obsahuje pokyny k proč k problému může být na kterých vám záleží a jak implementovat navrhované změny.

Můžete vybrat konkrétní oblasti, které jsou nejdůležitější pro vaši organizaci a sledovat svůj postup směrem ke spuštění prostředí pro riziko free a v dobrém stavu.

Poté, co jste přidali řešení a kontrola je dokončené a souhrnné informace pro konkrétní oblasti se zobrazí na **kontroly stavu AD** řídicí panel infrastruktury ve vašem prostředí. Následující části popisují, jak pomocí informací na **kontroly stavu AD** řídicí panel, kde můžete zobrazit a pak provést doporučené akce pro serverové infrastruktury služby Active Directory.  

![Obrázek kontroly stavu AD dlaždice](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Obrázek řídicího panelu AD kontroly stavu](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Kontrola stavu služby Active Directory řešení vyžaduje podporovanou verzi rozhraní .NET Framework 4.5.2 nebo nad nainstalovaný na každém počítači, který má Microsoft Monitoring Agent (MMA) nainstalovaný.  MMA agent používá System Center 2016 – Operations Manager a Operations Manageru 2012 R2 a služby Log Analytics.
* Řešení podporuje řadiče domény se systémem Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2 a Windows serveru 2016.
* Pracovní prostor Log Analytics můžete přidat Kontrola stavu služby Active Directory řešení z Azure marketplace na webu Azure Portal.  Není nutná žádná další konfigurace.

  > [!NOTE]
  > Po přidání řešení, se přidá soubor AdvisorAssessment.exe na servery s agenty. Konfigurační data je čtení a pak posílají do služby Log Analytics v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data.
  >
  >

Provádění kontroly stavu proti řadiči domény, které jsou členy domény, který se má vyhodnotit, vyžadují agenta a připojení ke službě Log Analytics pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) Pokud řadič domény není již monitorovaná System Center 2016 – Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovat pomocí nástroje System Center 2016 – Operations Manager nebo Operations Manager 2012 R2 a skupině pro správu není integrovaná se službou Log Analytics, může být řadič domény s více adresami pomocí Log Analytics ke shromažďování a předávání služby a pořád monitorované nástrojem Operations Manager.  
3. Jinak, pokud vaší skupině pro správu Operations Manageru je integrovaná se službou, budete muset přidat řadiče domény pro shromažďování dat podle pokynů v části služby [přidat počítače spravované bez agenta](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-log-analytics) po povolení řešení ve vašem pracovním prostoru.  

Agent na vašem řadiči domény, které sestavy pro skupinu pro správu nástroje Operations Manager shromažďuje data, předává do serveru pro správu přiřazené a pak se odešle přímo ze serveru pro správu služby Log Analytics.  Data není zapsána do databáze nástroje Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory zkontrolujte stav podrobnosti dat kolekce

Kontrola stavu služby Active Directory shromažďuje data z následujících zdrojů pomocí agenta, který jste povolili:

- Registr
- LDAP
- .NET Framework
- Protokol událostí
- Služba Active Directory rozhraní (ADSI)
- Windows PowerShell
- Data souborů
- Windows Management Instrumentation (WMI)
- Nástroj DCDIAG rozhraní API
- Rozhraní API služby (NTFRS) replikace souborů
- Vlastní C# kód

Data se shromažďují na řadiči domény a předávají do Log Analytics každých sedm dní.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Vysvětlení, jak mají určenou prioritu doporučení
Každé doporučení je přiřazena hodnota váhu, která identifikuje relativní důležitost doporučení. Zobrazí jenom 10 nejdůležitější doporučení.

### <a name="how-weights-are-calculated"></a>Jak se počítají váhy
Váhy jsou agregované hodnoty, které jsou založené na tři klíčové faktory:

* *Pravděpodobnost* , identifikovat problém způsobuje problémy. Vyšší pravděpodobnost rovná větší celkové skóre pro doporučení.
* *Dopad* problému ve vaší organizaci, pokud ho způsobovat problémy. Vyšší dopad odpovídá větší celkové skóre pro doporučení.
* *Úsilí* potřebnou k implementaci doporučení. Vyšší úsilí odpovídá menší celkové skóre pro doporučení.

Váhu pro jednotlivá doporučení je vyjádřené jako procentní podíl celkové skóre, které jsou k dispozici pro každou oblast zaměření. Například pokud doporučení v oblasti zabezpečení a dodržování předpisů zaměření má skóre % 5, implementaci tohoto doporučení zvýší vaše celkové skóre podle 5 % zabezpečení a dodržování předpisů.

### <a name="focus-areas"></a>Konkrétní oblasti
**Zabezpečení a dodržování předpisů** – zobrazuje tuto oblast zaměření doporučení ohledně potenciálních bezpečnostních hrozeb a porušení, podnikové zásady a technických, právních i regulačních požadavků.

**Dostupnost a plynulost obchodních procesů** -tuto oblast zaměření ukazuje doporučení pro služby dostupnost, odolnost vaší infrastruktury a ochrany firmy.

**Výkon a škálovatelnost** – zobrazuje tuto oblast zaměření doporučení, která vaší organizaci pomoct IT infrastruktury růst, ujistěte se, že splňuje aktuální požadavky na výkon vašeho IT prostředí a je schopný reagovat na měnící se infrastruktury potřebuje.

**Upgrade, migrace a nasazení** – zobrazuje tuto oblast zaměření doporučení, která vám pomohou upgrade, migrace a nasazení služby Active Directory vaší stávající infrastruktuře.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>By měl být cílem stanovení skóre 100 % v každé oblasti zaměření?
Ne nutně. Doporučení jsou založeny na znalosti a zkušenosti získané v Microsoftu přes návštěvě tisíců zákaznických. Ale žádné serverové infrastruktury jsou stejné, a konkrétní doporučení může být vyšší nebo nižší pro vás relevantní. Některá doporučení zabezpečení může být například méně relevantní, pokud vaše virtuální počítače nejsou zveřejněné na Internetu. Některá doporučení dostupnosti může být méně důležité pro služby, které poskytují shromažďování dat ad hoc s nízkou prioritou a generování sestav. Problémy, které jsou důležité pro až po zralé podnikání, může být méně důležité spouštěcí. Můžete určit, které konkrétní oblasti mají vašich priorit a podívejte se na tom, jak v průběhu času měnit skóre.

Každé doporučení obsahuje pokyny o tom, proč je důležité. Měli byste použít tento návod k vyhodnocení, jestli implementace doporučení je vhodné, vzhledem k povaze služeb IT a obchodní potřeby vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Kontroly stavu použijte doporučení oblasti zaměření
Po instalaci, zobrazí se přehled doporučení pomocí kontroly stavu dlaždice na stránce řešení na webu Azure Portal.

Zobrazení posouzení souhrnné dodržování předpisů pro infrastrukturu a pak přejít k podrobnostem doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Zobrazit doporučení pro oblast zaměření a provedení nápravné akce
3. Klikněte na tlačítko **přehled** dlaždici pro váš pracovní prostor Log Analytics na portálu Azure portal.
4. Na **přehled** stránky, klikněte na tlačítko **Kontrola stavu služby Active Directory** dlaždici.
5. Na **kontroly stavu** stránky, zkontrolujte souhrnné informace u některého okna oblasti zaměření a klikněte na z nich se má zobrazit doporučení pro tuto oblast zaměření.
6. Na žádném z oblasti stránek fokus můžete zobrazit prioritizovaných doporučení pro vaše prostředí. Kliknutím na doporučení v části **vliv na objekty** zobrazíte podrobnosti o tom, proč je provedeny doporučení.<br><br> ![Obrázek kontroly stavu doporučení](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
7. Můžete provést opravné akce navržený v **doporučené akce**. Když položka vyřeší, novější posouzení záznamy, které doporučené akce byly provedeny a zvýší vaše skóre dodržování předpisů. Opravené položky se zobrazí jako **předaný objekty**.

## <a name="ignore-recommendations"></a>Ignorujte doporučení
Pokud máte doporučení, která má být ignorována, můžete vytvořit textový soubor, který brání doporučení povolí, nebude vaše výsledky posouzení pomocí Log Analytics.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>K identifikaci doporučení, která se bude ignorovat.
1. Na portálu Azure na stránce pracovního prostoru Log Analytics pro váš vybraný pracovní prostor, klikněte na tlačítko **prohledávání protokolů** dlaždici.
2. Následující dotaz tak, aby seznam doporučení, které se nepodařilo použijte pro počítače se ve vašem prostředí.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Zde je snímek obrazovky zobrazující z dotazu prohledávání protokolu:<br><br> ![doporučení se nezdařilo](./media/ad-assessment/ad-failed-recommendations.png)

3. Zvolte doporučení, která má být ignorována. V dalším postupu budete používat hodnoty pro ID doporučení.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt
1. Vytvořte soubor s názvem IgnoreRecommendations.txt.
2. Vložte nebo zadejte ID jednotlivých doporučení pro jednotlivá doporučení, který má Log Analytics ignorovat na samostatném řádku a potom uložte a zavřete soubor.
3. Uložte soubor v následující složce na každém počítači místo, kam chcete Log Analytics pro ignorování doporučení.
   * Na počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím Operations managera) - *systemdrive % musí být*: \Program Files\Microsoft Monitoring Agent\Agent
   * Na serveru pro správu Operations Manageru 2012 R2 - *systemdrive % musí být*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu Operations Manageru 2016 - *systemdrive % musí být*: \Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Chcete-li ověřit, že jsou ignorovány doporučení
Až dalšího naplánovaného spuštění kontroly stavu, ve výchozím nastavení každých sedm dní, jsou označené zadanou doporučení *ignorováno* a nezobrazí se na řídicím panelu.

1. Do seznamu ignorovaných doporučení můžete použít následující dotazy prohledávání protokolů.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Pokud se později rozhodnete, že chcete zobrazit ignorované doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo RecommendationIDs můžete odebrat z nich.

## <a name="ad-health-check-solutions-faq"></a>AD Health Kontrola řešení – nejčastější dotazy
*Jak často Kontrola stavu spouštět?*

* Kontrola proběhne každých sedm dní.

*Existuje způsob, jak konfigurovat, jak často se kontrola stavu spouštět?*

* V tuto chvíli to není možné.

*Pokud jiný server pro zjištění po přidali jsme řešení kontroly stavu, bude kontrolován*

* Ano, jakmile je zjištěna zkontroluje z té, každých sedm dní.

*Pokud dojde k deaktivaci na serveru, když ho se odebere z kontroly stavu?*

* Pokud server není odesílání dat po dobu 3 týdnů, bude odebrán.

*Jaký je název procesu, která provádí sběr dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá shromažďování dat?*

* Skutečná data kolekce na serveru trvá přibližně 1 hodinu. Může trvat delší dobu na serverech, které mají velký počet serverů služby Active Directory.

*Existuje způsob, jak konfigurovat, když se shromažďují data?*

* V tuto chvíli to není možné.

*Proč zobrazit pouze prvních 10 doporučení?*

* Místo získáte vyčerpávající seznam náročné úkoly, doporučujeme vám zaměřit nejprve adresování prioritizovaných doporučení. Po jejich řešení, budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení, pomocí prohledávání protokolů.

*Existuje způsob, jak ignorujte doporučení?*

* Ano, naleznete v tématu [ignorujte doporučení](#ignore-recommendations) výše uvedené části.

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](../../azure-monitor/log-query/log-query-overview.md) informace o analýze podrobné doporučení a údaje o kontrolu stavu AD.
