---
title: Pomocí řešení Service Map v Azure | Dokumentace Microsoftu
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobnosti o nasazení řešení Service Map ve vašem prostředí a jejich použití v různých scénářích.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2018
ms.author: magoedte
ms.openlocfilehash: cd55e97edb6cd0b4a2a3eceee406ce5718db8bd4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186493"
---
# <a name="using-service-map-solution-in-azure"></a>Pomocí řešení Service Map v Azure
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Pomocí mapy služeb, můžete zobrazit vaše servery tak, aby přirozeným způsobem: jako propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy, příchozí a odchozí připojení čekací doba a požadované porty v jakékoli architektuře propojené TCP, žádnou konfiguraci kromě instalace agenta.

Tento článek popisuje podrobnosti o zařazení do systému a použitím řešení Service Map. Informace o konfiguraci řešení Service Map a připojováním agentů najdete v tématu [řešení Service Map konfigurace v Azure]( service-map-configure.md).

>[!NOTE]
>Pokud jste už nasadili řešení Service Map, můžete také prohlédnout vaše mapy ve službě Azure Monitor pro virtuální počítače, který obsahuje další funkce, které chcete sledovat výkon a stav virtuálního počítače. Další informace najdete v tématu [monitorování Azure pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md).


## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Povolení řešení Service Map
1. Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek**.
2. Na panelu hledání zadejte **Service Map** a stiskněte klávesu **Enter**.
3. Na stránce výsledků hledání marketplace vyberte **Service Map** ze seznamu.<br><br> ![Vyberte řešení Service Map z Azure Marketplace výsledků hledání](./media/service-map/marketplace-search-results.png)<br>
4. Na **Service Map** podokně s přehledem, projděte si podrobnosti o řešení a potom klikněte na **vytvořit** k zahájení procesu registrace do pracovního prostoru Log Analytics.<br><br> ![Začlenění řešení Service Map](./media/service-map/service-map-onboard.png).
5. V **konfigurace řešení** podokně, vyberte existující nebo vytvořte nový pracovní prostor Log Analytics.  Další informace o tom, jak vytvořit nový pracovní prostor, najdete v části [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../../azure-monitor/learn/quick-create-workspace.md). Po zadání požadovaných informací, klikněte na tlačítko **vytvořit**.  

Během ověřování informací a řešení nasadit, můžete sledovat jeho průběh **oznámení** z nabídky. 

Přístup k mapě služby na webu Azure Portal z pracovního prostoru Log Analytics a vyberte možnost **řešení** v levém podokně.<br><br> ![Vyberte možnost řešení v pracovním prostoru](./media/service-map/select-solution-from-workspace.png).<br> Seznam řešení, vyberte **ServiceMap(workspaceName)** a v Service Map řešení Přehled stránky klikněte na dlaždici souhrnu Service Map.<br><br> ![Řešení Service Map souhrnnou dlaždici](./media/service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Případy použití: Ujistěte se, vaši IT procesy závislost vědět

### <a name="discovery"></a>Zjišťování
Řešení Service Map automaticky vytvoří common odkaz mapování závislostí napříč servery, procesy a služby třetích stran. Zjišťuje a mapuje všechny závislosti TCP, identifikace překvapením, že připojení, vzdálené systémy třetích stran, závisí na a závislosti pro tradiční tmavě oblasti sítě, jako je Active Directory. Řešení Service Map zjistí selhání síťového připojení, která spravovaných systémech se pokoušíte provést, díky tomu můžete identifikovat potenciální chybnou konfiguraci serveru, výpadku služeb a problémy se sítí.

### <a name="incident-management"></a>Správa incidentů
Řešení Service Map pomáhá eliminovat izolovat problémy znázorňující, jak jsou připojené systémy a vzájemného ovlivnění. Kromě identifikace neúspěšná připojení, pomáhá identifikovat nástroje pro vyrovnávání zatížení nesprávně nakonfigurované, překvapivé nebo nadměrného zatížení kritických služeb a podvodné klientů, jako jsou počítače pro vývojáře s produkčními systémy. S použitím integrované pracovní postupy s Change Tracking, můžete také zjistit, jestli událost změny na počítači s back-end nebo služba vysvětluje původní příčinu incidentu.

### <a name="migration-assurance"></a>Migrace assurance
S použitím řešení Service Map, můžete efektivně plánování, zrychlení a ověření migrace do Azure, který pomáhá zajistit, že nic se zachovají a není dojde k výpadku překvapení. Je možné vyhledat všechny vzájemně závislých systémů, které je potřeba migrovat společně, posuzuje konfiguraci systému a kapacity a zjistit, jestli spuštěný systém stále poskytuje uživatelům nebo je kandidátem pro vyřazení z provozu místo migrace. Po dokončení přesunutí, můžete zkontrolovat na zatížení klienta a identit k ověření, že se připojujete testovací systémy a zákazníky. Pokud definic podsítí plánování a brány firewall mají problémy, neúspěšná připojení v rámci služby maps Service Map odkazovat na systémy, které je třeba připojení.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
Pokud používáte Azure Site Recovery a potřebujete pomoc definování posloupnosti obnovení pro vaše prostředí aplikací, řešení Service Map automaticky poznáte, jak systémy spoléhají na sobě. Ujistěte se, že je spolehlivé plánu obnovení. Výběrem možnosti důležitého serveru nebo skupiny a zobrazení jeho klienty, můžete určit, které front-endové systémy pro obnovení po obnovena a k dispozici serveru. Naopak zobrazením kritické servery back-end závislosti, můžete určit, které systémy na zotavení, než se obnoví vašich systémů fokus.

### <a name="patch-management"></a>Správa oprav
Řešení Service Map zlepšuje využití posouzení aktualizace systému ukazuje, které další týmy a servery závisí na vaší služby, tak může upozornit předem předtím, než přejdete dolů vašich systémů pro opravy. Řešení Service Map také rozšiřuje správu oprav ukazuje, zda služby jsou k dispozici a je správně připojená po jsou opravit a restartovat.

## <a name="mapping-overview"></a>Přehled mapování
Řešení Service Map agentů získat informace o všechny procesy připojené protokolem TCP na serveru, kde už nainstalovaný a podrobnosti o příchozích a odchozích připojení pro každý proces.

V seznamu v levém podokně můžete vybrat počítače nebo skupiny, které mají Service Map agentů k vizualizaci jejich závislosti za zadané časové období. Závislost počítače mapuje se zaměřují na konkrétní počítač, a zobrazí všechny počítače, které jsou s přímým přístupem TCP klientech nebo serverech tohoto počítače.  Skupina počítačů mapy zobrazit sady serverů a jejich závislosti.

![Přehled řešení Service Map](media/service-map/service-map-overview.png)

Počítače lze rozšířit na mapě zobrazíte spuštění zpracování skupiny a procesy s aktivními síťovými připojeními během vybraného časového úseku. Když vzdálený počítač s agentem Service Map rozbalen a zobrazuje podrobnosti o procesu, jsou uvedeny pouze procesy, které komunikují s počítačem fokus. Počet počítačů bez agentů front-endu, které se připojují k počítači fokus je uvedené na levé straně procesy, které se připojují. Pokud počítač fokus je při připojování k počítači s back-end, který nemá žádný agent, back endového serveru je zahrnutý do skupiny portu serveru, společně s další připojení ke stejné číslo portu.

Ve výchozím nastavení řešení Service Map mapy zobrazit posledních 30 minut informací o závislostech. Pomocí ovládacích prvků času v levém horním rohu se můžete dotazovat mapování pro historické časových rozsahů až jednu hodinu, než ukazují, jak závislosti hledá v minulosti (například během incident nebo předtím, než došlo ke změně). Řešení Service Map data se ukládají po dobu 30 dnů v placených pracovních prostorů a po dobu 7 dní v bezplatné pracovní prostory.

## <a name="status-badges-and-border-coloring"></a>Stav oznámení a barvy ohraničení
V dolní části každého serveru v objektu map lze seznam oznámení stavu takzvané stavové informace o serveru. Odznáčky naznačují, že některé důležité informace pro server z jednoho z řešení integrace. Kliknutím oznámení "BADGE" přejdete přímo na podrobnosti o stavu v pravém podokně. Odznáčky aktuálně k dispozici stav zahrnují výstrahy, oddělení služeb, změny, zabezpečení a aktualizace.

V závislosti na závažnosti odznáčků stav počítače uzlu ohraničení lze barevné červené (kritická), žlutém (varování) nebo modré (informativní). Barva představuje stav nejzávažnější odznáčků stav. Šedé ohraničení označuje uzel, který nemá žádné indikátory stavu.

![Stav oznámení](media/service-map/status-badges.png)

## <a name="process-groups"></a>Zpracování skupin
Zpracování skupin kombinovat procesy, které jsou spojeny s common produkt nebo službu do skupiny procesů.  Při rozbalení uzlu počítače zobrazí samostatné procesy spolu s skupin procesů.  Pokud příchozí a odchozí připojení k procesu v rámci skupiny procesu se nezdařilo pak připojení je zobrazen jako neúspěch pro skupinu celý proces.

## <a name="machine-groups"></a>Skupiny počítačů
Skupiny počítačů vám umožňují zobrazit mapování zaměřená na skupinu serverů, nikoli pouze jeden, zobrazí se všichni členové clusteru vícevrstvé aplikace nebo serveru v jedné mapy.

Uživatelé vybrat, které servery patří do skupiny k sobě a zvolte název pro skupinu.  Pak můžete zobrazit skupiny se všemi jeho procesy a připojení, nebo zobrazení s procesy a připojení přímo související se členy skupiny.

![Skupina počítačů](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Vytvořit skupinu počítačů
Pokud chcete vytvořit skupinu, vyberte počítače nebo počítače chcete, aby na počítačích seznamu a klikněte na tlačítko **přidat do skupiny**.

![Vytvoření skupiny](media/service-map/machine-groups-create.png)

Tam si můžete zvolit **vytvořit nový** a zadejte název skupiny.

![Název skupiny](media/service-map/machine-groups-name.png)

>[!NOTE]
>Skupiny počítačů jsou omezené na 10 serverů.

### <a name="viewing-a-group"></a>Zobrazení skupiny
Po vytvoření některé skupiny, můžete je zobrazit výběrem na kartě skupiny.

![Karty skupiny](media/service-map/machine-groups-tab.png)

Vyberte název skupiny, chcete-li zobrazit mapu pro tuto skupinu počítačů.
![Skupina počítačů](media/service-map/machine-group.png) počítačů, které patří do skupiny jsou uvedeny v bílé v objektu map.

Rozbalení skupiny se zobrazí seznam počítačů, které tvoří skupinu počítačů.

![Počítače seskupit počítače](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrovat podle procesů
Můžete přepínat mezi zobrazením všech procesů a připojení ve skupině a pouze ty, které se týkají přímo do skupiny počítačů zobrazení mapy.  Výchozí zobrazení je k zobrazení všech procesů.  Zobrazení můžete změnit kliknutím na ikonu filtru nad mapou.

![Skupinu filtrů](media/service-map/machine-groups-filter.png)

Když **všechny procesy** je vybrána, mapa bude obsahovat všechny procesy a připojení na všech počítačích ve skupině.

![Zpracuje všechny skupiny počítačů](media/service-map/machine-groups-all.png)

Pokud změníte zobrazení na pouze **procesy ve skupině**, na mapě se zúží pouze procesy a připojení, které jsou připojené přímo k jiným počítačům ve skupině, vytváření zjednodušené zobrazení.

![Skupina počítačů filtrovat procesy](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Přidání počítačů do skupiny
Chcete-li přidat počítače do existující skupiny, zaškrtněte políčka vedle počítače a potom klikněte na **přidat do skupiny**.  Zvolte skupinu, kterou chcete přidat počítače do.
 
### <a name="removing-machines-from-a-group"></a>Odebrání počítače ze skupiny
V seznamu skupiny rozbalte název skupiny seznam počítačů ve skupině počítačů.  Potom klikněte na tlačítko se třemi tečkami nabídku vedle počítače, kterou chcete odebrat a zvolte **odebrat**.

![Odebrání počítače ze skupiny](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Odebrání nebo přejmenování skupiny
Klikněte na tlačítko se třemi tečkami nabídku vedle názvu skupiny v seznamu skupin.

![Nabídka skupiny počítačů](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Role ikony
Některé procesy poskytovat konkrétní role na počítačích: webové servery, aplikační servery, databáze a tak dále. Řešení Service Map označí procesu a strojově polí s ikonami role vám pomůže identifikovat na první pohled role procesu nebo serveru jde skvěle dohromady.

| Ikona role | Popis |
|:--|:--|
| ![Webový server](media/service-map/role-web-server.png) | Webový server |
| ![Aplikační server](media/service-map/role-application-server.png) | Aplikační server |
| ![Databázový server](media/service-map/role-database.png) | Databázový server |
| ![LDAP server](media/service-map/role-ldap.png) | LDAP server |
| ![Server s protokolem SMB](media/service-map/role-smb.png) | Server s protokolem SMB |

![Role ikony](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Neúspěšná připojení
Neúspěšná připojení jsou uvedeny v rámci služby maps Service Map pro procesy a počítače, s přerušovanou červenou čáru indikující, že klientský systém nemůže kontaktovat procesu nebo port. Neúspěšná připojení jsou hlášeny z jakéhokoli systému s nasazenou agentem Service Map, pokud daný systém je pokusu o připojení se nezdařilo. Řešení Service Map měří tento proces pozorováním sockety TCP, které se nepodařilo navázat připojení. Tato chyba může být výsledkem bránu firewall, chybnou konfigurací v klientovi nebo serveru nebo vzdálené služby není k dispozici.

![Neúspěšná připojení](media/service-map/failed-connections.png)

Principy neúspěšná připojení může pomoci při řešení potíží, ověření migrace, analýzu zabezpečení a porozumění celkové architektury. Neúspěšná připojení jsou někdy neškodné, ale často ukazují přímo na problém, jako je převzetí služeb při selhání prostředí náhle stane nedostupný, nebo dvou aplikačních vrstev se nebude moct komunikovat po migrace do cloudu.

## <a name="client-groups"></a>Skupiny klientů
Skupiny klientů jsou pole na mapě, které představují klientské počítače, které nemají agenti závislosti. Jedné skupiny klientů reprezentuje klienty pro jednotlivé procesy nebo počítač.

![Skupiny klientů](media/service-map/client-groups.png)

Pokud chcete zobrazit IP adresy serverů ve skupině klientů, vyberte skupinu. Obsah skupiny jsou uvedeny v **vlastnosti skupiny klientů** podokně.

![Vlastnosti skupiny klientů](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Port serveru skupiny
Port serveru skupiny jsou pole, které představují porty serveru na serverech, které nemají agenti závislosti. Do pole obsahuje port serveru a počet serverů s připojením k tomuto portu. Rozbalte pole se zobrazí jednotlivé servery a připojení. Pokud v poli je pouze jeden server, je uvedený název nebo IP adresu.

![Port serveru skupiny](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Místní nabídka
Kliknutím na tři tečky (...) v horní pravé části jakéhokoli serveru zobrazí místní nabídku pro tento server.

![Neúspěšná připojení](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Načíst serverovou mapu
Kliknutím na **načíst serverovou mapu** vás přesměruje na mapu s novým s vybraným serverem jako nový počítač fokus.

### <a name="show-self-links"></a>Zobrazit odkazy na sebe sama
Kliknutím na **zobrazit Self-Links** překreslí uzlu serveru, včetně všech odkazů na sebe sama, které jsou připojení TCP, které začínají i končí na procesy v rámci serveru. Pokud odkazů na sebe sama se zobrazí změny příkaz nabídky **skrýt Self-Links**, takže je můžete vypnout.

## <a name="computer-summary"></a>Souhrn počítače
**Počítač Souhrn** podokně jsou základní informace o operační systém serveru, počet závislostí a data z jiných řešení. Tato data zahrnují metriky výkonu, lístky podpory, řešení change tracking, zabezpečení a aktualizace.

![Souhrn počítače](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Vlastnosti počítače a procesu
Při přechodu mapu Service Map, můžete vybrat počítače a procesy, abyste získali další kontext o svých vlastnostech. Počítače poskytují informace o DNS název, IPv4 adresy, procesoru a paměti kapacitu, typ virtuálních počítačů, operační systém a verze, poslední restartování čas a ID jejich agenty OMS a Service Map.

![Podokno vlastností počítače](media/service-map/machine-properties.png)

Podrobnosti o procesu bude možné shromažďovat z operačního systému metadata o spuštěných procesech, včetně název procesu, popis procesu, uživatelské jméno a doménu (na Windows), název společnosti, název produktu, verze produktu, pracovní adresář, příkazového řádku a procesu čas spuštění.

![Podokno vlastností procesu](media/service-map/process-properties.png)

**Souhrn procesů** podokno obsahuje další informace o připojení k procesu, včetně jeho vázaných portů, příchozí a odchozí připojení a připojení se nezdařilo.

![Souhrn procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrace výstrah
Řešení Service Map se integruje s Azure Alerts zobrazíte aktivovaná upozornění pro vybraný server ve vybraném časovém rozsahu. Na serveru zobrazuje ikonu, pokud aktuální výstrahy a **výstrahy počítače** podokně je seznam výstrah.

![Podokno oznámení počítače](media/service-map/machine-alerts.png)

Pokud chcete povolit řešení Service Map zobrazíte příslušné výstrahy, vytvoření pravidla upozornění, který se aktivuje pro určitý počítač. Chcete-li vytvořit správné výstrahy:
- Obsahovat klauzuli do skupiny podle počítače (například **počítače interval 1 minuta**).
- Vyberte výstrahy založené na základě měření metriky.

## <a name="log-events-integration"></a>Integrace protokolů událostí
Řešení Service Map se integruje s prohledáváním protokolů zobrazíte počet všech dostupných protokolu událostí pro vybraný server během vybraného časového úseku. Můžete kliknout na libovolný řádek v seznamu počty událostí přejít k prohledávání protokolů a zobrazte jednotlivé protokolu událostí.

![Podokno počítače události protokolu](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrace oddělení služeb
Integrace řešení Service Map se IT Service Management Connector je automatické, pokud obě řešení jsou povolené a nakonfigurované ve vašem pracovním prostoru Log Analytics. Integrace v Service Map je označené jako "Služby podpory." Další informace najdete v tématu [centrálně spravovat pracovní položky ITSM pomocí IT Service Management Connector](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

**Oddělení služeb Machine** podokno zobrazuje všechny události správy služeb IT pro vybraný server ve vybraném časovém rozsahu. Na serveru zobrazuje ikonu, pokud nejsou aktuální položky a v podokně oddělení služeb Machine seznamy je.

![Podokno počítače oddělení služeb](media/service-map/service-desk.png)

Položky v připojených řešení ITSM, klikněte na tlačítko **zobrazit pracovní položku**.

Chcete-li zobrazit podrobnosti o položce v prohledávání protokolu, klikněte na tlačítko **zobrazit v hledání v protokolu**.
Metrik připojení se zapisují do dvou nových tabulek ve službě Log Analytics 

## <a name="change-tracking-integration"></a>Změnit integrace sledování
Integrace mapy služeb pomocí řešení Change Tracking je automatické, pokud jsou obě řešení povolené a nakonfigurované ve vašem pracovním prostoru Log Analytics.

**Počítač řešení Change Tracking** podokno obsahuje všechny změny s nejnovější první, spolu s odkazem k podrobnostem a prohledávání protokolů pro další podrobnosti.

![Podokno počítač řešení Change Tracking](media/service-map/change-tracking.png)

Na následujícím obrázku je podrobné zobrazení ConfigurationChange událost, která může dojít po výběru **zobrazení v Log Analytics**.

![ConfigurationChange události](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integrace výkonu
**Výkonu počítačů** podokně se zobrazí standardních metrik výkonu pro vybraný server. Metriky zahrnují využití CPU, využití paměti, odeslané a Přijaté bajty v síti a seznam důležitých procesů podle odeslané a Přijaté bajty v síti.

![Podokno výkon počítače](media/service-map/machine-performance.png)

Pokud chcete zobrazit údaje o výkonu, budete muset [povolit příslušné čítače výkonu Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Čítače, které budete chtít povolit:

Windows:
- Procesor(*)\\čas procesoru v %
- Paměť\\% využívání svěřených bajtů
- Sítě Adapter(*)\\odeslané bajty/s
- Sítě Adapter(*)\\přijaté bajty/s

Linux:
- Procesor(*)\\čas procesoru v %
- Memory(*)\\% využité paměti
- Sítě Adapter(*)\\odeslané bajty/s
- Sítě Adapter(*)\\přijaté bajty/s

Pokud chcete získat data o výkonu sítě, musíte také povolíte řešení Wire Data 2.0 ve vašem pracovním prostoru.
 
## <a name="security-integration"></a>Integrace zabezpečení
Integrace řešení Service Map se zabezpečení a Audit je automatické, pokud obě řešení jsou povolené a nakonfigurované ve vašem pracovním prostoru Log Analytics.

**Zabezpečení počítačů** podokně zobrazí data z řešení zabezpečení a Audit pro vybraný server. V podokně obsahuje souhrnný seznam problémů nezpracovaných zabezpečení pro server během vybraného časového úseku. Některé problémy postupů zabezpečení kliknutí až na prohledávání protokolu podrobnosti o nich.

![Podokno zabezpečení počítače](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Aktualizace integrace
Řešení Service Map integrace se správu aktualizací je automatické, když jsou povolené a nakonfigurované ve vašem pracovním prostoru protokolu Anlaytics obě řešení.

**Aktualizace počítače** podokně se zobrazí data z řešení Update Management pro vybraný server. V podokně obsahuje souhrnný seznam všechny chybějící aktualizace pro server během vybraného časového úseku.

![Podokno počítač řešení Change Tracking](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Data inventáře počítače a procesu Service Map je k dispozici pro [hledání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics. Tato data můžete použít scénáře, které zahrnují plánování migrace, kapacitu analýza, zjišťování a řešení potíží s výkonem na vyžádání.

Jeden záznam se vygeneruje za hodinu pro každý počítač jedinečné a proces, kromě záznamy, které jsou generovány, pokud proces nebo počítače spustí nebo je zprovozněný do Service Map. Tyto záznamy mají vlastnosti v následujících tabulkách. Pole a hodnoty v událostech ServiceMapComputer_CL mapují na pole počítače zdroje v rozhraní API Azure Resource Manageru ServiceMap. Pole a hodnoty v událostech ServiceMapProcess_CL mapují na pole v rozhraní API Azure Resource Manageru ServiceMap prostředku procesu. Pole ResourceName_s odpovídá poli Název odpovídající prostředku Resource Manageru. 

>[!NOTE]
>Podle toho funkce Service Map, jak tato pole se mohou změnit.

Existují interně vygenerovanému vlastnosti, které můžete použít k identifikaci jedinečný procesy a počítače:

- Počítač: Použití *ResourceId* nebo *ResourceName_s* k jednoznačné identifikaci počítače v rámci pracovního prostoru Log Analytics.
- Proces: Použití *ResourceId* k jednoznačné identifikaci procesu v rámci pracovního prostoru Log Analytics. *ResourceName_s* je jedinečný v rámci počítače, na kterém je proces spuštěn (MachineResourceName_s) 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozmezí může existovat více záznamů, dotazy mohou vracet víc než jeden záznam pro stejný počítač nebo procesu. Chcete-li zahrnout pouze poslední záznam, přidejte "| Při odstraňování duplicitních dat ResourceId"v dotazu.

### <a name="connections"></a>Připojení
Metrik připojení se zapisují do nové tabulky ve službě Log Analytics – VMConnection. Tato tabulka obsahuje informace o připojení pro počítač (příchozí a odchozí). Metrik připojení jsou přístupné také pomocí rozhraní API, která poskytují způsob, jak získat určité metriky během časového intervalu.  Připojení TCP vyplývající z "*přijmout*- ing naslouchání soketu se příchozí při vytvořených *připojení*- ing k dané IP adresy a portu jsou odchozí. Směr připojení je reprezentována vlastnost směr, který může být nastaven na hodnotu **příchozí** nebo **odchozí**. 

Z data, která agenta závislostí se generují záznamy v těchto tabulkách. Každý záznam představuje hodnotu v časovém intervalu jedné minuty. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace k identifikaci příslušné entity, to znamená, připojení nebo port, jakož i metriky, které jsou přidružené k dané entitě. V současné době se hlásí pouze síťové aktivity, ke které dojde, pomocí protokolu TCP přes protokol IPv4.

Pokud chcete spravovat náklady a složitost, záznamy o připojení nepředstavují jednotlivých fyzických síťových připojení. Víc fyzických síťových připojení jsou seskupeny do logických připojení, který je pak v příslušné tabulce.  Význam, zaznamená *VMConnection* tabulce představují logické seskupení a nikoli jednotlivé fyzické připojení, která jsou sledována. Fyzické síťové připojení, které sdílejí stejnou hodnotu pro následující atributy během intervalu daném jedné minuty se agregují do jednoho logického záznamu v *VMConnection*. 

| Vlastnost | Popis |
|:--|:--|
|Směr |Směr připojení, je hodnota *příchozí* nebo *odchozí* |
|Počítač |Plně kvalifikovaný název domény počítače |
|Proces |Identita procesu nebo skupin procesů, inicializace a přijímá připojení |
|SourceIp |IP adresa zdroje |
|DestinationIp |Cílové IP adresy |
|DestinationPort |Číslo portu cíle |
|Protocol (Protokol) |Protokol použitý pro připojení.  Hodnoty *tcp*. |

Aby se zohlednily dopadu seskupení, informace o počtu seskupených fyzických připojení najdete v následující vlastnosti záznamu:

| Vlastnost | Popis |
|:--|:--|
|LinksEstablished |Počet fyzických síťových připojení, které se vytvořily časovém období vytváření sestav |
|LinksTerminated |Počet fyzických síťových připojení, která byla ukončena časovém období vytváření sestav |
|LinksFailed |Počet fyzických síťových připojení, které selhaly časovém období vytváření sestav. Tyto informace jsou aktuálně k dispozici pouze pro odchozí připojení. |
|LinksLive |Počet fyzických síťových připojení, které se otevřelo na konci generování sestav časový interval|

#### <a name="metrics"></a>Metriky

Kromě metrik počet připojení informace o objemu dat odeslaných a přijatých na dané logické propojení nebo síťový port jsou také uvedené v následující vlastnosti záznamu:

| Vlastnost | Popis |
|:--|:--|
|BytesSent |Celkový počet bajtů, které byly odeslány časovém období vytváření sestav |
|BytesReceived |Celkový počet bajtů, které byly přijaty časovém období vytváření sestav |
|Odezvy |Počet odpovědí zjištěnými časovém období vytváření sestav. 
|ResponseTimeMax |Maximální doba odezvy (milisekundy) zjištěnými časovém období vytváření sestav.  Pokud žádná hodnota vlastnosti je prázdná.|
|ResponseTimeMin |Minimální doba odezvy (milisekundy) zjištěnými časovém období vytváření sestav.  Pokud žádná hodnota vlastnosti je prázdná.|
|ResponseTimeSum |Součet všech doby odezvy (milisekundy) zjištěnými časovém období vytváření sestav.  Pokud žádná hodnota, vlastnost je prázdné|

Doba odezvy je třetí typ dat ohlašovaný – jak dlouho volající věnovat časový limit na žádosti zaslané prostřednictvím připojení ke zpracování a reagovalo oddělení vzdálený koncový bod. Doba odezvy hlášené je odhad doby odezvy true na základním protokolu aplikace. To je vypočítán s použitím heuristické metody založené na sledování tok dat mezi zdrojovou a cílovou konec připojení k fyzické síti. Koncepčně je rozdíl mezi časem poslední bajt požadavku opouští odesílatele a čas při přijetí posledního bajtu odpovědi k němu. Tyto dva časové razítko se používají od sebe odděluje událostí žádostí a odpovědí na jedno fyzické připojení. Rozdíl mezi nimi představuje doba odezvy jedné žádosti. 

V tomto prvním vydání této funkce je naše algoritmus přibližný, které může fungovat pro různé míře úspěšnosti v závislosti na skutečné aplikace protokol použitý pro připojení k dané síti. Například aktuální přístup funguje dobře pro žádost odpověď na základě protokolů jako jsou HTTP (S), ale pomocí jednosměrné nebo nefunguje protokolů založených na frontě zpráv.

Tady jsou některé důležité body ke zvážení:

1. Pokud proces akceptuje připojení na stejnou IP adresu, ale přes několik síťových rozhraní, uvádět bude samostatný záznam pro každé rozhraní. 
2. Záznamů se zástupnými znaky IP bude obsahovat žádná aktivita. Jsou zahrnuty představují skutečnost, že port na počítači je otevřená pro příchozí provoz.
3. Pokud chcete snížit úroveň podrobností a objem dat, záznamů se zástupnými znaky IP budou vypuštěny po odpovídající záznam (pro stejný proces, port a protokol) s konkrétní IP adresu. Pokud záznam IP zástupných znaků je vynechán, vlastnost záznamu IsWildcardBind s konkrétní IP adresu, bude nastavena na hodnotu "True" k označení, že port, který je přístupný přes každých rozhraní vytváření sestav počítačů.
4. Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na hodnotu "False".

#### <a name="naming-and-classification"></a>Zásady vytváření názvů a klasifikace
Pro usnadnění práce IP adresu ke konci vzdáleného připojení je součástí RemoteIp vlastnost. Pro příchozí připojení, RemoteIp je stejný jako SourceIp, zatímco pro odchozí připojení, je stejný jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje hlášených počítači pro RemoteIp canonical názvy DNS. Vlastnosti RemoteDnsQuestions a RemoteClassification jsou vyhrazené pro budoucí použití. 

#### <a name="geolocation"></a>Zeměpisná poloha
*VMConnection* obsahuje také informace o zeměpisné poloze informace o vzdáleným koncem záznamech připojení ve vlastnostech následující záznam: 

| Vlastnost | Popis |
|:--|:--|
|RemoteCountry |Název země hostování RemoteIp.  Například *USA* |
|RemoteLatitude |Zeměpisná poloha, zeměpisná šířka.  Například *47.68* |
|RemoteLongitude |Informace o zeměpisné poloze délky.  Například *-122.12* |

#### <a name="malicious-ip"></a>Škodlivá IP adresa
Každá vlastnost RemoteIp v *VMConnection* tabulky je porovnávána s sadu IP adres pomocí známých škodlivých aktivit. Pokud se zjistí, RemoteIp jako škodlivý naplní se následující vlastnosti (jsou prázdné, pokud IP adresa se považuje za škodlivou) v záznamu následující vlastnosti:

| Vlastnost | Popis |
|:--|:--|
|MaliciousIp |Vzdálená adresa IP adres |
|IndicatorThreadType |Indikátor hrozeb zjistila je jeden z následujících hodnot *Botnet*, *C2*, *CryptoMining*, *Darknet*, *před útoky DDos* , *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Seznamu ke zhlédnutí*.   |
|Popis |Popis zjištěných hrozeb. |
|TLPLevel |Úroveň protokolu semaforu (algoritmus TLP) je jedna z definovaných hodnot *prázdné*, *zelená*, *žlutou*, *Red*. |
|Spolehlivost |Hodnoty jsou *0 – 100*. |
|Severity |Hodnoty jsou *0 – 5*, kde *5* je nejzávažnější a *0* není natolik vůbec. Výchozí hodnota je *3*.  |
|FirstReportedDateTime |První zprostředkovatel ohlásil indikátoru. |
|LastReportedDateTime |Čas posledního ukazatele viděla Interflow. |
|IsActive |Označuje deaktivují se s indikátory *True* nebo *False* hodnotu. |
|ReportReferenceLink |Obsahuje odkazy na sestavy související se daný pozorovat. |
|AdditionalInformation |Poskytuje další informace, pokud je k dispozici informace o zjištěných hrozeb. |

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL záznamů
Záznamy typu *ServiceMapComputer_CL* mít data inventáře pro servery s agenty řešení Service Map. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ID prostředku | Jedinečný identifikátor pro počítač v pracovním prostoru |
| ResourceName_s | Jedinečný identifikátor pro počítač v pracovním prostoru |
| ComputerName_s | Plně kvalifikovaný název domény počítače |
| Ipv4Addresses_s | Seznam serveru IPv4 adres |
| Ipv6Addresses_s | Seznam serveru IPv6 adres |
| DnsNames_s | Pole názvů DNS |
| OperatingSystemFamily_s | Windows nebo Linux |
| OperatingSystemFullName_s | Úplný název operačního systému  |
| Bitness_s | Bitová verze počítače (32bitová nebo 64bitová verze)  |
| PhysicalMemory_d | Fyzická paměť v MB |
| Cpus_d | Počet procesorů |
| CpuSpeed_d | Rychlost procesoru v MHz|
| VirtualizationState_s | *Neznámý*, *fyzické*, *virtuální*, *hypervisoru* |
| VirtualMachineType_s | *Hyper-v*, *vmware*, a tak dále |
| VirtualMachineNativeMachineId_g | ID virtuálního počítače přiřazené službou jeho hypervisoru |
| VirtualMachineName_s | Název virtuálního počítače |
| BootTime_t | Čas spuštění |

### <a name="servicemapprocesscl-type-records"></a>Typ ServiceMapProcess_CL záznamů
Záznamy typu *ServiceMapProcess_CL* mít data inventáře pro procesy připojené protokolem TCP na serverech s agenty řešení Service Map. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ID prostředku | Jedinečný identifikátor procesu v rámci pracovního prostoru |
| ResourceName_s | Jedinečný identifikátor procesu v počítači, na kterém je spuštěná|
| MachineResourceName_s | Název prostředku počítače |
| ExecutableName_s | Název spustitelného souboru procesu |
| StartTime_t | Čas spuštění procesu fondu |
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
| Uživatelské jméno | Účet, pod kterým je spuštěn proces |
| UserDomain | Domény, pod kterým je spuštěn proces |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

### <a name="list-all-known-machines"></a>Seznam všech známých počítačů
ServiceMapComputer_CL | shrnutí arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Seznam kapacita fyzické paměti všech spravovaných počítačů.
ServiceMapComputer_CL | shrnutí arg_max(TimeGenerated, *) podle ID prostředku | Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Název počítače seznamu, DNS, IP a operačního systému.
ServiceMapComputer_CL | shrnutí arg_max(TimeGenerated, *) podle ID prostředku | Projekt ComputerName_s OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najít všechny procesy s "sql" v příkazovém řádku
ServiceMapProcess_CL | kde CommandLine_s contains_cs "sql" | shrnutí arg_max(TimeGenerated, *) podle ID prostředku

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Najít počítač (poslední záznam) podle názvu prostředku
hledání v (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | shrnutí arg_max(TimeGenerated, *) podle ID prostředku

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Najít počítač (poslední záznam) podle IP adresy
hledání v (ServiceMapComputer_CL) "10.229.243.232" | shrnutí arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-all-known-processes-on-a-specified-machine"></a>Seznam všech známých procesů v zadaném počítači
ServiceMapProcess_CL | kde MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | shrnutí arg_max(TimeGenerated, *) podle ID prostředku

### <a name="list-all-computers-running-sql"></a>Seznam všech počítačů s SQL
ServiceMapComputer_CL | kde ResourceName_s v ((vyhledávání v (ServiceMapProcess_CL) "\*sql\*" | odlišné MachineResourceName_s)) | odlišné ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Zobrazí seznam všech verzí produktu jedinečné nástroje curl do svého datacentra
ServiceMapProcess_CL | kde ExecutableName_s == "curl" | DISTINCT ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvořit skupinu počítačů všechny počítače se systémem CentOS
ServiceMapComputer_CL | kde OperatingSystemFullName_s contains_cs "CentOS" | DISTINCT ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Shrnutí odchozí připojení ze skupiny počítačů
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API
Všechna data serveru, proces a závislosti v Service Map je k dispozici prostřednictvím [rozhraní REST API služby mapy](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím vašeho používání služby Service Map. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby mapa služby. Pokud chcete poskytnout přesné a efektivní možnosti pro odstraňování potíží, data obsahují informace o konfiguraci vašeho softwaru, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Další postup
Další informace o [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics data, která shromažďují Service Map.


## <a name="troubleshooting"></a>Řešení potíží
Zobrazit [řešení potíží s konfigurací řešení Service Map dokumentu v části]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Váš názor
Máte jakoukoli zpětnou vazbu pro nás o mapy služeb nebo v této dokumentaci?  Navštivte naše [User Voice stránky](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhnout funkce nebo hlasovat do existujících návrhů.
