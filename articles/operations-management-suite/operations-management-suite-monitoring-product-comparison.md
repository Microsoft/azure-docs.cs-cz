---
title: Monitorování porovnání produktů Microsoft | Microsoft Docs
description: Microsoft Operations Management Suite (OMS) je řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudové infrastruktury založené na službě cloud společnosti Microsoft.  Tento článek identifikuje různé služby zahrnuté v OMS a poskytuje odkazy na podrobné informace.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: 6da876a0e6c1e98683caa864a4a2bcf85195cd10
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753315"
---
# <a name="microsoft-monitoring-product-comparison"></a>Porovnání monitorování produktů společnosti Microsoft
Tento článek obsahuje porovnání mezi službou System Center Operations Manager (SCOM) a analýzy protokolů v Operations Management Suite (OMS) z hlediska jejich architektury, logiku, jak je i sledování prostředků, a jak budou provádět analýzy dat, která se budou shromažďovat .  Toto je získáte základní přehled o jejich rozdíly a relativní síly.  

## <a name="basic-architecture"></a>Základní architektura
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Všechny součásti SCOM jsou nainstalovány ve vašem datovém centru.  [Jsou nainstalováni agenti](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-console) na počítačích systému Windows a Linux, které jsou spravovány nástrojem SCOM.  Agenti připojit k [servery pro správu](https://technet.microsoft.com/library/hh301922.aspx) který komunikovat s SCOM databáze a datový sklad.  Agentů závisí na ověřování pro připojení na servery pro správu v doméně.  Ty mimo důvěryhodnou doménu můžete provádět ověřování pomocí certifikátu nebo se připojit k [Server brány](https://technet.microsoft.com/library/hh212823.aspx).

SCOM vyžaduje dvě databáze SQL, jednu pro provozních dat a jiné datového skladu pro podporu analýzy dat a generování sestav.  A [serveru sestav](https://technet.microsoft.com/library/hh298611.aspx) spouštět služby SQL Reporting Services tak, aby odesílaly data z datového skladu. 

SCOM můžete monitorovat cloudových prostředků pomocí sady management Pack pro produkty, jako třeba [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708), a [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Tyto sady management Pack pomocí jednoho nebo více agentů místní jako proxy pro zjišťování cloudových prostředků a pracovních postupů běžících měření jejich výkonu a dostupnosti.  Agenti proxy se také používají pro [monitorování síťových zařízení](https://technet.microsoft.com/library/hh212935.aspx) a dalších externích zdrojů.

Konzole Operations Console je aplikace Windows, která se připojuje k jednomu serveru pro správu a umožňuje správci zobrazit a analyzovat shromážděná data a konfigurace prostředí SCOM.  Webová konzola může být hostovaný na libovolném serveru služby IIS a poskytuje analýzu dat prostřednictvím prohlížeče.

![Architektura SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
Většina OMS součásti jsou v cloudu Azure, abyste mohli nasadit a spravovat s minimálním nákladů a úsilí pro správu.  Všechna data shromažďovaná společností analýzy protokolů je uložen v úložišti OMS.

Analýzy protokolů můžete shromažďovat data z jednoho ze tří zdrojů:

* Fyzické a virtuální počítače s Windows a [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) nebo Linux a [Operations Management Suite agenta pro Linux](https://technet.microsoft.com/library/mt622052.aspx).  Tyto počítače může být místní nebo virtuálních počítačů v Azure nebo v jiném cloudu.
* Účet úložiště Azure s [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) data shromážděná pomocí role pracovního procesu Azure, webovou roli nebo virtuálního počítače.
* [Připojení ke skupině pro správu SCOM](https://technet.microsoft.com/library/mt484104.aspx).  V této konfiguraci agenti komunikovat se servery pro správu SCOM, které poskytovat data do databáze SCOM, kde je pak doručit do úložiště dat OMS.
  Správci analyzovat shromážděná data a nakonfigurovat na portálu OMS, který je hostován v Azure a je přístupný z libovolného prohlížeče analýzy protokolů.  Mobilní aplikace pro přístup k těmto datům jsou dostupné pro standardní platformy.

![Architektura protokolu analýzy](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrace SCOM a analýzy protokolů
Při SCOM slouží jako zdroj dat pro analýzy protokolů můžete využít funkce oba produkty v hybridním prostředí monitorování.  Můžete nakonfigurovat existující SCOM agentů prostřednictvím funkce Operations Console lze spravovat pomocí OMS, kromě pokračování v používání sad management Pack z SCOM.  
Data z připojené skupiny správy SCOM doručuje k analýze protokolů pomocí jedné ze čtyř metod:

* Události a data výkonu jsou shromážděné agentem a odeslána SCOM.  Servery pro správu v aplikaci SCOM k analýze protokolů doručí data.
* Některé události, jako jsou protokoly služby IIS a události zabezpečení i nadále doručena přímo k Log Analytics od agenta.
* Některá řešení bude poskytovat další software do agenta nebo vyžadují instalaci softwaru shromažďovat další data.  Tato data se odešlou obvykle přímo k Log Analytics.
* Některá řešení bude shromažďovat data přímo ze serverů pro správu SCOM, které nepochází z agenta.  Například [řešení pro správu výstrah](https://technet.microsoft.com/library/mt484092.aspx) shromažďuje výstrahy z SCOM. po jejich vytvoření.

## <a name="monitoring-logic"></a>Logika monitorování
SCOM a analýzy protokolů pracovat s podobnou data shromážděná z agentů, ale mají základní rozdíly v tom, jak definovat a implementovat jejich logiku pro shromažďování dat, a jak se analyzovat data, která budou shromažďovat.

### <a name="operations-manager"></a>Operations Manager
Monitorování logiku pro SCOM je implementována ve [sady management Pack](https://technet.microsoft.com/library/hh457558.aspx) který obsahují logiku pro zjišťování součásti ke sledování, měření stavu těchto součástí a pro shromažďování dat pro analýzu.  Data monitorování může být stejně jednoduché jako shromažďování čítačů událostí nebo výkonu, nebo by mohl použít komplexní logiku implementována ve skriptu.  Sady Management Pack, které zahrnují kompletní monitorování jsou k dispozici pro celou řadu [aplikací společnosti Microsoft a třetích stran](http://go.microsoft.com/fwlink/?LinkId=82105) kromě hardwaru a síťových zařízení.  Můžete [vytvářet vlastní sady management Pack](http://aka.ms/mpauthor) pro vlastní aplikace.

Sady Management Pack obsahují více pracovních postupů, které každý provádí některé odlišné monitorování funkce jako je například vzorkování čítače výkonu, kontrola stavu služby nebo spouštění skriptu.  Každý pracovní postup probíhá nezávisle a definuje vlastní výsledky, například databáze, která bude zapisovat a zda bude vygenerována výstraha. 

Podrobnosti o pracovní postup, například frekvenci, na které poběží, které považují za chybu prahovou hodnotu a závažnost výstrahy, které generují můžete přepsat.  Přidáním vlastních pracovních postupů můžete zadat také další funkce.

![Přepsání](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Sady Management Pack jsou nainstalovány v databázi nástroje Operations Manager a automaticky distribuovaného agentům pomocí serverů pro správu.  Každý agent bude automaticky stáhnout sady management Pack a spouštění pracovních postupů, které jsou relevantní pro aplikace, které mají být nainstalovány.  Údaje shromážděné agentem doručuje zpět do serveru pro správu za účelem vložení do databáze a datový sklad SCOM.  Konzole Operations Console můžete zobrazit a analyzovat tato data prostřednictvím vlastních zobrazení, řídicí panely a sestavy zahrnuté v sadě management pack.

Distribuci sad management Pack je znázorněno v následujícím diagramu.

![Tok Management pack](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Události a shromažďování výkonu
Analýzy protokolů shromažďuje události a čítače výkonu z agenta systémů použití zdrojů, jako je například protokol událostí systému Windows, protokoly služby IIS a Syslog.  Můžete definovat kritéria, pro které data se shromažďují prostřednictvím portálu analýzy protokolů a pak vytvořte protokolu dotazy k analýze shromážděných datech.  Sadu standardních kritérií je definován při vytváření pracovního prostoru OMS a můžete definovat další data pro konkrétní aplikace. 

![Definování protokoly událostí v analýzy protokolů](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Zatímco SCOM má mnoho podrobné pracovních postupů, které obvykle definují určitá kritéria pro data a akce, která má být provedena v odpovědi, analýzy protokolů má obecnější kritéria pro shromažďování dat.  Protokol dotazy a řešení poskytují více cílových kritéria pro analýzu a funguje na určité dat v cloudu, až se shromáždí.

#### <a name="solutions"></a>Řešení
Řešení poskytuje další logiku pro shromažďování dat a analýzu.  Můžete vybrat řešení, které chcete přidat do vašeho předplatného OMS z Galerie řešení.

![Galerie řešení](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Řešení především běží v cloudu poskytuje analýza události a počítadla výkonu shromažďovaných v úložišti OMS.  Může definovat taky shromažďovat další data, která lze analyzovat pomocí dotazů protokolu nebo další uživatelské rozhraní služby řešení v řídicím panelu OMS. 

Například [řešení pro sledování změn](https://technet.microsoft.com/library/mt484099.aspx) zjistí konfigurace změny v systémech agenta a zapisuje události do OMS úložiště, který lze analyzovat s několika grafické zobrazení, které shrnují zjistila změny.  Můžete k podrobnostem ze souhrnné zobrazení do dotazů protokolu, které zobrazují podrobné údaje shromážděné řešení.

Když budete moci vybrat řešení, které přidáte do vašeho předplatného, nemáte aktuálně možnost vytvářet vlastní řešení.  Můžete vybrat události a čítače výkonu pro shromažďování a vytvořit vlastní zobrazení založené na dotazech vlastního protokolu.

Shrnutí logiku sledování pro analýzy protokolů v následujícím diagramu.

![Tok řešení analýzy protokolů](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitorování stavu
### <a name="operations-manager"></a>Operations Manager
SCOM můžete model různé součásti aplikace a poskytují stavu v reálném čase pro všechny.  To umožňuje nejenom zobrazit zjistil chyby a výkonu v čase, ale také ověřit skutečný stav aplikace nebo systému a všechny jeho komponenty v daném okamžiku.  Vzhledem k tomu, že rozumí časová období, která aplikace je k dispozici, modul stavu v aplikaci SCOM také podporuje o úrovni služeb smlouvy (SLA) který analýza a tvorba sestav na dostupnost aplikace v průběhu času.

Například zobrazení níže ukazuje stav v reálném čase modulů databáze SQL sledovaných nástrojem SCOM.  Stav jednotlivých databází pro jednu databázi motory se zobrazí na dolní polovinu zobrazení.

![Zobrazení stavu](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Průzkumníka stavů pro jeden z modulů databáze jsou uvedené dole s monitory, které se používají k určení jeho celkový stav.  Tato monitorování jsou definovány v sadě management pack SQL a spouštění všechny databázové stroje SQL zjištěny nástrojem SCOM.

![Průzkumník stavů](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Součásti na více systémů je možné kombinovat k měření stavu distribuované aplikace.  To může být obzvláště užitečné pro obchodních aplikací, které obsahují několik součástí distribuované.  Tento kumulativní můžete vytvořit model, který se měří stav jednotlivých součástí do dostupnosti pro aplikaci.

Služba Active Directory je příkladem jeden management pack, která poskytuje model k analýze jeho distribuované komponenty.  Následující ukázka diagram znázorňuje stav celém prostředí a vztah mezi doménovými strukturami, doménami a řadiče domény.  Každou z těchto součástí zahrnuje tyto dílčí součásti a více monitorů podobně jako v předchozím příkladu SQL.

![Zobrazení diagramu SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS nezahrnuje modul common modelu aplikací a měření jejich stav v reálném čase.  Jednotlivá řešení může posouzení celkového stavu konkrétní služeb na základě shromážděných dat a jejich vlastní logiky nainstalovat agenta provádět analýzu v reálném čase.  Vzhledem k řešení běží v cloudu s přístupem k úložišti OMS, se často poskytují podrobnější analýzy, než se obvykle provádí pomocí sady management Pack. 

Například [AD posouzení a vyhodnocení SQL řešení](https://technet.microsoft.com/library/mt484102.aspx) analýza shromážděná data a zadání hodnocení různých aspektů prostředí.  Obsahuje doporučení pro vylepšení, které můžete provést za účelem zvýšení dostupnosti a výkonu prostředí.

![Řešení pro vyhodnocení AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analýza dat
SCOM a analýzy protokolů poskytují různé funkce pro analýzu shromážděná data.  SCOM má zobrazení a řídicí panely v konzoli Operations Console pro analýzu posledních dat v různých formátech a sestav pro prezentaci dat z datového skladu ve formě tabulky.  Analýzy protokolů poskytuje rozhraní a dokončení protokolu dotazovací jazyk pro analýzu dat v úložišti OMS.  Když SCOM slouží jako zdroj dat pro analýzy protokolů, úložiště zahrnuje údaje shromažďované nástrojem SCOM, takže nástroje analýzy protokolů můžete použít k analýze dat z obou systémů.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Zobrazení
Zobrazení v konzoli Operations Console umožňují zobrazit různé datové typy shromažďují SCOM v různých formátech, obvykle tabulkové pro události, výstrahy a dat o stavu a čárové grafy pro data výkonu.  Zobrazení provést minimální analýza nebo konsolidace dat ale umožňují filtrovat podle konkrétní kritéria. 

![Zobrazení](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Sady Management Pack obvykle zajistí podporu aplikace nebo systému, kterou monitoruje více zobrazení.  To může zahrnovat zobrazení stavu různých objektů, které sada management pack zjišťuje, zobrazení výstrah pro zjištěné problémy a zobrazení výkonu pro čítače.

Zobrazení jsou obzvláště vhodný pro analýzu aktuální stav prostředí včetně otevřené výstrahy a stav monitorovaných systémů a objekty.  Můžete přejít na podrobné události nebo údaje o výkonu podpora konkrétní výstrahu k diagnostice jeho hlavní příčinu. Podobně můžete zobrazit stav různých součástí aplikace k vyhodnocení jeho aktuální stav a výkon.

#### <a name="dashboards"></a>Řídicí panely
Řídicí panely v konzoli Operations Console se primárně pracovat s stejná data jako zobrazení ale jsou větší možnosti úprav a může obsahovat bohatší vizualizace.  Sadu standardních řídicí panely jsou k dispozici, můžete snadno přizpůsobit pro vlastní účely.  Můžete také použít widget prostředí PowerShell, které můžete zobrazit data vrácená z dotazu prostředí PowerShell.

![Řídicí panel](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Vývojáři mají možnost Přidat volitelné součásti na řídicí panely, které do svých sad management Pack.  To může být vysoce specializované konkrétní aplikaci, například řídicího panelu v sadě management pack SQL, který vidíte níže.  Tento řídicí panel můžete také použít jako šablonu pro vlastní verze.

![Řídicí panel SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Reports
Sestavy v aplikaci SCOM analyzovat data z datového skladu ve formě tabulky.  Může být vytisknout a naplánován pro odeslání automatizované v různých formátech, včetně souborů PDF, CSV a Word.  Sestavy pracovat s daty z datového skladu tak, aby byly obzvláště vhodný pro analýzu trendů dlouhou dobu.

Sady Management Pack obvykle poskytne vlastní sestavy pro konkrétní aplikace.  Můžete také vybrat z knihovny Obecné sestavy, které můžete přizpůsobit pro vlastní aplikace nebo pro provádění analýz ad hoc.

Následuje ukázka výkonu sestavy zobrazující data shromážděná sadou Active Directory Management Pack.

![Sestava](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Má analýzy protokolů [dotazu jazyka](https://technet.microsoft.com/library/mt484120.aspx) , můžete použít k analýze napříč data z více aplikací, aniž by bylo nutné vytvořit vlastní zobrazení nebo sestavy.  Protože OMS je implementované v cloudu, výkon dotazů a analýza dat se nevztahují žádné omezení hardwaru a můžete rychle analyzovat dotazů včetně miliony záznamů. 

Dotazy v analýzy protokolů jsou také základ pro další funkce.  Můžete uložit dotazu, jeho výsledky exportovat do Excelu nebo ji automaticky spustit v pravidelných intervalech a generování výstrah v případě své výsledky odpovídají konkrétním kritériím.  

![Protokol dotazu toku](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Dole je příklad dotazu analýzy protokolů.  V tomto příkladu jsou všechny události se "spustit" v názvu vrátí a seskupené podle událost ID.  Uživatel jednoduše zadá dotaz a analýzy protokolů dynamicky vygeneruje uživatelské rozhraní k provedení analýzy.  Vyberte libovolnou položku v seznamu vrátí data podrobné události.

![Protokol dotazu](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Kromě zajištění analýz ad hoc, můžete uložit pro budoucí použití dotazy v analýzy protokolů a také přidali do vaší [řídicí panel OMS](http://technet.microsoft.com/library/mt484090.aspx) jak je znázorněno v následujícím příkladu.

![Řídicí panel OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Další kroky
* Nasazení [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Zaregistrujte si [analýzy protokolů](https://azure.microsoft.com/documentation/services/log-analytics).  

