---
title: Monitorovat stav replikace služby Active Directory
description: Sada Active Directory Replication Status Pack řešení pravidelně monitoruje prostředí služby Active Directory při selhání replikace.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 01d641801b1b9a0cfaa4fd7ee11e3c55314dc53c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577522"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorovat stav replikace služby Active Directory pomocí Azure Monitor

![Symbol AD Replication Status](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory je klíčová součást podnikového IT prostředí. Pro zajištění vysoké dostupnosti a vysokého výkonu má každý řadič domény svou vlastní kopii databáze služby Active Directory. Řadiče domény se vzájemně replikují za účelem rozšíření změn napříč podnikem. Selhání v rámci tohoto procesu replikace může způsobit nejrůznější problémy v celém podniku.

Řešení AD Replication Status pravidelně monitoruje prostředí Active Directory pro všechny chyby při replikaci.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

### <a name="prerequisites"></a>Požadavky

* Řešení AD Replication Status vyžaduje, aby na každém počítači, který má Log Analytics agenta pro Windows, byla nainstalovaná podporovaná verze .NET Framework 4.6.2 nebo novější (označovaná taky jako Microsoft Monitoring Agent (MMA)).  Agent používá System Center 2016-Operations Manager, Operations Manager 2012 R2 a Azure Monitor.
* Řešení podporuje řadiče domény se systémy Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2 a Windows Server 2016.
* Pracovní prostor Log Analytics pro přidání řešení kontroly stavu služby Active Directory z webu Azure Marketplace v Azure Portal. Není vyžadována žádná další konfigurace.


### <a name="install-agents-on-domain-controllers"></a>Instalace agentů na řadičích domény
Musíte nainstalovat agenty na řadiče domény, které jsou členy domény k vyhodnocení. Nebo musíte nainstalovat agenty na členské servery a nakonfigurovat agenty tak, aby odesílali data replikace AD na Azure Monitor. Informace o tom, jak připojit počítače s Windows k Azure Monitor najdete v tématu [připojení počítačů s Windows k Azure monitor](../agents/agent-windows.md). Pokud je váš řadič domény už součástí stávajícího System Center Operations Manager prostředí, ke kterému se chcete připojit Azure Monitor, přečtěte si téma [připojení Operations Manager k Azure monitor](../agents/om-agents.md).

### <a name="enable-non-domain-controller"></a>Povolit jiný řadič než řadič domény
Pokud nechcete, aby se žádné řadiče domény připojovaly přímo k Azure Monitor, můžete použít jakýkoli jiný počítač ve vaší doméně, který je připojený k Azure Monitor ke shromažďování dat pro AD Replication Status balíček řešení a jeho odeslání.

1. Ověřte, zda je počítač členem domény, kterou chcete monitorovat pomocí řešení AD Replication Status.
2. [Připojte počítač se systémem Windows, aby se Azure monitor](../agents/om-agents.md) , nebo [ho propojte pomocí stávajícího Operations Manager prostředí s Azure monitor](../agents/om-agents.md), pokud ještě není připojený.
3. V tomto počítači nastavte následující klíč registru:<br>Klíč: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Hodnota: **cíl**<br>Údaj hodnoty: **true**

   > [!NOTE]
   > Tyto změny se projeví až po restartování služby Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Nainstalovat řešení
   > Postupujte podle kroků popsaných v tématu [instalace řešení monitorování](solutions.md#install-a-monitoring-solution) a přidejte **Active Directory Replication status** řešení do pracovního prostoru Log Analytics. Není nutná žádná další konfigurace.


## <a name="ad-replication-status-data-collection-details"></a>Podrobnosti o AD Replication Status shromažďování dat
V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují pro AD Replication Status.

| platforma | Přímý Agent | Agent SCOM | Azure Storage | Vyžaduje se SCOM? | Data agenta SCOM odesílaná prostřednictvím skupiny pro správu | frekvence shromažďování |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |každých pět dní |



## <a name="understanding-replication-errors"></a>Porozumění chybám replikace

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Na dlaždici AD Replication Status se zobrazuje počet chyb replikace, které aktuálně máte. **Kritické chyby replikace** jsou chyby, které jsou v doménové struktuře služby Active Directory nebo jsou vyšší než 75% [doby platnosti označení za neplatné](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) .

![Dlaždice AD Replication Status](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknutí na dlaždici si můžete zobrazit další informace o chybách.
![Řídicí panel AD Replication Status](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stav cílového serveru a stav zdrojového serveru
Tyto sloupce zobrazují stav cílových serverů a zdrojových serverů, u kterých dochází k chybám replikace. Číslo po názvu každého řadiče domény indikuje počet chyb replikace v řadiči domény.

Zobrazí se chyby pro cílové servery i zdrojové servery, protože některé problémy se snáze odstraňují z perspektivy zdrojového serveru a dalších z perspektivy cílového serveru.

V tomto příkladu vidíte, že mnoho cílových serverů má zhruba stejný počet chyb, ale existuje jeden zdrojový server (ADDC35), který má mnoho dalších chyb než všechny ostatní. Je možné, že došlo k nějakému problému v ADDC35, který způsobuje, že se nepodaří odeslat data partnerům pro replikaci. Řešení problémů v ADDC35 může vyřešit mnoho chyb, které se zobrazují v oblasti cílového serveru.

### <a name="replication-error-types"></a>Typy chyb replikace
Tato oblast vám poskytne informace o typech chyb zjištěných v celém podniku. Každá chyba má jedinečný číselný kód a zprávu, která vám může pomáhat určit hlavní příčinu chyby.

Prstenec v horní části poskytuje představu o tom, které chyby se ve vašem prostředí vyskytují častěji a méně často.

Zobrazuje se v případě, že při více řadičích domény dochází k jedné chybě replikace. V takovém případě budete možná schopni zjistit nebo identifikovat řešení na jednom řadiči domény a pak ho opakovat na dalších řadičích domény ovlivněných stejnou chybou.

### <a name="tombstone-lifetime"></a>Doba platnosti označení za neplatný
Doba platnosti označení za neplatné určuje, jak dlouho se odstraněný objekt, na který se odkazuje jako na označenou jako neplatný, zachová v databázi služby Active Directory. Když odstraněný objekt předá dobu neaktivního označení, proces uvolňování paměti ho automaticky odebere z databáze služby Active Directory.

Výchozí doba platnosti neaktivních položek je 180 dní pro nejnovější verze Windows, ale ve starších verzích bylo 60 dní a je možné ji explicitně změnit správcem služby Active Directory.

Je důležité, abyste věděli, jestli máte chyby replikace, ke kterým se přiblížíte, nebo které jsou po dobu životnosti neplatné. Pokud se u dvou řadičů domény setkáte s chybou replikace, která přetrvává po dobu platnosti označení za neplatné, replikace se mezi těmito dvěma řadiči domény vypne, i když je opravená základní Chyba replikace.

Oblast životnosti označená jako neplatná vám pomůže identifikovat místa, kde dochází k nenebezpečí zakázané replikace. Každá chyba v kategorii **Over 100% TSL** představuje oddíl, který se nereplikuje mezi zdrojovým a cílovým serverem minimálně pro dobu života doménové struktury.

V takové situaci nebude stačit opravit chybu replikace. Aby bylo možné restartovat replikaci, je třeba ručně prozkoumat, abyste identifikovali a vyčistili své objekty. Možná budete muset i vyřadit z provozu řadič domény.

Kromě Identifikace chyb replikace, které byly trvale trvalé po dobu životnosti, je také vhodné věnovat pozornost jakýmkoli chybám, které spadají do kategorií **50-75% TSL** nebo **75-100% TSL** .

Jedná se o chyby, které se jasně odstraňují, nejsou přechodné, takže pravděpodobně potřebují vyřešit váš zásah. Dobrá zpráva je, že ještě nedosáhla doby platnosti označení za neplatné. Pokud tyto problémy opravíte rychle a předtím, *než* dosáhnou doby neplatnosti, replikace se může restartovat s minimálním ručním zásahem.

Jak bylo uvedeno dříve, dlaždice řídicího panelu pro AD Replication Status řešení zobrazuje počet *kritických* chyb replikace ve vašem prostředí, které jsou definovány jako chyby, které jsou větší než 75% doby platnosti označení (včetně chyb, které jsou větší než 100% TSL). Snažte se zachovat toto číslo na 0.

> [!NOTE]
> Všechny výpočetních procentuálních hodnot životnosti za neplatné jsou založené na skutečné době neaktivních operací v doménové struktuře služby Active Directory, takže můžete důvěřovat tomu, že tyto procentuální hodnoty jsou přesné, a to i v případě, že máte nastavenou hodnotu vlastní doby platnosti
>
>

### <a name="ad-replication-status-details"></a>Podrobnosti o stavu replikace AD
Když kliknete na libovolnou položku v seznamu, zobrazí se další podrobnosti s použitím dotazu protokolu. Výsledky se filtrují tak, aby se zobrazily jenom chyby související s touto položkou. Pokud například kliknete na první řadič domény uvedený pod položkou **Stav cílového serveru (ADDC02)**, zobrazí se výsledky dotazu, které zobrazí chyby s řadičem domény uvedeným jako cílový server:

![Chyby stavu replikace AD ve výsledcích dotazu](./media/ad-replication-status/oms-ad-replication-search-details.png)

Odsud můžete filtrovat další, upravovat dotaz protokolu a tak dále. Další informace o používání dotazů protokolu v Azure Monitor najdete v tématu [Analýza dat protokolu v Azure monitor](../logs/log-query-overview.md).

V poli **HelpLink** se zobrazuje adresa URL stránky TechNet s dalšími podrobnostmi o této konkrétní chybě. Můžete zkopírovat a vložit tento odkaz do okna prohlížeče, abyste viděli informace o řešení potíží a opravení chyby.

Můžete také kliknout na tlačítko **exportovat** a exportovat výsledky do aplikace Excel. Export dat vám umožní vizualizovat data o chybách replikace jakýmkoli způsobem, který byste chtěli.

![exportované chyby stavu replikace AD v Excelu](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Nejčastější dotazy k AD Replication Status
**Otázka: jak často se aktualizují data stavu replikace AD?**
Odpověď: informace se aktualizují každých pět dní.

**Otázka: existuje způsob, jak nakonfigurovat, jak často se tato data aktualizují?**
Odpověď: ne v tomto okamžiku.

**Otázka: musím do svého pracovního prostoru Log Analytics přidat všechny řadiče domény, aby se zobrazil stav replikace?**
Odpověď: Ne, je nutné přidat pouze jeden řadič domény. Máte-li v pracovním prostoru Log Analytics více řadičů domény, budou do Azure Monitor odesílána data ze všech těchto řadičů.

**Otázka: Nechci do pracovního prostoru Log Analytics přidat žádné řadiče domény. Můžu pořád používat řešení AD Replication Status?**

Odpověď: Ano. Můžete nastavit hodnotu klíče registru, abyste ho mohli povolit. Viz [Povolení neřadič domény](#enable-non-domain-controller).

**Otázka: Jaký je název procesu, který provádí shromažďování dat?**
ODPOVĚĎ: AdvisorAssessment.exe

**Otázka: jak dlouho trvá shromažďování dat?**
Odpověď: čas shromažďování dat závisí na velikosti prostředí služby Active Directory, ale obvykle trvá méně než 15 minut.

**Otázka: jaký typ dat se shromáždí?**
Odpověď: informace o replikaci jsou shromažďovány prostřednictvím protokolu LDAP.

**Otázka: existuje způsob, jak nakonfigurovat, kdy se data shromažďují?**
Odpověď: ne v tomto okamžiku.

**Otázka: jaká oprávnění potřebuji ke shromažďování dat?**
O: jsou dostatečná standardní uživatelská oprávnění ke službě Active Directory.

## <a name="troubleshoot-data-collection-problems"></a>Řešení problémů se shromažďováním dat
Aby bylo možné shromažďovat data, AD Replication Status sada řešení vyžaduje, aby byl k pracovnímu prostoru Log Analytics připojen alespoň jeden řadič domény. Dokud nepřipojíte řadič domény, zobrazí se zpráva oznamující, že **se data stále shromažďují**.

Pokud potřebujete pomoc s připojením k jednomu z řadičů domény, můžete zobrazit dokumentaci v části [připojení počítačů se systémem Windows k Azure monitor](../agents/om-agents.md). Případně, pokud je váš řadič domény už připojený k existujícímu System Center Operations Manager prostředí, můžete zobrazit dokumentaci na adrese [připojení System Center Operations Manager a Azure monitor](../agents/om-agents.md).

Pokud nechcete, aby se žádné řadiče domény připojovaly přímo k Azure Monitor nebo System Center Operations Manager, přečtěte si téma [povolení jiného řadiče](#enable-non-domain-controller)domény.

## <a name="next-steps"></a>Další kroky
* K zobrazení podrobných dat o stavu replikace služby Active Directory použijte [dotazy protokolu v Azure monitor](../logs/log-query-overview.md) .

