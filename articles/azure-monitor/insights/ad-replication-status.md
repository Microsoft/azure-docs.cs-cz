---
title: Monitorování stavu replikace služby Active Directory prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Balíček řešení replikace Active Directory – stav pravidelně monitoruje prostředí služby Active Directory pro všechny chyby replikace.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: 3b7aa932d24b7879ee3f46419afa2327ee48b403
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000989"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorování stavu replikace služby Active Directory prostřednictvím služby Azure Monitor

![Symbol stav replikace AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Služba Active Directory je klíčovou součástí organizace IT prostředí. Chcete-li zajistit vysokou dostupnost a vysoký výkon, každý řadič domény má vlastní kopii databáze služby Active Directory. Řadiče domény replikovat mezi sebou k šíření změn napříč celým podnikem. V tomto procesu replikace mohou způsobit řadu problémů napříč celým podnikem.

Balíček stavem replikace AD řešení pravidelně monitoruje prostředí služby Active Directory pro všechny chyby replikace.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

### <a name="install-agents-on-domain-controllers"></a>Nainstalovat agenty na řadičích domény
Musíte nainstalovat agenty na řadiče domény, které jsou členy domény, který se má vyhodnotit. Nebo, musíte nainstalovat agenty na členských serverech a konfigurace agentů k odesílání dat replikace AD do Azure monitoru. Chcete-li pochopit, jak připojit počítače s Windows do Azure monitoru, přečtěte si téma [počítače připojit Windows do Azure monitoru](../../azure-monitor/platform/agent-windows.md). Pokud řadiče domény už je součástí existujícího prostředí System Center Operations Manager, který chcete připojit k Azure Monitor, naleznete v tématu [připojení Operations Manageru do Azure monitoru](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Povolit řadiči domény
Pokud nechcete, aby všechny řadiče domény připojit přímo do Azure monitoru, můžete použít jakýkoli jiný počítač v doméně připojené k Azure Monitor pro shromažďování dat pro balíček stavem replikace AD řešení a jeho odeslat data.

1. Ověřte, zda je počítač členem domény, který chcete monitorovat stav replikace AD řešení.
2. [Připojte počítač Windows do Azure monitoru](../../azure-monitor/platform/om-agents.md) nebo [připojte se pomocí svého stávajícího prostředí nástroje Operations Manager do Azure monitoru](../../azure-monitor/platform/om-agents.md), pokud už není připojený.
3. V tomto počítači nastavte následující klíč registru:<br>Klíč: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Hodnota: **IsTarget**<br>Údaj hodnoty: **true**

   > [!NOTE]
   > Tyto změny se projeví až do vaší restartování služby Microsoft Monitoring Agent (HealthService.exe).
### <a name="install-solution"></a>Instalace řešení
Postupujte podle procesu popsaného v [nainstalovat řešení pro monitorování](solutions.md#install-a-monitoring-solution) přidáte **stav replikace služby Active Directory** řešení do pracovního prostoru Log Analytics. Není nutná žádná další konfigurace.


## <a name="ad-replication-status-data-collection-details"></a>Podrobnosti kolekce dat stavu replikace AD
V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak data se shromažďují pro stav replikace AD.

| Platforma | Přímý agent | Agent nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta nástroje SCOM odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |každých pět dní |



## <a name="understanding-replication-errors"></a>Vysvětlení chyb replikace

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Stav replikace AD dlaždici se zobrazuje počet chyb replikace aktuálně máte. **Kritické chyby replikace** jsou chyby, které jsou dosahovalo nebo přesahovalo 75 % [životnosti objektů označených jako neplatné](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) pro vaši doménovou strukturu služby Active Directory.

![Dlaždice se stavem replikace AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknutí na dlaždici zobrazíte další informace o chybách.
![Řídicí panel stav replikace AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stav cílového serveru a stav zdrojového serveru
Tyto sloupce se zobrazí stav cílové servery a zdrojové servery, na kterých dochází k chybám replikace. Číslo za každý název řadiče domény označuje číslo chyby replikace na příslušném řadiči domény.

Chyby pro servery zdrojové a cílové servery jsou zobrazit, protože některé problémy se snadněji řešit z perspektivy zdrojového serveru a další z hlediska cílový server.

V tomto příkladu vidíte, že mnoho cílových serverů mají přibližně stejný počet chyb, ale je jeden server zdroje (ADDC35), který má mnoho chyb více než všechny ostatní. Je pravděpodobné, že je něco v nepořádku v ADDC35, který je příčinou selhání k odesílání dat do jeho replikaci. Řešení potíží na ADDC35 vyřešit řadu chyb, které se zobrazí v oblasti cílový server.

### <a name="replication-error-types"></a>Typy chyb replikace
Tato oblast poskytuje informace o typech chyby zjištěné v celé organizaci. Každá chyba má jedinečný číselný kód a napište zprávu, která vám pomohou určit hlavní příčinu chyby.

Prstencový v horní části vám dává představu, z které chyby objevit více a méně často ve vašem prostředí.

To se dozvíte, když víc řadičů domény dojít ke stejné chybě replikace. V takovém případě může mít možnost zjistit nebo identifikovat řešení na jednom řadiči domény a potom opakovat na jiných řadičích domény, které jsou ovlivněny ke stejné chybě.

### <a name="tombstone-lifetime"></a>Životnost objektů označených jako neplatné
Životnost objektů označených jako neplatné Určuje, jak dlouho odstraněný objekt, označuje jako nepotřebná data, se uchovávají v databázi služby Active Directory. Když je odstraněný objekt úspěšné životnosti objektů označených jako neplatné, proces shromažďování uvolňování paměti ji automaticky odstraní z databáze služby Active Directory.

Je výchozí dobu, po 180 dnech pro nejnovější verze systému Windows, ale byla 60 dní u starších verzí a lze jej změnit explicitně microsoftem nebo správcem Active Directory.

Je důležité vědět, pokud máte chyby replikace, které se blíží nebo uplynula doba života objektů označených jako neplatné. Pokud dva řadiče domény, který bude zachován po dobu, po chybě replikace, replikace je zakázaná mezi tyto dva řadiče domény, i v případě, že podkladová chyba je pevná.

Oblasti životnosti objektů označených jako neplatné pomáhá identifikovat místa, kde je zakázané replikace nebezpečí děje. Všechny chyby v **více než 100 % TSL** kategorie představuje oddíl, který nebyla replikována mezi jeho zdrojový a cílový server pro alespoň životnosti objektů označených jako neplatné pro doménovou strukturu.

V takovém případě stačí oprava chyby replikace nebude stačit. Přinejmenším budete muset ručně prozkoumat k identifikaci a vyčištění přetrvávání odstraněných objektů, než je možné restartovat replikaci. Můžete dokonce vyřadit z provozu řadiče domény.

Kromě identifikace chyby replikace, ke kterým nebyla uložena po dobu životnosti objektů označených jako neplatné, potřebujete věnovat pozornost nějaké chyby, které spadají do **50-75 % TSL** nebo **75 100 % TSL** kategorií.

Toto jsou chyby, které jsou jasně přetrvávajících odstraněných, není přechodná, proto potřebují pravděpodobně váš zásah, chcete-li vyřešit. Dobrou zprávou je, že se zatím nedosáhly životnosti objektů označených jako neplatné. Je-li vyřešit tyto problémy o tom bezodkladně informuje a *před* dosáhnou životnosti objektů označených jako neplatné, replikace můžete restartovat s minimálními ručního zásahu.

Jak je uvedeno výše, dlaždici řídicího panelu pro stav replikace AD řešení zobrazuje počet *kritické* chyby replikace ve vašem prostředí, která je definována jako chyby, které jsou více než 75 % životnosti objektů označených jako neplatné (včetně chyby které jsou více než 100 % protokol TLS). Přitom se snaží zachovat toto číslo na 0.

> [!NOTE]
> Všechny výpočty značek odstraněných položek životnost procento jsou založené na skutečné neplatného pro vaši doménovou strukturu služby Active Directory, kterému můžete důvěřovat, že jsou tyto procenta přesné, i v případě, že je nutné nastavit hodnotu doby života vlastních značek odstraněných položek.
>
>

### <a name="ad-replication-status-details"></a>Podrobnosti o stavu replikace AD
Po kliknutí na libovolnou položku v jednom seznamu se zobrazí další podrobnosti o použití protokolu dotazu. Výsledky se filtrují na Zobrazit pouze chyby související s danou položku. Například pokud kliknete na první řadič domény se uveden v části **stav cílového serveru (ADDC02)**, se zobrazí výsledky dotazu vyfiltrovaný tak, aby zobrazit chyby se tento řadič domény uvedený jako cílový server:

![Chyby stavu replikace AD ve výsledcích dotazu](./media/ad-replication-status/oms-ad-replication-search-details.png)

Z tohoto místa můžete dále filtrovat, upravte dotaz protokolu a podobně. Další informace o použití dotazů protokolu ve službě Azure Monitor, naleznete v tématu [analyzovat data protokolů ve službě Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

**HelpLink** poli se zobrazí adresa URL stránky TechNet s dalšími podrobnosti o této konkrétní chyba. Můžete zkopírovat a vložit tento odkaz do okna prohlížeče, pokud chcete zobrazit informace o řešení potíží a opravě chyby.

Můžete také kliknout na **exportovat** exportovat výsledky do Excelu. Export dat můžete vizualizovat data chyby replikace žádným způsobem, který byste chtěli.

![exportované chyby stavu replikace AD v aplikaci Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Stav replikace AD – nejčastější dotazy
**Otázka: Jak často se data Stav replikace AD aktualizovat?**
Odpověď: Informace jsou aktualizovány každých pět dní.

**Otázka: Existuje způsob, jak konfigurovat, jak často tato data se aktualizují?**
Odpověď: V tuto chvíli to není možné.

**Otázka: Je nutné přidat všechny moje řadičů domény do pracovního prostoru Log Analytics Chcete-li zobrazit stav replikace?**
Odpověď: Ne, je nutné přidat jenom jeden řadič domény. Pokud máte víc řadičů domény ve vašem pracovním prostoru Log Analytics, data ze všech z nich se odesílají do Azure monitoru.

**Otázka: Nechcete přidat žádné řadiče domény do pracovního prostoru Log Analytics Použití řešení stav replikace AD**

Odpověď: Ano. Můžete nastavit jako hodnotu klíče registru, aby je. Zobrazit [povolit řadiči domény](#enable-non-domain-controller).

**Otázka: Jaký je název procesu, která provádí sběr dat?**
Odpověď: AdvisorAssessment.exe

**Otázka: Jak dlouho trvá shromažďování dat?**
Odpověď: Doba shromažďování dat závisí na velikosti prostředí služby Active Directory, ale obvykle trvá méně než 15 minut.

**Otázka: Jaký typ dat se shromažďují?**
Odpověď: Replikace informace jsou shromažďovány prostřednictvím protokolu LDAP.

**Otázka: Existuje způsob, jak konfigurovat, když se shromažďují data?**
Odpověď: V tuto chvíli to není možné.

**Otázka: Jaká oprávnění potřebuji pro shromažďování dat?**
Odpověď: Postačují oprávněními běžných uživatelů do služby Active Directory.

## <a name="troubleshoot-data-collection-problems"></a>Poradce při potížích kolekce dat
Aby bylo možné shromažďovat data, balíček řešení stav replikace AD vyžaduje aspoň jeden řadič domény k připojení k pracovnímu prostoru Log Analytics. Až se připojíte řadič domény, zobrazí se zpráva označující, že **data se stále shromažďují**.

Pokud potřebujete pomoc s připojením jeden z řadičů domény, můžete zobrazit dokumentaci na [počítače připojit Windows do Azure monitoru](../../azure-monitor/platform/om-agents.md). Případně, pokud vaše řadiče domény je již připojen do existujícího prostředí System Center Operations Manager, dokumentaci si můžete prohlédnout v [připojení System Center Operations Manageru do Azure monitoru](../../azure-monitor/platform/om-agents.md).

Pokud nechcete, aby všechny řadiče domény připojit přímo do Azure Monitor nebo System Center Operations Manageru, najdete v článku [povolit řadiči domény](#enable-non-domain-controller).

## <a name="next-steps"></a>Další postup
* Použití [protokolu dotazů ve službě Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) zobrazíte podrobné údaje o stavu replikace služby Active Directory.
