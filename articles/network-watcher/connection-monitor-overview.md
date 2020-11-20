---
title: Monitorování připojení | Microsoft Docs
description: Naučte se používat monitorování připojení k monitorování síťové komunikace v distribuovaném prostředí.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 13b379fd3b4f788d79cbb6a9bf6d40cb1693eaf9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948971"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Monitorování připojení k síti pomocí monitorování připojení

Monitorování připojení nabízí sjednocené monitorování připojení v Azure Network Watcher. Funkce monitorování připojení podporuje hybridní a cloudová nasazení Azure. Network Watcher poskytuje nástroje pro monitorování, diagnostiku a zobrazení metrik souvisejících s připojením pro vaše nasazení Azure.

Tady jsou některé případy použití pro monitorování připojení:

- Virtuální počítač s front-end webovým serverem komunikuje s virtuálním počítačem s databázovým serverem v vícevrstvé aplikaci. Chcete kontrolovat síťové připojení mezi těmito dvěma virtuálními počítači.
- Chcete, aby se virtuální počítače v oblasti Východní USA v oblasti Střed USA vyy na virtuální počítače s příkazy pro otestování a chcete porovnat latence sítě mezi oblastmi.
- Máte několik místních webů Office v Seattlu, Washington a v Ashburn,) – Virginia. Vaše weby Office se připojují k Microsoft 365 adres URL. Pro uživatele z adres URL Microsoft 365 Porovnejte latence mezi Seattle a Ashburn.
- Vaše hybridní aplikace potřebuje připojení ke koncovému bodu Azure Storage. Vaše místní lokalita a vaše aplikace Azure se připojí ke stejnému koncovému bodu Azure Storage. Chcete porovnat latence místního serveru s latencí aplikace Azure.
- Chcete kontrolovat konektivitu mezi místními nastaveními a virtuálními počítači Azure, které hostují vaši cloudovou aplikaci.

Monitorování připojení spojuje nejlepší ze dvou funkcí: funkce [monitorování připojení Network Watcher (Classic)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) a služba Network Performance Monitor (npm) pro monitorování [připojení služby](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [monitorování ExpressRoute](../expressroute/how-to-npm.md)a funkce [monitorování výkonu](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) .

Tady jsou některé výhody monitorování připojení:

* Jednotné a intuitivní prostředí pro potřeby Azure a hybridní monitorování
* Monitorování připojení mezi jednotlivými oblastmi a mezi jednotlivými pracovními prostory
* Vyšší četnosti zjišťování a lepší přehled o výkonu sítě
* Rychlejší upozorňování pro hybridní nasazení
* Podpora kontrol připojení, které jsou založené na protokolech HTTP, TCP a ICMP 
* Metriky a Log Analytics podporu pro nastavení testů pro Azure i pro jiné platformy než Azure

![Diagram znázorňující, jak monitorování připojení spolupracuje s virtuálními počítači Azure, hostiteli mimo Azure, koncovými body a umístěními úložiště dat](./media/connection-monitor-2-preview/hero-graphic.png)

Pokud chcete začít používat monitorování připojení pro monitorování, postupujte podle těchto kroků: 

1. Nainstalujte agenty monitorování.
1. Povolte Network Watcher v předplatném.
1. Vytvořte monitorování připojení.
1. Nastavte analýzu dat a výstrahy.
1. Diagnostikujte problémy ve vaší síti.

Následující části obsahují podrobné informace o těchto krocích.

## <a name="install-monitoring-agents"></a>Instalace agentů monitorování

Monitorování připojení spoléhá na zjednodušené spustitelné soubory, aby se spouštěly kontroly připojení. Podporuje kontroly připojení z prostředí Azure i místních prostředí. Spustitelný soubor, který použijete, závisí na tom, jestli je váš virtuální počítač hostovaný v Azure nebo v místním prostředí.

### <a name="agents-for-azure-virtual-machines"></a>Agenti pro virtuální počítače Azure

Pokud chcete, aby monitorování připojení rozpoznalo vaše virtuální počítače Azure jako zdroje monitorování, nainstalujte na ně rozšíření Network Watcher agenta virtuálního počítače. Toto rozšíření se označuje také jako *rozšíření Network Watcher*. Virtuální počítače Azure vyžadují, aby rozšíření aktivovalo komplexní monitorování a další pokročilé funkce. 

Rozšíření Network Watcher můžete nainstalovat při [vytváření virtuálního počítače](./connection-monitor.md#create-the-first-vm). Můžete také samostatně instalovat, konfigurovat a řešit potíže s rozšířením Network Watcher pro [Linux](../virtual-machines/extensions/network-watcher-linux.md) a [Windows](../virtual-machines/extensions/network-watcher-windows.md).

Pravidla pro skupinu zabezpečení sítě (NSG) nebo bránu firewall můžou blokovat komunikaci mezi zdrojem a cílem. Monitorování připojení tento problém detekuje a zobrazí se jako diagnostická zpráva v topologii. Pokud chcete povolit monitorování připojení, zajistěte, aby NSG a pravidla brány firewall povolovaly pakety přes TCP nebo ICMP mezi zdrojem a cílem.

### <a name="agents-for-on-premises-machines"></a>Agenti pro místní počítače

Pokud chcete, aby monitorování připojení rozpoznalo vaše místní počítače jako zdroje pro monitorování, nainstalujte na počítače agenta Log Analytics. Pak povolte řešení Network Performance Monitor. Tito agenti jsou propojeni s Log Analytics pracovními prostory, takže musíte nastavit ID a primární klíč pracovního prostoru, aby mohli agenti spustit monitorování.

Chcete-li nainstalovat agenta Log Analytics pro počítače se systémem Windows, přečtěte si téma [Azure monitor rozšíření virtuálních počítačů pro systém Windows](../virtual-machines/extensions/oms-windows.md).

Pokud cesta obsahuje brány firewall nebo síťová virtuální zařízení (síťová virtuální zařízení), ujistěte se, že cíl je dosažitelný.

## <a name="enable-network-watcher-on-your-subscription"></a>Povolit Network Watcher v předplatném

Všechna předplatná, která mají virtuální síť, jsou povolená s Network Watcher. Když ve svém předplatném vytvoříte virtuální síť, Network Watcher se automaticky povolí v oblasti a předplatném virtuální sítě. Toto automatické povolování nijak neovlivní vaše prostředky ani se neúčtují. Ujistěte se, že ve vašem předplatném není explicitně zakázaná Network Watcher. 

Další informace najdete v tématu [povolení Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení 

Monitorování připojení monitoruje komunikaci v pravidelných intervalech. Informuje vás o změnách v dostupnosti a latenci. Můžete také kontrolovat aktuální a historicky topologii sítě mezi zdrojovými a cílovými koncovými body.

Zdroji můžou být virtuální počítače Azure nebo místní počítače, které mají nainstalovaného agenta monitorování. Cílovými koncovými body můžou být Microsoft 365 adresy URL, adresy URL Dynamics 365, vlastní adresy URL, ID prostředků virtuálních počítačů Azure, IPv4, IPv6, plně kvalifikovaný název domény nebo libovolný název domény.

### <a name="access-connection-monitor"></a>Monitorování připojení přístupu

1. Na domovské stránce Azure Portal přejít na **Network Watcher**.
1. Na levé straně v části **monitorování** vyberte **monitorování připojení**.
1. Zobrazí se všechna monitorování připojení, která byla vytvořena v monitorování připojení. Chcete-li zobrazit monitory připojení vytvořené v klasickém prostředí monitorování připojení, přejděte na kartu **monitorování připojení** .
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Snímek obrazovky zobrazující monitory připojení, které byly vytvořeny v monitorování připojení" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Vytvoření monitorování připojení

V okně Sledování připojení, které vytvoříte v monitorování připojení, můžete přidat místní počítače a virtuální počítače Azure jako zdroje. Tato sledování připojení také mohou monitorovat připojení k koncovým bodům. Koncové body můžou být na Azure nebo na jakékoli jiné adrese URL nebo IP adrese.

Monitorování připojení zahrnuje následující entity:

* **Prostředek sledování připojení** – prostředek Azure specifický pro oblast. Všechny následující entity jsou vlastnosti prostředku monitorování připojení.
* **Koncový bod** – zdroj nebo cíl, který se účastní kontrol připojení. Mezi příklady koncových bodů patří virtuální počítače Azure, místní agenti, adresy URL a IP adresy.
* **Konfigurace testu** – konfigurace specifická pro protokol pro test. V závislosti na zvoleném protokolu můžete definovat port, prahové hodnoty, frekvenci testování a další parametry.
* **Testovací skupina** – skupina, která obsahuje zdrojové koncové body, cílové koncové body a konfigurace testu. Monitorování připojení může obsahovat více než jednu skupinu testů.
* **Test** – kombinace zdrojového koncového bodu, cílového koncového bodu a konfigurace testu. Test je nejpřesnější úroveň, při které jsou dostupná data monitorování. Data monitorování zahrnují procento kontrol, které selhaly, a čas odezvy (RTT).

 ![Diagram znázorňující monitorování připojení, definování vztahu mezi testovacími skupinami a testy](./media/connection-monitor-2-preview/cm-tg-2.png)

Monitorování připojení můžete vytvořit pomocí [Azure Portal](./connection-monitor-create-using-portal.md) nebo [ARMClient](./connection-monitor-create-using-template.md)

Všechny zdroje, cíle a konfigurace testu, které přidáte do testovací skupiny, se rozdělují na jednotlivé testy. Tady je příklad rozdělení zdrojů a cílů:

* Testovací skupina: TG1
* Zdroje: 3 (A, B, C)
* Cílová umístění: 2 (D, E)
* Konfigurace testů: 2 (konfigurace 1, konfigurace 2)
* Celkem vytvořených testů: 12

| Testovací číslo | Zdroj | Cíl | Konfigurace testu |
| --- | --- | --- | --- |
| 1 | A | D | Konfigurace 1 |
| 2 | A | D | Konfigurace 2 |
| 3 | A | E | Konfigurace 1 |
| 4 | A | E | Konfigurace 2 |
| 5 | B | D | Konfigurace 1 |
| 6 | B | D | Konfigurace 2 |
| 7 | B | E | Konfigurace 1 |
| 8 | B | E | Konfigurace 2 |
| 9 | C | D | Konfigurace 1 |
| 10 | C | D | Konfigurace 2 |
| 11 | C | E | Konfigurace 1 |
| 12 | C | E | Konfigurace 2 |

### <a name="scale-limits"></a>Omezení škálování

Monitory připojení mají následující meze škálování:

* Maximální počet monitorování připojení na předplatné na oblast: 100
* Maximální počet testovacích skupin na monitorování připojení: 20
* Maximální počet zdrojů a cílů na monitorování připojení: 100
* Maximální počet konfigurací testu na monitorování připojení: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analýza dat monitorování a nastavení výstrah

Po vytvoření monitorování připojení zdroje kontrolují připojení k cílům na základě konfigurace testu.

### <a name="checks-in-a-test"></a>Zkontroluje test.

Na základě protokolu, který jste zvolili v konfiguraci testu, monitor připojení spouští sérii kontrol pro dvojici zdrojového a cílového umístění. Kontroly se spustí podle četnosti testů, kterou jste zvolili.

Pokud používáte protokol HTTP, služba vypočítá počet odpovědí HTTP, které vrátily platný kód odpovědi. Platné kódy odpovědí je možné nastavit pomocí PowerShellu a rozhraní příkazového řádku. Výsledek určuje procento neúspěšných kontrol. Pro výpočet času RTT služba měří čas mezi voláním HTTP a odpovědí.

Pokud používáte protokol TCP nebo ICMP, služba vypočítá procento ztrát paketů a určí Procento neúspěšných kontrol. Pokud chcete Vypočítat čas RTT, služba měří dobu trvání přijetí potvrzení (ACK) pro odeslané pakety. Pokud jste povolili traceroute data pro testy sítě, můžete zobrazit ztrátu směrování po směrování a latenci pro vaši místní síť.

### <a name="states-of-a-test"></a>Stavy testu

Na základě dat, která kontroly vrací, můžou testy obsahovat následující stavy:

* **Pass** – skutečné hodnoty pro Procento neúspěšných kontrol a RTT jsou v rámci zadaných prahových hodnot.
* **Selhání** – skutečné hodnoty pro Procento neúspěšných kontrol nebo RTT překročily zadané prahové hodnoty. Pokud není zadána žádná prahová hodnota, test dosáhne stavu selhání, pokud je procento neúspěšných kontrol 100.
* **Upozornění** – 
     * Pokud je zadána prahová hodnota a monitor připojení sleduje kontroly, které selhaly procento více než 80% prahové hodnoty, test je označený jako upozornění.
     * Pokud neexistují zadané prahové hodnoty, monitor připojení automaticky přiřadí prahovou hodnotu. Při překročení této prahové hodnoty se stav testu změní na upozornění.Pro dobu odezvy v testech TCP nebo ICMP je prahová hodnota 750msec. Pro kontroly, které selhaly, je prahová hodnota 10%. 
* **Neurčitelné**   – V pracovním prostoru Log Analytics nejsou žádná data.Kontrolovat metriky. 
* **Nespuštěno**   – Zakázáno zakázáním testovací skupiny  

### <a name="data-collection-analysis-and-alerts"></a>Shromažďování, analýza a výstrahy dat

Data, která monitorování připojení shromažďuje, se ukládají v pracovním prostoru Log Analytics. Tento pracovní prostor nastavíte při vytváření monitorování připojení. 

Data monitorování jsou také k dispozici v Azure Monitor metriky. Pomocí Log Analytics můžete uchovávat data o monitorování, pokud chcete. Ve výchozím nastavení Azure Monitor ukládá metriky pouze 30 dnů. 

[Pro data můžete nastavit výstrahy založené na metrikách](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorování řídicích panelů

Na řídicích panelech monitorování se zobrazí seznam monitorů připojení, ke kterým můžete získat přístup pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy.

Když přejdete na monitorování připojení z Network Watcher, můžete zobrazit data podle těchto údajů:

* **Monitorování připojení** – seznam všech monitorování připojení vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Toto zobrazení je výchozí.
* **Testovací skupiny** – seznam všech testovacích skupin vytvořených pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Tyto testovací skupiny nejsou filtrovány podle monitorování připojení.
* **Test** – seznam všech testů, které se spouštějí pro vaše předplatná, oblasti, časová razítka, zdroje a cílové typy. Tyto testy nejsou filtrovány pomocí monitorování připojení nebo skupin testů.

Na následujícím obrázku jsou tři zobrazení dat označena šipkou 1.

Na řídicím panelu můžete rozbalit každé monitorování připojení a zobrazit jeho testovací skupiny. Pak můžete rozbalit každou testovací skupinu a zobrazit tak testy, které jsou v něm spuštěné. 

Seznam můžete filtrovat podle:

* **Filtry nejvyšší úrovně** – hledání v seznamu podle textu, typu entity (sledování připojení, testovací skupina nebo test) časové razítko a rozsah. Scope zahrnuje odběry, oblasti, zdroje a cílové typy. Viz box 1 na následujícím obrázku.
* **Filtry založené na stavu** – filtrujte podle stavu monitorování připojení, testovací skupiny nebo testu. Viz Box 2 na následujícím obrázku.
* **Filtr založený na výstrahách** : filtrování podle výstrah vyvolaných v prostředku monitorování připojení. Viz Box 3 na následujícím obrázku.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Snímek obrazovky ukazující, jak filtrovat zobrazení monitorování připojení, testovacích skupin a testů v monitorování připojení " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Chcete-li například prohlédnout všechny testy v monitorování připojení, kde je zdrojová adresa IP 10.192.64.56:
1. Změňte zobrazení na **test**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V **oblasti** filtr na nejvyšší úrovni vyberte **zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v monitorování připojení, kde je zdrojová adresa IP 10.192.64.56:
1. Změňte zobrazení na **test**.
1. U filtru založeného na stavu vyberte **selhání**.
1. Do vyhledávacího pole zadejte *10.192.64.56*
1. V **oblasti** filtr na nejvyšší úrovni vyberte **zdroje**.

Chcete-li zobrazit pouze neúspěšné testy v monitorování připojení, kde je cíl outlook.office365.com:
1. Změňte zobrazení na **test**.
1. U filtru založeného na stavu vyberte **selhání**.
1. Do vyhledávacího pole zadejte *Office.Live.com*
1. V **oblasti** filtr na nejvyšší úrovni vyberte **cílová místa**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Snímek obrazovky znázorňující zobrazení, které se filtruje tak, aby se zobrazily jenom neúspěšné testy pro cíl Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Chcete-li zjistit příčinu selhání monitorování připojení nebo skupiny testů nebo testu, klikněte na sloupec s názvem důvod.  Tím se dozvíte, jaká prahová hodnota (kontroly, které selhaly% nebo RTT), se porušila a související diagnostické zprávy.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Snímek obrazovky znázorňující důvod selhání pro monitorování připojení, test nebo testovací skupinu" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Zobrazení trendů v RTT a Procento neúspěšných kontrol pro monitorování připojení:
1. Vyberte monitorování připojení, které chcete prozkoumat.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Snímek obrazovky zobrazující metriky pro monitorování připojení zobrazené skupinou testů" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Zobrazí se následující oddíly.  
    1. Essentials – vlastnosti pro vybrané monitorování připojení pro konkrétní prostředek 
    1. Shrnut 
        1. Agregované spojnice trendů pro čas RTT a Procento neúspěšných kontrol pro všechny testy v monitorování připojení. Můžete nastavit určitý čas pro zobrazení podrobností.
        1. Prvních 5 napříč testovacími skupinami, zdroji a cíli na základě času RTT nebo procenta neúspěšných kontrol. 
    1. Karty pro testovací skupiny, zdroje, cíle a konfigurace testu – seznam testovacích skupin, zdrojů nebo cílů v monitorování připojení. Kontrola testů se nezdařila, agregovaná doba RTT a kontrola neúspěšných% hodnot.  Můžete se také vrátit v čase a zobrazit data. 
    1. Problémy – problémy úrovně směrování pro každý test v monitorování připojení. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Snímek obrazovky zobrazující metriky pro monitorování připojení zobrazené skupinou testů část 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Můžeš
    * Klikněte na Zobrazit všechny testy – pro zobrazení všech testů v monitorování připojení.
    * Klikněte na Zobrazit všechny testovací skupiny, konfigurace testu, zdroje a cíle – pro zobrazení podrobností specifických pro každou z nich. 
    * Vyberte testovací skupinu, konfiguraci testu, zdroj nebo cíl – Chcete-li zobrazit všechny testy v entitě.

1. V zobrazení všechny testy můžete:
    * Vyberte testy a klikněte na porovnat.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Snímek obrazovky znázorňující porovnání dvou testů" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Pomocí clusteru rozbalíte složené prostředky, jako jsou virtuální sítě, podsítě do jejích podřízených prostředků.
    * Kliknutím na topologie Zobrazte topologii pro všechny testy.

Chcete-li zobrazit trendy v času RTT a Procento neúspěšných kontrol pro skupinu testů:
1. Vyberte testovací skupinu, kterou chcete prozkoumat. 
1. Zobrazí se podobné monitorování připojení – základy, souhrn, tabulka pro testovací skupiny, zdroje, cíle a konfigurace testů. Procházejte stejným způsobem jako u monitorování připojení

Zobrazení trendů v RTT a Procento neúspěšných kontrol testu:
1. Vyberte test, který chcete prozkoumat. Zobrazí se topologie sítě a koncové grafy trendu pro kontroly, které selhaly% a čas odezvy. Zjištěné problémy zobrazíte tak, že v topologii vyberete jakékoli směrování v cestě. (Tyto segmenty jsou prostředky Azure.) Tato funkce není aktuálně k dispozici pro místní sítě.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Snímek obrazovky s zobrazením topologie testu" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Dotazy protokolu v Log Analytics

Pomocí Log Analytics můžete vytvořit vlastní zobrazení dat monitorování. Všechna data, která jsou zobrazena v uživatelském rozhraní, jsou z Log Analytics. Data v úložišti můžete interaktivně analyzovat. Korelujte data z Agent Health nebo jiných řešení, která jsou založená na Log Analytics. Exportujte data do aplikace Excel nebo Power BI nebo vytvořte odkaz Shared.

#### <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

V monitorováních připojení vytvořených před prostředím monitorování připojení jsou k dispozici všechny čtyři metriky:% PROBE selhaly, AverageRoundtripMs, ChecksFailedPercent (Preview) a RoundTripTimeMs (Preview). V monitorováních připojení vytvořených v prostředí monitorování připojení jsou data dostupná jenom pro metriky, které jsou označené jako *(Preview)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Snímek obrazovky znázorňující metriky v monitorování připojení" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Když použijete metriky, nastavte typ prostředku jako Microsoft. Network/networkWatchers/connectionMonitors.

| Metrika | Zobrazované jméno | Jednotka | Typ agregace | Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % PROBE selhalo | Procento | Průměr | Procento sond monitorování připojení selhalo. | Žádné dimenze |
| AverageRoundtripMs | Průměrná doba odezvy (MS) | Milisekund | Průměr | Průměrná doba odezvy sítě pro testy monitorování připojení odesílané mezi zdrojem a cílem |             Žádné dimenze |
| ChecksFailedPercent (Preview) | % Kontroly selhaly (Preview) | Procento | Průměr | Procento neúspěšných kontrol testu | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Parametr sourceresourceid <br>SourceType <br>Protokol <br>DestinationAddress <br>Cílový. <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Oblast |
| RoundTripTimeMs (Preview) | Doba odezvy (MS) (Preview) | Milisekund | Průměr | Čas RTT pro kontroly odeslané mezi zdrojem a cílem. Tato hodnota není průměrná. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Parametr sourceresourceid <br>SourceType <br>Protokol <br>DestinationAddress <br>Cílový. <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Oblast |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Výstrahy založené na metrikách pro monitorování připojení

Výstrahy metriky můžete pro monitorování připojení vytvořit pomocí níže uvedených metod. 

1. Z monitorování připojení během vytváření monitorování připojení [pomocí Azure Portal](connection-monitor-preview-create-using-portal.md#) 
1. Z monitorování připojení pomocí možnosti konfigurovat výstrahy na řídicím panelu 
1. Z Azure Monitor – Chcete-li vytvořit výstrahu v Azure Monitor: 
    1. Vyberte prostředek sledování připojení, který jste vytvořili v monitorování připojení.
    1. Zajistěte, aby se **metrika** zobrazila jako typ signálu pro monitorování připojení.
    1. V části **Přidat podmínku** pro **Název signálu** vyberte **ChecksFailedPercent (Preview)** nebo **RoundTripTimeMs (Preview)**.
    1. Jako **typ signálu** vyberte **metriky**. Vyberte například **ChecksFailedPercent (Preview)**.
    1. V seznamu jsou uvedeny všechny dimenze metriky. Vyberte název dimenze a hodnotu dimenze. Vyberte například možnost **zdrojová adresa** a poté zadejte IP adresu libovolného zdroje v monitoru připojení.
    1. V **logice výstrahy** zadejte následující podrobnosti:
        * **Typ podmínky**: **static**.
        * **Podmínka** a **prahová hodnota**.
        * **Členitost agregace a frekvence hodnocení**: monitorování připojení aktualizuje data každou minutu.
    1. V **Možnosti akce** vyberte skupinu akcí.
    1. Zadejte podrobnosti výstrahy.
    1. Vytvořte pravidlo výstrahy.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Snímek obrazovky znázorňující oblast vytvořit pravidlo v Azure Monitor. Je zvýrazněna zdrojová adresa a název zdrojového koncového bodu." lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnostikujte problémy ve vaší síti

Monitorování připojení pomáhá diagnostikovat problémy v monitorování připojení a v síti. Problémy ve vaší hybridní síti zjistí agenti Log Analytics, které jste předtím nainstalovali. Problémy v Azure se zjišťují pomocí rozšíření Network Watcher. 

Problémy v síti Azure můžete zobrazit v síťové topologii.

Pro sítě, jejichž zdroje jsou místní virtuální počítače, se dají zjistit tyto problémy:

* Vypršel časový limit žádosti.
* Koncový bod nevyřešila služba DNS – dočasné nebo trvalé. Adresa URL je neplatná.
* Nenašly se žádní hostitelé.
* Zdroj se nemůže připojit k cíli. Cíl není dosažitelný pomocí protokolu ICMP.
* Problémy související s certifikátem: 
    * Je vyžadován klientský certifikát pro ověření agenta. 
    * Seznam přemístění certifikátu není přístupný. 
    * Název hostitele koncového bodu se neshoduje s alternativním názvem předmětu nebo předmětu certifikátu. 
    * V úložišti důvěryhodných certifikačních autorit místního počítače zdroje chybí kořenový certifikát. 
    * Platnost certifikátu SSL vypršela, je neplatný, odvolaný nebo nekompatibilní.

Pro sítě, jejichž zdroje jsou virtuálními počítači Azure, se dají zjistit tyto problémy:

* Problémy agenta:
    * Agent byl zastaven.
    * Překlad DNS se nezdařil.
    * Na cílovém portu nenaslouchá žádná aplikace ani naslouchací proces.
    * Soket nelze otevřít.
* Problémy se stavem virtuálního počítače: 
    * Spouštění
    * Zastavování
    * Zastaveno
    * Rušení přidělení
    * Přidělení zrušeno
    * Restartování
    * Nepřiděleno
* Chybí položka tabulky protokolu ARP.
* Provoz se zablokoval kvůli problémům s místními branami firewall nebo NSGým pravidlům.
* Problémy s bránou virtuální sítě: 
    * Chybějící trasy.
    * Tunelové propojení mezi dvěma branami je odpojené nebo chybí.
    * Druhá brána se nenašla tunelem.
    * Nenašly se žádné informace o partnerském vztahu.
* V Microsoft Edge chyběla trasa.
* Provoz se zastavil kvůli systémovým trasám nebo UDR.
* Protokol BGP není v připojení brány povolen.
* Sonda DIP je mimo provoz nástroje pro vyrovnávání zatížení.

## <a name="next-steps"></a>Další kroky
    
   * Naučte [se vytvářet monitorování připojení pomocí Azure Portal](./connection-monitor-create-using-portal.md)  
   * Naučte [se vytvářet monitorování připojení pomocí ARMClient](./connection-monitor-create-using-template.md) .