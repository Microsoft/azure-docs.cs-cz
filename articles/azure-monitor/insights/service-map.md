---
title: Použití řešení Mapy služeb v Azure | Dokumenty společnosti Microsoft
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobnosti pro nasazení mapy služeb ve vašem prostředí a jeho použití v různých scénářích.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f2f3e84462307f43ffe432fe878476d979f489f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480908"
---
# <a name="using-service-map-solution-in-azure"></a>Používání řešení Service Map v Azure

Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Service Map zobrazuje vaše servery tak, jak o nich přemýšlíte, tzn. jako propojené systémy, které zajišťují důležité služby. Service Map zobrazuje propojení serverů, procesů, latenci příchozích a odchozích připojení a porty v libovolné architektuře propojené protokolem TCP. Kromě instalace agenta se nevyžaduje žádná konfigurace.

Tento článek popisuje podrobnosti o zařazování a používání mapy služeb. Informace o konfiguraci předpokladů pro toto řešení najdete [v tématu Povolení přehledu sledování Azure pro virtuální počítače](vminsights-enable-overview.md#prerequisites). Chcete-li shrnout, potřebujete následující:

* Pracovní prostor Analýzy protokolů, který toto řešení povolí.

* Agent Log Analytics nainstalovaný na počítači se systémem Windows nebo na serveru S IP nakonfigurovaném tak, aby hlásil stejný pracovní prostor, se kterým jste povolili řešení.

* Agent závislostí nainstalovaný na počítači se systémem Windows nebo na serveru SIP.

>[!NOTE]
>Pokud jste už nasadili mapu služeb, teď můžete taky zobrazit mapy ve službě Azure Monitor pro virtuální počítače, která obsahuje další funkce pro monitorování stavu a výkonu virtuálních počítačů. Další informace najdete v tématu [Azure Monitor pro virtuální počítače přehled](../../azure-monitor/insights/vminsights-overview.md). Informace o rozdílech mezi řešením Mapy služeb a funkcí Azure Monitor for VMs najdete v následujících [nejčastějších dotazech](../faq.md#azure-monitor-for-vms).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="enable-service-map"></a>Povolit mapu služeb

1. Povolte řešení Mapy služeb z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) nebo pomocí procesu popsaného v části Přidat řešení monitorování z Galerie [řešení](solutions.md).
1. [Nainstalujte agenta závislostí v systému Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) nebo [nainstalujte agenta závislostí na Linuxu](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) do každého počítače, ve kterém chcete získat data. Závislý agent dokáže monitorovat připojení k bezprostředním sousedům, takže nepotřebujete mít agenta na každém počítači.

Přístup k mapování služeb na portálu Azure z pracovního prostoru Analýzy protokolů a v levém podokně vyberte možnost **Řešení.**<br><br> ![Vyberte možnost Řešení](./media/service-map/select-solution-from-workspace.png)v pracovním prostoru .<br> Ze seznamu řešení vyberte **ServiceMap(workspaceName)** a na stránce Přehled řešení služby klikněte na dlaždici Souhrn servisní mapa.<br><br> ![Souhrnná dlaždice](./media/service-map/service-map-summary-tile.png)Mapy služeb .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Případy použití: Zjitříme závislost procesů IT

### <a name="discovery"></a>Zjišťování

Mapa služeb automaticky vytváří společnou referenční mapu závislostí napříč servery, procesy a službami třetích stran. Zjišťuje a mapuje všechny závislosti protokolu TCP, identifikuje překvapivá připojení, vzdálené systémy třetích stran, na kterých jste závislí, a závislosti na tradičních tmavých oblastech sítě, jako je například služba Active Directory. Mapa služeb zjistí neúspěšná síťová připojení, která se vaše spravované systémy pokoušejí vytvořit, což vám pomůže identifikovat potenciální chybnou konfiguraci serveru, výpadek služby a problémy se sítí.

### <a name="incident-management"></a>Správa incidentů

Mapa služeb pomáhá eliminovat dohady o izolaci problémů tím, že ukazuje, jak jsou systémy propojeny a vzájemně ovlivňují. Kromě identifikace neúspěšných připojení pomáhá identifikovat nesprávně nakonfigurované vykladače zatížení, překvapivé nebo nadměrné zatížení kritických služeb a neautorizovaných klientů, jako jsou vývojářské počítače mluvící s produkčními systémy. Pomocí integrovaných pracovních postupů se sledováním změn můžete také zjistit, zda událost změny v back-endovém počítači nebo službě vysvětluje hlavní příčinu incidentu.

### <a name="migration-assurance"></a>Zajištění migrace

Pomocí mapy služeb můžete efektivně plánovat, zrychlovat a ověřovat migrace Azure, což pomáhá zajistit, že nic nezůstane pozadu a nedojde k výpadkům překvapení. Můžete zjistit všechny vzájemně závislé systémy, které je třeba migrovat společně, posoudit konfiguraci a kapacitu systému a zjistit, zda běžící systém stále slouží uživatelům nebo je kandidátem na vyřazení z provozu namísto migrace. Po dokončení přesunu můžete zkontrolovat zatížení a identitu klienta a ověřit, zda se testovací systémy a zákazníci připojují. Pokud má plánování podsítě a definice brány firewall problémy, neúspěšná připojení v mapách mapy služby vás odkazují na systémy, které potřebují připojení.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

Pokud používáte Azure Site Recovery a potřebujete pomoc s definováním posloupnosti obnovení pro vaše aplikační prostředí, mapa služeb vám může automaticky ukázat, jak se systémy spoléhají na sebe, aby zajistily, že váš plán obnovení je spolehlivý. Výběrem kritického serveru nebo skupiny a zobrazením jeho klientů můžete určit, které front-endové systémy se mají obnovit po obnovení a dostupnosti serveru. Naopak při pohledu na back-end ové závislosti kritických serverů můžete určit, které systémy se mají obnovit před obnovením systémů fokusu.

### <a name="patch-management"></a>Správa oprav

Mapa služeb vylepšuje vaše používání hodnocení aktualizací systému tím, že vám ukazuje, které další týmy a servery jsou závislé na vaší službě, takže je můžete předem upozornit, než systém zneškodníte pro opravy. Mapa služeb také vylepšuje správu oprav tím, že vám ukáže, zda jsou vaše služby po opravě a restartování k dispozici a správně připojeny.

## <a name="mapping-overview"></a>Přehled mapování

Agenti mapy služeb shromažďují informace o všech procesech připojených k protokolu TCP na serveru, kde jsou nainstalovány, a podrobnosti o příchozích a odchozích připojeních pro každý proces.

Ze seznamu v levém podokně můžete vybrat počítače nebo skupiny, které mají agenty mapy služeb, aby vizualizovaly jejich závislosti v zadaném časovém rozsahu. Mapy závislostí počítače se zaměřují na konkrétní počítač a zobrazují všechny počítače, které jsou přímými klienty TCP nebo servery tohoto počítače.  Mapy skupiny strojů zobrazují sady serverů a jejich závislosti.

![Mapa služeb – přehled](media/service-map/service-map-overview.png)

Počítače lze rozbalit v mapě tak, aby zobrazovala skupiny spuštěných procesů a procesy s aktivními síťovými připojeními během vybraného časového rozsahu. Při vzdáleném počítači s agentem mapy služeb je rozbalen zobrazit podrobnosti procesu, jsou zobrazeny pouze ty procesy, které komunikují s fokus počítače. Počet front-endových strojů bez agentů, které se připojují k zaostřovacímu stroji, je uveden na levé straně procesů, ke kterým se připojují. Pokud zařízení fokus vytváří připojení k back-endpočítač, který nemá žádného agenta, back-end server je součástí skupiny portů serveru, spolu s dalšími připojeními ke stejnému číslu portu.

Ve výchozím nastavení mapy mapy služby zobrazují posledních 30 minut informací o závislostech. Pomocí ovládacích prvků času v levém horním rohu můžete dotazovat mapy historických časových rozsahů až jedné hodiny a zobrazit tak, jak vypadaly závislosti v minulosti (například během incidentu nebo před změnou). Data mapy služeb jsou uložena po dobu 30 dnů v placených pracovních prostorech a po dobu 7 dnů ve volných pracovních prostorech.

## <a name="status-badges-and-border-coloring"></a>Odznaky stavu a zbarvení ohraničení

V dolní části každého serveru na mapě může být seznam stavových odznaků, které převádí informace o stavu serveru. Odznaky označují, že existuje některé relevantní informace pro server z jedné z integrace řešení. Kliknutím na odznak přejdete přímo k podrobnostem o stavu v pravém podokně. Aktuálně dostupné stavové odznaky zahrnují výstrahy, oddělení služeb, změny, zabezpečení a aktualizace.

V závislosti na závažnosti stavových odznaků mohou být ohraničení uzlů stroje zbarveny červeně (kriticky), žlutě (upozornění) nebo modře (informační). Barva představuje nejzávažnější stav některého z odznaky stavu. Šedé ohraničení označuje uzel, který nemá žádné indikátory stavu.

![Odznaky za stav](media/service-map/status-badges.png)

## <a name="process-groups"></a>Skupiny procesů

Skupiny procesů kombinují procesy, které jsou přidruženy ke společnému produktu nebo službě, do skupiny procesů.  Když je uzel počítače rozbalen, zobrazí samostatné procesy spolu se skupinami procesů.  Pokud všechna příchozí a odchozí připojení k procesu v rámci skupiny procesů selhala, je připojení zobrazeno jako neúspěšné pro celou skupinu procesů.

## <a name="machine-groups"></a>Skupiny počítačů

Skupiny strojů umožňují zobrazit mapy soustředěné kolem sady serverů, nikoli pouze jednoho, takže můžete zobrazit všechny členy vícevrstvého aplikačního nebo serverového clusteru v jedné mapě.

Uživatelé vybírají, které servery patří do skupiny společně, a zvolte název skupiny.  Potom můžete zobrazit skupinu se všemi jejími procesy a připojeními nebo ji zobrazit pouze s procesy a připojeními, které se přímo vztahují k ostatním členům skupiny.

![Skupina strojů](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Vytvoření skupiny strojů

Chcete-li vytvořit skupinu, vyberte v seznamu Počítače požadovaný počítač nebo počítače a klepněte na tlačítko **Přidat do skupiny**.

![Vytvoření skupiny](media/service-map/machine-groups-create.png)

Zde můžete zvolit **Vytvořit nový** a dát skupině název.

![Skupina názvů](media/service-map/machine-groups-name.png)

>[!NOTE]
>Skupiny strojů jsou omezeny na 10 serverů.

### <a name="viewing-a-group"></a>Zobrazení skupiny

Po vytvoření některých skupin je můžete zobrazit na kartě Skupiny.

![Karta Skupiny](media/service-map/machine-groups-tab.png)

Pak vyberte název skupiny, chcete-li zobrazit mapu pro tuto skupinu strojů.
![Skupina](media/service-map/machine-group.png) strojů Stroje, které patří do skupiny, jsou na mapě vyznačeny bíle.

Rozšířením skupiny budou uvedeny počítače, které tvoří skupinu strojů.

![Stroje se skupinou strojů](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrování podle procesů

Zobrazení mapy můžete přepínat mezi zobrazením všech procesů a připojení ve skupině a pouze těmi, které přímo souvisejí se skupinou strojů.  Výchozí zobrazení je zobrazit všechny procesy.  Zobrazení můžete změnit kliknutím na ikonu filtru nad mapou.

![Skupina filtrů](media/service-map/machine-groups-filter.png)

Když jsou **vybrány všechny procesy,** mapa bude obsahovat všechny procesy a připojení na každém z počítačů ve skupině.

![Seskupit všechny procesy](media/service-map/machine-groups-all.png)

Pokud změníte zobrazení tak, aby zobrazovalo pouze **procesy připojené ke skupině**, bude mapa zúžena pouze na procesy a připojení, která jsou přímo připojena k jiným počítačům ve skupině, čímž se vytvoří zjednodušené zobrazení.

![Filtrované procesy skupiny strojů](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Přidání strojů do skupiny

Chcete-li přidat počítače do existující skupiny, zaškrtněte políčka vedle požadovaných počítačů a klepněte na tlačítko **Přidat do skupiny**.  Potom vyberte skupinu, do které chcete počítače přidat.
 
### <a name="removing-machines-from-a-group"></a>Odebrání strojů ze skupiny

V seznamu skupin rozbalte název skupiny a seznam počítačů ve skupině strojů.  Poté klikněte na nabídku se třemi tečkami vedle zařízení, které chcete odebrat, a zvolte **Odebrat**.

![Odebrat stroj ze skupiny](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Odebrání nebo přejmenování skupiny

Klikněte na nabídku se třemi tečkami vedle názvu skupiny v seznamu skupin.

![Nabídka skupiny strojů](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony rolí

Některé procesy slouží určité role na počítačích: webové servery, aplikační servery, databáze a tak dále. Služba Mapa anotuje proces a počítače s ikonami rolí, které pomáhají na první pohled identifikovat roli procesu nebo serveru hraje.

| Ikona role | Popis |
|:--|:--|
| ![Webový server](media/service-map/role-web-server.png) | Webový server |
| ![Aplikační server](media/service-map/role-application-server.png) | Aplikační server |
| ![Databázový server](media/service-map/role-database.png) | Databázový server |
| ![Server LDAP](media/service-map/role-ldap.png) | Server LDAP |
| ![Server SMB](media/service-map/role-smb.png) | Server SMB |

![Ikony rolí](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Neúspěšná připojení

Neúspěšná připojení jsou zobrazena v mapách mapy služeb pro procesy a počítače s přerušovanou červenou čárou označující, že klientský systém nedosahuje procesu nebo portu. Neúspěšná připojení jsou hlášena z libovolného systému s nasazeným agentem mapy služeb, pokud se tento systém pokouší o neúspěšné připojení. Mapa služeb měří tento proces sledováním soketů TCP, které nenavazují připojení. Tato chyba může být důsledkem brány firewall, chybné konfigurace klienta nebo serveru nebo vzdálené služby, která není k dispozici.

![Neúspěšná připojení](media/service-map/failed-connections.png)

Principy neúspěšných připojení mohou pomoci při řešení potíží, ověření migrace, analýze zabezpečení a celkovém porozumění architektury. Selhání připojení jsou někdy neškodné, ale často poukazují přímo na problém, jako je například prostředí s podporou převzetí služeb při selhání náhle stává nedostupný, nebo dvě vrstvy aplikace není schopen mluvit po migraci do cloudu.

## <a name="client-groups"></a>Skupiny klientů

Skupiny klientů jsou pole na mapě, které představují klientské počítače, které nemají agenty závislostí. Jedna skupina klientů představuje klienty pro jednotlivé procesy nebo počítače.

![Skupiny klientů](media/service-map/client-groups.png)

Chcete-li zobrazit adresy IP serverů ve skupině klientů, vyberte skupinu. Obsah skupiny je uveden v podokně **Vlastnosti skupiny klientů.**

![Vlastnosti skupiny klientů](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Skupiny portů serveru

Skupiny portů serveru jsou pole, která představují porty serveru na serverech, které nemají agenty závislostí. Pole obsahuje port serveru a počet serverů s připojením k tomuto portu. Rozbalte pole a zobejít jednotlivé servery a připojení. Pokud je v poli pouze jeden server, je uveden název nebo adresa IP.

![Skupiny portů serveru](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Místní nabídka

Kliknutím na tři tečky (...) v pravém horním rohu libovolného serveru se zobrazí kontextová nabídka pro tento server.

![Neúspěšná připojení](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Načíst mapu serveru

Kliknutím na **načíst mapu serveru** přejdete na novou mapu s vybraným serverem jako novým zaostřovacím strojem.

### <a name="show-self-links"></a>Zobrazit vlastní odkazy

Klepnutím na tlačítko **Zobrazit vlastní odkazy** překreslíte uzel serveru, včetně všech vlastních propojení, což jsou připojení TCP, která začínají a končí procesy v rámci serveru. Pokud se zobrazí vlastní odkazy, příkaz nabídky se změní na **Skrýt vlastní odkazy**, abyste je mohli vypnout.

## <a name="computer-summary"></a>Souhrn počítače

Podokno **Souhrn počítače** obsahuje přehled operačního systému serveru, počty závislostí a data z jiných řešení. Tato data zahrnují metriky výkonu, lístky na servisní přepážku, sledování změn, zabezpečení a aktualizace.

![Podokno Souhrn počítače](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Vlastnosti počítače a procesu

Při procházení mapy služby, můžete vybrat počítače a procesy získat další kontext o jejich vlastnostech. Počítače poskytují informace o názvu DNS, adresách IPv4, kapacitě procesoru a paměti, typu virtuálního počítače, operačním systému a verzi, době posledního restartování a ID jejich agentů OMS a Map služeb.

![Podokno Vlastnosti počítače](media/service-map/machine-properties.png)

Můžete shromažďovat podrobnosti o procesu z metadat operačního systému o spuštěných procesech, včetně názvu procesu, popisu procesu, uživatelského jména a domény (v systému Windows), názvu společnosti, názvu produktu, verze produktu, pracovního adresáře, příkazového řádku a procesu čas zahájení.

![Podokno Vlastnosti procesu](media/service-map/process-properties.png)

Podokno **Souhrn procesů** poskytuje další informace o připojení procesu, včetně jeho vázaných portů, příchozích a odchozích připojení a neúspěšných připojení.

![Podokno Souhrn procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrace výstrah

Mapa služeb se integruje s výstrahami Azure a zobrazuje vypalující výstrahy pro vybraný server ve vybraném časovém rozsahu. Server zobrazí ikonu, pokud existují aktuální výstrahy, a podokno **Výstrahy počítače** uvádí výstrahy.

![Podokno Upozornění na počítač](media/service-map/machine-alerts.png)

Chcete-li povolit mapování služeb pro zobrazení příslušných výstrah, vytvořte pravidlo výstrahy, které se aktivuje pro konkrétní počítač. Vytvoření správných výstrah:
- Zahrňte klauzuli do seskupení podle počítače (například **podle intervalu počítače 1 minuta).**
- Zvolte upozornění na základě měření metriky.

## <a name="log-events-integration"></a>Integrace událostí protokolu

Mapa služeb se integruje s hledáním protokolu a zobrazí počet všech dostupných událostí protokolu pro vybraný server během vybraného časového rozsahu. Kliknutím na libovolný řádek v seznamu počty událostí přejděte na hledání protokolu a zobrazíte jednotlivé události protokolu.

![Podokno Události protokolu počítače](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrace služby Service Desk

Integrace mapy služeb s konektorem správy služeb IT je automatická, když jsou obě řešení povolena a nakonfigurována v pracovním prostoru Log Analytics. Integrace do mapy služeb je označena jako "Service Desk". Další informace naleznete v [tématu Centrální správa pracovních položek ITSM pomocí konektoru pro správu služeb IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

Podokno **Machine Service Desk** obsahuje seznam všech událostí správy služeb IT pro vybraný server ve vybraném časovém rozsahu. Server zobrazí ikonu, pokud existují aktuální položky a podokno Machine Service Desk je uvádí.

![Podokno Obsluha počítače](media/service-map/service-desk.png)

Chcete-li položku otevřít v připojeném řešení ITSM, klepněte na tlačítko **Zobrazit pracovní položku**.

Chcete-li zobrazit podrobnosti o položce ve vyhledávání protokolů, klepněte na tlačítko **Zobrazit ve vyhledávání protokolů**.
Metriky připojení se zapisují do dvou nových tabulek v Log Analytics 

## <a name="change-tracking-integration"></a>Integrace sledování změn

Integrace mapy služeb se sledováním změn je automatická, když jsou obě řešení povolena a nakonfigurována v pracovním prostoru Log Analytics.

Podokno **Sledování změn počítače** obsahuje seznam všech změn s nejnovějšími prvními, spolu s odkazem na přechod k podrobnostem hledání protokolů.

![Podokno Sledování změn stroje](media/service-map/change-tracking.png)

Následující obrázek je podrobné zobrazení události ConfigurationChange, která se může zobrazit po výběru **zobrazit v Log Analytics**.

![Událost ConfigurationChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integrace výkonu

Podokno **Výkon počítače** zobrazuje standardní metriky výkonu pro vybraný server. Metriky zahrnují využití procesoru, využití paměti, odeslané a přijaté síťové bajty a seznam nejvyšších procesů podle odeslaných a přijatých síťových bajtů.

![Podokno Výkon počítače](media/service-map/machine-performance.png)

Chcete-li zobrazit údaje o výkonu, může být nutné [povolit příslušné čítače výkonu Analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Čítače, které budete chtít povolit:

Windows:
- Procesor(*)\\% času procesoru
- Paměť\\% potvrzených bajtů, které jsou používány
- Odeslané bajty\\síťového adaptéru(*)/s
- Přijatý síťový adaptér(*)\\přijaté bajty/s

Linux:
- Procesor(*)\\% času procesoru
- Paměť(*)\\% využité paměti
- Odeslané bajty\\síťového adaptéru(*)/s
- Přijatý síťový adaptér(*)\\přijaté bajty/s

Chcete-li získat data o výkonu sítě, musíte také povolit řešení Wire Data 2.0 v pracovním prostoru.
 
## <a name="security-integration"></a>Integrace zabezpečení

Integrace mapy služeb se zabezpečením a auditem je automatická, pokud jsou obě řešení povolena a nakonfigurována v pracovním prostoru Analýzy protokolů.

Podokno **Zabezpečení počítače** zobrazuje data z řešení zabezpečení a auditování vybraného serveru. Podokno obsahuje souhrn všech nevyřešených problémů se zabezpečením serveru během vybraného časového rozsahu. Kliknutím na některý z problémů se zabezpečením přejdete do hledání protokolu a zobrazíte podrobnosti o nich.

![Podokno Zabezpečení počítače](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integrace aktualizací

Integrace mapy služeb se správou aktualizací je automatická, pokud jsou obě řešení povolena a nakonfigurována v pracovním prostoru Log Analytics.

Podokno **Aktualizace počítače** zobrazuje data z řešení správy aktualizací pro vybraný server. Podokno obsahuje souhrn všech chybějících aktualizací serveru během vybraného časového rozsahu.

![Podokno Sledování změn stroje](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Služba Mapa počítače a zpracování údajů o inventáři je k dispozici pro [vyhledávání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics. Tato data můžete použít na scénáře, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

Jeden záznam je generován za hodinu pro každý jedinečný počítač a proces, kromě záznamů, které jsou generovány při spuštění procesu nebo počítače nebo je vestavěné do mapy služeb. Tyto záznamy mají vlastnosti v následujících tabulkách. Pole a hodnoty v ServiceMapComputer_CL událostí se mapují na pole prostředku počítače v rozhraní API ServiceMap Azure Resource Manager. Pole a hodnoty v ServiceMapProcess_CL událostí mapovat na pole prostředku procesu v rozhraní API ServiceMap Azure Resource Manager. Pole ResourceName_s odpovídá poli názvu v odpovídajícím zdroji Správce zdrojů. 

>[!NOTE]
>S růstem funkcí mapy služeb se tato pole mohou měnit.

Existují interně generované vlastnosti, které můžete použít k identifikaci jedinečných procesů a počítačů:

- Počítač: Pomocí *resourceid* nebo *ResourceName_s* jednoznačně identifikovat počítač v pracovním prostoru Analýzy protokolů.
- Proces: Pomocí *resourceid* jednoznačně identifikovat proces v rámci pracovního prostoru Log Analytics. *ResourceName_s* je jedinečný v kontextu počítače, na kterém je proces spuštěn (MachineResourceName_s) 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozsahu může existovat více záznamů, mohou dotazy vrátit více než jeden záznam pro stejný počítač nebo proces. Chcete-li zahrnout pouze nejnovější záznam, přidejte "| dedup ResourceId" do dotazu.

### <a name="connections"></a>Připojení

Metriky připojení se zapisují do nové tabulky v Log Analytics – Připojení v y VMConnection. Tato tabulka obsahuje informace o připojení pro počítač (příchozí a odchozí). Metriky připojení jsou také vystaveny s api, které poskytují prostředky k získání konkrétní metriky během časového okna.  Připojení TCP vyplývající z přijetí na naslouchání soketu jsou příchozí, zatímco připojení k dané IP a portu jsou odchozí. Směr připojení je reprezentován Direction vlastnost, která může být nastavena na **příchozí** nebo **odchozí**. 

Záznamy v těchto tabulkách jsou generovány z dat vykazovaných agentem závislostí. Každý záznam představuje pozorování v časovém intervalu jedné minuty. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace k identifikaci příslušné entity, tedy připojení nebo portu, stejně jako metriky přidružené k této entitě. V současné době je hlášena pouze síťová aktivita, ke které dochází pomocí protokolu TCP přes IPv4.

Chcete-li spravovat náklady a složitost, záznamy připojení nepředstavují jednotlivá fyzická síťová připojení. Více fyzických síťových připojení je seskupeno do logického připojení, které se pak projeví v příslušné tabulce.  To znamená, že záznamy v tabulce *VMConnection* představují logické seskupení a nikoli jednotlivá fyzická připojení, která jsou sledována. Fyzické síťové připojení sdílející stejnou hodnotu pro následující atributy během daného intervalu jedné minuty, jsou agregovány do jednoho logického záznamu v *programu VMConnection*. 

| Vlastnost | Popis |
|:--|:--|
| `Direction` |Směr připojení, hodnota je *příchozí* nebo *odchozí* |
| `Machine` |Hlavní název fqdn počítače |
| `Process` |Identita procesu nebo skupin procesů, zahájení/přijetí připojení |
| `SourceIp` |IP adresa zdroje |
| `DestinationIp` |IP adresa cíle |
| `DestinationPort` |Číslo portu cíle |
| `Protocol` |Protokol používaný pro připojení.  Hodnoty je *tcp*. |

Chcete-li zohlednit dopad seskupení, informace o počtu seskupených fyzických připojení jsou uvedeny v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
| `LinksEstablished` |Počet fyzických síťových připojení, která byla navázána během časového období hlášení |
| `LinksTerminated` |Počet fyzických síťových připojení, která byla ukončena během časového období hlášení |
| `LinksFailed` |Počet fyzických síťových připojení, která selhala během časového období vykazování. Tyto informace jsou v současné době k dispozici pouze pro odchozí připojení. |
| `LinksLive` |Počet fyzických síťových připojení, která byla otevřena na konci časového období hlášení|

#### <a name="metrics"></a>Metriky

Kromě metrik počtu připojení jsou informace o objemu dat odeslaných a přijatých na daném logickém připojení nebo síťovém portu zahrnuty také v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
| `BytesSent` |Celkový počet bajtů odeslaných během časového období vykazování |
| `BytesReceived` |Celkový počet bajtů, které byly přijaty během časového období vykazování |
| `Responses` |Počet odpovědí pozorovaných během časového období hlášení. 
| `ResponseTimeMax` |Největší doba odezvy (milisekundy) pozorovaná během časového období hlášení.  Pokud žádná hodnota, vlastnost je prázdná.|
| `ResponseTimeMin` |Nejmenší doba odezvy (milisekundy) pozorovaná během časového období hlášení.  Pokud žádná hodnota, vlastnost je prázdná.|
| `ResponseTimeSum` |Součet všech časů odezvy (milisekund) pozorovaných během časového období hlášení.  Pokud žádná hodnota není, vlastnost je prázdná.|

Třetí typ dat, které jsou hlášeny je doba odezvy – jak dlouho volající tráví čekání na požadavek odeslaný přes připojení, které mají být zpracovány a odpověděl vzdálenékoncového bodu. Hlášená doba odezvy je odhad skutečné doby odezvy základního aplikačního protokolu. Vypočítává se pomocí heuristiky založené na pozorování toku dat mezi zdrojovým a cílovým koncem fyzického síťového připojení. Koncepčně je rozdíl mezi čas poslední bajt požadavku opustí odesílatele a čas, kdy poslední bajt odpovědi dorazí zpět k němu. Tato dvě časová razítka se používají k vymezení událostí požadavku a odpovědi na daném fyzickém připojení. Rozdíl mezi nimi představuje dobu odezvy jednoho požadavku. 

V této první verzi této funkce je náš algoritmus aproximací, která může pracovat s různým stupněm úspěchu v závislosti na skutečném aplikačním protokolu používaném pro dané síťové připojení. Například aktuální přístup funguje dobře pro protokoly založené na odezvě na požadavcích, jako je HTTP(S), ale nefunguje s jednosměrnými protokoly nebo protokoly založené na frontě zpráv.

Zde jsou některé důležité body, které je třeba zvážit:

1. Pokud proces přijímá připojení na stejné adrese IP, ale přes více síťových rozhraní, bude hlášen samostatný záznam pro každé rozhraní. 
2. Záznamy se zástupným znakem IP nebudou obsahovat žádnou aktivitu. Jsou zahrnuty představují skutečnost, že port v počítači je otevřen pro příchozí přenosy.
3. Chcete-li snížit podrobnost a objem dat, záznamy se zástupným znakem IP budou vynechány, pokud existuje odpovídající záznam (pro stejný proces, port a protokol) s konkrétní adresou IP. Pokud je vynechán zástupný záznam IP, iswildcardbind záznam vlastnost s konkrétní IP adresu, bude nastavena na "True" označuje, že port je vystaven a každé rozhraní stroje pro vykazování.
4. Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na "False".

#### <a name="naming-and-classification"></a>Pojmenování a klasifikace

Pro větší pohodlí je ip adresa vzdáleného konce připojení zahrnuta ve vlastnosti RemoteIp. Pro příchozí připojení RemoteIp je stejný jako SourceIp, zatímco pro odchozí připojení je stejný jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje kanonické názvy DNS hlášené počítačem pro RemoteIp. RemoteDnsQuestions a RemoteClassification vlastnosti jsou vyhrazeny pro budoucí použití. 

#### <a name="geolocation"></a>Geografická poloha

*Připojení VMConnection* také obsahuje informace o geografické poloze pro vzdálený konec každého záznamu připojení v následujících vlastnostech záznamu: 

| Vlastnost | Popis |
|:--|:--|
| `RemoteCountry` |Název země nebo oblasti hostující RemoteIp.  Například *Spojené státy americké* |
| `RemoteLatitude` |Geolokační šířka.  Například *47,68* |
| `RemoteLongitude` |Geolokační longituda.  Například *-122,12* |

#### <a name="malicious-ip"></a>Škodlivá adresa IP

Každá vlastnost RemoteIp v tabulce *VMConnection* je zkontrolována podle sady IP adres se známou škodlivou aktivitou. Pokud RemoteIp je identifikován jako škodlivý budou vyplněny následující vlastnosti (jsou prázdné, pokud IP není považován za škodlivý) v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
| `MaliciousIp` |Adresa RemoteIp |
| `IndicatorThreadType` |Indikátor ohrožení zjištěn je jedním z následujících hodnot, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
| `Description` |Popis pozorované hrozby. |
| `TLPLevel` |Stupeň semaforu (TLP) Úroveň je jedna z definovaných hodnot, *bílá*, *zelená*, *žlutá*, *červená*. |
| `Confidence` |Hodnoty jsou *0 – 100*. |
| `Severity` |Hodnoty jsou *0 – 5*, kde *5* je nejzávažnější a *0* není závažné vůbec. Výchozí hodnota je *3*.  |
| `FirstReportedDateTime` |Při prvním oznamuji indikátor. |
| `LastReportedDateTime` |Při posledním pohledu byl indikátor viděn interflow. |
| `IsActive` |Označuje, že indikátory jsou deaktivovány hodnotou *True* nebo *False.* |
| `ReportReferenceLink` |Odkazy na zprávy týkající se daného pozorovatelného. |
| `AdditionalInformation` |Obsahuje další informace o zjištěné hrozbě. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL záznamů

Záznamy s typem *ServiceMapComputer_CL* mají data inventáře pro servery s agenty mapy služeb. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Jedinečný identifikátor počítače v pracovním prostoru |
| `ResourceName_s` | Jedinečný identifikátor počítače v pracovním prostoru |
| `ComputerName_s` | Hlavní název fqdn počítače |
| `Ipv4Addresses_s` | Seznam adres IPv4 serveru |
| `Ipv6Addresses_s` | Seznam adres IPv6 serveru |
| `DnsNames_s` | Pole názvů DNS |
| `OperatingSystemFamily_s` | Windows nebo Linux |
| `OperatingSystemFullName_s` | Úplný název operačního systému  |
| `Bitness_s` | Bitnost stroje (32bitová nebo 64bitová)  |
| `PhysicalMemory_d` | Fyzická paměť v MB |
| `Cpus_d` | Počet procesorů |
| `CpuSpeed_d` | Rychlost procesoru v MHz|
| `VirtualizationState_s` | *neznámý*, *fyzický*, *virtuální*, *hypervisor* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, a tak dále |
| `VirtualMachineNativeMachineId_g` | ID virtuálního měn přiřazené jeho hypervisorem |
| `VirtualMachineName_s` | Název virtuálního virtuálního soudu |
| `BootTime_t` | Doba spuštění |

### <a name="servicemapprocess_cl-type-records"></a>záznamy typu ServiceMapProcess_CL

Záznamy s typem *ServiceMapProcess_CL* mají data inventáře pro procesy připojené k protokolu TCP na serverech s agenty mapy služeb. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Jedinečný identifikátor procesu v pracovním prostoru |
| `ResourceName_s` | Jedinečný identifikátor pro proces v počítači, na kterém je spuštěn|
| `MachineResourceName_s` | Název prostředku počítače |
| `ExecutableName_s` | Název spustitelného procesu |
| `StartTime_t` | Čas zahájení fondu procesů |
| `FirstPid_d` | První PID ve fondu procesů |
| `Description_s` | Popis procesu |
| `CompanyName_s` | Název společnosti |
| `InternalName_s` | Interní název |
| `ProductName_s` | Název produktu |
| `ProductVersion_s` | Verze produktu |
| `FileVersion_s` | Verze souboru |
| `CommandLine_s` | Příkazový řádek |
| `ExecutablePath _s` | Cesta ke spustitelnému souboru |
| `WorkingDirectory_s` | Pracovní adresář |
| `UserName` | Účet, pod kterým proces provádí |
| `UserDomain` | Doména, pod kterou je proces prováděn |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

### <a name="list-all-known-machines"></a>Seznam všech známých strojů

ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Uveďte kapacitu fyzické paměti všech spravovaných počítačů.

ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ResourceId | PhysicalMemory_d projektu, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Seznam názvů počítačů, DNS, IP a operačního systému.

ServiceMapComputer_CL | shrnout arg_max(TimeGenerated, *) podle ResourceId | projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najít všechny procesy s "sql" v příkazovém řádku

ServiceMapProcess_CL | kde CommandLine_s contains_cs "sql" | shrnout arg_max(TimeGenerated, *) podle ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Vyhledání počítače (nejnovější záznam) podle názvu prostředku

hledat v (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | shrnout arg_max(TimeGenerated, *) podle ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Vyhledání počítače (nejnovější záznam) podle IP adresy

vyhledávání v (ServiceMapComputer_CL) "10.229.243.232" | shrnout arg_max(TimeGenerated, *) podle ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Vypsat všechny známé procesy na určeném počítači

ServiceMapProcess_CL | kde MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | shrnout arg_max(TimeGenerated, *) podle ResourceId

### <a name="list-all-computers-running-sql"></a>Seznam všech počítačů se systémem SQL

ServiceMapComputer_CL | kde ResourceName_s v ((vyhledávání v\*\*(ServiceMapProcess_CL) " sql " | odlišné MachineResourceName_s)) | odlišné ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Seznam všech jedinečných verzí produktu curl v mém datovém centru

ServiceMapProcess_CL | kde ExecutableName_s == "curl" | odlišné ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvoření skupiny počítačů všech počítačů se systémem CentOS

ServiceMapComputer_CL | kde contains_cs OperatingSystemFullName_s "CentOS" | odlišné ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sumarizovat odchozí připojení ze skupiny počítačů

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

Všechna data serveru, procesu a závislostí v mapě služeb jsou k dispozici prostřednictvím [rozhraní REST API mapy služeb](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje údaje o využití a výkonu prostřednictvím vašeho používání služby Service Map. Společnost Microsoft používá tato data k poskytování a zlepšování kvality, zabezpečení a integrity služby Service Map. Chcete-li poskytnout přesné a efektivní možnosti řešení potíží, data obsahují informace o konfiguraci softwaru, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní informace.

Další informace o shromažďování a používání dat naleznete v Prohlášení o [zásadách ochrany osobních údajů služeb Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Další kroky

Další informace o [hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics k načtení dat shromážděných service map.

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte nějaké problémy s instalací nebo spuštěním mapy služeb, může vám tato část pomoci. Pokud stále nemůžete problém vyřešit, obraťte se na podporu společnosti Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problémy s instalací agenta závislostí

#### <a name="installer-prompts-for-a-reboot"></a>Instalační program vyzve k restartování počítače
Agent závislostí *obecně* nevyžaduje restartování při instalaci nebo odebrání. V některých výjimečných případech však systém Windows Server vyžaduje restartování, aby mohl pokračovat v instalaci. K tomu dochází, když závislost, obvykle knihovna Microsoft Visual C++ Redistributable vyžaduje restartování z důvodu uzamčeného souboru.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Zpráva "Nelze nainstalovat agenta závislostí: Knihovny runtime sady Visual Studio se nepodařilo nainstalovat (kód = [code_number])" se zobrazí

Agent závislostí společnosti Microsoft je postaven na runtime knihovnách sady Microsoft Visual Studio. Pokud se při instalaci knihoven zobrazí problém, zobrazí se zpráva. 

Instalátory knihovny runtime vytvářejí protokoly ve složce %LOCALAPPDATA%\temp. Soubor je `dd_vcredist_arch_yyyymmddhhmmss.log`, kde `x86` `amd64` *arch* je nebo a *yyyymmddhhmmss* je datum a čas (24 hodin), kdy byl vytvořen protokol. Protokol obsahuje podrobnosti o problému, který blokuje instalaci.

Může být užitečné nejprve nainstalovat [nejnovější knihovny runtime.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

V následující tabulce jsou uvedena kódová čísla a navrhovaná řešení.

| kód | Popis | Řešení |
|:--|:--|:--|
| 0x17 | Instalační služba knihovny vyžaduje aktualizaci systému Windows, která nebyla nainstalována. | Podívejte se do posledního protokolu instalačního programu knihovny.<br><br>Pokud odkaz `Windows8.1-KB2999226-x64.msu` na následuje řádek `Error 0x80240017: Failed to execute MSU package,` nemáte předpoklady pro instalaci KB2999226. Postupujte podle pokynů v části požadavky v [univerzálním prostředí C Runtime v](https://support.microsoft.com/kb/2999226) článku windows. Chcete-li nainstalovat požadavky, může být nutné spustit službu Windows Update a restartovat ji vícekrát.<br><br>Spusťte znovu instalační program agenta závislostí společnosti Microsoft. |

### <a name="post-installation-issues"></a>Problémy po instalaci

#### <a name="server-doesnt-appear-in-service-map"></a>Server se nezobrazuje v mapě služeb

Pokud byla instalace agenta závislostí úspěšná, ale počítač v řešení Mapa služeb nevidíte:
* Je agent závislostí úspěšně nainstalován? Můžete ověřit kontrolou, zda je služba nainstalována a spuštěna.<br><br>
**Windows**: Vyhledejte službu s názvem **Microsoft Dependency Agent**.
**Linux**: Vyhledejte spuštěný proces **Microsoft-Dependency-Agent**.

* Jste na [úrovni free Log Analytics?](https://azure.microsoft.com/pricing/details/monitor/) Plán Free umožňuje až pět unikátních strojů service map. Žádné další počítače se v mapě služeb nezobrazí, a to ani v případě, že předchozí pětka již neodesílá data.

* Odesílá váš server data protokolu a perf do protokolů monitorování Azure? Přejděte na Azure Monitor\Protokoly a spusťte pro svůj počítač následující dotaz: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Dostali jste různé události ve výsledcích? Jsou data nedávná? Pokud ano, váš agent Log Analytics pracuje správně a komunikuje s pracovním prostorem. Pokud ne, zkontrolujte agenta na vašem počítači: [Agent Log Analytics pro řešení potíží systému Windows](../platform/agent-windows-troubleshoot.md) nebo Agent Log [Analytics pro řešení potíží s Linuxem](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Server se zobrazí v mapě služeb, ale nemá žádné procesy

Pokud se váš počítač zobrazí v mapě služeb, ale nemá žádná data procesu nebo připojení, znamená to, že agent závislostí je nainstalován a spuštěn, ale ovladač jádra se nenačetl. 

Zkontrolujte `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) `/var/opt/microsoft/dependency-agent/log/service.log file` nebo (Linux). Poslední řádky souboru by měly označovat, proč se jádro nenačetlo. Například jádro nemusí být v Linuxu podporováno, pokud jste aktualizovali jádro.

## <a name="feedback"></a>Váš názor

Máte pro nás nějakou zpětnou vazbu ohledně mapy služeb nebo této dokumentace?  Navštivte naši [stránku User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhovat funkce nebo hlasovat o stávajících návrzích.
