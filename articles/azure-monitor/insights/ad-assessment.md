---
title: Optimalizujte prostředí Active Directory pomocí Azure Monitor | Microsoft Docs
description: Řešení kontroly stavu služby Active Directory můžete použít k vyhodnocení rizik a stavu prostředí v pravidelných intervalech.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723244"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimalizace prostředí Active Directory s využitím řešení kontroly stavu Active Directory ve službě Azure Monitor

![Symbol kontroly stavu služby AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Řešení pro kontrolu stavu služby Active Directory můžete použít k vyhodnocení rizik a stavu prostředí serveru v pravidelných intervalech. Tento článek vám pomůže s instalací a používáním řešení, abyste mohli provádět nápravné akce pro potenciální problémy.

Toto řešení poskytuje prioritní seznam doporučení specifických pro vaši nasazenou serverovou infrastrukturu. Doporučení jsou rozdělená do kategorií v rámci čtyř oblastí fokusu, které vám pomůžou rychle pochopit rizika a provádět akce.

Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu z tisíců návštěv zákazníků. Každé doporučení poskytuje pokyny týkající se toho, proč k problému může dojít, a postup implementace navrhovaných změn.

Můžete vybrat oblasti, které jsou pro vaši organizaci nejdůležitější, a sledovat svůj pokrok směrem k provozu bezplatného a zdravého prostředí.

Po přidání řešení a dokončení kontroly se na řídicím panelu **kontroly stavu služby AD** zobrazí souhrnné informace pro infrastrukturu ve vašem prostředí. Následující části popisují, jak používat informace na řídicím panelu **kontroly stavu služby** Active Directory, kde můžete zobrazit a následně provést doporučené akce pro infrastrukturu serveru služby Active Directory.  

![Obrázek dlaždice kontroly stavu služby AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Obrázek řídicího panelu kontroly stavu služby AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Požadavky

* Řešení kontroly stavu služby Active Directory vyžaduje, aby na každém počítači, který má Log Analytics agenta pro Windows (také označovaný jako Microsoft Monitoring Agent (MMA)), byla nainstalovaná podporovaná verze .NET Framework 4.6.2 nebo vyšší.  Agent používá System Center 2016-Operations Manager, Operations Manager 2012 R2 a Azure Monitor.
* Řešení podporuje řadiče domény se systémy Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2, Windows Server 2016 a Windows Server 2019.
* Pracovní prostor Log Analytics pro přidání řešení kontroly stavu služby Active Directory z Azure Marketplace v Azure Portal. Není vyžadována žádná další konfigurace.

  > [!NOTE]
  > Po přidání řešení se soubor AdvisorAssessment.exe přidá na servery s agenty. Konfigurační data se čtou a pak se odesílají do Azure Monitor v cloudu ke zpracování. Na přijatá data se aplikuje logika a cloudová služba data zaznamená.
  >
  >

Aby bylo možné provést kontrolu stavu proti řadičům domény, které jsou členy domény, který se má vyhodnotit, vyžaduje každý řadič domény v této doméně agenta a připojení k Azure Monitor pomocí jedné z následujících podporovaných metod:

1. Nainstalujte [agenta Log Analytics pro systém Windows](../agents/agent-windows.md) , pokud řadič domény ještě není monitorovaný pomocí nástroje System Center 2016-Operations Manager nebo Operations Manager 2012 R2.
2. Pokud je monitorovaná pomocí nástroje System Center 2016-Operations Manager nebo Operations Manager 2012 R2 a skupina pro správu není integrována s Azure Monitor, může být řadič domény s Azure Monitorem k shromažďování dat a jejich přeposílání do služby a nadále monitorován pomocí Operations Manager.  
3. V opačném případě, pokud je vaše skupina pro správu Operations Manager integrovaná se službou, je nutné přidat řadiče domény pro shromažďování dat službou podle kroků v části [Přidání počítačů spravovaných agentem](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) po povolení řešení ve vašem pracovním prostoru.  

Agent na vašem řadiči domény, který hlásí skupinu pro správu Operations Manager, shromažďuje data, přepošle jim přiřazenou management server a pak se pošle přímo z management server do Azure Monitor.  Data nejsou zapsána do databází Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Podrobnosti o shromažďování dat pro kontrolu stavu Active Directory

Při kontrole stavu služby Active Directory se shromažďují data z následujících zdrojů pomocí agenta, který jste povolili:

- Registr
- LDAP
- .NET Framework
- Protokol událostí
- Rozhraní ADSI (Active Directory Service Interfaces)
- Windows PowerShell
- Data souborů
- Windows Management Instrumentation (WMI)
- Rozhraní API nástroje DCDIAG
- Rozhraní API služby replikace souborů (NTFRS)
- Vlastní kód C#

Data se shromažďují na řadiči domény a předají se Azure Monitor každých 7 dní.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Principy určování priority doporučení

Každé provedené doporučení je předána hodnota váhy, která identifikuje relativní důležitost doporučení. Zobrazuje se jenom 10 nejdůležitějších doporučení.

### <a name="how-weights-are-calculated"></a>Způsob počítání vah

Váhy jsou agregované hodnoty založené na třech klíčových faktorech:

* *Pravděpodobnost* , že problém identifikuje, způsobuje problémy. Vyšší pravděpodobnost odpovídá většímu celkovému skóre pro doporučení.
* *Dopad* problému ve vaší organizaci, pokud dojde k problému. Vyšší dopad odpovídá většímu celkovému skóre pro doporučení.
* *Úsilí* potřebné k implementaci doporučení. Vyšší úsilí se rovná menšímu celkovému skóre pro doporučení.

Váha pro každé doporučení se vyjádří jako procento z celkového skóre dostupného pro každou oblast zaměření. Pokud například doporučení v oblasti zaměření zabezpečení a dodržování předpisů má skóre 5%, implementace tohoto doporučení zvyšuje celkové zabezpečení a skóre dodržování předpisů o 5%.

### <a name="focus-areas"></a>Oblasti zaměření

**Zabezpečení a dodržování předpisů** – tato oblast pro výběr obsahuje doporučení pro potenciální bezpečnostní hrozby a porušení zabezpečení, podnikové zásady a požadavky na dodržování předpisů v souladu s právními předpisy.

**Dostupnost a provozní kontinuita** – tato oblast pro výběr zobrazuje doporučení pro dostupnost služby, odolnost vaší infrastruktury a obchodní ochranu.

**Výkon a škálovatelnost** – tato oblast pro výběr obsahuje doporučení, která vám pomohou zvýšit infrastrukturu IT vaší organizace, zajistit, že vaše IT prostředí splňuje aktuální požadavky na výkon a je schopné reagovat na měnící se potřeby infrastruktury.

**Upgrade, migrace a nasazení** – tato oblast pro výběr vám ukáže doporučení, která vám pomůžou s upgradem, migrací a nasazením služby Active Directory na stávající infrastrukturu.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Měli byste v každé oblasti zaměření cílit na skóre 100 %?

Ne nutně. Doporučení jsou založená na znalostech a zkušenostech získaných odborníky Microsoftu v různých tisících návštěv zákazníků. Nicméně žádné dvě serverové infrastruktury nejsou stejné a specifická doporučení můžou být pro vás méně důležitá. Některá doporučení zabezpečení mohou být například méně důležitá, pokud vaše virtuální počítače nejsou vystaveny pro Internet. Některá doporučení k dostupnosti mohou být méně důležitá pro služby, které poskytují nízkou prioritu shromažďování a generování dat ad hoc. Problémy, které jsou důležité pro vyspělou firmu, můžou být pro spuštění méně důležité. Možná budete chtít určit, které oblasti fokusu jsou vašimi prioritami, a pak se podívat, jak se vaše skóre v průběhu času mění.

Každé doporučení obsahuje pokyny k tomu, proč je důležité. Tyto pokyny byste měli použít k vyhodnocení, jestli je implementace doporučení vhodná pro vás, a to s ohledem na povahu vašich IT služeb a obchodních potřeb vaší organizace.

## <a name="use-health-check-focus-area-recommendations"></a>Použít doporučení oblasti pro kontrolu stavu

Po instalaci můžete zobrazit souhrn doporučení pomocí dlaždice kontroly stavu na stránce řešení v Azure Portal.

Podívejte se na souhrnná vyhodnocení dodržování předpisů pro vaši infrastrukturu a pak na doporučení pro přechod k podrobnostem.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Chcete-li zobrazit doporučení pro oblast zaměření a provést nápravná opatření

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na stránce **Přehled** klikněte na dlaždici **kontroly stavu služby Active Directory** .

2. Na stránce **Kontrola stavu** zkontrolujte souhrnné informace v jednom z oken detailní oblasti a potom klikněte na jednu pro zobrazení doporučení pro tuto oblast výběru.

3. Na kterékoli stránce oblasti pro zaměření si můžete prohlédnout doporučení s prioritou vytvořená pro vaše prostředí. Kliknutím na doporučení v části **Ovlivněné objekty** zobrazíte podrobnosti o tom, proč se doporučení udělalo.

    ![Obrázek doporučení pro kontrolu stavu](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. V **navrhovaných akcích** můžete provést opravné akce. Po vyřešení této položky si později vyhodnotí, že byly provedeny doporučené akce a vaše skóre dodržování předpisů se zvýší. Opravené položky se zobrazí jako **předané objekty**.

## <a name="ignore-recommendations"></a>Ignorování doporučení

Pokud máte doporučení, která chcete ignorovat, můžete vytvořit textový soubor, který Azure Monitor použít k tomu, aby se předešlo zobrazování doporučení ve výsledcích hodnocení.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Určení doporučení, která budete ignorovat

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

K vypsání doporučení, která se pro počítače ve vašem prostředí nezdařila, použijte následující dotaz.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Tady je snímek obrazovky, který ukazuje dotaz protokolu: <

![neúspěšná doporučení](media/ad-assessment/ad-failed-recommendations.png)

Vyberte doporučení, která chcete ignorovat. V dalším postupu použijete hodnoty pro RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Vytvoření a použití textového souboru IgnoreRecommendations.txt

1. Vytvořte soubor s názvem IgnoreRecommendations.txt.

2. Vložte nebo zadejte jednotlivé RecommendationIdy pro každé doporučení, které chcete, Azure Monitor ignorovat na samostatném řádku a pak soubor uložte a zavřete.

3. Uložte soubor do následující složky na každém počítači, na kterém chcete ignorovat doporučení Azure Monitor.

   * V počítačích s Microsoft Monitoring Agent (připojené přímo nebo prostřednictvím Operations Manager) – *systémová_jednotka*: \Program Files\Microsoft monitoring Agent\Agent
   * V Operations Manager 2012 R2 management server- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * V Operations Manager 2016 management server- *systémová_jednotka*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Ověření, že se doporučení ignorují

Po dalším naplánovaném spuštění kontroly stavu každých 7 dnů jsou uvedená doporučení označena jako *ignorovaná* a na řídicím panelu se nezobrazí.

1. Pomocí následujících dotazů protokolu můžete zobrazit seznam všech ignorovaných doporučení.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Pokud se později rozhodnete, že chcete zobrazit ignorovaná doporučení, odeberte všechny IgnoreRecommendations.txt soubory nebo z nich můžete odebrat RecommendationIDs.

## <a name="ad-health-check-solutions-faq"></a>Nejčastější dotazy k řešením kontroly stavu AD

*Jaké kontroly provádí řešení AD Assessment?*

* Následující dotaz ukazuje popis všech aktuálně prováděných kontrol:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Výsledky pak lze exportovat do aplikace Excel pro další kontrolu.

*Jak často se spouští kontroly stavu?*

* Tato kontroler se spustí každých 7 dní.

*Existuje způsob, jak nakonfigurovat, jak často se má kontrolovat stav spouštění?*

* V tuto chvíli to není možné.

*Pokud je po přidání řešení kontroly stavu zjištěn jiný server pro, bude zkontrolován*

* Ano, jakmile je zjištěno, že se bude kontrolovat, každých 7 dní.

*Pokud je server vyřazený z provozu, když ho odeberete z kontroly stavu?*

* Pokud server neodesílá data po dobu 3 týdnů, dojde k jejímu odebrání.

*Jaký je název procesu, který provádí shromažďování dat?*

* AdvisorAssessment.exe

*Jak dlouho trvá shromažďování dat?*

* Skutečná shromažďování dat na serveru trvá přibližně 1 hodinu. Může to trvat déle na serverech, které mají velký počet serverů služby Active Directory.

*Existuje způsob, jak nakonfigurovat, kdy se data shromažďují?*

* V tuto chvíli to není možné.

*Proč zobrazit jenom 10 nejčastějších doporučení?*

* Místo toho, abyste vám pomohli vyčerpávající úplný seznam úkolů, doporučujeme nejprve se zaměřit na doporučení přednostních doporučení. Po vyřešení těchto adres budou k dispozici další doporučení. Pokud chcete zobrazit podrobný seznam, můžete zobrazit všechna doporučení pomocí dotazu protokolu.

*Existuje způsob, jak ignorovat doporučení?*

* Ano, podívejte se na část [Ignorovat doporučení](#ignore-recommendations) výše.

## <a name="next-steps"></a>Další kroky

Pomocí [Azure Monitorch dotazů protokolu](../logs/log-query-overview.md) se dozvíte, jak analyzovat podrobné údaje o kontrole stavu a doporučení služby AD.

