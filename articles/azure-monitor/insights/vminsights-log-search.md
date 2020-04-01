---
title: Jak dotazovat protokoly z Azure Monitor pro virtuální počítače
description: Azure Monitor pro virtuální počítače řešení shromažďuje metriky a data protokolu a tento článek popisuje záznamy a zahrnuje ukázkové dotazy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396925"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Jak dotazovat protokoly z Azure Monitor u virtuálních počítačů

Azure Monitor pro virtuální počítače shromažďuje metriky výkonu a připojení, data inventáře počítačů a procesů a informace o stavu a předává je do pracovního prostoru Log Analytics ve službě Azure Monitor.  Tato data jsou dostupná pro [dotaz](../../azure-monitor/log-query/log-query-overview.md) v Azure Monitoru. Tato data můžete použít na scénáře, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="map-records"></a>Mapování záznamů

Jeden záznam se generuje za hodinu pro každý jedinečný počítač a proces, kromě záznamů, které jsou generovány při spuštění procesu nebo počítače nebo je na palubě azure monitor u virtuálních počítačů funkce. Tyto záznamy mají vlastnosti v následujících tabulkách. Pole a hodnoty v ServiceMapComputer_CL událostí se mapují na pole prostředku počítače v rozhraní API ServiceMap Azure Resource Manager. Pole a hodnoty v ServiceMapProcess_CL událostí mapovat na pole prostředku procesu v rozhraní API ServiceMap Azure Resource Manager. Pole ResourceName_s odpovídá poli názvu v odpovídajícím zdroji Správce zdrojů. 

Existují interně generované vlastnosti, které můžete použít k identifikaci jedinečných procesů a počítačů:

- Počítač: Pomocí *resourceid* nebo *ResourceName_s* jednoznačně identifikovat počítač v pracovním prostoru Analýzy protokolů.
- Proces: Pomocí *resourceid* jednoznačně identifikovat proces v rámci pracovního prostoru Log Analytics. *ResourceName_s* je jedinečný v kontextu počítače, na kterém je proces spuštěn (MachineResourceName_s) 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozsahu může existovat více záznamů, mohou dotazy vrátit více než jeden záznam pro stejný počítač nebo proces. Chcete-li zahrnout pouze nejnovější `| summarize arg_max(TimeGenerated, *) by ResourceId` záznam, přidejte do dotazu.

### <a name="connections-and-ports"></a>Připojení a porty

Funkce Metriky připojení zavádí dvě nové tabulky v protokolech Azure Monitor – VMConnection a VMBoundPort. Tyto tabulky obsahují informace o připojení pro počítač (příchozí a odchozí), stejně jako porty serveru, které jsou otevřené nebo aktivní na nich. ConnectionMetrics jsou také vystaveny prostřednictvím api, které poskytují prostředky k získání konkrétní metriky během časového období. Připojení TCP vyplývající z *přijetí* na naslouchání soketu jsou příchozí, zatímco připojení *k* dané IP a portu jsou odchozí. Směr připojení je reprezentován Direction vlastnost, která může být nastavena na **příchozí** nebo **odchozí**. 

Záznamy v těchto tabulkách jsou generovány z dat vykazovaných agentem závislostí. Každý záznam představuje pozorování v časovém intervalu 1 minuty. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace k identifikaci příslušné entity, tedy připojení nebo portu, stejně jako metriky přidružené k této entitě. V současné době je hlášena pouze síťová aktivita, ke které dochází pomocí protokolu TCP přes IPv4. 

#### <a name="common-fields-and-conventions"></a>Společné oblasti a konvence 

Následující pole a konvence platí pro připojení VMa i port VMBoundPort: 

- Počítač: Plně kvalifikovaný název domény vykazovacího počítače 
- AgentId: Jedinečný identifikátor pro počítač s agentem Analýzy protokolů  
- Počítač: Název prostředku Azure Resource Manager pro počítač vystavený ServiceMap. Jedná se o formulář *m-{GUID}*, kde *identifikátor GUID* je stejný identifikátor GUID jako AgentId  
- Proces: Název prostředku Azure Resource Manager pro proces vystavený ServiceMap. Je ve tvaru *p-{hex string}*. Proces je jedinečný v rámci rozsahu počítače a generovat jedinečné ID procesu mezi počítači, kombinovat pole stroje a proces. 
- ProcessName: Název spustitelného souboru procesu vykazování.
- Všechny adresy IP jsou řetězce v kanonickém formátu IPv4, například *13.107.3.160* 

Chcete-li spravovat náklady a složitost, záznamy připojení nepředstavují jednotlivá fyzická síťová připojení. Více fyzických síťových připojení je seskupeno do logického připojení, které se pak projeví v příslušné tabulce.  To znamená, že záznamy v tabulce *VMConnection* představují logické seskupení a nikoli jednotlivá fyzická připojení, která jsou sledována. Fyzické síťové připojení sdílející stejnou hodnotu pro následující atributy během daného intervalu jedné minuty jsou agregovány do jednoho logického záznamu v *programu VMConnection*. 

| Vlastnost | Popis |
|:--|:--|
|Směr |Směr připojení, hodnota je *příchozí* nebo *odchozí* |
|Počítač |Hlavní název fqdn počítače |
|Proces |Identita procesu nebo skupin procesů, zahájení/přijetí připojení |
|ZdrojIp |IP adresa zdroje |
|DestinationIp |IP adresa cíle |
|Cílový port |Číslo portu cíle |
|Protocol (Protokol) |Protokol používaný pro připojení.  Hodnoty je *tcp*. |

Chcete-li zohlednit dopad seskupení, informace o počtu seskupených fyzických připojení jsou uvedeny v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
|LinksEstablishedEstablished |Počet fyzických síťových připojení, která byla navázána během časového období hlášení |
|Odkazyukončené |Počet fyzických síťových připojení, která byla ukončena během časového období hlášení |
|Odkazy se nezdařily. |Počet fyzických síťových připojení, která selhala během časového období vykazování. Tyto informace jsou v současné době k dispozici pouze pro odchozí připojení. |
|OdkazyLive |Počet fyzických síťových připojení, která byla otevřena na konci časového období hlášení|

#### <a name="metrics"></a>Metriky

Kromě metrik počtu připojení jsou informace o objemu dat odeslaných a přijatých na daném logickém připojení nebo síťovém portu zahrnuty také v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
|BajtůOdeslání |Celkový počet bajtů odeslaných během časového období vykazování |
|Přijaté bajty |Celkový počet bajtů, které byly přijaty během časového období vykazování |
|Odezvy |Počet odpovědí pozorovaných během časového období hlášení. 
|ResponseTimeMax |Největší doba odezvy (milisekundy) pozorovaná během časového období hlášení. Pokud žádná hodnota, vlastnost je prázdná.|
|ResponseTimeMin |Nejmenší doba odezvy (milisekundy) pozorovaná během časového období hlášení. Pokud žádná hodnota, vlastnost je prázdná.|
|ResponseTimeSum |Součet všech časů odezvy (milisekund) pozorovaných během časového období hlášení. Pokud žádná hodnota, vlastnost je prázdná.|

Třetí typ dat, které jsou hlášeny je doba odezvy – jak dlouho volající tráví čekání na požadavek odeslaný přes připojení, které mají být zpracovány a odpověděl vzdálenékoncového bodu. Hlášená doba odezvy je odhad skutečné doby odezvy základního aplikačního protokolu. Vypočítává se pomocí heuristiky založené na pozorování toku dat mezi zdrojovým a cílovým koncem fyzického síťového připojení. Koncepčně je rozdíl mezi čas poslední bajt požadavku opustí odesílatele a čas, kdy poslední bajt odpovědi dorazí zpět k němu. Tato dvě časová razítka se používají k vymezení událostí požadavku a odpovědi na daném fyzickém připojení. Rozdíl mezi nimi představuje dobu odezvy jednoho požadavku. 

V této první verzi této funkce je náš algoritmus aproximací, která může pracovat s různým stupněm úspěchu v závislosti na skutečném aplikačním protokolu používaném pro dané síťové připojení. Například aktuální přístup funguje dobře pro protokoly založené na odezvě na požadavcích, jako je HTTP(S), ale nefunguje s jednosměrnými protokoly nebo protokoly založené na frontě zpráv.

Zde jsou některé důležité body, které je třeba zvážit:

1. Pokud proces přijímá připojení na stejné adrese IP, ale přes více síťových rozhraní, bude hlášen samostatný záznam pro každé rozhraní. 
2. Záznamy se zástupným znakem IP nebudou obsahovat žádnou aktivitu. Jsou zahrnuty představují skutečnost, že port v počítači je otevřen pro příchozí přenosy.
3. Chcete-li snížit podrobnost a objem dat, záznamy se zástupným znakem IP budou vynechány, pokud existuje odpovídající záznam (pro stejný proces, port a protokol) s konkrétní adresou IP. Pokud je vynechán zástupný záznam IP, iswildcardbind záznam vlastnost s konkrétní IP adresu, bude nastavena na "True" označuje, že port je vystaven a každé rozhraní stroje pro vykazování.
4. Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na *False*.

#### <a name="naming-and-classification"></a>Pojmenování a klasifikace

Pro větší pohodlí je ip adresa vzdáleného konce připojení zahrnuta ve vlastnosti RemoteIp. Pro příchozí připojení RemoteIp je stejný jako SourceIp, zatímco pro odchozí připojení je stejný jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje kanonické názvy DNS hlášené počítačem pro RemoteIp. RemoteDnsQuestions a RemoteClassification vlastnosti jsou vyhrazeny pro budoucí použití. 

#### <a name="geolocation"></a>Geografická poloha

*Připojení VMConnection* také obsahuje informace o geografické poloze pro vzdálený konec každého záznamu připojení v následujících vlastnostech záznamu: 

| Vlastnost | Popis |
|:--|:--|
|Vzdálená země |Název země nebo oblasti hostující RemoteIp.  Například *Spojené státy americké* |
|RemoteLatitude |Geolokační šířka. Například *47,68* |
|Vzdálená zeměpisná jižadlouhá |Geolokační longituda. Například *-122,12* |

#### <a name="malicious-ip"></a>Škodlivá adresa IP

Každá vlastnost RemoteIp v tabulce *VMConnection* je zkontrolována podle sady IP adres se známou škodlivou aktivitou. Pokud RemoteIp je identifikován jako škodlivý budou vyplněny následující vlastnosti (jsou prázdné, pokud IP není považován za škodlivý) v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
|ŠkodlivýIp |Adresa RemoteIp |
|Typ vlákna indikátoru |Indikátor ohrožení zjištěn je jedním z následujících hodnot, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Popis |Popis pozorované hrozby. |
|Úroveň TLP |Stupeň semaforu (TLP) Úroveň je jedna z definovaných hodnot, *bílá*, *zelená*, *žlutá*, *červená*. |
|Spolehlivost |Hodnoty jsou *0 – 100*. |
|Severity |Hodnoty jsou *0 – 5*, kde *5* je nejzávažnější a *0* není závažné vůbec. Výchozí hodnota je *3*.  |
|Doba prvního okamžiku |Při prvním oznamuji indikátor. |
|Doba posledního okamžiku |Při posledním pohledu byl indikátor viděn interflow. |
|Isactive |Označuje, že indikátory jsou deaktivovány hodnotou *True* nebo *False.* |
|Odkaz Na odkaz ReportReferenceLink |Odkazy na zprávy týkající se daného pozorovatelného. |
|Další informace |Obsahuje další informace o zjištěné hrozbě. |

### <a name="ports"></a>Porty 

Porty v počítači, které aktivně přijímají příchozí přenosy nebo mohou potenciálně přijímat přenosy, ale jsou nečinné během časového okna hlášení, jsou zapsány do tabulky VMBoundPort.  

Každý záznam v portu VMBoundPort je identifikován následujícími poli: 

| Vlastnost | Popis |
|:--|:--|
|Proces | Identita procesu (nebo skupiny procesů), ke kterým je port přidružen.|
|Ip | IP adresa portu (může být zástupná adresa IP, *0.0.0.0*) |
|Port |Číslo portu |
|Protocol (Protokol) | Protokol.  Například *tcp* nebo *udp* (aktuálně je podporován pouze *protokol TCP).*|
 
Identita portu je odvozen a z výše uvedených polí a je uložen v PortId vlastnost. Tuto vlastnost lze rychle najít záznamy pro konkrétní port v čase. 

#### <a name="metrics"></a>Metriky 

Záznamy portů zahrnují metriky představující připojení, která jsou k nim přidružena. V současné době jsou hlášeny následující metriky (podrobnosti pro každou metriku jsou popsány v předchozí části): 

- Přijaté bajty a bajty 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Zde jsou některé důležité body, které je třeba zvážit:

- Pokud proces přijímá připojení na stejné adrese IP, ale přes více síťových rozhraní, bude hlášen samostatný záznam pro každé rozhraní.  
- Záznamy se zástupným znakem IP nebudou obsahovat žádnou aktivitu. Jsou zahrnuty představují skutečnost, že port v počítači je otevřen pro příchozí přenosy. 
- Chcete-li snížit podrobnost a objem dat, záznamy se zástupným znakem IP budou vynechány, pokud existuje odpovídající záznam (pro stejný proces, port a protokol) s konkrétní adresou IP. Pokud je vynechán zástupný záznam IP, vlastnost *IsWildcardBind* pro záznam s konkrétní IP adresou bude nastavena na *hodnotu True*.  To znamená, že port je vystaven a to u každého rozhraní oznamovacího počítače. 
- Porty, které jsou vázány pouze na určité rozhraní mají IsWildcardBind nastavena na *False*. 

### <a name="vmcomputer-records"></a>Záznamy VMComputer

Záznamy s typem *Počítače VMComputer* obsahují data inventáře pro servery s agentem závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
|TenantId | Jedinečný identifikátor pracovního prostoru |
|SourceSystem | *Insights* | 
|TimeGenerated | Časové razítko záznamu (UTC) |
|Počítač | Hlavní název fqdn počítače | 
|AgentId | Jedinečné ID agenta analýzy protokolů |
|Počítač | Název prostředku Azure Resource Manager pro počítač vystavený ServiceMap. Je formuláře *m-{GUID}*, kde *identifikátor GUID* je stejný identifikátor GUID jako AgentId. | 
|DisplayName | Zobrazované jméno | 
|Název fulldisplay | Úplný zobrazovaný název | 
|HostName | Název počítače bez názvu domény |
|Zaváděcí čas | Doba spouštění stroje (UTC) |
|TimeZone | Normalizované časové pásmo |
|Virtualizační stav | *virtuální*, *hypervisor*, *fyzické* |
|Adresy Ipv4Adresy | Pole adres IPv4 | 
|Masky ipv4podsítě | Pole masek podsítě IPv4 (ve stejném pořadí jako Ipv4Addresses). |
|Standardní brány Ipv4 | Pole bran IPv4 | 
|Adresy Ipv6Adresy | Pole adres IPv6 | 
|MacAdresy | Pole MAC adres | 
|Názvy dns | Pole názvů DNS přidružených k počítači. |
|Verze DependencyAgentVersion | Verze agenta závislostí spuštěná v počítači. | 
|Rodina operačního systému | *Linux*, *Windows* |
|Název operačního systému | Úplný název operačního systému | 
|PhysicalMemoryMB | Fyzická paměť v megabajtech | 
|Procesory | Počet procesorů | 
|Rychlost procesoru | Rychlost procesoru v MHz | 
|VirtualMachineType | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId | ID virtuálního měn přiřazené jeho hypervisorem | 
|VirtualMachineNativeName | Název virtuálního virtuálního soudu |
|VirtualMachineHypervisorId | Jedinečný identifikátor hypervisoru hostujícího virtuální hod. |
|HypervisorTyp | *hyperv* |
|HypervisorId | Unikátní ID hypervisoru | 
|Zprostředkovatel hostingu | *Azure* |
|_ResourceId | Jedinečný identifikátor prostředku Azure |
|AzureSubscriptionId | Globálně jedinečný identifikátor, který identifikuje vaše předplatné | 
|AzureResourceGroup | Název skupiny prostředků Azure, jejíž je počítač členem. |
|AzureResourceName | Název prostředku Azure |
|AzureLocation | Umístění prostředku Azure |
|AzureUpdateDomain | Název domény aktualizace Azure |
|AzureFaultDomain | Název domény selhání Azure |
|AzureVmId | Jedinečný identifikátor virtuálního počítače Azure |
|Velikost Azure | Velikost virtuálního počítače Azure |
|AzureImagePublisher | Název vydavatele virtuálního počítače Azure |
|AzureImageOffering | Název typu nabídky virtuálního počítače Azure | 
|AzureImageSku | Skladová položka image virtuálního počítače Azure | 
|AzureImageVersion | Verze image virtuálního počítače Azure | 
|AzureCloudServiceName | Název cloudové služby Azure |
|Nasazení AzureCloudService | ID nasazení pro cloudovou službu |
|AzureCloudServiceNázev role | Název role cloudové služby |
|AzureCloudServiceTyp role | Typ role cloudové služby: *pracovník* nebo *web* |
|AzureCloudServiceInstanceId | ID instance instance cloudové služby |
|AzureVmScaleSetName | Název škálovací sady virtuálních strojů |
|Nasazení AzureVmScaleSetDeployment | ID nasazení škálovací sady virtuálních strojů |
|AzureVmScaleSetId | Jedinečný identifikátor prostředku škálovací sady virtuálních strojů.|
|AzureVmScaleSetId | Jedinečný identifikátor škálovací sady virtuálních strojů |
|AzureServiceFabricClusterId | Jedinečný identifer clusteru Azure Service Fabric | 
|AzureServiceFabricNázev clusteru | Název clusteru Azure Service Fabric |

### <a name="vmprocess-records"></a>Záznamy procesu VMProcess

Záznamy s typem *Procesu virtuálního* zařízení mají data zásob pro procesy připojené k protokolu TCP na serverech s agentem závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
|TenantId | Jedinečný identifikátor pracovního prostoru |
|SourceSystem | *Insights* | 
|TimeGenerated | Časové razítko záznamu (UTC) |
|Počítač | Hlavní název fqdn počítače | 
|AgentId | Jedinečné ID agenta analýzy protokolů |
|Počítač | Název prostředku Azure Resource Manager pro počítač vystavený ServiceMap. Je formuláře *m-{GUID}*, kde *identifikátor GUID* je stejný identifikátor GUID jako AgentId. | 
|Proces | Jedinečný identifikátor procesu mapy služeb. Je ve formě *p-{GUID}*. 
|Název spustitelného souboru | Název spustitelného procesu | 
|DisplayName | Zobrazovaný název procesu |
|Role | Role *procesu: webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Skupina | Zpracovat název skupiny. Procesy ve stejné skupině jsou logicky spojeny, například součástí stejného produktu nebo systémové součásti. |
|StartTime | Čas zahájení fondu procesů |
|PrvníPid | První PID ve fondu procesů |
|Popis | Popis procesu |
|CompanyName | Název společnosti |
|InternalName | Interní název |
|ProductName | Název produktu |
|ProductVersion | Verze produktu |
|FileVersion | Verze souboru |
|Cesta spustitelného souboru |Cesta spustitelného souboru |
|Commandline | Příkazový řádek |
|WorkingDirectory | Pracovní adresář |
|Služby | Pole služeb, v rámci kterých je proces spuštěn |
|UserName | Účet, pod kterým proces provádí |
|UserDomain | Doména, pod kterou je proces prováděn |
|_ResourceId | Jedinečný identifikátor procesu v pracovním prostoru |


## <a name="sample-map-queries"></a>Ukázkové mapové dotazy

### <a name="list-all-known-machines"></a>Seznam všech známých strojů

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Kdy byl virtuální počítač naposledy restartován

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Souhrn virtuálních počítačů Azure podle image, umístění a skladové položky

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Seznam fyzické kapacity paměti všech spravovaných počítačů

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Seznam názvů počítačů, DNS, IP a operačního systému

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najít všechny procesy s "sql" v příkazovém řádku

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Vyhledání počítače (nejnovější záznam) podle názvu prostředku

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Vyhledání počítače (nejnovější záznam) podle IP adresy

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Vypsat všechny známé procesy na určeném počítači

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Seznam všech počítačů se systémem SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Seznam všech jedinečných verzí produktu curl v mém datovém centru

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvoření skupiny počítačů všech počítačů se systémem CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Trendy odeslaných a přijatých bajtů

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Které virtuální počítače Azure přenášejí nejvíce bajtů

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Trendy stavu propojení

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend selhání připojení

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Vázané porty

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Počet otevřených portů napříč počítači

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Procesy skóre ve vašem pracovním prostoru podle počtu otevřených portů

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Agregační chování pro každý port

Tento dotaz pak lze použít ke skóre portů podle aktivity, například portů s největším příchozím/odchozím provozem, porty s většinou připojení
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sumarizovat odchozí připojení ze skupiny počítačů

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Záznamy o výkonu
Záznamy s typem *InsightsMetrics* mají údaje o výkonu z hostovaného operačního systému virtuálního počítače. Tyto záznamy mají vlastnosti v následující tabulce:


| Vlastnost | Popis |
|:--|:--|
|TenantId | Jedinečný identifikátor pracovního prostoru |
|SourceSystem | *Insights* | 
|TimeGenerated | Čas, kdy byla hodnota shromážděna (UTC) |
|Počítač | Hlavní název fqdn počítače | 
|Zdroj | *vm.azm.ms* |
|Obor názvů | Kategorie čítače výkonnosti | 
|Name (Název) | Název čítače výkonu |
|Val | Shromážděná hodnota | 
|Značky | Související podrobnosti o záznamu. Značky používané s různými typy záznamů najdete v následující tabulce.  |
|AgentId | Jedinečný identifikátor agenta každého počítače |
|Typ | *InsightsMetrics* |
|_ResourceId_ | ID prostředku virtuálního počítače |

Čítače výkonu aktuálně shromážděné v tabulce *InsightsMetrics* jsou uvedeny v následující tabulce:

| Obor názvů | Name (Název) | Popis | Jednotka | Značky |
|:---|:---|:---|:---|:---|
| Počítač    | Prezenční signál             | Prezenční signál počítače                        | | |
| Memory (Paměť)      | Dostupný MB           | Dostupné bajty v paměti                    | Bajty          | memorySizeMB - Celková velikost paměti|
| Síť     | WriteBytesPerSecond   | Síťové zápisy bajtů za sekundu            | BajtyPerSekunda | NetworkDeviceId - Id zařízení<br>bajty - celkový počet odeslaných bajtů |
| Síť     | ReadBytesPerSecond    | Čtení bajtů sítě za sekundu             | BajtyPerSekunda | networkDeviceId - Id zařízení<br>bajty - celkový počet přijatých bajtů |
| Procesor   | Procento využití | Procento využití procesoru          | Procento        | totalCpus - celkový počet procesorů |
| Logicaldisk | Zapisujepersekundu       | Zápisy logického disku za sekundu            | CountPerSecond | mountId - Mount ID zařízení |
| Logicaldisk | WriteLatencyMs        | Milisekunda zápisu logického disku    | Milisekund   | mountId - Mount ID zařízení |
| Logicaldisk | WriteBytesPerSecond   | Bajty zápisu logického disku za sekundu       | BajtyPerSekunda | mountId - Mount ID zařízení |
| Logicaldisk | TransfersPerSecond    | Přenosy logického disku za sekundu         | CountPerSecond | mountId - Mount ID zařízení |
| Logicaldisk | TransferLatencyMs     | Milisekunda logického přenosu disku | Milisekund   | mountId - Mount ID zařízení |
| Logicaldisk | Čtepersekunda        | Čtení logického disku za sekundu             | CountPerSecond | mountId - Mount ID zařízení |
| Logicaldisk | ReadLatencyMs         | Latence čtení logického disku millisekunda     | Milisekund   | mountId - Mount ID zařízení |
| Logicaldisk | ReadBytesPerSecond    | Bajty čtení logického disku za sekundu        | BajtyPerSekunda | mountId - Mount ID zařízení |
| Logicaldisk | FreeSpacePercentage   | Procento volného místa na logickém disku        | Procento        | mountId - Mount ID zařízení |
| Logicaldisk | Volný prostorový prostor           | Bajty volného místa logického disku             | Bajty          | mountId - Mount ID zařízení<br>diskSizeMB - celková velikost disku |
| Logicaldisk | BajtyPerSekunda        | Bajty logického disku za sekundu             | BajtyPerSekunda | mountId - Mount ID zařízení |


## <a name="next-steps"></a>Další kroky

* Pokud jste s psaním dotazů protokolu v Azure Monitoru noví, přečtěte si, [jak používat Log Analytics](../../azure-monitor/log-query/get-started-portal.md) na webu Azure Portal k zápisu dotazů protokolu.

* Informace o [psaní vyhledávacích dotazů](../../azure-monitor/log-query/search-queries.md).
