---
title: Monitorování stavu replikace služby Active Directory s Azure Log Analytics | Dokumentace Microsoftu
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
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: be519dd2a6e6948bedeedd4166c7adf466dbb365
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836368"
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Monitorování stavu replikace služby Active Directory pomocí služby Log Analytics

![Symbol stav replikace AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Služba Active Directory je klíčovou součástí organizace IT prostředí. Chcete-li zajistit vysokou dostupnost a vysoký výkon, každý řadič domény má vlastní kopii databáze služby Active Directory. Řadiče domény replikovat mezi sebou k šíření změn napříč celým podnikem. V tomto procesu replikace mohou způsobit řadu problémů napříč celým podnikem.

Balíček stavem replikace AD řešení pravidelně monitoruje prostředí služby Active Directory pro všechny chyby replikace.

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Musíte nainstalovat agenty na řadiče domény, které jsou členy domény, který se má vyhodnotit. Nebo, musíte nainstalovat agenty na členských serverech a konfigurace agentů k odesílání dat replikace AD do služby Log Analytics. Chcete-li pochopit, jak připojit počítače s Windows ke službě Log Analytics, přečtěte si téma [počítače Windows se připojit ke službě Log Analytics](../../azure-monitor/platform/agent-windows.md). Pokud řadiče domény už je součástí existujícího prostředí System Center Operations Manager, který chcete připojit ke službě Log Analytics, najdete v článku [připojení Operations Manageru k Log Analytics](../../log-analytics/log-analytics-om-agents.md).
* Přidejte řešení pro stav replikace služby Active Directory do pracovního prostoru Log Analytics pomocí postupu popsaného v [přidání řešení Log Analytics z Galerie řešení](../../azure-monitor/insights/solutions.md).  Není nutná žádná další konfigurace.

## <a name="ad-replication-status-data-collection-details"></a>Podrobnosti kolekce dat stavu replikace AD
V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak data se shromažďují pro stav replikace AD.

| Platforma | Přímý agent | Agent nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta nástroje SCOM odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |každých pět dní |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Volitelně můžete povolte řadiči domény k odesílání dat AD do služby Log Analytics
Pokud nechcete, aby na všech řadičích domény připojit přímo k Log Analytics, můžete použít jakýkoli jiný počítač v doméně připojené ke službě Log Analytics pro shromažďování dat pro balíček stavem replikace AD řešení a jeho odeslat data.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Chcete-li povolit řadiči domény k odesílání dat AD do služby Log Analytics
1. Ověřte, zda je počítač členem domény, který chcete monitorovat stav replikace AD řešení.
2. [Připojte počítač Windows ke službě Log Analytics](../../log-analytics/log-analytics-om-agents.md) nebo [připojte se pomocí svého stávajícího prostředí Operations Manageru k Log Analytics](../../log-analytics/log-analytics-om-agents.md), pokud už není připojený.
3. V tomto počítači nastavte následující klíč registru:

   * Klíč: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management skupiny\<ManagementGroupName > \Solutions\ADReplication**
   * Hodnota: **IsTarget**
   * Údaj hodnoty: **true**

   > [!NOTE]
   > Tyto změny se projeví až do vaší restartování služby Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Vysvětlení chyb replikace
Jakmile budete mít data Stav replikace AD odeslaných do Log Analytics, zobrazí se dlaždice podobně jako na následujícím obrázku v Log Analytics označující počet chyby replikace momentálně máte.  
![Dlaždice se stavem replikace AD](./media/ad-replication-status/oms-ad-replication-tile.png)

**Kritické chyby replikace** jsou chyby, které jsou dosahovalo nebo přesahovalo 75 % [životnosti objektů označených jako neplatné](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) pro vaši doménovou strukturu služby Active Directory.

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
Po kliknutí na libovolnou položku v jednom seznamu se zobrazí další podrobnosti o něm pomocí prohledávání protokolů. Výsledky se filtrují na Zobrazit pouze chyby související s danou položku. Například pokud kliknete na první řadič domény se uveden v části **stav cílového serveru (ADDC02)**, se zobrazí výsledky hledání filtrovat, aby zobrazit chyby se tento řadič domény uvedený jako cílový server:

![Chyby stavu replikace AD ve výsledcích hledání](./media/ad-replication-status/oms-ad-replication-search-details.png)

Z tohoto místa můžete dále filtrovat, Upravit vyhledávací dotaz a podobně. Další informace o používání prohledávání protokolů, najdete v části [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md).

**HelpLink** poli se zobrazí adresa URL stránky TechNet s dalšími podrobnosti o této konkrétní chyba. Můžete zkopírovat a vložit tento odkaz do okna prohlížeče, pokud chcete zobrazit informace o řešení potíží a opravě chyby.

Můžete také kliknout na **exportovat** exportovat výsledky do Excelu. Export dat můžete vizualizovat data chyby replikace žádným způsobem, který byste chtěli.

![exportované chyby stavu replikace AD v aplikaci Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Stav replikace AD – nejčastější dotazy
**Otázka: jak často jsou data Stav replikace AD aktualizovat?**
Odpověď: informace se aktualizuje každých pět dní.

**Otázka: existuje způsob, jak konfigurovat, jak často tato data se aktualizují?**
Odpověď: v tuto chvíli.

**Dotaz: Potřebuji přidejte všechny moje řadičů domény do pracovního prostoru Log Analytics, chcete-li zobrazit stav replikace?**
Odpověď: Ne, je nutné přidat jenom jeden řadič domény. Pokud máte víc řadičů domény ve vašem pracovním prostoru Log Analytics, data ze všech z nich odesílají do Log Analytics.

**Otázka: nemůžu nechcete přidat žádné řadiče domény do pracovního prostoru Log Analytics. Použití řešení stav replikace AD**
Odpověď: Ano. Můžete nastavit jako hodnotu klíče registru, aby je. Zobrazit [povolit řadiči domény k odesílání dat AD do služby Log Analytics](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**D: Jaký je název procesu, která provádí sběr dat?**
A: AdvisorAssessment.exe

**Otázka: jak dlouho trvá shromažďování dat?**
A: doba shromažďování dat závisí na velikosti prostředí služby Active Directory, ale obvykle trvá méně než 15 minut.

**Otázka: Jaký typ dat se shromažďují?**
Odpověď: replikace informace jsou shromažďovány prostřednictvím protokolu LDAP.

**Otázka: existuje způsob, jak konfigurovat, když se shromažďují data?**
Odpověď: v tuto chvíli.

**D: jaká oprávnění potřebuji pro shromažďování dat?**
Odpověď: normální uživatelských oprávnění k Active Directory je dostatečné.

## <a name="troubleshoot-data-collection-problems"></a>Poradce při potížích kolekce dat
Aby bylo možné shromažďovat data, balíček řešení stav replikace AD vyžaduje aspoň jeden řadič domény k připojení k pracovnímu prostoru Log Analytics. Až se připojíte řadič domény, zobrazí se zpráva označující, že **data se stále shromažďují**.

Pokud potřebujete pomoc s připojením jeden z řadičů domény, můžete zobrazit dokumentaci na [počítače Windows se připojit ke službě Log Analytics](../../log-analytics/log-analytics-om-agents.md). Případně, pokud vaše řadiče domény je již připojen do existujícího prostředí System Center Operations Manager, dokumentaci si můžete prohlédnout v [připojení System Center Operations Manageru k Log Analytics](../../log-analytics/log-analytics-om-agents.md).

Pokud nechcete, aby všechny řadiče domény připojit přímo ke službě Log Analytics nebo System Center Operations Manager, najdete v článku [povolit řadiči domény k odesílání dat AD do služby Log Analytics](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](../../azure-monitor/log-query/log-query-overview.md) zobrazíte podrobné údaje o stavu replikace služby Active Directory.
