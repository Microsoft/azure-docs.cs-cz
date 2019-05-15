---
title: Jak dotazu protokolů ze služby Azure Monitor pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Azure Monitor pro virtuální počítače řešení shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje ukázkové dotazy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: bca1b96e7dc5673cabef26fe6b2cfb8daa41fbf5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702520"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Jak provádět dotazy protokolů ze služby Azure Monitor pro virtuální počítače (preview)
Azure Monitor pro virtuální počítače shromažďuje výkonu a metrik připojení, počítače a zpracování dat inventáře a informace o stavu a předá ji do pracovního prostoru Log Analytics ve službě Azure Monitor.  Tato data jsou k dispozici pro [dotazu](../../azure-monitor/log-query/log-query-overview.md) ve službě Azure Monitor. Tato data můžete použít scénáře, které zahrnují plánování migrace, kapacitu analýza, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="map-records"></a>Záznamy mapy
Jeden záznam se vygeneruje za hodinu pro každý počítač jedinečné a proces, kromě záznamy, které jsou generovány, pokud proces nebo počítače spustí nebo je zprovozněný do Azure monitoru pro funkci mapování virtuálních počítačů. Tyto záznamy mají vlastnosti v následujících tabulkách. Pole a hodnoty v událostech ServiceMapComputer_CL mapují na pole počítače zdroje v rozhraní API Azure Resource Manageru ServiceMap. Pole a hodnoty v událostech ServiceMapProcess_CL mapují na pole v rozhraní API Azure Resource Manageru ServiceMap prostředku procesu. Pole ResourceName_s odpovídá poli Název odpovídající prostředku Resource Manageru. 

Existují interně vygenerovanému vlastnosti, které můžete použít k identifikaci jedinečný procesy a počítače:

- Počítač: Použití *ResourceId* nebo *ResourceName_s* k jednoznačné identifikaci počítače v rámci pracovního prostoru Log Analytics.
- Proces: Použití *ResourceId* k jednoznačné identifikaci procesu v rámci pracovního prostoru Log Analytics. *ResourceName_s* je jedinečný v rámci počítače, na kterém je proces spuštěn (MachineResourceName_s) 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozmezí může existovat více záznamů, dotazy mohou vracet víc než jeden záznam pro stejný počítač nebo procesu. Chcete-li zahrnout pouze poslední záznam, přidejte "| Při odstraňování duplicitních dat ResourceId"v dotazu.

### <a name="connections-and-ports"></a>Připojení a porty
Metrik připojení funkce uvádí dvou nových tabulek v Azure Monitor protokoly – VMConnection a VMBoundPort. Tyto tabulky obsahují informace o připojení pro počítač (příchozí a odchozí), stejně jako server porty, které jsou open/aktivní s nimi. ConnectionMetrics jsou přístupné také prostřednictvím rozhraní API, která poskytují způsob, jak získat určité metriky během časového intervalu. Připojení TCP vyplývající z *přijímá* naslouchání soketu se příchozí při vytvořených *připojení* k dané IP adresy a portu jsou odchozí. Směr připojení je reprezentována vlastnost směr, který může být nastaven na hodnotu **příchozí** nebo **odchozí**. 

Z data, která agenta závislostí se generují záznamy v těchto tabulkách. Každý záznam představuje hodnotu za interval 1 minuta čas. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace k identifikaci příslušné entity, to znamená, připojení nebo port, jakož i metriky, které jsou přidružené k dané entitě. V současné době se hlásí pouze síťové aktivity, ke které dojde, pomocí protokolu TCP přes protokol IPv4. 

#### <a name="common-fields-and-conventions"></a>Společné pole a konvence 
VMConnection a VMBoundPort platí následující pole a konvence: 

- Počítač: Plně kvalifikovaný název domény počítače generování sestav 
- ID agenta: Jedinečný identifikátor pro počítač pomocí agenta Log Analytics  
- Machine: Název prostředku Azure Resource Manageru pro počítač vystavené ServiceMap. Je ve formátu *m-{GUID}*, kde *GUID* je stejný identifikátor GUID jako ID agenta  
- Proces: Název prostředku Azure Resource Manageru pro proces vystavené ServiceMap. Je ve formátu *p-{hexadecimální řetězec}*. Proces je jedinečný v rámci oboru počítače a ke generování ID procesu jedinečný mezi počítači, kombinovat pole počítače a procesu. 
- Název procesu: Název spustitelného souboru procesu vytváření sestav.
- Všechny IP adresy jsou řetězce v kanonickém formátu IPv4, například *13.107.3.160* 

Pokud chcete spravovat náklady a složitost, záznamy o připojení nepředstavují jednotlivých fyzických síťových připojení. Víc fyzických síťových připojení jsou seskupeny do logických připojení, který je pak v příslušné tabulce.  Význam, zaznamená *VMConnection* tabulce představují logické seskupení a nikoli jednotlivé fyzické připojení, která jsou sledována. Fyzické připojení sdílejí stejnou hodnotu pro následující atributy během danému intervalu jedné minuty se agregují do jednoho logického záznamu v *VMConnection*. 

| Vlastnost | Description |
|:--|:--|
|Direction |Směr připojení, je hodnota *příchozí* nebo *odchozí* |
|Machine |Plně kvalifikovaný název domény počítače |
|Proces |Identita procesu nebo skupin procesů, inicializace a přijímá připojení |
|SourceIp |IP adresa zdroje |
|DestinationIp |Cílové IP adresy |
|DestinationPort |Číslo portu cíle |
|Protocol |Protokol použitý pro připojení.  Hodnoty *tcp*. |

Aby se zohlednily dopadu seskupení, informace o počtu seskupených fyzických připojení najdete v následující vlastnosti záznamu:

| Vlastnost | Description |
|:--|:--|
|LinksEstablished |Počet fyzických síťových připojení, které se vytvořily časovém období vytváření sestav |
|LinksTerminated |Počet fyzických síťových připojení, která byla ukončena časovém období vytváření sestav |
|LinksFailed |Počet fyzických síťových připojení, které selhaly časovém období vytváření sestav. Tyto informace jsou aktuálně k dispozici pouze pro odchozí připojení. |
|LinksLive |Počet fyzických síťových připojení, které se otevřelo na konci generování sestav časový interval|

#### <a name="metrics"></a>Metriky

Kromě metrik počet připojení informace o objemu dat odeslaných a přijatých na dané logické propojení nebo síťový port jsou také uvedené v následující vlastnosti záznamu:

| Vlastnost | Description |
|:--|:--|
|BytesSent |Celkový počet bajtů, které byly odeslány časovém období vytváření sestav |
|BytesReceived |Celkový počet bajtů, které byly přijaty časovém období vytváření sestav |
|Odezvy |Počet odpovědí zjištěnými časovém období vytváření sestav. 
|ResponseTimeMax |Maximální doba odezvy (milisekundy) zjištěnými časovém období vytváření sestav. Pokud žádná hodnota vlastnosti je prázdná.|
|ResponseTimeMin |Minimální doba odezvy (milisekundy) zjištěnými časovém období vytváření sestav. Pokud žádná hodnota vlastnosti je prázdná.|
|ResponseTimeSum |Součet všech doby odezvy (milisekundy) zjištěnými časovém období vytváření sestav. Pokud žádná hodnota vlastnosti je prázdná.|

Doba odezvy je třetí typ dat ohlašovaný – jak dlouho volající věnovat časový limit na žádosti zaslané prostřednictvím připojení ke zpracování a reagovalo oddělení vzdálený koncový bod. Doba odezvy hlášené je odhad doby odezvy true na základním protokolu aplikace. To je vypočítán s použitím heuristické metody založené na sledování tok dat mezi zdrojovou a cílovou konec připojení k fyzické síti. Koncepčně je rozdíl mezi časem poslední bajt požadavku opouští odesílatele a čas při přijetí posledního bajtu odpovědi k němu. Tyto dva časové razítko se používají od sebe odděluje událostí žádostí a odpovědí na jedno fyzické připojení. Rozdíl mezi nimi představuje doba odezvy jedné žádosti. 

V tomto prvním vydání této funkce je naše algoritmus přibližný, které může fungovat pro různé míře úspěšnosti v závislosti na skutečné aplikace protokol použitý pro připojení k dané síti. Například aktuální přístup funguje dobře pro žádost odpověď na základě protokolů jako jsou HTTP (S), ale pomocí jednosměrné nebo nefunguje protokolů založených na frontě zpráv.

Tady jsou některé důležité body ke zvážení:

1. Pokud proces akceptuje připojení na stejnou IP adresu, ale přes několik síťových rozhraní, uvádět bude samostatný záznam pro každé rozhraní. 
2. Záznamů se zástupnými znaky IP bude obsahovat žádná aktivita. Jsou zahrnuty představují skutečnost, že port na počítači je otevřená pro příchozí provoz.
3. Pokud chcete snížit úroveň podrobností a objem dat, záznamů se zástupnými znaky IP budou vypuštěny po odpovídající záznam (pro stejný proces, port a protokol) s konkrétní IP adresu. Pokud záznam IP zástupných znaků je vynechán, vlastnost záznamu IsWildcardBind s konkrétní IP adresu, bude nastavena na hodnotu "True" k označení, že port, který je přístupný přes každých rozhraní vytváření sestav počítačů.
4. Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na *False*.

#### <a name="naming-and-classification"></a>Zásady vytváření názvů a klasifikace
Pro usnadnění práce IP adresu ke konci vzdáleného připojení je součástí RemoteIp vlastnost. Pro příchozí připojení, RemoteIp je stejný jako SourceIp, zatímco pro odchozí připojení, je stejný jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje hlášených počítači pro RemoteIp canonical názvy DNS. Vlastnosti RemoteDnsQuestions a RemoteClassification jsou vyhrazené pro budoucí použití. 

#### <a name="geolocation"></a>Zeměpisná poloha
*VMConnection* obsahuje také informace o zeměpisné poloze informace o vzdáleným koncem záznamech připojení ve vlastnostech následující záznam: 

| Vlastnost | Description |
|:--|:--|
|RemoteCountry |Název země hostování RemoteIp.  Například *USA* |
|RemoteLatitude |Zeměpisná poloha, zeměpisná šířka. Například *47.68* |
|RemoteLongitude |Informace o zeměpisné poloze délky. Například *-122.12* |

#### <a name="malicious-ip"></a>Škodlivá IP adresa
Každá vlastnost RemoteIp v *VMConnection* tabulky je porovnávána s sadu IP adres pomocí známých škodlivých aktivit. Pokud se zjistí, RemoteIp jako škodlivý naplní se následující vlastnosti (jsou prázdné, pokud IP adresa se považuje za škodlivou) v záznamu následující vlastnosti:

| Vlastnost | Description |
|:--|:--|
|MaliciousIp |Vzdálená adresa IP adres |
|IndicatorThreadType |Indikátor hrozeb zjistila je jeden z následujících hodnot *Botnet*, *C2*, *CryptoMining*, *Darknet*, *před útoky DDos* , *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Seznamu ke zhlédnutí*.   |
|Description |Popis zjištěných hrozeb. |
|TLPLevel |Úroveň protokolu semaforu (algoritmus TLP) je jedna z definovaných hodnot *prázdné*, *zelená*, *žlutou*, *Red*. |
|Confidence |Hodnoty jsou *0 – 100*. |
|Severity |Hodnoty jsou *0 – 5*, kde *5* je nejzávažnější a *0* není natolik vůbec. Výchozí hodnota je *3*.  |
|FirstReportedDateTime |První zprostředkovatel ohlásil indikátoru. |
|LastReportedDateTime |Čas posledního ukazatele viděla Interflow. |
|IsActive |Označuje deaktivují se s indikátory *True* nebo *False* hodnotu. |
|ReportReferenceLink |Obsahuje odkazy na sestavy související se daný pozorovat. |
|AdditionalInformation |Poskytuje další informace, pokud je k dispozici informace o zjištěných hrozeb. |

### <a name="ports"></a>Porty 
Porty na počítači, které aktivně přijímat příchozí provoz nebo potenciálně může přijímat provoz, ale jsou nečinné generování sestav časovém období se zapisují do tabulky VMBoundPort.  

>[!NOTE]
>Azure Monitor pro virtuální počítače se nepodporuje, shromažďování a zaznamenávání dat portu v pracovním prostoru Log Analytics v těchto oblastech:  
>- USA – východ  
>- Západní Evropa
>
> Shromažďování těchto dat je povolená v jiném [podporované oblasti](vminsights-onboard.md#log-analytics) pro monitorování Azure pro virtuální počítače. 

Každý záznam v VMBoundPort je identifikován následující pole: 

| Vlastnost | Description |
|:--|:--|
|Proces | Identita procesu (nebo skupiny procesů) se kterými port, který je přidružen.|
|Ip | Port IP adresa (může být zástupný znak IP *0.0.0.0*) |
|Port |Číslo portu |
|Protocol | Protokol.  Například *tcp* nebo *udp* (pouze *tcp* momentálně se podporuje).|
 
Identita port je odvozen z výše uvedených pěti oblastech a je uložená ve vlastnosti identifikátor PortId. Tato vlastnost umožňuje rychle vyhledat záznamy specifického portu v čase. 

#### <a name="metrics"></a>Metriky 
Port záznamy zahrnout metriky představující připojení k nim má přiřazené. V současné době se tyto metriky se vykazují (podrobnosti pro jednotlivé metriky jsou popsané v předchozí části): 

- BytesSent a BytesReceived 
- LinksLive LinksEstablished LinksTerminated, 
- ResposeTime ResponseTimeSum ResponseTimeMin ResponseTimeMax, 

Tady jsou některé důležité body ke zvážení:

- Pokud proces akceptuje připojení na stejnou IP adresu, ale přes několik síťových rozhraní, uvádět bude samostatný záznam pro každé rozhraní.  
- Záznamů se zástupnými znaky IP bude obsahovat žádná aktivita. Jsou zahrnuty představují skutečnost, že port na počítači je otevřená pro příchozí provoz. 
- Pokud chcete snížit úroveň podrobností a objem dat, záznamů se zástupnými znaky IP budou vypuštěny po odpovídající záznam (pro stejný proces, port a protokol) s konkrétní IP adresu. Pokud záznam IP zástupných znaků je vynechán, *IsWildcardBind* vlastnost pro záznam s konkrétní IP adresu, bude nastavena na *True*.  To znamená, že port, který je přístupný přes každých rozhraní vytváření sestav počítačů. 
- Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na *False*. 

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL records
Záznamy typu *ServiceMapComputer_CL* mít data inventáře pro servery s agenta závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Description |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Jedinečný identifikátor pro počítač v pracovním prostoru |
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
Záznamy typu *ServiceMapProcess_CL* mít data inventáře pro procesy připojené protokolem TCP na serverech s agenta závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Description |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Jedinečný identifikátor procesu v rámci pracovního prostoru |
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
| ExecutablePath_s | Cesta ke spustitelnému souboru |
| WorkingDirectory_s | Pracovní adresář |
| Uživatelské jméno | Účet, pod kterým je spuštěn proces |
| UserDomain | Domény, pod kterým je spuštěn proces |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

### <a name="list-all-known-machines"></a>Seznam všech známých počítačů
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>Pokud byl virtuální počítač poslední restartovat
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Přehled virtuálních počítačů Azure pomocí bitové kopie, umístění a skladové položky
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Seznam kapacita fyzické paměti všech spravovaných počítačů.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Název počítače seznamu, DNS, IP a operačního systému.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najít všechny procesy s "sql" v příkazovém řádku
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Najít počítač (poslední záznam) podle názvu prostředku
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Najít počítač (poslední záznam) podle IP adresy
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Seznam všech známých procesů v zadaném počítači
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Seznam všech počítačů s SQL serverem
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Zobrazí seznam všech verzí produktu jedinečné nástroje curl do svého datacentra
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvořit skupinu počítačů všechny počítače se systémem CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Bajty odeslané a přijaté trendů
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Které virtuální počítače Azure na maximum bajtů přenosu
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Trendy stavu propojení
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Trend selhání připojení
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Vázané porty
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Počet otevřených portů v počítačích
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Skóre procesů ve vašem pracovním prostoru podle čísla portů, které se mají otevřít
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Agregační chování pro každý z portů
Tento dotaz lze potom použít ke stanovení skóre porty podle počtu aktivit, například portů se většina odchozího/příchozího provozu, porty se většina připojení
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Shrnutí odchozí připojení ze skupiny počítačů
```kusto
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

## <a name="next-steps"></a>Další postup
* Pokud jste ještě psaní dotazů protokolu ve službě Azure Monitor, zkontrolujte [tom, jak používat službu Log Analytics](../../azure-monitor/log-query/get-started-portal.md) na webu Azure Portal k vytváření dotazů protokolu.
* Další informace o [zápis vyhledávací dotazy](../../azure-monitor/log-query/search-queries.md).
