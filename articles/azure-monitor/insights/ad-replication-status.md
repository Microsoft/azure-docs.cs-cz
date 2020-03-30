---
title: Sledování stavu replikace služby Active Directory
description: Sada řešení Stav replikace služby Active Directory pravidelně monitoruje prostředí služby Active Directory, aby neumějí případné chyby replikace.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055901"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Sledování stavu replikace služby Active Directory pomocí nástroje Azure Monitor

![Symbol stavu replikace služby AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Služba Active Directory je klíčovou součástí podnikového it prostředí. Aby byla zajištěna vysoká dostupnost a vysoký výkon, má každý řadič domény vlastní kopii databáze služby Active Directory. Řadiče domény se vzájemně replikují, aby mohly šířit změny v rámci celé ho sazbě. Chyby v tomto procesu replikace může způsobit řadu problémů v rámci rozlehlé sítě.

Řešení Stavu replikace služby AD pravidelně sleduje prostředí služby Active Directory, pokud nenapájí všechny chyby replikace.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

### <a name="prerequisites"></a>Požadavky

* Řešení Stavu replikace služby AD vyžaduje nainstalovanou podporovanou verzi rozhraní .NET Framework 4.6.2 nebo vyšší v každém počítači, který má nainstalovaného agenta Log Analytics pro systém Windows (označovaného také jako Agent monitorování společnosti Microsoft (MMA).  Agent a používá System Center 2016 – Operations Manager, Operations Manager 2012 R2 a Azure Monitor.
* Toto řešení podporuje řadiče domény se systémem Windows Server 2008 a 2008 R2, Windows Server 2012 a 2012 R2 a Windows Server 2016.
* Pracovní prostor Analýzy protokolů pro přidání řešení kontroly stavu služby Active Directory z webu Azure Marketplace na webu Azure Portal. Není vyžadována žádná další konfigurace.


### <a name="install-agents-on-domain-controllers"></a>Instalace agentů v řadičích domény
Je nutné nainstalovat agenty do řadičů domény, které jsou členy domény, která má být vyhodnocena. Nebo je nutné nainstalovat agenty na členské servery a nakonfigurovat agenty tak, aby odesílali data replikace služby AD do služby Azure Monitor. Informace o tom, jak připojit počítače se systémem Windows ke službě Azure Monitor, najdete [v tématu Připojení počítačů s Windows k programu Azure Monitor](../../azure-monitor/platform/agent-windows.md). Pokud je váš řadič domény již součástí existujícího prostředí System Center Operations Manager, které chcete připojit k Azure Monitoru, přečtěte si část [Připojení nástroje Operations Manager k nástroji Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Povolení řadiče domény, který není v oblasti domény
Pokud nechcete připojit žádný řadič domény přímo k Azure Monitoru, můžete použít jakýkoli jiný počítač ve vaší doméně připojené k Azure Monitor u shromažďovat data pro balíček řešení stav replikace služby AD a nechat je odeslat data.

1. Ověřte, zda je počítač členem domény, kterou chcete sledovat pomocí řešení Stavu replikace služby AD.
2. [Připojte počítač s Windows k Azure Monitoru](../../azure-monitor/platform/om-agents.md) nebo [ho připojte pomocí stávajícího prostředí Operations Manager u Azure Monitoru](../../azure-monitor/platform/om-agents.md), pokud ještě není připojený.
3. V tomto počítači nastavte následující klíč registru:<br>Klíč: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<Groups ManagementGroupName>\Solutions\ADReplication**<br>Hodnota: **Istarget**<br>Data hodnoty: **true**

   > [!NOTE]
   > Tyto změny se projeví až po restartování služby Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Instalační řešení
   > Postupujte podle postupu popsaného v [části Instalace monitorovacího řešení](solutions.md#install-a-monitoring-solution) a přidejte řešení stavu **replikace služby Active Directory** do pracovního prostoru Analýzy protokolů. Není nutná žádná další konfigurace.


## <a name="ad-replication-status-data-collection-details"></a>Podrobnosti o shromažďování dat o stavu replikace služby AD
V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak jsou data shromažďována pro stav replikace služby AD.

| platforma | Přímý agent | Agent SCOM | Azure Storage | SCOM požadované? | Data agenta SCOM odeslaná prostřednictvím skupiny pro správu | sběrová frekvence |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |každých pět dní |



## <a name="understanding-replication-errors"></a>Principy chyb replikace

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Dlaždice Stav replikace služby AD zobrazuje, kolik chyb replikace aktuálně máte. **Kritické chyby replikace** jsou chyby, které jsou na nebo vyšší než 75 % [životnosti označených jako neplatné](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) pro doménovou strukturu služby Active Directory.

![Dlaždice Stavu replikace služby AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknutí na dlaždici můžete zobrazit další informace o chybách.
![Řídicí panel Stavu replikace služby AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stav cílového a zdrojového serveru
V těchto sloupcích se zobrazuje stav cílových serverů a zdrojových serverů, u kterých dochází k chybám replikace. Číslo za každým názvem řadiče domény označuje počet chyb replikace v tomto řadiči domény.

Chyby pro cílové i zdrojové servery jsou zobrazeny, protože některé problémy jsou snadněji řešit z hlediska zdrojového serveru a jiné z hlediska cílového serveru.

V tomto příkladu můžete vidět, že mnoho cílových serverů má zhruba stejný počet chyb, ale existuje jeden zdrojový server (ADDC35), který má mnohem více chyb než všechny ostatní. Je pravděpodobné, že je nějaký problém na ADDC35, který způsobuje, že se nezdaří odeslat data svým replikačním partnerům. Řešení problémů na ADDC35 může vyřešit mnoho chyb, které se zobrazí v oblasti cílového serveru.

### <a name="replication-error-types"></a>Typy chyb replikace
Tato oblast poskytuje informace o typech chyb zjištěných v rámci celého podniku. Každá chyba má jedinečný číselný kód a zprávu, která vám může pomoci určit hlavní příčinu chyby.

Kobliha v horní části vám dává představu o tom, které chyby se objevují stále méně často ve vašem prostředí.

Zobrazuje, když se u více řadičů domény vyskytne stejná chyba replikace. V takovém případě můžete zjistit nebo identifikovat řešení na jednom řadiči domény a potom jej opakovat v jiných řadičích domény, kterých se týká stejná chyba.

### <a name="tombstone-lifetime"></a>Životnost náhrobku
Životnost označených jako neplatné určuje, jak dlouho bude odstraněný objekt označovaný jako označený jako neoznačený, uchován v databázi služby Active Directory. Když odstraněný objekt předá životnost označených jako neplatné, proces uvolňování paměti jej automaticky odebere z databáze služby Active Directory.

Výchozí životnost označení za neplatné je 180 dní pro nejnovější verze systému Windows, ale ve starších verzích byla 60 dní a správce služby Active Directory ji může explicitně změnit.

Je důležité vědět, pokud máte chyby replikace, které se blíží nebo jsou v minulosti životnost označených jako neplatné. Pokud u dvou řadičů domény dojde k chybě replikace, která přetrvává i po doporučování označení za neplatné, je replikace mezi těmito dvěma řadiči domény zakázána, a to i v případě, že je opravena základní chyba replikace.

Oblast Životnost označených jako neplatné vám pomůže identifikovat místa, kde je ohrožena nezakázaná replikace. Každá chyba v kategorii **Více než 100 % TSL** představuje oddíl, který nebyl replikován mezi zdrojovým a cílovým serverem alespoň po dobu platnosti označení za neplatné pro doménovou strukturu.

V takovém případě jednoduše oprava chyby replikace nebude stačit. Minimálně je třeba ručně prozkoumat k identifikaci a vyčištění permanentní objekty před restartováním replikace. Možná dokonce budete muset vyřadit řadič domény z provozu.

Kromě identifikace všech chyb replikace, které přetrvávaly po životnosti označených jako neplatné, chcete také věnovat pozornost chybám, které spadají do kategorií **TSL 50-75%** nebo **75-100% TSL.**

Jedná se o chyby, které jsou jasně přetrvávající, ne přechodné, takže pravděpodobně potřebují váš zásah k vyřešení. Dobrou zprávou je, že ještě nedosáhli životnosti náhrobku. Pokud tyto problémy opravíte okamžitě a *před* dosažením životnosti označených jako neplatné, replikace může restartovat s minimální ruční zásah.

Jak již bylo uvedeno dříve, dlaždice řídicího panelu pro řešení stavu replikace služby AD zobrazuje počet *kritických* chyb replikace ve vašem prostředí, které jsou definovány jako chyby, které jsou více než 75 % životnosti označených jako neplatné (včetně chyb, které jsou více než 100 % TSL). Snažte se udržet toto číslo na 0.

> [!NOTE]
> Všechny výpočty procenta životnosti označených jako neplatné jsou založeny na skutečné životnosti objektu označených jako neplatné pro doménovou strukturu služby Active Directory, takže můžete důvěřovat, že tato procenta jsou přesná, i když máte vlastní sadu hodnot za neplatné.
>
>

### <a name="ad-replication-status-details"></a>Podrobnosti o stavu replikace služby AD
Po klepnutí na libovolnou položku v některém ze seznamů se zobrazí další podrobnosti o ní pomocí dotazu protokolu. Výsledky jsou filtrovány tak, aby zobrazovaly pouze chyby související s uvedenou položkou. Pokud například kliknete na první řadič domény uvedený v části **Stav cílového serveru (ADDC02),** zobrazí se filtrované výsledky dotazu, aby se zobrazily chyby s tímto řadičem domény uvedeným jako cílový server:

![Chyby stavu replikace služby AD ve výsledcích dotazu](./media/ad-replication-status/oms-ad-replication-search-details.png)

Odtud můžete dále filtrovat, upravit dotaz protokolu a tak dále. Další informace o používání dotazů protokolu v Azure Monitoru najdete v [tématu Analýza dat protokolu v Azure Monitoru](../../azure-monitor/log-query/log-query-overview.md).

Pole **HelpLink** zobrazuje adresu URL stránky TechNet s dalšími podrobnostmi o této konkrétní chybě. Tento odkaz můžete zkopírovat a vložit do okna prohlížeče a zobrazit informace o řešení potíží a opravě chyby.

Chcete-li **exportovat** výsledky do aplikace Excel, můžete také klepnout na exportovat. Export dat vám může pomoci vizualizovat data chyb replikace jakýmkoli způsobem.

![chyby stavu exportované replikace služby AD v excelu](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Nejčastější dotazy k stavu replikace služby AD
**Otázka: Jak často se aktualizují data o stavu replikace služby AD?**
A: Informace jsou aktualizovány každých pět dní.

**Otázka: Existuje způsob, jak nakonfigurovat, jak často se tato data aktualizují?**
A: Ne v tomto okamžiku.

**Otázka: Musím přidat všechny řadiče domény do pracovního prostoru Log Analytics, aby se zoírá stav replikace?**
A: Ne, musí být přidán pouze jeden řadič domény. Pokud máte v pracovním prostoru Analýzy protokolů více řadičů domény, data ze všech se odesílají do Azure Monitoru.

**Otázka: Nechci do pracovního prostoru Log Analytics přidávat žádné řadiče domény. Mohu stále používat řešení stavu replikace služby AD?**

Odpověď: Ano. Můžete nastavit hodnotu klíče registru, aby byl povolen. Viz [Povolení řadiče domény, který není v oblasti domény.](#enable-non-domain-controller)

**Otázka: Jaký je název procesu, který provádí shromažďování dat?**
A: AdvisorAssessment.exe

**Otázka: Jak dlouho trvá shromažďování dat?**
A: Doba shromažďování dat závisí na velikosti prostředí služby Active Directory, ale obvykle trvá méně než 15 minut.

**Otázka: Jaký typ dat se shromažďuje?**
A: Replikace informace jsou shromažďovány prostřednictvím LDAP.

**Otázka: Existuje způsob, jak nakonfigurovat, kdy jsou data shromažďována?**
A: Ne v tomto okamžiku.

**Otázka: Jaká oprávnění potřebuji ke shromažďování dat?**
A: Normální uživatelská oprávnění ke službě Active Directory jsou dostatečná.

## <a name="troubleshoot-data-collection-problems"></a>Poradce při potížích s shromažďováním dat
Aby bylo možné shromažďovat data, sada řešení Stav replikace služby AD vyžaduje připojení k pracovnímu prostoru Log Analytics alespoň jeden řadič domény. Dokud nepřipojíte řadič domény, zobrazí se zpráva oznamující, že **data jsou stále shromažďována**.

Pokud potřebujete pomoc s připojením jednoho z řadičů domény, můžete zobrazit dokumentaci v [počítačích Connect Windows k Azure Monitoru](../../azure-monitor/platform/om-agents.md). Pokud je váš řadič domény již připojen k existujícímu prostředí Nástroje pro operations manager system center, můžete zobrazit dokumentaci v centru [Connect System Center Operations Manager na Azure Monitor](../../azure-monitor/platform/om-agents.md).

Pokud nechcete připojit žádný řadič domény přímo k Azure Monitoru nebo k nástroji Operations Manager system center, přečtěte si část [Povolení řadiče domény, který není v oblasti domény.](#enable-non-domain-controller)

## <a name="next-steps"></a>Další kroky
* Pomocí [dotazů protokolu v programu Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) zobrazte podrobná data o stavu služby Replikace služby Active Directory.
