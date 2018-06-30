---
title: Monitorování stavu replikace služby Active Directory s Azure Log Analytics | Microsoft Docs
description: Sada pro stav replikace Active Directory řešení pravidelně monitoruje prostředí služby Active Directory k jeho selhání replikace.
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
ms.component: na
ms.openlocfilehash: 0ccd457295584f871088bc20864ef103648f1654
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128720"
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Monitorování stavu replikace služby Active Directory s analýzy protokolů

![Stav replikace AD – symbol](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Služba Active Directory je klíčovou součástí organizace IT prostředí. Zajistit vysokou dostupnost a vysoký výkon, každý řadič domény má svou vlastní kopii databáze služby Active Directory. Řadiče domény replikaci mezi sebou, aby bylo možné rozšířit změny celém podniku. Selhání v tomto procesu replikace může způsobit různé problémy celém podniku.

Sada pro stav replikace AD řešení pravidelně monitoruje prostředí služby Active Directory k jeho selhání replikace.

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Je nutné nainstalovat agenty na řadičích domény, které jsou členy domény, který se má vyhodnotit. Nebo, je nutné nainstalovat agenty na členských serverech a konfigurace agentů k odesílání dat replikace AD k analýze protokolů. Chcete-li pochopit, jak se připojit k analýze protokolů počítače se systémem Windows, přečtěte si téma [počítače se systémem Windows se připojit k analýze protokolů](log-analytics-windows-agent.md). Pokud řadič domény už je součástí stávajícího prostředí System Center Operations Manager, kterou chcete připojit k analýze protokolů najdete v tématu [připojení nástroje Operations Manager k analýze protokolů](log-analytics-om-agents.md).
* Přidat řešení stav replikace Active Directory do pracovního prostoru analýzy protokolů pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).  Není nutná žádná další konfigurace.

## <a name="ad-replication-status-data-collection-details"></a>Podrobnosti služby AD stav replikace dat kolekce
Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se údaje pro stav replikace AD.

| Platforma | Přímý agent | Agenta nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta SCOM odeslána prostřednictvím skupiny pro správu | Frekvence kolekce |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |každých pět dní |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Volitelně můžete povolte řadiči domény pro odesílání dat AD k analýze protokolů
Pokud nechcete, aby všechny řadiče domény připojit přímo k Log Analytics, můžete použít jakéhokoli jiného počítače ve vaší doméně připojené k analýze protokolů pro shromažďování dat pro sady řešení stav replikace AD a mějte ho odeslat data.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Chcete-li povolit řadiči domény pro odesílání dat AD k analýze protokolů
1. Ověřte, že je počítač členem domény, který chcete monitorovat pomocí řešení stav replikace AD.
2. [Připojení počítače se systémem Windows k analýze protokolů](log-analytics-windows-agent.md) nebo [připojit pomocí svého stávajícího prostředí Operations Manager k analýze protokolů](log-analytics-om-agents.md), pokud již není připojen.
3. Na tomto počítači nastavte následující klíč registru:

   * Klíč: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management skupiny\<ManagementGroupName > \Solutions\ADReplication**
   * Hodnota: **IsTarget**
   * Údaj hodnoty: **true**

   > [!NOTE]
   > Tyto změny se projeví až vaše restartování služby Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Principy chyby replikace
Až budete mít data stavu replikace AD posílaná k analýze protokolů, zobrazí se podobně jako na následujícím obrázku v analýzy protokolů, která určuje, kolik chyby replikace, které máte aktuálně dlaždice.  
![Stav replikace AD dlaždici](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Kritické chyby replikace** chyby, které jsou nebo vyšší 75 % [životnosti objektů označených jako neplatné](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) pro doménovou strukturu Active Directory.

Když kliknete na dlaždici, můžete zobrazit další informace o chybách.
![Řídicí panel stavu replikace AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stav cílového serveru a stav zdrojového serveru
Těchto sloupcích se zobrazují stav cílových serverů a zdrojové servery, ve kterých dochází k chybám při replikaci. Počet po každé název řadiče domény označuje počet chyb replikace u tohoto řadiče domény.

Chyby pro zdrojové servery i cílové servery jsou zobrazit, protože některé problémy jsou usnadňují řešení potíží s z hlediska zdrojového serveru a ostatní z pohledu cílový server.

V tomto příkladu vidíte, že mnoho cílové servery mít zhruba stejný počet chyb, ale je jeden zdrojový server (ADDC35), který obsahuje mnoho další chyby, než všechny ostatní. Je pravděpodobné, že dochází k problému na ADDC35, který je příčinou selhání při odesílání dat do své partnerské servery. Oprava problémů na ADDC35 může vyřešit mnoho chyb, které se zobrazí v oblasti cílový server.

### <a name="replication-error-types"></a>Typy chyb replikace
Tato oblast získáte informace o typech chyb zjištěných ve vašem podniku. Jednotlivé chyby má jedinečný číselný kód a zprávu, která vám pomohou určit hlavní příčinu chyby.

Prstenec v horní části získáte představu, které chyby zobrazí informace a méně často ve vašem prostředí.

Zobrazuje při několika řadičů domény dojde ke stejné chybě replikace. V takovém případě pravděpodobně moci vyhledat a identifikovat řešení na jeden řadič domény a pak opakujte na jiných řadičích domény ke stejné chybě vliv.

### <a name="tombstone-lifetime"></a>Životnosti objektů označených jako neplatné
Dobu životnosti objektů označených jako neplatné Určuje, jak dlouho odstraněného objektu, označuje jako nepotřebná data, se uchovávají v databázi služby Active Directory. Pokud odstraněný objekt předá životnosti objektů označených jako neplatné, proces kolekce paměti ji automaticky odstraní z databáze služby Active Directory.

Výchozí doba života objektů označených jako neplatné je 180 dnů pro nejnovější verze systému Windows, ale bylo 60 dnů na starší verze a může se změnit explicitně správcem služby Active Directory.

Je důležité vědět, pokud máte s chyby replikace, které se blíží nebo jsou po dobu životnosti objektů označených jako neplatné. Pokud dva řadiče domény dojde k chybě replikace, která je uchována po dobu životnosti objektů označených jako neplatné, replikace je zakázaná mezi tyto dva řadiče domény, i když je pevná základní Chyba replikace.

Oblasti životnosti objektů označených jako neplatné pomáhá identifikovat místech, je-li zakázáno replikace nebezpečí situaci. Jednotlivé chyby v **více než 100 % TSL** kategorie představuje oddíl, který nebyla replikována mezi jeho zdrojový a cílový server pro alespoň životnosti objektů označených jako neplatné pro doménovou strukturu.

V takovém případě jednoduše opravě chyby replikace nebude stačit. Minimálně je třeba ručně prozkoumat identifikovat a vyčistit přetrvávání odstraněných objektů, než je možné restartovat replikace. Může i musíte vyřadit z provozu řadiče domény.

Kromě identifikaci případné chyby replikace, které mají trvalé po dobu životnosti objektů označených jako neplatné, také můžete chtít věnovat pozornost všechny chyby, které spadají do **50 75 % TSL** nebo **75 100 % TSL** kategorií.

Toto jsou chyby, které jsou jasně přetrvávajících odstraněných, není přechodný, a proto potřebují pravděpodobně váš zásah, chcete-li vyřešit. Dobrá zpráva je, že se ještě nedosáhly životnosti objektů označených jako neplatné. Pokud jste tyto problémy opravit rychle a *před* nedostanou životnosti objektů označených jako neplatné, replikace můžete restartovat s minimálním ruční zásah.

Jak už jsme zmínili dřív, dlaždici řídicího panelu pro stav replikace AD řešení zobrazuje číslo *kritické* chyby replikace ve vašem prostředí, která je definována jako chyby, které se víc než 75 % životnosti objektů označených jako neplatné (včetně chyby které jsou více než 100 % TSL). Zajistit, aby toto číslo na 0.

> [!NOTE]
> Všech výpočtů procento životnosti objektů označených jako neplatné jsou založené na životnosti objektů označených jako neplatné skutečné pro doménové struktury služby Active Directory, takže můžete důvěřovat, že jsou tyto procenta správné, i když máte nastaví hodnota životnosti objektů označených jako neplatné vlastní.
>
>

### <a name="ad-replication-status-details"></a>Podrobnosti o stavu replikace AD
Pokud kliknete na libovolnou položku v jednom ze seznamů, uvidíte další podrobnosti o pomocí protokolu vyhledávání. Výsledky jsou vyfiltrovány a zobrazí se pouze chyby související s danou položku. Například pokud kliknete na první řadič domény, které se v části **stav cílového serveru (ADDC02)**, se zobrazí výsledky hledání filtrovat, aby chyb zobrazit pomocí tohoto řadiče domény uvedený jako cílový server:

![Chybám stav replikace služby Active Directory ve výsledcích hledání](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Tady můžete filtrovat další, Upravit vyhledávací dotaz a tak dále. Další informace o používání hledání protokolů najdete v tématu [protokolu hledání](log-analytics-log-searches.md).

**HelpLink** poli se zobrazí adresu URL stránky TechNet o další podrobnosti o konkrétní chybu. Můžete zkopírovat a vložit tento odkaz do okna prohlížeče zobrazíte informace o řešení potíží a opravě chyby.

Můžete také kliknout na **exportovat** exportu výsledků do aplikace Excel. Export dat můžete vizualizovat data chyby replikace žádným způsobem, které si přejete.

![exportovaný chybám stav replikace služby Active Directory v aplikaci Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Stav replikace AD – nejčastější dotazy
**Otázka: jak často se data stavu replikace AD aktualizovat?**
Odpověď: informace se aktualizuje každých pět dní.

**Otázka: je způsob, jak nakonfigurovat, jak často tato data se aktualizují?**
Odpověď: není v tuto chvíli.

**Otázka: potřebuji přidat všechny moje řadičů domény do pracovní prostor analýzy protokolů, chcete-li zobrazit stav replikace?**
Odpověď: Ne, je nutné přidat jenom jeden řadič domény. Pokud máte víc řadičů domény v pracovním prostoru analýzy protokolů, se budou odesílat data ze všech z nich k analýze protokolů.

**Otázka: nechcete přidat všechny řadiče domény, do pracovního prostoru analýzy protokolů. Pomocí řešení stav replikace AD**
Odpověď: Ano. Můžete nastavit hodnotu klíče registru povolit. V tématu [povolit řadiči domény pro odesílání dat AD k analýze protokolů](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**Otázka: co je název procesu, který nemá shromažďování dat?**
A: AdvisorAssessment.exe

**Otázka: jak dlouho trvá na shromáždění dat?**
A: doba shromažďování dat závisí na velikosti prostředí služby Active Directory, ale obvykle trvá méně než 15 minut.

**Otázka: Jaký typ dat shromažďovaných?**
Odpověď: replikace informace jsou shromažďovány prostřednictvím protokolu LDAP.

**Otázka: je způsob, jak nakonfigurovat, když jsou shromažďována data?**
Odpověď: není v tuto chvíli.

**Otázka: jaký oprávnění jsou nutná ke shromažďování dat?**
Odpověď: normální uživatelské oprávnění ke službě Active Directory je dostatečné.

## <a name="troubleshoot-data-collection-problems"></a>Poradce při potížích kolekce dat
Chcete-li shromažďovat data, sady řešení stav replikace AD vyžaduje aspoň jeden řadič domény k připojení k pracovní prostor analýzy protokolů. Až se připojíte řadič domény, zobrazí se zpráva označující, že **stále nejsou shromažďována data**.

Pokud potřebujete pomoc připojení jedním z řadičů domény, můžete zobrazit dokumentaci v [počítače se systémem Windows se připojit k analýze protokolů](log-analytics-windows-agent.md). Případně, pokud řadič domény je již připojen do existujícího prostředí System Center Operations Manager, můžete zobrazit dokumentaci v [připojit System Center Operations Manager k analýze protokolů](log-analytics-om-agents.md).

Pokud nechcete, aby všechny řadiče domény připojit se přímo k Log Analytics nebo System Center Operations Manager, najdete v části [povolit řadiči domény pro odesílání dat AD k analýze protokolů](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Další postup
* Použití [přihlásit analýzy protokolů hledání](log-analytics-log-searches.md) zobrazíte podrobné údaje o stavu replikace služby Active Directory.
