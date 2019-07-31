---
title: Používání řešení Service Map v Azure | Microsoft Docs
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobnosti o nasazení řešení Service Map ve vašem prostředí a jejich použití v různých scénářích.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: magoedte
ms.openlocfilehash: 1f06345995e30f4d7f165230f4292c560c89e2e8
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68489767"
---
# <a name="using-service-map-solution-in-azure"></a>Použití řešení Service Map v Azure
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Služba Service Map poskytuje zobrazení vašich serverů tak, jak si je představujete – jako vzájemně propojené systémy, které zajišťují důležité služby. Service Map zobrazuje propojení mezi servery, procesy, latenci příchozích a odchozích připojení a porty napříč libovolnou architekturou propojenou protokolem TCP. Nevyžaduje se přitom žádná konfigurace kromě instalace agenta.

Tento článek popisuje podrobnosti o připojování a používání Service Map. Informace o konfiguraci požadavků pro toto řešení najdete v tématu [Povolení přehledu Azure monitor pro virtuální počítače](vminsights-enable-overview.md#prerequisites). Pro Shrnutí budete potřebovat následující:

* Log Analytics pracovní prostor pro povolení tohoto řešení.

* Agent Log Analytics nainstalovaný na počítači s Windows nebo na serveru Linux nakonfigurovaný tak, aby nahlásil stejný pracovní prostor, ve kterém jste řešení povolili.

* Agent závislostí nainstalovaný na počítači s Windows nebo na serveru se systémem Linux.

>[!NOTE]
>Pokud jste už nasadili Service Map, můžete teď také zobrazit vaše mapy v Azure Monitor pro virtuální počítače, které obsahují další funkce pro monitorování stavu a výkonu virtuálních počítačů. Další informace najdete v tématu [přehled Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md). Další informace o rozdílech mezi funkcí Service Map řešení a mapa Azure Monitor pro virtuální počítače najdete v následujících nejčastějších [dotazech](vminsights-faq.md#how-is-azure-monitor-for-vms-map-feature-different-from-service-map).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Povolit Service Map

1. Povolte řešení Service Map z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [Přidání řešení monitorování z galerie řešení](solutions.md).
1. [Nainstalujte agenta závislostí do systému Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) nebo [nainstalujte agenta závislostí na Linux](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) do každého počítače, kde chcete získat data. Závislý agent dokáže monitorovat připojení k bezprostředním sousedům, takže nepotřebujete mít agenta na každém počítači.

Přístup k Service Map v Azure Portal z pracovního prostoru Log Analytics a v levém podokně vyberte **řešení** možností.<br><br> ![V pracovním prostoru](./media/service-map/select-solution-from-workspace.png)vyberte možnost řešení.<br> V seznamu řešení vyberte **ServiceMap (pracovní prostor)** a na stránce přehled řešení Service map klikněte na dlaždici Service map souhrn.<br><br> ![Dlaždice](./media/service-map/service-map-summary-tile.png)souhrnu Service map

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Případy použití: Zajistěte, aby vaše IT procesy v závislosti na závislosti

### <a name="discovery"></a>Zjišťování

Service Map automaticky vytvoří společnou referenční mapu závislostí napříč vašimi servery, procesy a službami třetích stran. Zjišťuje a mapuje všechny závislosti TCP, identifikaci neohlášených připojení, vzdálených systémů třetích stran, na kterých jsou závislé, a závislostí na tradičních tmavých oblastech vaší sítě, jako je například služba Active Directory. Service Map zjistí neúspěšná síťová připojení, která se snaží provést vaše spravované systémy, a pomůže vám identifikovat potenciální chybnou konfiguraci serveru, výpadku služby a problémy se sítí.

### <a name="incident-management"></a>Správa incidentů

Service Map pomáhá eliminovat přibližnou přibližnou izolaci problémů tím, že ukazuje, jak jsou systémy propojeny a vzájemně ovlivněny. Kromě identifikace neúspěšných připojení pomáhá identifikovat nesprávně nakonfigurované nástroje pro vyrovnávání zatížení, překvapivé nebo nadměrné zatížení v důležitých službách a podvodné klienty, jako jsou vývojářské počítače, které mluví s produkčními systémy. Pomocí integrovaných pracovních postupů s Change Tracking můžete také zjistit, jestli událost změny na back-endovém počítači nebo službě vysvětluje hlavní příčinu incidentu.

### <a name="migration-assurance"></a>Záruka migrace

Pomocí Service Map můžete efektivně plánovat, zrychlit a ověřovat migrace do Azure, což pomáhá zajistit, aby nic nezůstalo a nedocházelo k výpadkům. Můžete zjistit všechny vzájemně závislé systémy, které se musí migrovat společně, vyhodnocovat konfiguraci a kapacitu systému a určit, jestli operační systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace. Po dokončení přesunu můžete zkontrolovat zatížení a identitu klienta a ověřit tak, že se zkušební systémy a zákazníci připojují. Pokud má vaše podsíť definice plánování a brány firewall problémy, neúspěšná připojení v Service Map mapách ukazují na systémy, které vyžadují připojení.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

Pokud používáte Azure Site Recovery a potřebujete nápovědu definující sekvenci obnovení pro prostředí aplikace, Service Map vám může automaticky Ukázat, jak se systémy vzájemně spoléhají, aby se zajistilo, že je váš plán obnovení spolehlivý. Výběrem důležitého serveru nebo skupiny a zobrazením klientů můžete určit, které klientské systémy se mají obnovit po obnovení a zpřístupnění serveru. V opačném případě si prohlédněte kritické závislosti back-endu serverů, které vám pomůžou zjistit, které systémy se mají obnovit, než se obnoví vaše Fokusové systémy.

### <a name="patch-management"></a>Správa oprav

Service Map zdokonaluje používání posouzení aktualizací systému tím, že ukazuje, které jiné týmy a servery na vaší službě závisí, abyste je mohli předem informovat před tím, než zaberete systémy na opravy. Service Map taky vylepšuje správu oprav tím, že vám ukáže, jestli jsou vaše služby k dispozici a správně připojené po opravě a restartu.

## <a name="mapping-overview"></a>Přehled mapování

Agenti Service Map shromažďují informace o všech procesech propojených s protokolem TCP na serveru, kde jsou nainstalovány, a obsahují podrobnosti o příchozích a odchozích připojeních pro jednotlivé procesy.

V seznamu v levém podokně můžete vybrat počítače nebo skupiny, které Service Map agenti, aby vizualizují své závislosti v zadaném časovém rozsahu. Mapování závislostí počítačů se soustředí na konkrétní počítač a zobrazí všechny počítače, které jsou přímými klienty TCP nebo servery tohoto počítače.  Mapy skupin počítačů zobrazují sady serverů a jejich závislosti.

![Přehled Service Map](media/service-map/service-map-overview.png)

Počítače lze v mapě rozbalit a zobrazit spuštěné skupiny procesů a procesy s aktivními síťovými připojeními v průběhu vybraného časového rozsahu. Když je vzdálený počítač s agentem Service Map rozbalený tak, aby zobrazoval podrobnosti procesu, zobrazují se jenom ty procesy, které komunikují s vybraným počítačem. Počet počítačů front-endu bez agentů, které se připojují k počítači Focus, je uvedený na levé straně procesů, ke kterým se připojují. Pokud se fokus stane připojením k back-endovému počítači, který nemá žádného agenta, je back-end Server součástí skupiny portů serveru spolu s dalšími připojeními ke stejnému číslu portu.

Ve výchozím nastavení Service Map Maps zobrazuje posledních 30 minut informací o závislostech. Pomocí ovládacích prvků pro čas v levém horním rohu se můžete dotazovat na mapování historických časových rozsahů po dobu až jedné hodiny, abyste viděli, jak se závislosti prohlédly v minulosti (například během incidentu nebo před změnou). Data Service Map se ukládají po dobu 30 dnů v placených pracovních prostorech a 7 dní v bezplatných pracovních prostorech.

## <a name="status-badges-and-border-coloring"></a>Označení stavu a zvýraznění ohraničení

V dolní části každého serveru na mapě může být seznam stavových zpráv, které oznamují informace o stavu serveru. Označení označují, že pro server existují nějaké relevantní informace z jedné z integrací řešení. Kliknutím na označení se dostanete přímo k podrobnostem o stavu v pravém podokně. V aktuálně dostupných označeních stavu jsou výstrahy, oddělení služeb, změny, zabezpečení a aktualizace.

V závislosti na závažnosti stavových znaků můžou být hranice uzlu počítače barevně červené (kritické), žlutou (varovnou) nebo modrou (informativní). Barva představuje nejzávažnější stav kterékoli ze stavových oznámení. Šedé ohraničení označuje uzel, který nemá žádné indikátory stavu.

![Označení stavového znaku](media/service-map/status-badges.png)

## <a name="process-groups"></a>Skupiny procesů

Skupiny procesů spojují procesy, které jsou přidruženy ke společnému produktu nebo službě, do skupiny procesů.  Když je uzel počítače rozbalený, zobrazí se samostatné procesy společně se skupinami procesů.  Pokud se nezdařila příchozí a odchozí připojení k procesu v rámci skupiny procesů, je připojení zobrazeno jako neúspěšné pro celou skupinu procesů.

## <a name="machine-groups"></a>Skupiny počítačů

Skupiny počítačů vám umožní zobrazit mapy, které se provedly na základě sady serverů, ne jenom jednoho, abyste viděli všechny členy vícevrstvé aplikace nebo serverového clusteru v jedné mapě.

Uživatelé si můžou vybrat, které servery patří do skupiny dohromady, a zvolit název skupiny.  Pak se můžete rozhodnout zobrazit skupinu se všemi jejími procesy a připojeními, nebo je zobrazit jenom pomocí procesů a připojení, která přímo souvisejí s ostatními členy skupiny.

![Skupina počítačů](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Vytvoření skupiny počítačů

Pokud chcete vytvořit skupinu, vyberte počítače nebo počítače, které chcete v seznamu počítače, a klikněte na **Přidat do skupiny**.

![Vytvořit skupinu](media/service-map/machine-groups-create.png)

Zde můžete zvolit **vytvořit nové** a zadat název skupiny.

![Skupina názvů](media/service-map/machine-groups-name.png)

>[!NOTE]
>Skupiny počítačů jsou omezené na 10 serverů.

### <a name="viewing-a-group"></a>Zobrazení skupiny

Po vytvoření některých skupin si je můžete zobrazit tak, že kliknete na kartu skupiny.

![Karta skupiny](media/service-map/machine-groups-tab.png)

Pak vyberte název skupiny, chcete-li zobrazit mapu pro tuto skupinu počítačů.
![Skupina](media/service-map/machine-group.png) počítačů: počítače, které patří do této skupiny, jsou v mapě uvedené bíle.

Rozbalením skupiny se zobrazí seznam počítačů, které tvoří skupinu počítačů.

![Počítače skupiny počítačů](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrovat podle procesů

Zobrazení mapy můžete přepínat mezi zobrazením všech procesů a připojení ve skupině a jenom těch, které se přímo vztahují k této skupině počítačů.  Výchozím zobrazením je zobrazení všech procesů.  Zobrazení můžete změnit kliknutím na ikonu filtru nad mapou.

![Skupina filtru](media/service-map/machine-groups-filter.png)

Když je vybraná možnost **všechny procesy** , bude mapa zahrnovat všechny procesy a připojení na každém z počítačů ve skupině.

![Všechny procesy skupiny počítačů](media/service-map/machine-groups-all.png)

Pokud změníte zobrazení tak, aby zobrazovalo pouze **procesy spojené s skupinou**, bude tato mapa zúžena pouze na procesy a připojení, která jsou přímo připojena k ostatním počítačům ve skupině, což vytvoří zjednodušené zobrazení.

![Filtrované procesy skupiny počítačů](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Přidávání počítačů do skupiny

Pokud chcete přidat počítače do existující skupiny, zaškrtněte políčka vedle počítačů, které chcete, a pak klikněte na **Přidat do skupiny**.  Pak vyberte skupinu, do které chcete počítače přidat.
 
### <a name="removing-machines-from-a-group"></a>Odebírání počítačů ze skupiny

V seznamu skupiny rozbalte název skupiny a seznam počítačů ve skupině počítačů.  Pak klikněte na nabídku se třemi tečkami vedle počítače, který chcete odebrat, a zvolte **Odebrat**.

![Odebrat počítač ze skupiny](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Odebrání nebo přejmenování skupiny

V seznamu skupin klikněte na nabídku se třemi tečkami vedle názvu skupiny.

![Nabídka skupiny počítačů](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony rolí

Některé procesy obsluhují konkrétní role na počítačích: webové servery, aplikační servery, databáze a tak dále. Service Map v oknech procesu a počítače s ikonami rolí, které vám pomůžou identifikovat na první pohled roli a proces, který server hraje.

| Ikona role | Popis |
|:--|:--|
| ![Webový server](media/service-map/role-web-server.png) | Webový server |
| ![Aplikační server](media/service-map/role-application-server.png) | Aplikační server |
| ![Databázový server](media/service-map/role-database.png) | Databázový server |
| ![Server LDAP](media/service-map/role-ldap.png) | Server LDAP |
| ![Server SMB](media/service-map/role-smb.png) | Server SMB |

![Ikony rolí](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Neúspěšná připojení

Neúspěšná připojení se zobrazují v Service Map mapách pro procesy a počítače se přerušovanou červenou čárou, která značí, že se klientský systém nedaří připojit k procesu nebo portu. Neúspěšná připojení se oznamují z libovolného systému pomocí nasazeného agenta Service Map, pokud je tento systém jedním pokusem o připojení, které selhalo. Service Map měří tento proces pomocí soketů TCP, u kterých se nepodaří navázat připojení. K této chybě mohlo dojít z brány firewall, chybné konfigurace klienta nebo serveru nebo vzdálené služby, která není k dispozici.

![Neúspěšná připojení](media/service-map/failed-connections.png)

Porozumění neúspěšným připojením může pomáhat při řešení potíží, ověřování migrace, analýze zabezpečení a celkovém porozumění architektuře. Neúspěšná připojení jsou někdy neškodná, ale často přímo odkazují na problém, například prostředí s podporou převzetí služeb při selhání se náhle stane nedosažitelné, nebo dvě aplikační vrstvy, které nemůžou mluvit po migraci do cloudu.

## <a name="client-groups"></a>Skupiny klientů

Skupiny klientů jsou pole na mapě, která představuje klientské počítače, které nemají agenty závislosti. Jedna skupina klientů představuje klienty pro jednotlivý proces nebo počítač.

![Skupiny klientů](media/service-map/client-groups.png)

Pokud chcete zobrazit IP adresy serverů ve skupině klientů, vyberte skupinu. Obsah skupiny je uveden v podokně **Vlastnosti skupiny klientů** .

![Vlastnosti skupiny klientů](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Skupiny portů serveru

Skupiny portů serveru jsou pole, která představují porty serveru na serverech, které nemají agenty závislosti. Pole obsahuje port serveru a počet serverů s připojením k tomuto portu. Rozbalením okna zobrazíte jednotlivé servery a připojení. Pokud je v poli jenom jeden server, zobrazí se název nebo IP adresa.

![Skupiny portů serveru](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Místní nabídka

Kliknutím na tlačítko se třemi tečkami (...) v pravém horním rohu kteréhokoli serveru se zobrazí místní nabídka pro tento server.

![Neúspěšná připojení](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Načíst mapu serveru

Kliknutím na **Načíst mapu serveru** přejdete k nové mapě s vybraným serverem jako s novým vybraným počítačem.

### <a name="show-self-links"></a>Zobrazit odkazy na sebe

Kliknutím na možnost **Zobrazit odkazy na sebe** překreslí uzel serveru, včetně všech odkazů na sebe, což jsou připojení TCP, která začínají a končí na procesech v rámci serveru. Pokud se zobrazují odkazy na sebe, příkaz nabídky se změní, aby se **skryly odkazy na sebe**, abyste je mohli vypnout.

## <a name="computer-summary"></a>Shrnutí počítače

Podokno **Souhrn počítače** obsahuje přehled operačního systému serveru, počty závislostí a data z jiných řešení. Taková data zahrnují metriky výkonu, lístky oddělení služeb, sledování změn, zabezpečení a aktualizace.

![Podokno Souhrn počítače](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Vlastnosti počítače a procesu

Když přejdete na mapu Service Map, můžete vybrat počítače a procesy a získat další kontext o jejich vlastnostech. Počítače poskytují informace o názvu DNS, adresách IPv4, kapacitě procesoru a paměti, typu virtuálního počítače, operačním systému a verzi, času posledního restartování a ID jejich agentů OMS a Service Map.

![Podokno vlastností počítače](media/service-map/machine-properties.png)

Podrobnosti o procesu můžete shromáždit z metadat operačního systému o spuštěných procesech, včetně názvu procesu, popisu procesu, uživatelského jména a domény (ve Windows), názvu společnosti, názvu produktu, verze produktu, pracovního adresáře, příkazového řádku a procesu. čas spuštění.

![Podokno vlastností procesu](media/service-map/process-properties.png)

Podokno **Souhrn procesu** obsahuje další informace o připojení procesu, včetně jeho vázaných portů, příchozích a odchozích připojení a neúspěšných připojení.

![Podokno souhrnu procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrace výstrah

Service Map se integruje s výstrahami Azure, aby se zobrazily aktivované výstrahy pro vybraný server ve vybraném časovém rozsahu. Pokud jsou k dispozici aktuální výstrahy a v podokně **výstrahy počítače** jsou uvedeny výstrahy, Server zobrazí ikonu.

![Podokno upozornění počítače](media/service-map/machine-alerts.png)

Pokud chcete povolit Service Map zobrazovat relevantní výstrahy, vytvořte pravidlo upozornění, které se aktivuje pro konkrétní počítač. Vytvoření správných výstrah:
- Zahrňte klauzuli pro seskupení podle počítače (například **podle intervalu počítače 1 minuty**).
- Vyberte výstrahu na základě měření metriky.

## <a name="log-events-integration"></a>Integrace událostí protokolu

Service Map se integruje s prohledáváním protokolů a zobrazuje počet všech dostupných událostí protokolu pro vybraný server během vybraného časového rozsahu. Můžete kliknout na libovolný řádek v seznamu počtů událostí a přejít tak na prohledávání protokolu a zobrazit jednotlivé události protokolu.

![Podokno události protokolu počítače](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrace oddělení služeb

Service Map integrace s konektorem pro správu služby IT je automaticky, pokud jsou obě řešení ve vašem pracovním prostoru Log Analytics povolená a nakonfigurovaná. Integrace v Service Map je označená jako oddělení služeb. Další informace najdete v tématu [centrálně spravovat pracovní položky ITSM pomocí konektoru správy služeb IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

Podokno **Služba Machine Service** obsahuje seznam všech událostí správy služeb IT pro vybraný server ve vybraném časovém rozsahu. Server zobrazí ikonu, pokud jsou k dispozici aktuální položky a podokno služba Machine Service.

![Pracovní podokno služby počítače](media/service-map/service-desk.png)

Chcete-li otevřít položku v připojeném řešení ITSM, klikněte na tlačítko **Zobrazit pracovní položku**.

Chcete-li zobrazit podrobnosti o položce v hledání v protokolu, klikněte na možnost **Zobrazit v hledání v protokolu**.
Metriky připojení jsou v Log Analytics zapsány do dvou nových tabulek. 

## <a name="change-tracking-integration"></a>Integrace Change Tracking

Service Map integrace s Change Tracking je automatická, pokud jsou obě řešení povolená a nakonfigurovaná v pracovním prostoru Log Analytics.

V podokně **Change Tracking počítače** jsou uvedeny všechny změny, s nejnovějšími posledními, spolu s odkazem pro přechod k podrobnostem o hledání v protokolu, kde najdete další podrobnosti.

![Počítač Change Tracking – podokno](media/service-map/change-tracking.png)

Následující obrázek je detailní zobrazení události ConfigurationChange, která se může zobrazit po výběru možností **Zobrazit v Log Analytics**.

![Událost ConfigurationChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integrace výkonu

V podokně **výkon počítače** se zobrazí standardní metriky výkonu pro vybraný server. Metriky zahrnují využití CPU, využití paměti, odeslané a přijímané síťové bajty a seznam nejčastějších procesů podle bajtů odesílaných a přijímaných v síti.

![Podokno výkon počítače](media/service-map/machine-performance.png)

Chcete-li zobrazit údaje o výkonu, bude pravděpodobně nutné [Povolit příslušné čítače výkonu Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Čítače, které budete chtít povolit:

Windows:
- Procesor (*)\\% času procesoru
- Paměť\\% používaných potvrzených bajtů
- Síťový adaptér (*)\\odeslané bajty/s
- Síťový adaptér (*)\\přijaté bajty/s

Linux:
- Procesor (*)\\% času procesoru
- Paměť (*)\\% využité paměti
- Síťový adaptér (*)\\odeslané bajty/s
- Síťový adaptér (*)\\přijaté bajty/s

Chcete-li získat údaje o výkonu sítě, musíte také povolit řešení Wire Data 2.0 ve vašem pracovním prostoru.
 
## <a name="security-integration"></a>Integrace zabezpečení

Service Map integrace s Security and Audit je automatická, pokud jsou obě řešení povolená a nakonfigurovaná v pracovním prostoru Log Analytics.

Podokno **zabezpečení počítače** zobrazuje data z řešení Security and Audit pro vybraný server. V podokně se zobrazí souhrn všech nevyřešených problémů zabezpečení serveru během vybraného časového rozsahu. Kliknutím na některý z problémů se zabezpečením přejdete k podrobnostem o prohledávání protokolu.

![Podokno zabezpečení počítače](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integrace aktualizací

Service Map integrace s Update Management je automatická, pokud jsou obě řešení povolená a nakonfigurovaná v pracovním prostoru Log Analytics.

Podokno **aktualizace počítače** zobrazuje data z řešení Update Management pro vybraný server. V podokně jsou uvedeny souhrnné informace o chybějících aktualizacích serveru během vybraného časového rozsahu.

![Počítač Change Tracking – podokno](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Data inventáře Service Map počítačů a procesů jsou k dispozici pro [vyhledávání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

Jeden záznam je vygenerován za hodinu pro každý jedinečný počítač a proces, kromě záznamů, které jsou generovány při spuštění nebo zprovoznění procesu nebo počítače na Service Map. Tyto záznamy obsahují vlastnosti v následujících tabulkách. Pole a hodnoty v událostech ServiceMapComputer_CL se mapují na pole prostředku počítače v rozhraní API pro ServiceMap Azure Resource Manager. Pole a hodnoty v událostech ServiceMapProcess_CL se mapují na pole prostředku procesu v rozhraní API pro ServiceMap Azure Resource Manager. Pole ResourceName_s se shoduje s polem název v odpovídajícím prostředku Správce prostředků. 

>[!NOTE]
>Jak Service Mapch funkcí roste, tato pole se mohou změnit.

K dispozici jsou interně generované vlastnosti, které můžete použít k identifikaci jedinečných procesů a počítačů:

- Počítač Pomocí *ResourceID* nebo *ResourceName_s* můžete jedinečně identifikovat počítač v rámci Log Analytics pracovního prostoru.
- Proces: Použijte *ResourceID* k jednoznačné identifikaci procesu v rámci Log Analytics pracovního prostoru. *ResourceName_s* je jedinečný v rámci kontextu počítače, na kterém je spuštěný proces (MachineResourceName_s). 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozsahu může existovat více záznamů, můžou dotazy vracet více než jeden záznam pro stejný počítač nebo proces. Pokud chcete zahrnout jenom poslední záznam, přidejte | odstranění duplicitních dat ResourceId do dotazu.

### <a name="connections"></a>Připojení

Metriky připojení se zapisují do nové tabulky v Log Analytics-VMConnection. Tato tabulka poskytuje informace o připojeních pro určitý počítač (příchozí a odchozí). Metriky připojení jsou také vystaveny rozhraním API, která poskytují prostředky pro získání konkrétní metriky během časového intervalu.  Připojení TCP vyplývající z "*přijetí*-změn na naslouchajícím soketu jsou příchozí, zatímco ta vytvořená *připojením*k dané IP adrese a portu jsou odchozí. Směr připojení je reprezentován vlastností Direction, která může být nastavena na hodnotu **příchozí** nebo **odchozí**. 

Záznamy v těchto tabulkách jsou generovány z dat hlášených agentem závislosti. Každý záznam představuje pozorování během jednoho minutového časového intervalu. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace, které identifikují příslušnou entitu, připojení nebo port a také metriky přidružené k dané entitě. V současné době je hlášena pouze síťová aktivita, která se používá pomocí TCP přes IPv4.

Pro správu nákladů a složitost nepředstavuje záznam o připojení jednotlivá fyzická síťová připojení. Několik fyzických síťových připojení se seskupí do logického připojení, které se pak odrazí v příslušné tabulce.  To znamená, že záznamy v tabulce *VMConnection* představují logické seskupení, nikoli jednotlivá fyzická připojení, která jsou pozorována. Připojení fyzické sítě sdílející stejnou hodnotu pro následující atributy během daného intervalu minut, jsou agregovány do jednoho logického záznamu v *VMConnection*. 

| Vlastnost | Popis |
|:--|:--|
| `Direction` |Směr připojení, hodnota je *příchozí* nebo *odchozí* |
| `Machine` |Plně kvalifikovaný název domény počítače |
| `Process` |Identita procesu nebo skupin procesů, zahájení/přijetí připojení |
| `SourceIp` |IP adresa zdroje |
| `DestinationIp` |IP adresa cíle |
| `DestinationPort` |Číslo portu cílového umístění |
| `Protocol` |Protokol použitý pro připojení  Hodnoty jsou *TCP*. |

Informace o počtu skupinových fyzických připojení, které se mají přihlédnout k dopadu seskupení, najdete v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
| `LinksEstablished` |Počet fyzických síťových připojení, která byla vytvořena v časovém intervalu generování sestav |
| `LinksTerminated` |Počet fyzických síťových připojení, které byly ukončeny během časového intervalu generování sestav |
| `LinksFailed` |Počet fyzických síťových připojení, u kterých došlo k chybě v časovém intervalu generování sestav. Tyto informace jsou aktuálně k dispozici pouze pro odchozí připojení. |
| `LinksLive` |Počet fyzických síťových připojení otevřených na konci časového intervalu generování sestav|

#### <a name="metrics"></a>Metriky

Kromě metriky počtu připojení jsou do následujících vlastností záznamu zahrnuty také informace o objemu dat odesílaných a přijatých na daném logickém připojení nebo síťovém portu:

| Vlastnost | Popis |
|:--|:--|
| `BytesSent` |Celkový počet bajtů, které byly odeslány během časového intervalu generování sestav |
| `BytesReceived` |Celkový počet bajtů přijatých během časového intervalu generování sestav |
| `Responses` |Počet odpovědí zaznamenaných v časovém intervalu generování sestav. 
| `ResponseTimeMax` |Největší doba odezvy (v milisekundách) zjištěná během časového intervalu generování sestav.  Pokud není žádná hodnota, vlastnost je prázdná.|
| `ResponseTimeMin` |Nejmenší doba odezvy (v milisekundách) zjištěná během časového intervalu generování sestav.  Pokud není žádná hodnota, vlastnost je prázdná.|
| `ResponseTimeSum` |Součet všech dob odezvy (milisekund) zjištěných během časového intervalu generování sestav.  Pokud není žádná hodnota, vlastnost bude prázdná.|

Třetí typ vykázaných dat je doba odezvy – jak dlouho bude volající čekat na zpracování požadavku odeslaného přes připojení a na něj reagovat vzdáleným koncovým bodem. Hlášená doba odezvy je odhadem skutečné doby odezvy základního aplikačního protokolu. Počítá se pomocí heuristiky na základě pozorování toku dat mezi zdrojovým a cílovým koncem fyzického síťového připojení. V koncepčním případě je to rozdíl mezi časem, kdy poslední bajt žádosti opustí odesílatele, a čas, kdy se do něj vrátí poslední bajt odpovědi. Tato dvě časová razítka slouží k vymezení událostí požadavků a odpovědí v daném fyzickém připojení. Rozdíl mezi nimi představuje dobu odezvy jednoho požadavku. 

V této první vydané verzi této funkce je náš algoritmus aproximace, která může fungovat s proměnlivým stupněm úspěšnosti v závislosti na skutečném aplikačním protokolu používaném pro dané síťové připojení. Aktuální přístup například funguje v závislosti na protokolech založených na odpovědích, jako je HTTP (S), ale nepracuje s jednosměrným protokolem nebo protokoly založenými na frontě zpráv.

Tady jsou některé důležité body, které je potřeba vzít v úvahu:

1. Pokud proces přijme připojení na stejné IP adrese, ale přes více síťových rozhraní, nahlásí se samostatný záznam pro každé rozhraní. 
2. Záznamy s IP adresou se zástupnými znaky nebudou obsahovat žádné aktivity. Jsou zahrnuty k vyjádření faktu, že je port v počítači otevřený pro příchozí provoz.
3. Aby se snížila úroveň podrobností a objem dat, budou záznamy se zástupnými adresami IP vynecháné, pokud existuje shodný záznam (pro stejný proces, port a protokol) s konkrétní IP adresou. Je-li vynechán záznam IP adresy se zástupnými znaky, bude vlastnost IsWildcardBind záznamu s konkrétní IP adresou nastavena na hodnotu "true", která označuje, že port je vystaven pro každé rozhraní počítače pro vytváření sestav.
4. Porty, které jsou vázány pouze na konkrétní rozhraní, mají IsWildcardBind nastavenou na hodnotu false (NEPRAVDA).

#### <a name="naming-and-classification"></a>Pojmenovávání a klasifikace

Pro usnadnění práce se do vlastnosti RemoteIp zahrne IP adresa vzdáleného konce připojení. U příchozích připojení je RemoteIp stejná jako SourceIp, zatímco u odchozích připojení je stejná jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje kanonické názvy DNS hlášené počítačem pro RemoteIp. Vlastnosti RemoteDnsQuestions a RemoteClassification jsou vyhrazené pro budoucí použití. 

#### <a name="geolocation"></a>Zeměpisná poloha

*VMConnection* také obsahuje informace o geografickém umístění pro vzdálené konce každého záznamu připojení v následujících vlastnostech záznamu: 

| Vlastnost | Popis |
|:--|:--|
| `RemoteCountry` |Název země nebo oblasti hostující RemoteIp.  Například *USA* |
| `RemoteLatitude` |Zeměpisná šířka zeměpisné polohy.  Například *47,68* |
| `RemoteLongitude` |Zeměpisná délka zeměpisné polohy.  Například *-122,12* |

#### <a name="malicious-ip"></a>Škodlivá IP adresa

Každá vlastnost RemoteIp v tabulce *VMConnection* je kontrolována na základě sady IP adres se známou škodlivou aktivitou. Pokud je RemoteIp identifikovaný jako škodlivý, budou se naplnit následující vlastnosti (Pokud se IP adresa nepovažuje za škodlivou) v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
| `MaliciousIp` |Adresa RemoteIp |
| `IndicatorThreadType` |Zjištěného indikátoru hrozby je jedna z následujících hodnot: *botnetu*, *C2*, *CryptoMining*, *adres darknetu*, *DDos*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *PUA*,  *Seznamu ke zhlédnutí*.   |
| `Description` |Popis pozorované hrozby. |
| `TLPLevel` |Úroveň TLP (provoz Light Protocol) je jedna z definovaných hodnot, *bílá*, *zelená*, *oranžová*a *červená*. |
| `Confidence` |Hodnoty jsou *0 – 100*. |
| `Severity` |Hodnoty jsou *0 – 5*, přičemž *5* je nejzávažnější a *0* není u sebe závažná. Výchozí hodnota je *3*.  |
| `FirstReportedDateTime` |První, kdy zprostředkovatel nahlásil ukazatel. |
| `LastReportedDateTime` |Čas posledního výskytu indikátoru v rámci přetečení. |
| `IsActive` |Označuje, že indikátory jsou dezaktivovány hodnotou *true* nebo *false* . |
| `ReportReferenceLink` |Odkazuje na sestavy související s daným pozorovatelem. |
| `AdditionalInformation` |Poskytuje další informace, pokud je to možné, o zjištěné hrozbě. |

### <a name="servicemapcomputercl-records"></a>Záznamy ServiceMapComputer_CL

Záznamy s typem *ServiceMapComputer_CL* obsahují data inventáře pro servery s Service map agenty. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Jedinečný identifikátor počítače v pracovním prostoru |
| `ResourceName_s` | Jedinečný identifikátor počítače v pracovním prostoru |
| `ComputerName_s` | Plně kvalifikovaný název domény počítače |
| `Ipv4Addresses_s` | Seznam adres IPv4 serveru |
| `Ipv6Addresses_s` | Seznam IPv6 adres serveru |
| `DnsNames_s` | Pole názvů DNS |
| `OperatingSystemFamily_s` | Windows nebo Linux |
| `OperatingSystemFullName_s` | Úplný název operačního systému  |
| `Bitness_s` | Bitová verze počítače (32-bit nebo 64)  |
| `PhysicalMemory_d` | Fyzická paměť v MB |
| `Cpus_d` | Počet procesorů |
| `CpuSpeed_d` | Rychlost procesoru v MHz|
| `VirtualizationState_s` | *Neznámý*, *fyzický*, *virtuální*, *hypervisor* |
| `VirtualMachineType_s` | *Hyper*-v, *VMware*atd. |
| `VirtualMachineNativeMachineId_g` | ID virtuálního počítače přiřazené hypervisorem |
| `VirtualMachineName_s` | Název virtuálního počítače |
| `BootTime_t` | Čas spuštění |

### <a name="servicemapprocesscl-type-records"></a>Záznamy typu ServiceMapProcess_CL

Záznamy s typem *ServiceMapProcess_CL* mají data inventáře pro procesy připojené k protokolu TCP na serverech s agenty Service map. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Jedinečný identifikátor procesu v pracovním prostoru |
| `ResourceName_s` | Jedinečný identifikátor procesu v počítači, na kterém je spuštěný|
| `MachineResourceName_s` | Název prostředku počítače |
| `ExecutableName_s` | Název spustitelného procesu |
| `StartTime_t` | Čas spuštění fondu procesů |
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
| `UserName` | Účet, pod kterým se proces spouští |
| `UserDomain` | Doména, pod kterou je prováděn proces |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

### <a name="list-all-known-machines"></a>Zobrazit seznam všech známých počítačů

ServiceMapComputer_CL | shrnout arg_max (TimeGenerated, *) podle ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Vypíše kapacitu fyzické paměti pro všechny spravované počítače.

ServiceMapComputer_CL | shrnout arg_max (TimeGenerated, *) podle ResourceId | projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Vypíše název počítače, DNS, IP adresu a operační systém.

ServiceMapComputer_CL | shrnout arg_max (TimeGenerated, *) podle ResourceId | projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najde všechny procesy pomocí SQL na příkazovém řádku.

ServiceMapProcess_CL | kde CommandLine_s contains_cs "SQL" | shrnout arg_max (TimeGenerated, *) podle ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Vyhledání počítače (nejaktuálnější záznam) podle názvu prostředku

Hledat v (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | shrnout arg_max (TimeGenerated, *) podle ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Vyhledání počítače (nejaktuálnější záznam) podle IP adresy

Hledat v (ServiceMapComputer_CL) "10.229.243.232" | shrnout arg_max (TimeGenerated, *) podle ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Vypíše všechny známé procesy v zadaném počítači.

ServiceMapProcess_CL | kde MachineResourceName_s = = "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | shrnout arg_max (TimeGenerated, *) podle ResourceId

### <a name="list-all-computers-running-sql"></a>Vypsat všechny počítače se systémem SQL

ServiceMapComputer_CL | kde ResourceName_s in (((ServiceMapProcess_CL) "\*SQL\*" | DISTINCT MachineResourceName_s) | DISTINCT ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Vypíše všechny jedinečné verze produktu ve vaší datacentru.

ServiceMapProcess_CL | kde ExecutableName_s = = "kudrlinkou" | jedinečné ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvoření skupiny počítačů na všech počítačích se systémem CentOS

ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Shrnutí odchozích připojení ze skupiny počítačů

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

Všechna data serveru, procesu a závislostí v Service Map jsou k dispozici prostřednictvím [REST API Service map](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití

Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím vašeho používání služby Service Map. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby mapa služby. Aby poskytovaly přesné a efektivní možnosti odstraňování potíží, obsahují data informace o konfiguraci softwaru, jako je například operační systém a verze, IP adresa, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [hledání v protokolu](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics k načtení dat shromažďovaných pomocí Service map.


## <a name="troubleshooting"></a>Řešení potíží

Další informace najdete v [části řešení potíží v dokumentu konfigurace Service map]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Zpětná vazba

Máte pro nás informace o Service Map nebo této dokumentaci?  Navštivte naši [hlasovou stránku uživatele](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhovat funkce nebo hlasovat o stávajících návrzích.
