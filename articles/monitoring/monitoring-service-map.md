---
title: Pomocí mapy služeb řešení v Azure | Microsoft Docs
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobné informace pro nasazení mapy služeb ve vašem prostředí a jejich použití v různých scénářů.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: aa9a6b54576ce8399471891c9ab5b80216f00ee1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887906"
---
# <a name="using-service-map-solution-in-azure"></a>Pomocí mapy služeb řešení v Azure
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Pomocí mapy služeb, můžete zobrazit vaše servery ve způsobu, jakým se domníváte, že z nich: jako vzájemně propojena systémy, které doručují důležité služby. Mapy služeb zobrazí připojení mezi servery, procesy, a vyžaduje porty mezi žádné připojení TCP architektura žádnou konfiguraci, jiné než instalaci agenta.

Tento článek popisuje podrobnosti o pomocí mapy služeb. Informace o konfiguraci mapy služeb a agentů registrace najdete v tématu [mapy služeb konfigurace řešení v Azure]( monitoring-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Případy použití: Ujistěte se, IT procesy závislostí clustery

### <a name="discovery"></a>Zjišťování
Mapy služeb automaticky vytvoří běžné odkaz mapu závislostí mezi servery, procesy a služby třetích stran. Ji zjistí a mapuje všechny závislosti TCP, identifikace neočekávaném připojení, vzdálené systémy jiných výrobců, které závisí na a závislosti pro tradiční tmavý oblasti sítě, jako je Active Directory. Mapy služeb zjistí selhání síťová připojení, která spravovaných systémech se pokoušíte provést, pomáhá identifikovat potenciální chybné konfigurace serveru, výpadkem služby a problémů se sítí.

### <a name="incident-management"></a>Správa incidentů
Mapa služeb pomáhá eliminovat průběhu problém izolace ukazuje, jak jsou připojené systémy a které mají vliv na sebe navzájem. Kromě identifikaci selhání připojení, pomáhá identifikovat nástroje pro vyrovnávání zatížení nesprávně nakonfigurované, překvapivé nebo nadměrnému zatížení důležité služby a podvodné klientů, jako jsou počítače vývojáře rozhovoru s produkční systémy. Pomocí integrovaného pracovních změnit sledování, můžete také zjistit, zda událost změny na back-end počítače nebo služby vysvětluje příčinu incidentu.

### <a name="migration-assurance"></a>Zajištění migrace
Pomocí mapy služeb můžete efektivně plánování, urychlit a ověření Azure migrace, který pomáhá zajistit že nic je ponecháno a nedojde k výpadku neočekávaném. Můžete zjistit všechny konkrétní systémy, které je potřeba migrovat společně, posuzovat konfiguraci systému a kapacity a zjistit, jestli je spuštěný systém stále obsluhuje uživatelé nebo dojít k vyřazení z provozu místo migrace. Po dokončení přesunu můžete zkontrolovat na zatížení klienta a identit k ověření připojení testovací systémy a zákazníků. Pokud podsíť plánování a brány firewall definic problémy, bodu se nezdařilo připojení v mapě služby maps na systémy, které je třeba připojení.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
Pokud používáte Azure Site Recovery a potřebovat pomoc definování posloupnost obnovení pro prostředí aplikace mapy služeb můžete automaticky zjistit, jak systémy závisí na jiné zajistit, aby váš plán obnovení je spolehlivé. Zvolit důležitého serveru nebo skupiny a zobrazením jeho klienty, můžete určit, které front-endu systémy pro obnovení po serveru obnovena a k dispozici. Naopak prohlížením důležité servery back-end závislosti, můžete určit, které systémy obnovení předtím, než se obnoví vaše systémy fokus.

### <a name="patch-management"></a>Opravy správy
Mapa služeb vylepšuje používání vyhodnocení aktualizací systému ukazuje, který ostatními týmy a servery závisí na službě, tak můžete upozornit předem před vypnout vaše systémy pro opravy. Mapy služeb taky zlepšuje správu oprava ukazuje, zda jsou k dispozici a správně připojené po vaší služby jsou opravit a restartovat.


## <a name="mapping-overview"></a>Přehled mapování
Mapy služeb agenty shromažďovat informace o všech procesů připojení protokolu TCP na server, kam jste nainstalován a podrobnosti o příchozí a odchozí připojení pro jednotlivé procesy. V seznamu v levém podokně můžete vybrat počítače nebo skupiny, které mají mapy služeb agentů k vizualizaci závislé v zadaném časovém období. Počítač závislostí mapuje zaměřit na konkrétní počítač a zobrazují všechny počítače, které jsou přímé TCP klientů nebo serverů tohoto počítače.  Mapování skupin počítačů zobrazit sady serverů a jejich závislosti.

![Přehled mapy služeb](media/monitoring-service-map/service-map-overview.png)

Počítače lze rozšířit v mapě zobrazíte spuštění zpracování skupiny a procesů pomocí aktivní síťové připojení během vybraný časový rozsah. Když vzdálený počítač s agentem mapy služeb je rozbalit a zobrazit podrobnosti o procesu, se zobrazí pouze procesy, které komunikují s počítačem fokus. Na levé straně procesů, které se připojují k uvedené počet bez agentů klientské počítače, které připojit do počítače fokus. Pokud je fokus počítač je při připojování k back-end počítač, který nemá žádný agent, back-end serverů je součástí skupiny Port serveru, včetně jiné připojení ke stejné číslo portu.

Ve výchozím nastavení mapy služby maps zobrazit posledních 30 minut informace o závislostech. Pomocí ovládacích prvků čas v levém horním se můžete dotazovat mapy pro historické časových rozsahů ukazují, jak závislosti hledá v minulosti (například během incident nebo před došlo ke změně) až jednu hodinu. Mapa služeb data jsou uložena po dobu 30 dnů v placené pracovních prostorů a 7 dní v bezplatné pracovní prostory.

## <a name="status-badges-and-border-coloring"></a>Stav odznaky a barvy ohraničení
V dolní části každý server v mapě může být seznam stav odznaky zdůraznění stavové informace o serveru. Odznaky znamenat, že některé důležité informace pro server z jednoho z integrace řešení. Kliknutím oznámení "BADGE" přejdete přímo na podrobnosti o stavu v pravém podokně. Odznaky aktuálně k dispozici stav zahrnují výstrahy, technickou podporu, změny, zabezpečení a aktualizace.

V závislosti na závažnosti odznaky stav můžete počítač uzel ohraničení být barevnou red (kritická), žlutý (varování) nebo modrá (informativní). Barva představuje stav nejzávažnějšího odznaky stavu. Šedé ohraničení označuje uzel, který nemá žádné indikátory stavu.

![Stav oznámení](media/monitoring-service-map/status-badges.png)

## <a name="process-groups"></a>Skupin procesů
Skupin procesů kombinovat procesy, které jsou přidružené k běžné produktu nebo službě do skupiny procesu.  Když se uzel počítači rozbalí zobrazí samostatné procesy společně s skupin procesů.  Pokud všechny příchozí a odchozí připojení s procesem v rámci skupiny procesu se nezdařila pak připojení, je zobrazena jako pro skupinu celý proces se nezdařilo.

## <a name="machine-groups"></a>Skupiny počítačů
Skupiny počítačů umožňují zobrazit mapování zaměřená na sadu serverů, nikoli pouze jeden, abyste viděli všichni členové clusteru vícevrstvé aplikace nebo serveru v jedna mapa.

Uživatelé vybrat, které servery patří ve skupině společně a zvolte název pro skupinu.  Potom můžete zobrazit skupiny se všemi jeho připojení a procesů, nebo zobrazit s procesy a připojení, které se vztahují přímo na ostatní členy skupiny.

![Skupinu počítačů](media/monitoring-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Vytvoření skupiny počítačů
Chcete-li vytvořit skupinu, vyberte počítače nebo počítačů, které chcete, aby na počítačích a klikněte na **přidat do skupiny**.

![Vytvoření skupiny](media/monitoring-service-map/machine-groups-create.png)

Zde můžete **vytvořit nový** a zadejte název skupiny.

![Název skupiny](media/monitoring-service-map/machine-groups-name.png)

>[!NOTE]
>Skupiny počítače jsou aktuálně omezená na 10 serverů, ale Plánujeme brzy tento limit zvýšit.

### <a name="viewing-a-group"></a>Zobrazení skupiny
Po vytvoření některé skupiny, můžete je zobrazit výběrem kartu skupiny.

![Karta skupiny](media/monitoring-service-map/machine-groups-tab.png)

Pak vyberte název skupiny k zobrazení mapy pro tuto skupinu pro počítač.
![Skupinu počítače](media/monitoring-service-map/machine-group.png) na počítače, které patří do skupiny jsou uvedeny v bílé v mapě.

Rozšíření skupiny se zobrazí seznam počítačů, které tvoří skupinu počítače.

![Počítač skupiny počítačů](media/monitoring-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrovat podle procesy
Můžete přepínat mezi zobrazuje všechny procesy a připojení ve skupině a pouze ty, které do skupiny počítače se přímo týkají zobrazení mapy.  Výchozí zobrazení je k zobrazení všech procesů.  Zobrazení můžete změnit kliknutím na ikonu filtru výše mapy.

![Filtr skupiny](media/monitoring-service-map/machine-groups-filter.png)

Když **všechny procesy** je vybrána, mapy bude obsahovat všechny procesy a připojení na všechny počítače ve skupině.

![Zpracuje všechny skupinu počítačů](media/monitoring-service-map/machine-groups-all.png)

Pokud změníte zobrazení. Chcete-li zobrazit pouze **připojené skupiny procesy**, mapy bude být co nejlépe určen pouze pro tyto procesy a připojení, které jsou přímo připojené k jiné počítače ve skupině, vytváření zjednodušené zobrazení.

![Skupina počítačů filtrovaná procesy](media/monitoring-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Přidání počítačů do skupiny
Chcete-li přidat počítače do existující skupiny, zaškrtněte políčka u počítačů a potom klikněte na **přidat do skupiny**.  Zvolte skupinu, kterou chcete přidat počítače do.
 
### <a name="removing-machines-from-a-group"></a>Odebrání počítače ze skupiny
V seznamu skupiny rozbalte název skupiny k zobrazení seznamu počítačů ve skupině počítačů.  Klikněte v nabídce třemi tečkami vedle počítače, které chcete odebrat a zvolte **odebrat**.

![Odeberte počítače ze skupiny](media/monitoring-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Odebrání nebo přejmenování skupiny
Klikněte v nabídce třemi tečkami vedle názvu skupiny v seznamu skupiny.

![Počítač skupiny nabídky](media/monitoring-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Role ikony
Některé procesy sloužit konkrétní role na počítačích: webové servery, aplikační servery, databáze a tak dále. Mapa služeb označí polí procesů a počítače s ikonami role, aby bylo možné identifikovat na první pohled role procesu nebo plní serveru.

| Ikona role | Popis |
|:--|:--|
| ![Webový server](media/monitoring-service-map/role-web-server.png) | Webový server |
| ![Aplikace serveru](media/monitoring-service-map/role-application-server.png) | Aplikační server |
| ![Databázový server](media/monitoring-service-map/role-database.png) | Databázový server |
| ![LDAP server](media/monitoring-service-map/role-ldap.png) | LDAP server |
| ![Server s protokolem SMB](media/monitoring-service-map/role-smb.png) | Server s protokolem SMB |

![Role ikony](media/monitoring-service-map/role-icons.png)


## <a name="failed-connections"></a>Neúspěšná připojení
Neúspěšné připojení jsou zobrazeny v rámci služby maps mapy služeb pro počítače a procesy s na přerušovanou red čáru indikující, že klientský systém selhává k dosažení procesu nebo portu. Neúspěšné připojení jsou hlášeny z jakéhokoli systému s nasazené agentem mapy služeb, pokud daný systém je ten, pokusu o připojení se nezdařilo. Mapa služeb měří tento proces sledování sockets TCP, které se nepodařilo navázat spojení. Tato chyba může být výsledkem bránu firewall, chybné konfigurace klienta nebo serveru nebo vzdálené služby není k dispozici.

![Neúspěšná připojení](media/monitoring-service-map/failed-connections.png)

Seznámení se nezdařilo připojení může pomoci při řešení, ověření migrace, analýzu zabezpečení a porozumění celkového architektury. Neúspěšné připojení jsou někdy neškodné, ale jejich často přejděte přímo k problému, jako je například prostředí převzetí služeb při selhání najednou stane nedostupný, nebo dvě úrovně aplikace není schopen komunikovat po migraci cloudu.

## <a name="client-groups"></a>Skupin klientů
Skupin klientů jsou polí na mapě, která představují klientské počítače, které nemají závislostí agenty. Jedné skupiny klientů reprezentuje klienty pro jednotlivé procesu nebo počítače.

![Skupin klientů](media/monitoring-service-map/client-groups.png)

Pokud chcete zobrazit IP adresy serverů ve skupině pro klienta, vyberte skupinu. Obsah skupiny jsou uvedeny v **vlastnosti skupiny klienta** podokně.

![Vlastnosti skupiny klientů](media/monitoring-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Port serveru skupiny
Port serveru skupiny jsou polí, která představují porty serveru na serverech, které nemají závislostí agenty. Pole obsahuje port serveru a počet serverů s připojení k tomuto portu. Rozbalte pole se zobrazí jednotlivé servery a připojení. Pokud v poli existuje pouze jeden server, je uvedený název nebo IP adresu.

![Port serveru skupiny](media/monitoring-service-map/server-port-groups.png)

## <a name="context-menu"></a>Místní nabídka
Kliknutím na tlačítko se třemi tečkami (...) v horní pravé žádného serveru zobrazuje v místní nabídce pro tento server.

![Neúspěšná připojení](media/monitoring-service-map/context-menu.png)

### <a name="load-server-map"></a>Zatížení serveru mapy
Kliknutím na tlačítko **zatížení serveru mapy** přejdete na nové mapování k vybranému serveru jako nový počítač fokus.

### <a name="show-self-links"></a>Zobrazit odkazů na sebe sama
Kliknutím na tlačítko **zobrazit Self-Links** překreslí ho uzlu serveru, včetně všech odkazů na sebe sama, které jsou připojení TCP, které začínají a končí na procesy v rámci serveru. Pokud odkazů na sebe sama se zobrazují, změn příkaz nabídky **skrýt Self-Links**, takže je můžete vypnout.

## <a name="computer-summary"></a>Souhrn počítače
**Počítač Souhrn** podokně obsahuje přehled operačního systému serveru, počtu závislostí a data z jiných řešení. Taková data zahrnuje metrik výkonu, lístků podpory služby, sledování změn, zabezpečení a aktualizace.

![Podokno Souhrn počítače](media/monitoring-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Vlastnosti počítače a procesu
Když přejdete mapu mapy služeb, můžete vybrat počítače a procesy, které se získají další kontext o svých vlastnostech. Počítače poskytují informace o DNS název, IPv4 adresy, procesoru a paměti kapacitu, typ virtuálního počítače, operační systém a verze poslední restartovat čas a ID jejich agentů OMS a mapy služeb.

![Podokno vlastností počítače](media/monitoring-service-map/machine-properties.png)

Podrobnosti o procesu můžete shromáždit z operačního systému metadata o spuštěných procesů, včetně název procesu, popis procesu, uživatelské jméno a doménu (v systému Windows), název společnosti, název produktu, verze produktu, pracovní adresář, příkazový řádek a proces Počáteční čas.

![Podokno vlastností procesu](media/monitoring-service-map/process-properties.png)

**Souhrn procesu** podokno poskytuje další informace o procesu připojení, včetně jeho vázané porty, příchozí a odchozí připojení a připojení se nezdařilo.

![Podokno Souhrn procesu](media/monitoring-service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrace výstrah
Mapa služeb se integruje s výstrahami v analýzy protokolů pro zobrazení aktivní výstrahy pro vybraný server v vybraný časový rozsah. Server Pokud aktuální výstrahy, zobrazí ikonu a **počítač výstrahy** podokně zobrazí výstrahy.

![Počítač podokně výstrahy](media/monitoring-service-map/machine-alerts.png)

Pokud chcete povolit mapy služeb zobrazíte příslušné výstrahy, vytvořte pravidlo výstrahy, která aktivuje se v určitém počítači. Pokud chcete vytvořit správné výstrahy:
- Obsahovat klauzuli do skupiny podle počítače (například **počítače interval 1 minuta**).
- Zvolte výstrahy podle metriky měření.

![Konfigurace upozornění](media/monitoring-service-map/alert-configuration.png)


## <a name="log-events-integration"></a>Integrace protokolu událostí
Mapa služeb se integruje s protokolu hledání a zobrazit počet všechny dostupné protokolu události pro vybraný server během vybraný časový rozsah. Můžete kliknout na všechny řádek v seznamu událostí počty přejít na hledání protokolů a zobrazte jednotlivé protokolu události.

![V podokně protokolu události počítače](media/monitoring-service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrace služby podpory
Integrace mapy služeb s konektorem služby správy IT je automatické, pokud obě řešení jsou povolené a nakonfigurované v pracovním prostoru analýzy protokolů. Integrace ve mapy služeb se s označením "Technickou podporu." Další informace najdete v tématu [centrálně spravovat ITSM pracovních položek pomocí konektoru služby správy IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

**Počítač technickou podporu** podokně zobrazí všechny události správy služeb IT pro vybraný server v vybraný časový rozsah. Na server zobrazí ikona, pokud je aktuální počet položek a jsou uvedené v podokně počítač technickou podporu, je.

![Služby podpory podokně počítače](media/monitoring-service-map/service-desk.png)

Otevřete položku v řešení připojených ITSM klikněte na **zobrazení pracovní položka**.

Chcete-li zobrazit podrobnosti o položce v hledání protokolů, klikněte na tlačítko **zobrazit v protokolu vyhledávání**.


## <a name="change-tracking-integration"></a>Změna sledování integrace
Integrace mapy služeb s sledování změn je automaticky, pokud obě řešení jsou povolené a nakonfigurované v pracovním prostoru analýzy protokolů.

**Sledování změn počítače** podokně jsou uvedeny všechny změny, s nejnovější první, spolu s odkazem na Přejít k podrobnostem a protokolu vyhledejte další podrobnosti.

![Sledování změn podokně počítače](media/monitoring-service-map/change-tracking.png)

Na následujícím obrázku je podrobný přehled o ConfigurationChange událost, která může dojít po výběru **zobrazit v analýzy protokolů**.

![Změnakonfigurace událostí](media/monitoring-service-map/configuration-change-event.png)


## <a name="performance-integration"></a>Integrace výkonu
**Výkon počítače** podokně se zobrazí metriky standardní výkonu pro vybraný server. Metriky zahrnují využití procesoru, využití paměti, sítě Bajty odeslané a přijaté a seznam důležitých procesů pomocí sítě přijatých a odeslaných bajtů.

![Počítač podokně výkonu](media/monitoring-service-map/machine-performance.png)

Pokud chcete zobrazit data výkonu, budete muset [povolit příslušné čítače výkonu analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Čítače, které budete chtít povolit:

Windows:
- Procesor(*)\\% času procesoru
- Paměť\\% využití potvrzených bajtů
- Sítě Adapter(*)\\odeslané bajty/s
- Sítě Adapter(*)\\přijaté bajty/s

Linux:
- Procesor(*)\\% času procesoru
- Memory(*)\\% použité paměti
- Sítě Adapter(*)\\odeslané bajty/s
- Sítě Adapter(*)\\přijaté bajty/s

Chcete-li získat data o výkonu sítě, musí taky povolíte řešení přenosu dat 2.0 v pracovním prostoru.
 
## <a name="security-integration"></a>Integrace zabezpečení
Integrace mapy služeb se zabezpečení a Audit je automatické, pokud obě řešení jsou povolené a nakonfigurované v pracovním prostoru analýzy protokolů.

**Zabezpečení počítače** podokně se zobrazují data z řešení zabezpečení a auditu pro vybraný server. V podokně obsahuje souhrnný seznam všechny zbývající bezpečnostní problémy pro server během vybraný časový rozsah. Kliknutím na některé z projde problémy zabezpečení dolů do hledání protokolů podrobnosti o nich.

![Podokno zabezpečení počítače](media/monitoring-service-map/machine-security.png)


## <a name="updates-integration"></a>Integrace aktualizací
Mapy služeb integrace se Správa aktualizací je automatické, pokud obě řešení jsou povolené a nakonfigurované v pracovním prostoru Anlaytics protokolu.

**Machine aktualizace** podokně se zobrazí data z řešení správy aktualizací pro vybraný server. V podokně obsahuje souhrnný seznam všechny chybějící aktualizace pro server během vybraný časový rozsah.

![Sledování změn podokně počítače](media/monitoring-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Data inventáře počítače a procesu mapy služeb je k dispozici pro [vyhledávání](../log-analytics/log-analytics-log-searches.md) v analýzy protokolů. Tato data můžete použít pro scénáře, které zahrnují plánování migrace, analýzy kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

Generuje se jeden záznam za hodinu pro každý jedinečný počítač a proces, kromě záznamy, které jsou generovány, pokud začíná nebo procesu nebo počítače je na zahrnuté do mapy služeb. Tyto záznamy mají vlastnosti v následujících tabulkách. Pole a hodnoty v událostech ServiceMapComputer_CL mapování polí a počítač prostředku v rozhraní API ServiceMap Azure Resource Manager. Pole a hodnoty v událostech ServiceMapProcess_CL namapovat na pole proces prostředku v rozhraní API ServiceMap Azure Resource Manager. Pole ResourceName_s odpovídá pole název odpovídající prostředku Resource Manager. 

>[!NOTE]
>Jelikož funkce mapy služeb, tato pole jsou mohou podléhat změnám.

Nejsou k dispozici interně generované vlastnosti, které můžete použít k identifikaci jedinečný procesy a počítače:

- Počítač: Použití ResourceId nebo ResourceName_s k jednoznačné identifikaci počítač v rámci pracovní prostor analýzy protokolů.
- Proces: ResourceId použít k jednoznačné identifikaci procesu v rámci pracovní prostor analýzy protokolů. ResourceName_s je jedinečné v rámci kontextu počítače, na kterém je proces spuštěný (MachineResourceName_s) 

Protože pro proces a počítač v zadaném časovém rozmezí může existovat více záznamů, dotazy mohou vracet víc než jeden záznam pro stejný počítač nebo proces. Chcete-li zahrnout pouze poslední záznam, přidejte "| Při odstraňování duplicitních dat ResourceId"do dotazu.

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL záznamů
Záznamů s typem *ServiceMapComputer_CL* mít data inventáře pro servery s agenty mapy služeb. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ID prostředku | Jedinečný identifikátor pro počítač v pracovním prostoru |
| ResourceName_s | Jedinečný identifikátor pro počítač v pracovním prostoru |
| ComputerName_s | Plně kvalifikovaný název domény počítače |
| Ipv4Addresses_s | Seznam serveru IPv4 adres |
| Ipv6Addresses_s | Seznam serveru IPv6 adres |
| DnsNames_s | Pole názvy DNS |
| OperatingSystemFamily_s | Windows nebo Linux |
| OperatingSystemFullName_s | Úplný název operačního systému  |
| Bitness_s | Počtu bitů na počítač (32bitová nebo 64bitová verze)  |
| PhysicalMemory_d | Fyzická paměť v MB |
| Cpus_d | Počet procesorů |
| CpuSpeed_d | Rychlost procesoru v MHz|
| VirtualizationState_s | *Neznámý*, *fyzické*, *virtuální*, *hypervisoru* |
| VirtualMachineType_s | *HyperV*, *vmware*a tak dále |
| VirtualMachineNativeMachineId_g | ID virtuálního počítače přiřazené službou jeho hypervisoru |
| VirtualMachineName_s | Název virtuálního počítače |
| BootTime_t | Čas spuštění |



### <a name="servicemapprocesscl-type-records"></a>Typ ServiceMapProcess_CL záznamů
Záznamů s typem *ServiceMapProcess_CL* mít data inventáře pro připojení TCP procesy na serverech s agenty mapy služeb. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ID prostředku | Jedinečný identifikátor pro proces v pracovním prostoru |
| ResourceName_s | Jedinečný identifikátor pro proces v počítači, na kterém je spuštěn|
| MachineResourceName_s | Název prostředků počítače |
| ExecutableName_s | Název spustitelného souboru procesu |
| StartTime_t | Čas zahájení procesu fondu |
| FirstPid_d | První identifikátor PID ve fondu procesů |
| Description_s | Popis procesu |
| CompanyName_s | Název společnosti |
| InternalName_s | Interní název |
| ProductName_s | Název produktu |
| ProductVersion_s | Verze produktu |
| FileVersion_s | Verze souboru |
| CommandLine_s | Příkazový řádek |
| ExecutablePath _Malá | Cesta ke spustitelnému souboru |
| WorkingDirectory_s | Pracovní adresář |
| Uživatelské jméno | Účet, pod kterým proces spouštění |
| UserDomain | Domény, pod kterým proces spouštění |


## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

### <a name="list-all-known-machines"></a>Seznam známých všechny počítače
ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Zobrazí seznam všech spravovaných počítačů kapacita fyzické paměti.
ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ResourceId | Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Název počítače seznamu, DNS, IP a operačního systému.
ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ResourceId | Projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Vyhledá všechny procesy s "sql" v příkazovém řádku
ServiceMapProcess_CL | kde CommandLine_s contains_cs "sql" | shrnout arg_max(TimeGenerated, *) podle ID prostředku

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Najít počítač (poslední záznam) podle názvu prostředku
hledání v (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | shrnout arg_max(TimeGenerated, *) podle ID prostředku

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Najít počítač (poslední záznam) podle IP adresy
hledání v (ServiceMapComputer_CL) "10.229.243.232" | shrnout arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-all-known-processes-on-a-specified-machine"></a>Seznam všech známých procesů v zadaném počítači
ServiceMapProcess_CL | kde MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | shrnout arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-all-computers-running-sql"></a>Zobrazí seznam všech počítačů se systémem SQL
ServiceMapComputer_CL | kde ResourceName_s v ((hledání v (ServiceMapProcess_CL) "\*sql\*" | odlišné MachineResourceName_s)) | odlišné ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Seznam všech verzí produktu jedinečný curl Moje datového centra.
ServiceMapProcess_CL | kde ExecutableName_s == "curl" | odlišné ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvořit skupinu počítačů všech počítačů se systémem CentOS
ServiceMapComputer_CL | kde OperatingSystemFullName_s contains_cs "CentOS" | odlišné ComputerName_s


## <a name="rest-api"></a>REST API
Všechna data serveru, proces a závislostí v mapy služeb je k dispozici prostřednictvím [rozhraní API REST služby mapy](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>data o využití a Diagnostika
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby mapy služeb. Tato data Microsoft používá k poskytování a zlepšování kvality, zabezpečení a integrity služby mapy služeb. Pokud chcete zadat přesné a efektivní možnosti pro odstraňování potíží, data zahrnují informace o konfiguraci vašeho softwaru, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní informace.

Další informace o shromažďování a používání dat najdete v tématu [prohlášení o ochraně osobních údajů služeb Microsoft Online](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Další postup
Další informace o [protokolu hledání](../log-analytics/log-analytics-log-searches.md) v analýzy protokolů pro načtení data shromážděná pomocí mapy služeb.


## <a name="troubleshooting"></a>Řešení potíží
Najdete v článku [řešení potíží s konfigurací Map služeb dokumentu v části]( monitoring-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Váš názor
Máte k dispozici žádné zpětná vazba nám o mapy služeb nebo této dokumentace?  Navštivte naše [User Voice stránky](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhnout funkce nebo hlasovat až existující návrhy.
