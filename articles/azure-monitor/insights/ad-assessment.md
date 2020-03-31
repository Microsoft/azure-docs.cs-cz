---
title: Optimalizace prostředí služby Active Directory pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Řešení kontroly stavu služby Active Directory můžete použít k posouzení rizika a stavu prostředí v pravidelných intervalech.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 57c474c8391168702154b71e0c454253ab921dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667223"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimalizace prostředí Active Directory s využitím řešení kontroly stavu Active Directory ve službě Azure Monitor

![Symbol kontroly stavu služby AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Řešení kontroly stavu služby Active Directory můžete použít k posouzení rizika a stavu serverových prostředí v pravidelných intervalech. Tento článek vám pomůže nainstalovat a používat řešení, takže můžete provést nápravná opatření pro potenciální problémy.

Toto řešení poskytuje seznam doporučení s prioritou specifických pro nasazenou serverovou infrastrukturu. Doporučení jsou rozdělena do kategorií do čtyř oblastí zájmu, které vám pomohou rychle pochopit riziko a přijmout opatření.

Doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft z tisíců návštěv zákazníků. Každé doporučení obsahuje pokyny o tom, proč vám problém může být důležité a jak implementovat navrhované změny.

Můžete zvolit oblasti fokusu, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezrizikového a zdravého prostředí.

Po přidání řešení a dokončení kontroly se na řídicím panelu **kontroly stavu služby AD** pro infrastrukturu ve vašem prostředí zobrazí souhrnné informace o prioritních oblastech. Následující části popisují, jak používat informace na řídicím panelu **kontroly stavu služby AD,** kde můžete zobrazit a provést doporučené akce pro infrastrukturu serveru služby Active Directory.  

![Obrázek dlaždice Kontrola stavu služby AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Obrázek řídicího panelu kontroly stavu služby AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Řešení kontroly stavu služby Active Directory vyžaduje podporovanou verzi rozhraní .NET Framework 4.6.2 nebo vyšší nainstalovanou v každém počítači, který má nainstalovaného agenta Log Analytics pro systém Windows (označovaného také jako Microsoft Monitoring Agent (MMA).  Agent a používá System Center 2016 – Operations Manager, Operations Manager 2012 R2 a Azure Monitor.
* Toto řešení podporuje řadiče domény se systémem Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2 a Windows Server 2016.
* Pracovní prostor Analýzy protokolů pro přidání řešení kontroly stavu služby Active Directory z webu Azure Marketplace na webu Azure Portal. Není vyžadována žádná další konfigurace.

  > [!NOTE]
  > Po přidání řešení je soubor AdvisorAssessment.exe přidán na servery s agenty. Konfigurační data se přečtou a pak se odesílají do Azure Monitoru v cloudu ke zpracování. Na přijatá data se aplikuje logika a cloudová služba data zaznamená.
  >
  >

Chcete-li provést kontrolu stavu u řadičů domény, které jsou členy domény, která má být vyhodnocena, každý řadič domény v této doméně vyžaduje agenta a připojení k Azure Monitoru pomocí jedné z následujících podporovaných metod:

1. Pokud řadič domény ještě není monitorován system center 2016 – Operations Manager nebo Operations Manager 2012 R2 , nainstalujte [agenta Analýzy protokolů pro Windows.](../../azure-monitor/platform/agent-windows.md)
2. Pokud je monitorována pomocí System Center 2016 – Operations Manager nebo Operations Manager 2012 R2 a skupina pro správu není integrovaná s Azure Monitorem, může být řadič domény s Azure Monitorem s více lokalitami pro sběr dat a předávání do služby a stále být sledována operačním manažerem.  
3. V opačném případě, pokud je vaše skupina pro správu nástroje Operations Manager integrována se službou, je třeba po povolení řešení v pracovním prostoru přidat řadiče domény pro shromažďování dat službou podle kroků uvedených v části [Přidání počítačů spravovaných agenty.](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor)  

Agent na vašem řadiči domény, který je podřízen skupině správy nástroje Operations Manager, shromažďuje data, předává na přiřazený server pro správu a pak se odesílá přímo ze serveru pro správu do služby Azure Monitor.  Data nejsou zapsána do databází nástroje Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro kontrolu stavu Active Directory

Kontrola stavu služby Active Directory shromažďuje data z následujících zdrojů pomocí povoleného agenta:

- Registr
- LDAP
- .NET Framework
- Protokol událostí
- Rozhraní služby Active Directory (ADSI)
- Windows PowerShell
- Data souborů
- Windows Management Instrumentation (WMI)
- ROZHRANÍ API nástroje DCDIAG
- Rozhraní API služby replikace souborů (NTFRS)
- Vlastní kód jazyka C#

Data se shromažďují na řadiči domény a předávají se do Azure Monitoru každých sedm dní.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení

Každé provedené doporučení má váhovou hodnotu, která identifikuje relativní důležitost doporučení. Je zobrazeno pouze 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah

Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

* *Pravděpodobnost,* že zjištěný problém způsobuje problémy. Vyšší pravděpodobnost se rovná větší celkové skóre pro doporučení.
* *Dopad* problému na vaši organizaci, pokud to způsobit problém. Vyšší dopad se rovná větší celkové skóre pro doporučení.
* *Úsilí* potřebné k provedení doporučení. Vyšší úsilí se rovná menší celkové skóre pro doporučení.

Váha každého doporučení je vyjádřena jako procento z celkového skóre, které je k dispozici pro každou zaostřovací oblast. Pokud má například doporučení v oblasti fokusu zabezpečení a dodržování předpisů skóre 5 %, implementace tohoto doporučení zvýší celkové skóre zabezpečení a dodržování předpisů o 5 %.

### <a name="focus-areas"></a>Oblasti zaměření

**Zabezpečení a dodržování předpisů** – tato oblast fokusu zobrazuje doporučení pro potenciální bezpečnostní hrozby a porušení, podnikové zásady a technické, právní a regulační požadavky na dodržování předpisů.

**Dostupnost a kontinuita provozu** – tato oblast fokus zobrazuje doporučení pro dostupnost služeb, odolnost infrastruktury a ochranu podniku.

**Výkon a škálovatelnost** – tato oblast fokusu zobrazuje doporučení, která pomáhají vaší organizaci růst infrastruktury IT, zajišťují, aby vaše IT prostředí splňovalo aktuální požadavky na výkon a bylo schopno reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast fokusu zobrazuje doporučení, která vám pomohou upgradovat, migrovat a nasadit službu Active Directory do stávající infrastruktury.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?

Ne nutně. Doporučení jsou založena na znalostech a zkušenostech získaných inženýry společnosti Microsoft v tisících návštěv zákazníků. Žádné dvě serverové infrastruktury však nejsou stejné a konkrétní doporučení pro vás mohou být více či méně relevantní. Některá doporučení zabezpečení mohou být například méně relevantní, pokud vaše virtuální počítače nejsou vystaveny Internetu. Některá doporučení týkající se dostupnosti mohou být méně relevantní pro služby, které poskytují shromažďování a vykazování dat ad hoc s nízkou prioritou. Otázky, které jsou důležité pro vyspělý podnik, mohou být pro start-up méně důležité. Možná budete chtít určit, které oblasti zájmu jsou vaše priority a pak se podívat na to, jak se vaše skóre měnit v průběhu času.

Každé doporučení obsahuje pokyny, proč je důležité. Tyto pokyny byste měli použít k vyhodnocení, zda je implementace doporučení vhodná pro vás, vzhledem k povaze vašich it služeb a obchodním potřebám vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použití doporučení pro kontrolu stavu fokusu

Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice Kontrola stavu na stránce řešení na webu Azure Portal.

Prohlédněte si souhrnné posouzení dodržování předpisů pro vaši infrastrukturu a potom projděte doporučení.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro zaostřovací oblast a přijmout nápravná opatření

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na stránce **Přehled** klikněte na dlaždici **Kontrola stavu služby Active Directory.**

2. Na stránce **Kontrola stavu** zkontrolujte souhrnné informace v jednom z fokusů a potom klepnutím na jednu z nich zobrazte doporučení pro tuto oblast fokusu.

3. Na libovolné stránce fokusové oblasti můžete zobrazit doporučení s prioritou pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč je doporučení provedeno.

    ![Obrázek doporučení pro kontrolu stavu](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Můžete provést nápravná opatření navržená v **části Navrhované akce**. Po vyřešení položky se později záznamy hodnocení, že byly podniknuty doporučené akce a vaše skóre dodržování předpisů se zvýší. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení

Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který Azure Monitor použije, aby se zabránilo zobrazení doporučení ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Chcete-li identifikovat doporučení, která budete ignorovat

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Pomocí následujícího dotazu můžete zobrazit doporučení, která se nezdařila pro počítače ve vašem prostředí.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Zde je snímek obrazovky zobrazující dotaz protokolu:<

![neúspěšná doporučení](media/ad-assessment/ad-failed-recommendations.png)

Zvolte doporučení, která chcete ignorovat. Budete používat hodnoty pro RecommendationId v dalším postupu.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.

2. Vložte nebo zadejte každé DoporučeníId pro každé doporučení, které chcete Azure Monitor ignorovat na samostatném řádku a pak uložit a zavřít soubor.

3. Vložte soubor do následující složky v každém počítači, kde chcete Azure Monitor ignorovat doporučení.

   * V počítačích s agentem Microsoft Monitoring Agent (připojeným přímo nebo prostřednictvím nástroje Operations Manager) – *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Na serveru pro správu Nástroje pro správu nástroje Operations Manager 2012 R2 – *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na serveru pro správu nástroje Operations Manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že doporučení jsou ignorována

Po spuštění další naplánované kontroly stavu, ve výchozím nastavení každých sedm dní, zadaná doporučení jsou *označeny ignorovány* a nezobrazí se na řídicím panelu.

1. Následující dotazy protokolu můžete použít k zobrazení seznamu všech ignorovaných doporučení.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny soubory IgnoreRecommendations.txt nebo z nich můžete odebrat ID doporučení.

## <a name="ad-health-check-solutions-faq"></a>Nejčastější dotazy k řešením kontroly stavu AD

*Jaké kontroly provádí řešení hodnocení ad?*

* Následující dotaz zobrazuje popis všech aktuálně provedených kontrol:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Výsledky pak lze exportovat do aplikace Excel k dalšímu přezkoumání.

*Jak často se kontrola stavu běží?*

* Šek probíhá každých sedm dní.

*Existuje způsob, jak nastavit, jak často se kontrola stavu spustí?*

* V tuto chvíli to není možné.

*Pokud je po přidání řešení kontroly stavu zjištěn jiný server pro*

* Ano, jakmile je zjištěno, že je kontrolována od té doby, každých sedm dní.

*Pokud je server vyřazen z provozu, kdy bude odebrán z kontroly stavu?*

* Pokud server neodešle data po dobu 3 týdnů, je odebrán.

*Jaký je název procesu, který provádí shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá, než budou údaje shromažďovány?*

* Skutečné shromažďování dat na serveru trvá přibližně 1 hodinu. Na serverech s velkým počtem serverů služby Active Directory může trvat déle.

*Existuje způsob, jak nastavit, kdy jsou data shromažďována?*

* V tuto chvíli to není možné.

*Proč zobrazit pouze 10 nejlepších doporučení?*

* Místo toho, abyste vám poskytli vyčerpávající ohromující seznam úkolů, doporučujeme, abyste se nejprve zaměřili na řešení prioritních doporučení. Po jejich řešení budou k dispozici další doporučení. Pokud dáváte přednost zobrazení podrobného seznamu, můžete zobrazit všechna doporučení pomocí dotazu protokolu.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, viz [Ignorovat doporučení](#ignore-recommendations) část výše.

## <a name="next-steps"></a>Další kroky

Pomocí dotazů protokolu Azure Monitor se [dozvíte,](../log-query/log-query-overview.md) jak analyzovat podrobná data a doporučení kontroly stavu služby AD.
