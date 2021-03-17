---
title: Nastavení sítě
description: Seznamte se s architekturou řešení, přípravou sítě, požadavky a dalšími informacemi potřebnými k tomu, abyste měli jistotu, že jste úspěšně nastavili síť pro práci s Azure Defenderem pro zařízení IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0f85eebbfa8fcdfd9ad6e31a564f27b5d9bfbdfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733240"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>O programu Azure Defender pro síť IoT

Azure Defender pro IoT zajišťuje nepřetržité monitorování a zjišťování hrozeb ICS. Platforma zahrnuje tyto komponenty:

**Defender pro senzory IoT:** Senzory shromažďují síťový provoz ICS pomocí pasivního monitorování (bez agentů). Pasivní a nerušivé, senzory mají žádný vliv na výkon a sítě a zařízení IoT. Senzor se připojí k portu SPAN nebo k síti KLEPNE a hned začne monitorovat vaši síť. Detekce se zobrazí v konzole senzorů. Zde je můžete zobrazit, prozkoumat a analyzovat v mapě sítě, v inventáři zařízení a v rozsáhlých různých sestavách. Mezi příklady patří sestavy posouzení rizik, dotazy dolování dat a vektory útoku. 

**Defender pro místní konzolu pro správu IoT**: místní Konzola pro správu nabízí konsolidované zobrazení všech síťových zařízení. Poskytuje v reálném čase přehled o klíčových ukazatelích a výstrahách rizik a IoT ve všech vašich zařízeních. Úzce se integruje s vašimi pracovními postupy SOC a playbooky umožňuje snadnou prioritu aktivit zmírňování a korelace mezi různými pracovišti hrozeb. 

**Defender pro IoT Portal:** Aplikace Defender for IoT vám může pomáhat při nákupu zařízení řešení, instalaci a aktualizaci softwaru a aktualizaci balíčků ČŘ. 

Tento článek poskytuje informace o architektuře řešení, přípravě sítě, požadavcích a dalších možnostech, které vám pomůžou úspěšně nastavit síť pro práci s Defenderem pro zařízení IoT. Čtenáři, kteří pracují s informacemi v tomto článku, by měli mít zkušenosti s provozem a správou sítí z a IoT. Mezi příklady patří technici pro automatizaci, vedoucí podniku, poskytovatelé síťových infrastruktur, kyberbezpečnosti týmy, CISO nebo ředitelé informačních technologií.

Pokud potřebujete pomoc nebo podporu, kontaktujte [Podpora Microsoftu](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Úlohy nasazení na pracovišti

Mezi úlohy nasazení lokality patří:

- [Shromažďovat informace o lokalitě](#collect-site-information)

- [Příprava pracovní stanice konfigurace](#prepare-a-configuration-workstation)

- [Plánování instalace racku](#planning-rack-installation)

### <a name="collect-site-information"></a>Shromažďovat informace o lokalitě

Zaznamenejte informace o lokalitě, jako například:

- Informace o síti pro správu senzorů.

- Architektura sítě lokality.

- Fyzické prostředí.

- Integrace systému.

- Plánované přihlašovací údaje uživatele.

- Pracovní stanice konfigurace.

- Certifikáty SSL (volitelné, ale doporučené).

- Ověřování SMTP (volitelné). Chcete-li použít server SMTP s ověřováním, připravte pověření požadovaná pro váš server.

- Servery DNS (volitelné). Připravte IP adresu a název hostitele serveru DNS.

Podrobný seznam a popis důležitých informací o lokalitách najdete v tématu [příklad knihy webů](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Úspěšné pokyny pro monitorování

Pokud chcete najít optimální místo pro připojení zařízení v každé z produkčních sítí, doporučujeme postupovat podle tohoto postupu:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Příklad diagramu nastavení produkční sítě":::

### <a name="prepare-a-configuration-workstation"></a>Příprava pracovní stanice konfigurace

Připravte pracovní stanici Windows, včetně následujících:

- Připojení k rozhraní správy senzorů.

- Podporovaný prohlížeč

- Terminálový software, jako je například výstup.

Ujistěte se, že požadovaná pravidla brány firewall jsou v pracovní stanici otevřená. Podrobnosti najdete v tématu [požadavky na přístup k síti](#network-access-requirements) .

#### <a name="supported-browsers"></a>Podporované prohlížeče

Pro senzory a místní webové aplikace konzoly pro správu se podporují následující prohlížeče:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Požadavky na přístup k síti

Ověřte, že vaše zásada zabezpečení vaší organizace umožňuje přístup k následujícím akcím:

| Protokol | Přenos | Vstupně-výstupní | Port | Použito | Účel | Zdroj | Cíl |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | VSTUPNĚ-VÝSTUPNÍ | 443 | Senzor a místní Konzola pro správu – webová konzola | Přístup ke webové konzole | Klient | Senzor a místní Konzola pro správu |
| SSH | TCP | VSTUPNĚ-VÝSTUPNÍ | 22 | Rozhraní příkazového řádku | Přístup k rozhraní příkazového řádku | Klient | Senzor a místní Konzola pro správu |
| SSL | TCP | VSTUPNĚ-VÝSTUPNÍ | 443 | Senzor a místní Konzola pro správu | Propojení mezi platformou CyberX a platformou centrální správy | snímač | Místní Konzola pro správu |
| NTP | UDP | IN | 123 | Čas synchronizace | Místní Konzola pro správu se používá jako NTP pro senzory. | snímač | místní Konzola pro správu |
| NTP | UDP | VSTUPNĚ-VÝSTUPNÍ | 123 | Čas synchronizace | Senzor připojený k externímu serveru NTP, když není nainstalovaná žádná místní Konzola pro správu | snímač | NTP |
| SMTP | TCP | MIMO | 25 | E-mail | Spojení mezi platformou CyberX a platformou správy a poštovním serverem | Senzor a místní Konzola pro správu | E-mailový server |
| Syslog | UDP | MIMO | 514 | LEEF | Protokoly, které odesílají z místní konzoly pro správu na server syslog | Místní Konzola pro správu a senzor | Server syslogu |
| DNS |  | VSTUPNĚ-VÝSTUPNÍ | 53 | DNS | Port serveru DNS | Místní Konzola pro správu a senzor | Server DNS |
| LDAP | TCP | VSTUPNĚ-VÝSTUPNÍ | 389 | Active Directory | Připojení mezi platformou CyberX a platformou správy ke službě Active Directory | Místní Konzola pro správu a senzor | Server LDAP |
| LDAPS | TCP | VSTUPNĚ-VÝSTUPNÍ | 636 | Active Directory | Připojení mezi platformou CyberX a platformou správy ke službě Active Directory | Místní Konzola pro správu a senzor | Server LDAPs |
| SNMP | UDP | MIMO | 161 | Sledování | Vzdálené sběrače SNMP. | Místní Konzola pro správu a senzor | Server SNMP |
| Rozhraní WMI | UDP | MIMO | 135 | Monitorování | Monitorování koncového bodu systému Windows | Elektrické | Příslušný prvek sítě |
| Tunelové propojení | TCP | IN | 9000 <br /><br />– nad portem 443 <br /><br />Od koncového uživatele k místní konzole pro správu. <br /><br />– Port 22 od senzoru k místní konzole pro správu  | Monitorování | Tunelové propojení | Elektrické | Místní Konzola pro správu |

### <a name="planning-rack-installation"></a>Plánování instalace racku

Naplánování instalace racku:

1. Připravte monitor a klávesnici pro nastavení sítě vašeho zařízení.

1. Přidělte prostor stojanu pro dané zařízení.

1. Máte k dispozici AC napájení zařízení.
1. Připravte kabel LAN pro připojení správy k síťovému přepínači.
1. Připravte kabely LAN pro připojení portů přepínače SPAN (mirror) a nebo sítě do programu Defender pro zařízení IoT. 
1. Nakonfigurujte, připojte a ověřte porty SPAN v zrcadlených přepínačích, jak je popsáno v relaci revize architektury.
1. Připojte nakonfigurovaný port SPAN k počítači, na kterém je spuštěný Nástroj Wireshark, a ověřte, jestli je port správně nakonfigurovaný.
1. Otevřete všechny příslušné porty brány firewall.

## <a name="about-passive-network-monitoring"></a>Informace o pasivním monitorování sítě

Zařízení přijímá provoz z více zdrojů, a to buď přepnutím portů zrcadlení (porty), nebo klepnutím na síť. Port pro správu je připojený k síti pro správu podnikových, firemních nebo senzorů s možností připojení k místní konzole pro správu nebo k portálu pro službu IoT Portal.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagram spravovaného přepínače s zrcadlením portů.":::

### <a name="purdue-model"></a>Model Purdue

Následující části popisují Purdue úrovně.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagram modelu Purdue":::

####  <a name="level-0-cell-and-area"></a>Úroveň 0: buňka a oblast  

Úroveň 0 se skládá z nejrůznějších senzorů, poháněcí zařízení a zařízení zapojených do základního výrobního procesu. Tato zařízení provádějí základní funkce pro průmyslové automatizaci a systém řízení, jako například:

- Řízení motoru.

- Měření proměnných.
- Nastavení výstupu.
- Provádění klíčových funkcí, jako je malování, svařování a ohnutí.

#### <a name="level-1-process-control"></a>Úroveň 1: řízení procesů

Úroveň 1 se skládá z vestavěných řadičů, které řídí a manipulují s výrobním procesem, jehož klíčovou funkcí je komunikace s zařízeními úrovně 0. V diskrétní výrobě jsou tato zařízení programovatelné Logic Controllers (PLCs) nebo jednotky vzdálené telemetrie (RTUs). V procesu výroby se základní kontroler nazývá systém distribuovaných ovládacích prvků (DCS).

#### <a name="level-2-supervisory"></a>Úroveň 2: dozorčí

Úroveň 2 představuje systémy a funkce přidružené k dohledu za běhu a provozu oblasti produkčního zařízení. To obvykle zahrnuje následující: 

- Rozhraní operátorů nebo HMIs

- Alarmy nebo systémy pro upozorňování

- Zpracování historian a systémů pro správu dávek

- Ovládání místnostních pracovních stanic

Tyto systémy komunikují s PLCs a RTUs na úrovni 1. V některých případech komunikují nebo sdílejí data s lokalitou nebo podnikem (úroveň 4 a 5) systémy a aplikace. Tyto systémy jsou primárně založené na standardních výpočetních zařízeních a operačních systémech (UNIX nebo Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Úrovně 3 a 3,5: úroveň lokality a průmyslový hraniční síť

Úroveň webu představuje nejvyšší úroveň průmyslových automatizovaných a kontrolních systémů. Systémy a aplikace, které na této úrovni existují, spravují průmyslové automatizace a řídicí funkce v rámci lokality. Úrovně 0 až 3 se považují za kritické pro operace na pracovišti. Systémy a funkce, které existují na této úrovni, můžou zahrnovat následující:

- Generování sestav výroby (například časy cyklů, index kvality, prediktivní údržba)

- Historian rostlinného podniku

- Podrobné plánování výroby

- Správa operací na úrovni webu

- Správa zařízení a materiálů

- Oprava – spustit server

- Souborový server

- Průmyslový doména, Active Directory, terminálový server

Tyto systémy komunikují s produkční zónou a sdílejí data s systémy a aplikacemi Enterprise (úroveň 4 a 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Úrovně 4 a 5: podnikové a podnikové sítě

Úroveň 4 a 5 reprezentují lokalitu nebo podnikovou síť, ve které existují centralizované systémy IT a funkce. Organizace IT přímo spravuje služby, systémy a aplikace na těchto úrovních.

## <a name="planning-for-network-monitoring"></a>Plánování monitorování sítě

V následujících příkladech jsou k dispozici různé typy topologií pro sítě průmyslového řízení společně s důležitými informacemi pro optimální monitorování a umísťování senzorů.

### <a name="what-should-be-monitored"></a>Co je třeba monitorovat?

Provoz, který přechází z vrstev 1 a 2, by měl být monitorován.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>K čemu se má Defender pro zařízení IoT připojit?

Program Defender pro zařízení IoT by se měl připojit ke spravovaným přepínačům, které vidí průmyslovou komunikaci mezi vrstvami 1 a 2 (v některých případech také ve vrstvě 3).

Následující diagram představuje obecnou abstrakci Multilayer architektury s obsáhlém kyberbezpečnosti ekosystémem, který obvykle provozuje SOC a MSSP.

Senzory NTA jsou obvykle nasazeny v vrstvách 0 až 3 modelu OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagram modelu OSI":::

#### <a name="example-ring-topology"></a>Příklad: kruhová topologie

KRUHOVÁ síť je topologie, ve které se každý přepínač nebo uzel připojuje k přesně dvěma dalším přepínačům a vytváří jednu nepřetržitou cestu pro provoz.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagram kruhové topologie":::

#### <a name="example-linear-bus-and-star-topology"></a>Příklad: lineární sběrnice a topologie hvězdičky

V síti s hvězdičkami je každý hostitel připojený k centrálnímu centru. V nejjednodušším tvaru jeden centrální hub funguje jako trubka pro přenos zpráv. V následujícím příkladu nejsou sledovány méně přepínačů a přenos, který zůstane místní pro tyto přepínače, nebude vidět. Zařízení se můžou identifikovat na základě zpráv protokolu ARP, ale chybí informace o připojení.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagram lineární sběrnice a topologie hvězdičky":::

#### <a name="multisensor-deployment"></a>Nasazení s více senzory

Tady je několik doporučení pro nasazení různých senzorů:

| **Číselná** | **Měřiče** | **Závislost** | **Počet senzorů** |
|--|--|--|--|
| Maximální vzdálenost mezi přepínači | 80 měřičů | Připravený ethernetový kabel | Více než 1 |
| Počet sítí typu OT. | Více než 1 | Bez fyzického připojení | Více než 1 |
| Počet přepínačů | Dá se použít ke konfiguraci RSPAN | Až osm přepínačů s místním rozsahem blízko ke senzoru podle vzdálenosti kabelů | Více než 1 |

#### <a name="traffic-mirroring"></a>Zrcadlení provozu  

Chcete-li zobrazit pouze relevantní informace pro analýzu provozu, je nutné připojit se k portu zrcadlení na přepínači nebo klepnutím, který zahrnuje pouze průmyslový ICS a SCADA provoz, pro platformu IoT. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Tento přepínač použijte pro instalaci.":::

Přenos přepínačů můžete monitorovat pomocí následujících metod:

- [Přepnout port SPAN](#switch-span-port)

- [Vzdálené rozpětí (RSPAN)](#remote-span-rspan)

- [Aktivní a pasivní agregace klepněte](#active-and-passive-aggregation-tap)

Rozsah a RSPAN jsou terminologie Cisco. Jiné značky přepínačů mají podobné funkce, ale mohou používat odlišnou terminologii.

#### <a name="switch-span-port"></a>Přepnout port SPAN

Analyzátor portů přepínače zrcadlí místní provoz z rozhraní v přepínači na rozhraní na stejném přepínači. Tady je několik důležitých informací:

- Ověřte, zda příslušný přepínač podporuje funkci zrcadlení portů.  

- Možnost zrcadlení je ve výchozím nastavení zakázána.

- Doporučujeme nakonfigurovat všechny porty přepínače, a to i v případě, že k nim nejsou připojená žádná data. V opačném případě může být neoprávněné zařízení připojené k nemonitorovanému portu a na senzoru se neupozorní.

- V sítích, které využívají všesměrové vysílání nebo zasílání zpráv vícesměrového vysílání, nakonfigurujte přepínač na zrcadlení jenom pro přenos příjmu (příjem). V opačném případě se zprávy vícesměrového vysílání zopakují pro tolik aktivních portů a šířka pásma se vynásobí.

Následující příklady konfigurace jsou určené jenom pro referenci a jsou založené na přepínači Cisco 2960 (24 portů) se systémem IOS. Jsou to jenom typické příklady, proto je nepoužívejte jako pokyny. Zrcadlené porty v jiných operačních systémech Cisco a dalších značkách přepínačů se konfigurují jinak.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagram terminálu konfigurace portů rozsahu":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagram režimu konfigurace portu SPAN":::

##### <a name="monitoring-multiple-vlans"></a>Monitorování více sítí VLAN

Defender pro IoT umožňuje monitorovat sítě VLAN, které jsou ve vaší síti nakonfigurované. Není nutná žádná konfigurace programu Defender pro systém IoT. Uživatel musí zajistit, aby byl přepínač ve vaší síti nakonfigurovaný tak, aby odesílal značky VLAN do programu Defender pro IoT.

Následující příklad ukazuje vyžadované příkazy, které je třeba nakonfigurovat na přepínači Cisco pro povolení monitorování sítí VLAN v programu Defender pro IoT:

**Monitorování relace**: Tento příkaz zodpovídá za proces odeslání sítí VLAN do portu span.

- monitorování zdrojového rozhraní relace 1 Gi1/2

- monitorovat typ paketů filtru relace 1 dobrý příjem

- monitorování cílového rozhraní fastEthernet1/1 dot1q zapouzdření relace 1

**Monitorovat port F.E. Gi1/1**: sítě VLAN jsou nakonfigurovány na portu šachty.

- rozhraní GigabitEthernet1/1

- dot1q zapouzdření switchport

- switchport režimu

#### <a name="remote-span-rspan"></a>Vzdálené rozpětí (RSPAN)

Vzdálená relace rozsahu odráží provoz z více distribuovaných zdrojových portů do vyhrazené vzdálené sítě VLAN.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagram vzdáleného rozsahu":::

Data v síti VLAN se pak doručí prostřednictvím prodaných portů mezi několika přepínači a konkrétním přepínačem, který obsahuje fyzický cílový port. Tento port se připojuje k programu Defender pro IoT Platform.  

##### <a name="more-about-rspan"></a>Další informace o RSPAN

- RSPAN je pokročilá funkce, která vyžaduje speciální síť VLAN k přenosu provozu mezi přepínači. RSPAN se nepodporuje u všech přepínačů. Ověřte, že přepínač podporuje funkci RSPAN.

- Možnost zrcadlení je ve výchozím nastavení zakázána.

- Vzdálená síť VLAN musí být povolená na prohlášeném portu mezi zdrojovými a cílovými přepínači.

- Všechny přepínače, které spojují stejnou relaci RSPAN, musí být od stejného dodavatele.

> [!NOTE]
> Ujistěte se, že port kmene, který sdílí vzdálenou síť VLAN mezi přepínači, není definovaný jako zdrojový port relace zrcadlení.
>
> Vzdálená síť VLAN zvyšuje šířku pásma na prodaném portu podle velikosti šířky pásma zrcadlené relace. Ověřte, zda je na něm podporován port ústředny přepínače.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagram vzdálené sítě VLAN":::

#### <a name="rspan-configuration-examples"></a>Příklady konfigurace RSPAN

RSPAN: na základě Cisco Catalyst 2960 (24 portů).

**Příklad konfigurace zdrojového přepínače:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Snímek obrazovky s konfigurací RSPAN":::

1. Zadejte režim globální konfigurace.

1. Vytvořte vyhrazenou síť VLAN.

1. Identifikujte síť VLAN jako RSPAN VLAN.

1. Vraťte se do režimu konfigurace terminálu.

1. Nakonfigurujte všechny 24 portů jako zdroje relací.

1. Nakonfigurujte síť VLAN RSPAN tak, aby byla cílem relace.

1. Vrátí se do režimu privilegované EXEC.

1. Ověřte konfiguraci zrcadlení portů.

**Příklad konfigurace cílového přepínače:**

1. Zadejte režim globální konfigurace.

1. Nakonfigurujte síť VLAN RSPAN tak, aby byla zdrojem relace.

1. Nastavte fyzický port 24 tak, aby byl cílem relace.

1. Vrátí se do režimu privilegované EXEC.

1. Ověřte konfiguraci zrcadlení portů.

1. Konfiguraci uložte.

#### <a name="active-and-passive-aggregation-tap"></a>Aktivní a pasivní agregace klepněte

Klepnutím na aktivní nebo pasivní agregaci se nainstalují vložené do síťového kabelu. V případě, že se ke snímači monitorování duplikuje jak RX, tak i TX.

Koncová přístupová klávesa (klepněte na) je hardwarové zařízení, které umožňuje tok síťového provozu z portu A na port B a z portu B na port A bez přerušení. Vytváří přesnou kopii obou stran toku provozu, a to nepřetržitě, bez narušení integrity sítě. V případě potřeby se v některých případech agreguje přenos a příjem provozu pomocí nastavení přepínače. Pokud není agregace podporovaná, každý klepnutím použije dva porty senzoru k monitorování odesílání a přijímání provozu.

V různých důvodech je výhodné vyklepnutí. Jsou založené na hardwaru a nemůžou být ohrožené. Předávají veškerý provoz, dokonce i poškozené zprávy, které přepínače často přecházejí. Nerozlišují se s procesorem, takže časování paketů je přesné, pokud přepínače zpracovávají funkci zrcadlení jako úlohu s nízkou prioritou, která může ovlivnit načasování zrcadlených paketů. Pro účely forenzní je klepněte na nejlepší zařízení.

Klepnutím na agregátory lze také použít pro monitorování portů. Tato zařízení jsou založená na procesorech a nejsou tak vnitřně zabezpečená jako při klepnutí na hardware. Nemusí odrážet přesné časování paketů.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagram aktivních a pasivních klepnutí":::

##### <a name="common-tap-models"></a>Běžné modely klepnutí

Tyto modely byly testovány na kompatibilitu. Ostatní dodavatelé a modely můžou být taky kompatibilní.

| Image | Modelování |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Snímek obrazovky Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Snímek obrazovky IXIA TPA2-CU3."::: | IXIA TPA2 – CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Snímek obrazovky US Robotics USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Konfigurace speciálního klepnutí

| Klepněte na Garland | Klepněte na tlačítko US Robotics |
| ----------- | --------------- |
| Ujistěte se, že jsou propojky nastaveny takto:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Snímek obrazovky s přepínačem US Robotics":::| Ujistěte se, že je aktivní režim agregace. |

## <a name="deployment-validation"></a>Ověřování nasazení

### <a name="engineering-self-review"></a>Inženýr – přezkoumání  

Nejlepší způsob, jak definovat, jak se má připojit, můžete si projít diagram sítě z pracovního postupu a ICS.

Technici na pracovišti znají, co jejich síť vypadá. Kontrola relace s místní sítí a provozními týmy obvykle vyjasní očekávání a určí, co je nejlepší místo pro připojení zařízení.

Relevantní informace:

- Seznam známých zařízení (tabulka)  

- Odhadovaný počet zařízení  

- Dodavatelé a průmyslové protokoly

- Model přepínačů pro ověření, že je k dispozici možnost zrcadlení portů

- Informace o tom, kdo spravuje přepínače (například IT) a zda se jedná o externí prostředky

- Seznam sítí typu OT v lokalitě

#### <a name="common-questions"></a>Časté dotazy

- Jaké jsou celkové cíle implementace? Je důležitý úplný inventář a správná mapa sítě?

- Existují v ICS více nebo redundantní sítě? Jsou všechny sítě monitorovány?

- Existují komunikace mezi službou ICS a podnikovou (firemní) sítí? Jsou tato komunikace monitorována?

- Jsou sítě VLAN nakonfigurované v návrhu sítě?

- Jak se provádí údržba služby ICS s pevnými nebo přechodnými zařízeními?

- Kde jsou brány firewall nainstalované ve sledovaných sítích?

- Je ve sledovaných sítích nějaké směrování?

- Jaké jsou protokoly ve sledovaných sítích aktivní?

- Pokud se připojíme k tomuto přepínači, uvidíme komunikaci mezi HMI a PLCs?

- Jaká je fyzická vzdálenost mezi přepínači ICS a podnikovou bránou firewall? 

- Můžou být nespravované přepínače nahrazené spravovanými přepínači, nebo pokud se jedná o možnost použití sítě?

- Je v síti nějaká sériová komunikace? Pokud ano, zobrazte ho v diagramu sítě.

- Pokud se k tomuto přepínači má připojit program Defender pro IoT, je v něm k dispozici fyzické místo v racku?

#### <a name="other-considerations"></a>Další důležité informace

Účelem programu Defender pro zařízení IoT je monitorování provozu z vrstev 1 a 2.

U některých architektur bude program Defender pro zařízení IoT monitorovat také vrstvu 3, pokud na této vrstvě existuje přenos. Když kontrolujete architekturu lokality a rozhodujete, jestli chcete monitorovat přepínač, vezměte v úvahu následující proměnné:

- Jaké jsou náklady a přínosy oproti důležitosti monitorování tohoto přepínače?

- Pokud je přepínač nespravovaný, bude možné monitorovat provoz z přepínače vyšší úrovně?

  Pokud je architektura ICS cyklickou topologií, je nutné monitorovat pouze jeden přepínač v tomto okruhu.

- Jaké je zabezpečení nebo provozní riziko v této síti?

- Je možné monitorovat síť VLAN přepínače? Je síť VLAN viditelná v jiném přepínači, který můžeme monitorovat?

#### <a name="technical-validation"></a>Technické ověření

Získání ukázky zaznamenaného provozu (soubor PCAP) z portu přepínače s ROZSAHem (neboli zrcadlového) může přispět k těmto akcím:

- Ověří, jestli je přepínač správně nakonfigurovaný.

- Ověřte, zda je přenos, který prochází přepínačem, relevantní pro monitorování (přenos).

- Identifikujte šířku pásma a odhadovaný počet zařízení v tomto přepínači.

Ukázkový soubor PCAP (několik minut) můžete zaznamenat připojením přenosného počítače k již nakonfigurovanému portu SPAN prostřednictvím aplikace Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Snímek obrazovky přenosného počítače připojeného k portu SPAN":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Snímek obrazovky záznamu ukázkového souboru PCAP.":::

#### <a name="wireshark-validation"></a>Ověření Wireshark

- Ověřte, že jsou *pakety jednosměrového vysílání* přítomny při zaznamenávání provozu. Jednosměrná vysílání je z jedné adresy na druhou. Pokud je většina přenosů zprávy protokolu ARP, není nastavení přepínače správné.

- Přejít na   >  **hierarchii protokolu** statistiky. Ověřte, že jsou k dispozici protokoly průmyslových OT.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Snímek obrazovky s ověřováním Wireshark":::

## <a name="troubleshooting"></a>Řešení potíží

Pro řešení potíží použijte tyto části:

- [Nejde se připojit pomocí webového rozhraní.](#cant-connect-by-using-a-web-interface)

- [Zařízení neodpovídá.](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Nejde se připojit pomocí webového rozhraní.

1. Ověřte, že se počítač, ke kterému se pokoušíte připojit, nachází ve stejné síti jako zařízení.

2. Ověřte, zda je síť grafického uživatelského rozhraní připojena k portu pro správu na senzoru.

3. Odešlete žádost o IP adresu zařízení. Pokud není k dispozici žádná odpověď na příkazy pro odeslání:

    1. Připojte monitor a klávesnici k zařízení.

    1. K přihlášení použijte uživatele **podpory** a heslo.

    1. Aktuální IP adresu zobrazíte pomocí **seznamu síť** příkazů.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Snímek obrazovky s příkazem seznam sítí":::

4. Pokud jsou parametry sítě nesprávně nakonfigurované, změňte je pomocí následujícího postupu:

    1. Použijte příkaz **Upravit nastavení sítě**.

    1. Pokud chcete změnit IP adresu sítě pro správu, vyberte **Y**.

    1. Chcete-li změnit masku podsítě, vyberte **Y**.

    1. Pokud chcete změnit DNS, vyberte **Y**.

    1. Pokud chcete změnit výchozí IP adresu brány, vyberte **Y**.

    1. Pro změnu vstupního rozhraní (jenom pro senzor) vyberte **Y**.

    1. V poli rozhraní mostu vyberte **N**.

    1. Chcete-li použít nastavení, vyberte **Y**.

5. Po restartování se připojte pomocí podpory uživatele a pomocí příkazu **seznam sítí** ověřte, zda byly parametry změněny.

6. Zkuste znovu provést test a znovu se připojte z grafického uživatelského rozhraní.

### <a name="appliance-is-not-responding"></a>Zařízení neodpovídá.

1. Připojte se pomocí monitoru a klávesnice k zařízení nebo pomocí výstupu se připojte vzdáleně k rozhraní příkazového řádku.

2. Přihlaste se pomocí přihlašovacích údajů podpory.

3. Použijte příkaz **System správnosti** a ověřte, zda jsou spuštěné všechny procesy.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Snímek obrazovky s příkazem System správnosti":::

V případě jakýchkoli dalších problémů kontaktujte [Podpora Microsoftu](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Příklad knihy webů

Použijte ukázkovou knihu webů k načtení a kontrole důležitých informací, které potřebujete pro instalaci sítě.

### <a name="site-checklist"></a>Kontrolní seznam webu

Zkontrolujte tento seznam před nasazením lokality:

| **#** | **Úkol nebo aktivita** | **Stav** | **Komentáře** |
|--|--|--|--|
| 1 | Objednat zařízení. | ☐ |  |
| 2 | Připravte seznam podsítí v síti. | ☐ |  |
| 3 | Zadejte seznam sítí VLAN pro produkční sítě. | ☐ |  |
| 4 | Zadejte seznam modelů přepínače v síti. | ☐ |  |
| 5 | Poskytněte seznam dodavatelů a protokolů průmyslového vybavení. | ☐ |  |
| 6 | Zadejte podrobnosti sítě pro senzory (IP adresa, podsíť, D-GS, DNS). | ☐ |  |
| 7 | Vytvořte nezbytná pravidla brány firewall a seznam přístupu. | ☐ |  |
| 8 | Vytvořte pokrývání portů na přepínačích pro monitorování portů nebo nakonfigurujte síťové vypínání podle potřeby. | ☐ |  |
| 9 | Příprava prostoru stojanu pro zařízení senzorů. | ☐ |  |
| 10 | Připravte pracovní stanici pro pracovníky. | ☐ |  |
| 11 | Poskytněte klávesnici, monitor a myš pro program Defender pro zařízení se systémem IoT rack. | ☐ |  |
| 12 | Stojan a kabely zařízení. | ☐ |  |
| 13 | Přidělte prostředky lokality pro podporu nasazení. | ☐ |  |
| 14 | Vytvořte skupiny služby Active Directory nebo místní uživatele. | ☐ |  |
| 15 | Nastavení školení (samoobslužné vzdělávání). | ☐ |  |
| 16 | Přejít nebo No – přejít. | ☐ |  |
| 17 | Naplánování data nasazení. | ☐ |  |


| **Date** (Datum) | **Poznámka** | **Datum nasazení** | **Poznámka** |
|--|--|--|--|
| Defender pro IoT |  | Název lokality * |  |
| Název |  | Název |  |
| Pozice |  | Pozice |  |

#### <a name="architecture-review"></a>Přezkoumání architektury

Přehled diagramu průmyslové sítě vám umožní definovat vhodné umístění pro program Defender pro zařízení IoT.

1.  Zobrazení globálního diagramu sítě prostředí v průmyslu Například:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagram prostředí průmyslových prostředí pro globální síť":::

    > [!NOTE]
    > Program Defender pro zařízení IoT by měl být připojen k přepínači nižší úrovně, který prohlíží přenos mezi porty na přepínači.  

2. Zadejte přibližný počet síťových zařízení, která se budou monitorovat. Tyto informace budete potřebovat při připojování předplatného k portálu Azure Defender pro IoT Portal. Během procesu připojování budete vyzváni k zadání počtu zařízení v přírůstcích po 1000.

3. Zadejte seznam podsítí pro produkční sítě a popis (volitelné). 

    |  **#**  | **Název podsítě** | **Popis** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Zadejte seznam sítí VLAN pro produkční sítě.

    | **#** | **Název sítě VLAN** | **Popis** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Chcete-li ověřit, zda mají přepínače schopnost zrcadlení portů, zadejte čísla modelů přepínačů, ke kterým se má platforma Defender for IoT připojit:

    | **#** | **Přepínač** | **Modelování** | **Podpora zrcadlení přenosů (SPAN, RSPAN nebo None)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Spravuje tento přepínač třetí strana? A a N 

    Pokud ano, kdo? __________________________________ 

    Co je jejich zásada? __________________________________ 

    Například:

    - Siemens

    - Automatizace služby Rockwell – Ethernet nebo IP

    - Společností Emerson – DeltaV, Ovation
    
6. Existují zařízení, která komunikují prostřednictvím sériového připojení v síti? Ano nebo Ne 

    Pokud ano, zadejte, který sériový komunikační protokol: ________________ 

    Pokud ano, označte v diagramu sítě, která zařízení komunikuje se sériovými protokoly a kde jsou: 
 
    <Add your network diagram with marked serial connection> 

7. V případě technologie QoS je výchozí nastavení snímače 1,5 MB/s. Určete, zda jej chcete změnit: ________________ 

   Business Unit (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Specifikace pro vybavení lokality

#### <a name="network"></a>Síť  

Zařízení snímače je připojené k přepínači portu SPAN přes síťový adaptér. Je připojený k podnikové síti zákazníka za účelem správy prostřednictvím jiného vyhrazeného síťového adaptéru.

Zadejte podrobnosti adresy pro síťovou kartu senzoru, která bude připojena k podnikové síti: 

|  Položka               | Zařízení 1 | Zařízení 2 | Zařízení 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP adresa zařízení    |               |               |               |
| Podsíť          |               |               |               |
| Výchozí brána |               |               |               |
| DNS             |               |               |               |
| Název hostitele       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/MOP/Server Management

|       Položka          | Zařízení 1 | Zařízení 2 | Zařízení 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP adresa zařízení     |               |               |               |
| Podsíť          |               |               |               |
| Výchozí brána |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Místní Konzola pro správu  

|       Položka          | Aktivní | Pasivní (při použití HA) |
| --------------- | ------ | ----------------------- |
| IP adresa             |        |                         |
| Podsíť          |        |                         |
| Výchozí brána |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Položka              | Podrobnosti |
| --------------- | ------ |
| IP adresa              |        |
| IP adresa | |
| Uživatelské jméno | |
| Heslo | |
| Typ ověřování | MD5 nebo SHA |
| Šifrování | DES nebo AES |
| Tajný klíč | |
| Řetězec komunity SNMP v2 |

### <a name="on-premises-management-console-ssl-certificate"></a>Certifikát SSL místní konzoly pro správu

Plánujete použít certifikát SSL? Ano nebo Ne

Pokud ano, jakou službu použijete k jejímu vygenerování? Jaké atributy budete do certifikátu zahrnovat (například doména nebo IP adresa)?

### <a name="smtp-authentication"></a>Ověřování SMTP

Plánujete používat protokol SMTP k přeposílání výstrah na e-mailový server? Ano nebo Ne

Pokud ano, jakou metodu ověřování budete používat?  

### <a name="active-directory-or-local-users"></a>Služba Active Directory nebo místní uživatelé

Obraťte se na správce služby Active Directory a vytvořte skupinu uživatelů lokality služby Active Directory nebo vytvořte místní uživatele. Ujistěte se, že jsou vaši uživatelé připraveni na den nasazení. 

### <a name="iot-device-types-in-the-network"></a>Typy zařízení IoT v síti

| Typ zařízení | Počet zařízení v síti | Průměrná šířka pásma |
| --------------- | ------ | ----------------------- |
| Camera | |
| Rentgenový počítač | |

## <a name="see-also"></a>Viz také

[O instalaci Defenderu pro IoT](how-to-install-software.md)
