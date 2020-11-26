---
title: Dotazování protokolů z Azure Monitor pro virtuální počítače
description: Azure Monitor pro virtuální počítače řešení shromažďuje metriky a data protokolů do a tento článek popisuje záznamy a obsahuje vzorové dotazy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 118bdcb6929abfc162ff05e91f1621f087b6c50c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186724"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Dotazování protokolů z Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače shromažďuje metriky výkonu a připojení, data inventáře počítače a procesu a informace o stavu a předávají je do pracovního prostoru Log Analytics v Azure Monitor.  Tato data jsou k dispozici pro [dotazy](../log-query/log-query-overview.md) v Azure monitor. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="map-records"></a>Mapování záznamů

Jeden záznam je vygenerován za hodinu pro každý jedinečný počítač a proces, kromě záznamů, které jsou generovány při spuštění procesu nebo počítače nebo jeho zprovoznění do Azure Monitor pro virtuální počítače funkce map. Tyto záznamy obsahují vlastnosti v následujících tabulkách. Pole a hodnoty v událostech ServiceMapComputer_CL se mapují na pole prostředku počítače v rozhraní API pro Azure Resource Manager ServiceMap. Pole a hodnoty v ServiceMapProcess_CLch událostech se mapují na pole prostředku procesu v rozhraní API pro Azure Resource Manager ServiceMap. Pole ResourceName_s se shoduje s polem název v odpovídajícím prostředku Správce prostředků. 

K dispozici jsou interně generované vlastnosti, které můžete použít k identifikaci jedinečných procesů a počítačů:

- Počítač: k jednoznačné identifikaci počítače v pracovním prostoru Log Analytics použijte *ResourceID* nebo *ResourceName_s* .
- Proces: použijte *ResourceID* k jednoznačné identifikaci procesu v rámci Log Analytics pracovního prostoru. *ResourceName_s* je jedinečný v rámci kontextu počítače, na kterém je spuštěný proces (MachineResourceName_s). 

Vzhledem k tomu, že pro zadaný proces a počítač v zadaném časovém rozsahu může existovat více záznamů, můžou dotazy vracet více než jeden záznam pro stejný počítač nebo proces. Chcete-li zahrnout pouze nejaktuálnější záznam, přidejte `| summarize arg_max(TimeGenerated, *) by ResourceId` do dotazu.

### <a name="connections-and-ports"></a>Připojení a porty

Funkce metriky připojení zavádí dvě nové tabulky v Azure Monitor logs – VMConnection a VMBoundPort. Tyto tabulky obsahují informace o připojeních pro určitý počítač (příchozí a odchozí) a také porty serveru, které jsou na nich otevřené/aktivní. ConnectionMetrics se také zveřejňují prostřednictvím rozhraní API, která poskytují prostředky pro získání konkrétní metriky během časového intervalu. Připojení TCP vyplývající z *přijetí* na naslouchajícím soketu jsou příchozí, zatímco ta vytvořená *připojením* k dané IP adrese a portu jsou odchozí. Směr připojení je reprezentován vlastností Direction, která může být nastavena na hodnotu **příchozí** nebo **odchozí**. 

Záznamy v těchto tabulkách jsou generovány z dat hlášených Dependency Agent. Každý záznam představuje pozorování v časovém intervalu od 1 minuty. Vlastnost TimeGenerated označuje začátek časového intervalu. Každý záznam obsahuje informace, které identifikují příslušnou entitu, připojení nebo port a také metriky přidružené k dané entitě. V současné době je hlášena pouze síťová aktivita, která se používá pomocí TCP přes IPv4. 

#### <a name="common-fields-and-conventions"></a>Společná pole a konvence 

Následující pole a konvence platí pro VMConnection i VMBoundPort: 

- Počítač: plně kvalifikovaný název domény počítače pro vytváření sestav 
- ID agenta: jedinečný identifikátor počítače s agentem Log Analytics  
- Počítač: název prostředku Azure Resource Manager pro počítač vystavený pomocí ServiceMap. Má formu *m-{GUID}*, kde *GUID* je stejný identifikátor GUID jako ID agenta  
- Process: název prostředku Azure Resource Manager pro proces vystavený pomocí ServiceMap. Má formu *p-{hex String}*. Proces je jedinečný v rámci oboru počítače a generuje jedinečné ID procesu napříč počítači, kombinuje pole počítačů a procesů. 
- Název procesu: název spustitelného souboru procesu vytváření sestav.
- Všechny IP adresy jsou řetězce v kanonickém formátu IPv4, například *13.107.3.160* . 

Pro správu nákladů a složitost nepředstavuje záznam o připojení jednotlivá fyzická síťová připojení. Několik fyzických síťových připojení se seskupí do logického připojení, které se pak odrazí v příslušné tabulce.  To znamená, že záznamy v tabulce *VMConnection* představují logické seskupení, nikoli jednotlivá fyzická připojení, která jsou pozorována. Připojení fyzické sítě sdílející stejnou hodnotu pro následující atributy během daného intervalu minut je agregované do jednoho logického záznamu v *VMConnection*. 

| Vlastnost | Popis |
|:--|:--|
|Směr |Směr připojení, hodnota je *příchozí* nebo *odchozí* |
|Počítač |Plně kvalifikovaný název domény počítače |
|Proces |Identita procesu nebo skupin procesů, zahájení/přijetí připojení |
|SourceIp |IP adresa zdroje |
|DestinationIp |IP adresa cíle |
|DestinationPort |Číslo portu cílového umístění |
|Protokol |Protokol použitý pro připojení  Hodnoty jsou *TCP*. |

Informace o počtu skupinových fyzických připojení, které se mají přihlédnout k dopadu seskupení, najdete v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
|LinksEstablished |Počet fyzických síťových připojení, která byla vytvořena v časovém intervalu generování sestav |
|LinksTerminated |Počet fyzických síťových připojení, které byly ukončeny během časového intervalu generování sestav |
|LinksFailed |Počet fyzických síťových připojení, u kterých došlo k chybě v časovém intervalu generování sestav. Tyto informace jsou aktuálně k dispozici pouze pro odchozí připojení. |
|LinksLive |Počet fyzických síťových připojení otevřených na konci časového intervalu generování sestav|

#### <a name="metrics"></a>Metriky

Kromě metriky počtu připojení jsou do následujících vlastností záznamu zahrnuty také informace o objemu dat odesílaných a přijatých na daném logickém připojení nebo síťovém portu:

| Vlastnost | Popis |
|:--|:--|
|BytesSent |Celkový počet bajtů, které byly odeslány během časového intervalu generování sestav |
|BytesReceived |Celkový počet bajtů přijatých během časového intervalu generování sestav |
|Odpovědi |Počet odpovědí zaznamenaných v časovém intervalu generování sestav. 
|ResponseTimeMax |Největší doba odezvy (v milisekundách) zjištěná během časového intervalu generování sestav. Pokud není žádná hodnota, vlastnost je prázdná.|
|ResponseTimeMin |Nejmenší doba odezvy (v milisekundách) zjištěná během časového intervalu generování sestav. Pokud není žádná hodnota, vlastnost je prázdná.|
|ResponseTimeSum |Součet všech dob odezvy (milisekund) zjištěných během časového intervalu generování sestav. Pokud není žádná hodnota, vlastnost je prázdná.|

Třetí typ vykázaných dat je doba odezvy – jak dlouho bude volající čekat na zpracování požadavku odeslaného přes připojení a na něj reagovat vzdáleným koncovým bodem. Hlášená doba odezvy je odhadem skutečné doby odezvy základního aplikačního protokolu. Počítá se pomocí heuristiky na základě pozorování toku dat mezi zdrojovým a cílovým koncem fyzického síťového připojení. V koncepčním případě je to rozdíl mezi časem, kdy poslední bajt žádosti opustí odesílatele, a čas, kdy se do něj vrátí poslední bajt odpovědi. Tato dvě časová razítka slouží k vymezení událostí požadavků a odpovědí v daném fyzickém připojení. Rozdíl mezi nimi představuje dobu odezvy jednoho požadavku. 

V této první vydané verzi této funkce je náš algoritmus aproximace, která může fungovat s proměnlivým stupněm úspěšnosti v závislosti na skutečném aplikačním protokolu používaném pro dané síťové připojení. Aktuální přístup například funguje v závislosti na protokolech založených na odpovědích, jako je HTTP (S), ale nepracuje s jednosměrným protokolem nebo protokoly založenými na frontě zpráv.

Tady jsou některé důležité body, které je potřeba vzít v úvahu:

1. Pokud proces přijme připojení na stejné IP adrese, ale přes více síťových rozhraní, nahlásí se samostatný záznam pro každé rozhraní. 
2. Záznamy s IP adresou se zástupnými znaky nebudou obsahovat žádné aktivity. Jsou zahrnuty k vyjádření faktu, že je port v počítači otevřený pro příchozí provoz.
3. Aby se snížila úroveň podrobností a objem dat, budou záznamy se zástupnými adresami IP vynecháné, pokud existuje shodný záznam (pro stejný proces, port a protokol) s konkrétní IP adresou. Je-li vynechán záznam IP adresy se zástupnými znaky, bude vlastnost IsWildcardBind záznamu s konkrétní IP adresou nastavena na hodnotu "true", která označuje, že port je vystaven pro každé rozhraní počítače pro vytváření sestav.
4. Porty, které jsou vázány pouze na konkrétní rozhraní, mají IsWildcardBind nastaven na *hodnotu false*.

#### <a name="naming-and-classification"></a>Pojmenovávání a klasifikace

Pro usnadnění práce se do vlastnosti RemoteIp zahrne IP adresa vzdáleného konce připojení. U příchozích připojení je RemoteIp stejná jako SourceIp, zatímco u odchozích připojení je stejná jako DestinationIp. Vlastnost RemoteDnsCanonicalNames představuje kanonické názvy DNS hlášené počítačem pro RemoteIp. Vlastnosti RemoteDnsQuestions a RemoteClassification jsou vyhrazené pro budoucí použití. 

#### <a name="geolocation"></a>Geografická poloha

*VMConnection* také obsahuje informace o geografickém umístění pro vzdálené konce každého záznamu připojení v následujících vlastnostech záznamu: 

| Vlastnost | Popis |
|:--|:--|
|RemoteCountry |Název země nebo oblasti hostující RemoteIp.  Například *USA* |
|RemoteLatitude |Zeměpisná šířka zeměpisné polohy. Například *47,68* |
|RemoteLongitude |Zeměpisná délka zeměpisné polohy. Například *-122,12* |

#### <a name="malicious-ip"></a>Škodlivá IP adresa

Každá vlastnost RemoteIp v tabulce *VMConnection* je kontrolována na základě sady IP adres se známou škodlivou aktivitou. Pokud je RemoteIp identifikovaný jako škodlivý, budou se naplnit následující vlastnosti (Pokud se IP adresa nepovažuje za škodlivou) v následujících vlastnostech záznamu:

| Vlastnost | Popis |
|:--|:--|
|MaliciousIp |Adresa RemoteIp |
|IndicatorThreadType |Zjištěného indikátoru hrozby je jedna z následujících hodnot: *botnetu*, *C2*, *CryptoMining*, *adres darknetu*, *DDos*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *PUA*, *seznamu ke zhlédnutí*.   |
|Popis |Popis pozorované hrozby. |
|TLPLevel |Úroveň TLP (provoz Light Protocol) je jedna z definovaných hodnot, *bílá*, *zelená*, *oranžová* a *červená*. |
|Spolehlivost |Hodnoty jsou *0 – 100*. |
|Závažnost |Hodnoty jsou *0 – 5*, přičemž *5* je nejzávažnější a *0* není u sebe závažná. Výchozí hodnota je *3*.  |
|FirstReportedDateTime |První, kdy zprostředkovatel nahlásil ukazatel. |
|LastReportedDateTime |Čas posledního výskytu indikátoru v rámci přetečení. |
|IsActive |Označuje, že indikátory jsou dezaktivovány hodnotou *true* nebo *false* . |
|ReportReferenceLink |Odkazuje na sestavy související s daným pozorovatelem. |
|AdditionalInformation |Poskytuje další informace, pokud je to možné, o zjištěné hrozbě. |

### <a name="ports"></a>Porty 

Porty v počítači, které aktivně přijímají příchozí provoz nebo potenciálně nepřijímají provoz, ale během časového období vytváření sestav jsou nečinné, se zapisují do tabulky VMBoundPort.  

Každý záznam v VMBoundPort je určený následujícími poli: 

| Vlastnost | Popis |
|:--|:--|
|Proces | Identita procesu (nebo skupin procesů), ke kterým je port přidružen|
|IP | IP adresa portu (může to být zástupný znak IP, *0.0.0.0*) |
|Port |Číslo portu |
|Protokol | Protokol.  Příklad: *TCP* nebo *UDP* (v současné době se podporuje jenom *TCP* ).|
 
Identita, kterou port je odvozený z výše uvedených pěti polí a je uložený ve vlastnosti identifikátor portid. Tato vlastnost slouží k rychlému vyhledání záznamů pro určitý port v čase. 

#### <a name="metrics"></a>Metriky 

Záznamy portů zahrnují metriky představující připojení, která jsou k nim přidružená. V současné době jsou hlášeny následující metriky (podrobnosti o jednotlivých metrikách jsou popsány v předchozí části): 

- BytesSent a BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Tady jsou některé důležité body, které je potřeba vzít v úvahu:

- Pokud proces přijme připojení na stejné IP adrese, ale přes více síťových rozhraní, nahlásí se samostatný záznam pro každé rozhraní.  
- Záznamy s IP adresou se zástupnými znaky nebudou obsahovat žádné aktivity. Jsou zahrnuty k vyjádření faktu, že je port v počítači otevřený pro příchozí provoz. 
- Aby se snížila úroveň podrobností a objem dat, budou záznamy se zástupnými adresami IP vynecháné, pokud existuje shodný záznam (pro stejný proces, port a protokol) s konkrétní IP adresou. Je-li vynechán záznam IP adresy se zástupnými znaky, bude vlastnost *IsWildcardBind* pro záznam s konkrétní IP adresou nastavena na *hodnotu true*.  To znamená, že port je vystavený přes každé rozhraní počítače pro vytváření sestav. 
- Porty, které jsou vázány pouze na konkrétní rozhraní, mají IsWildcardBind nastaven na *hodnotu false*. 

### <a name="vmcomputer-records"></a>Záznamy VMComputer

Záznamy s typem *VMComputer* mají data inventáře pro servery s agentem závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
|TenantId | Jedinečný identifikátor pracovního prostoru |
|SourceSystem | *Přehledy* | 
|TimeGenerated | Časové razítko záznamu (UTC) |
|Počítač | Plně kvalifikovaný název domény počítače | 
|ID agenta | Jedinečné ID agenta Log Analytics |
|Počítač | Název prostředku Azure Resource Manager pro počítač vystavený pomocí ServiceMap. Má formu *m-{GUID}*, kde *GUID* je stejný identifikátor GUID jako ID agenta. | 
|DisplayName | Zobrazované jméno | 
|FullDisplayName | Úplné zobrazované jméno | 
|Název hostitele | Název počítače bez názvu domény |
|BootTime | Čas spuštění počítače (UTC) |
|TimeZone | Normalizované časové pásmo |
|VirtualizationState | *virtuální*, *hypervisor*, *fyzický* |
|Ipv4Addresses | Pole IPv4 adres | 
|Ipv4SubnetMasks | Pole masek podsítí IPv4 (ve stejném pořadí jako Ipv4Addresses). |
|Ipv4DefaultGateways | Pole bran IPv4 | 
|Ipv6Addresses | Pole IPv6 adres | 
|MacAddresses | Pole adres MAC | 
|DnsNames | Pole názvů DNS přidružených k počítači |
|DependencyAgentVersion | Verze agenta závislostí spuštěného v počítači. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Úplný název operačního systému | 
|PhysicalMemoryMB | Fyzická paměť v megabajtech | 
|Procesor | Počet procesorů | 
|CpuSpeed | Rychlost procesoru v MHz | 
|VirtualMachineType | *Hyper*-v, *VMware*, *Xen* |
|VirtualMachineNativeId | ID virtuálního počítače přiřazené hypervisorem | 
|VirtualMachineNativeName | Název virtuálního počítače |
|VirtualMachineHypervisorId | Jedinečný identifikátor hypervisoru, který hostuje virtuální počítač |
|HypervisorType | *HyperV* |
|HypervisorId | Jedinečné ID hypervisoru | 
|HostingProvider | *Azure* |
|_ResourceId | Jedinečný identifikátor prostředku Azure |
|AzureSubscriptionId | Globálně jedinečný identifikátor, který identifikuje vaše předplatné | 
|AzureResourceGroup | Název skupiny prostředků Azure, se kterou je počítač členem. |
|AzureResourceName | Název prostředku Azure |
|AzureLocation | Umístění prostředku Azure |
|AzureUpdateDomain | Název domény služby Azure Update |
|AzureFaultDomain | Název domény selhání Azure |
|AzureVmId | Jedinečný identifikátor virtuálního počítače Azure |
|AzureSize | Velikost virtuálního počítače Azure |
|AzureImagePublisher | Název vydavatele virtuálního počítače Azure |
|AzureImageOffering | Název typu nabídky virtuálního počítače Azure | 
|AzureImageSku | SKU image virtuálního počítače Azure | 
|AzureImageVersion | Verze image virtuálního počítače Azure | 
|AzureCloudServiceName | Název cloudové služby Azure |
|AzureCloudServiceDeployment | ID nasazení pro cloudovou službu |
|AzureCloudServiceRoleName | Název role cloudové služby |
|AzureCloudServiceRoleType | Typ role cloudové služby: *pracovní proces* nebo *Web* |
|AzureCloudServiceInstanceId | ID instance role cloudové služby |
|AzureVmScaleSetName | Název sady škálování virtuálního počítače |
|AzureVmScaleSetDeployment | ID nasazení sady škálování virtuálního počítače |
|AzureVmScaleSetResourceId | Jedinečný identifikátor prostředku sady škálování virtuálních počítačů.|
|AzureVmScaleSetInstanceId | Jedinečný identifikátor sady škálování virtuálních počítačů |
|AzureServiceFabricClusterId | Jedinečné identifikátorem clusteru Azure Service Fabric | 
|AzureServiceFabricClusterName | Název clusteru Azure Service Fabric |

### <a name="vmprocess-records"></a>Záznamy VMProcess

Záznamy s typem *VMProcess* mají data inventáře pro procesy připojené k protokolu TCP na serverech s agentem závislostí. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--|:--|
|TenantId | Jedinečný identifikátor pracovního prostoru |
|SourceSystem | *Přehledy* | 
|TimeGenerated | Časové razítko záznamu (UTC) |
|Počítač | Plně kvalifikovaný název domény počítače | 
|ID agenta | Jedinečné ID agenta Log Analytics |
|Počítač | Název prostředku Azure Resource Manager pro počítač vystavený pomocí ServiceMap. Má formu *m-{GUID}*, kde *GUID* je stejný identifikátor GUID jako ID agenta. | 
|Proces | Jedinečný identifikátor procesu Service Map. Má formu *p-{GUID}*. 
|Spustitelný soubor | Název spustitelného procesu | 
|DisplayName | Zobrazovaný název procesu |
|Role | Role procesu: *webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Skupina | Název skupiny procesů Procesy ve stejné skupině jsou logicky související, například součást stejné produktové nebo systémové komponenty. |
|StartTime | Čas spuštění fondu procesů |
|FirstPid | První PID ve fondu procesů |
|Popis | Popis procesu |
|CompanyName | Název společnosti |
|Vnitřní | Interní název |
|ProductName | Název produktu |
|ProductVersion | Verze produktu |
|FileVersion | Verze souboru |
|ExecutablePath |Cesta ke spustitelnému souboru |
|CommandLine | Příkazový řádek |
|WorkingDirectory | Pracovní adresář |
|Služby | Pole služeb, na kterých se proces provádí |
|Uživatelské jméno | Účet, pod kterým se proces spouští |
|UserDomain | Doména, pod kterou je prováděn proces |
|_ResourceId | Jedinečný identifikátor procesu v pracovním prostoru |


## <a name="sample-map-queries"></a>Ukázkové dotazy na mapování

### <a name="list-all-known-machines"></a>Zobrazit seznam všech známých počítačů

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Kdy byl virtuální počítač naposledy restartován

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Shrnutí virtuálních počítačů Azure podle image, umístění a SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Zobrazit kapacitu fyzické paměti všech spravovaných počítačů

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Vypíše název počítače, DNS, IP adresu a operační systém.

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Najde všechny procesy pomocí SQL na příkazovém řádku.

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Vyhledání počítače (nejaktuálnější záznam) podle názvu prostředku

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Vyhledání počítače (nejaktuálnější záznam) podle IP adresy

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Vypíše všechny známé procesy v zadaném počítači.

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Vypíše všechny počítače se systémem SQL Server.

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Vypíše všechny jedinečné verze produktu ve vaší datacentru.

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Vytvoření skupiny počítačů na všech počítačích se systémem CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Odeslané bajty a přijaté trendy

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Které virtuální počítače Azure přenáší nejvíc bajtů

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

### <a name="bound-ports"></a>Porty svázané

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

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Skóre procesů ve vašem pracovním prostoru podle počtu portů, které jsou otevřené

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Agregované chování pro každý port

Tento dotaz se pak dá použít k určení skóre portů podle aktivity, například portů s největším příchozím a odchozím provozem, porty s největším připojením.
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Shrnutí odchozích připojení ze skupiny počítačů

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
|SourceSystem | *Přehledy* | 
|TimeGenerated | Čas, kdy byla hodnota shromážděna (UTC) |
|Počítač | Plně kvalifikovaný název domény počítače | 
|Zdroj | *vm.azm.ms* |
|Obor názvů | Kategorie čítače výkonu | 
|Název | Název čítače výkonu |
|Val | Shromážděná hodnota | 
|Značky | Související podrobnosti o záznamu Značky používané s různými typy záznamů najdete v následující tabulce.  |
|ID agenta | Jedinečný identifikátor pro každého agenta počítače |
|Typ | *InsightsMetrics* |
|_ResourceId_ | ID prostředku virtuálního počítače |

Čítače výkonu, které jsou aktuálně shromážděny do tabulky *InsightsMetrics* , jsou uvedeny v následující tabulce:

| Obor názvů | Název | Popis | Jednotka | Značky |
|:---|:---|:---|:---|:---|
| Počítač    | Tep             | Prezenční signál počítače                        | | |
| Paměť      | AvailableMB           | Bajty dostupné paměti                    | Megabajty      | memorySizeMB – celková velikost paměti|
| Síť     | WriteBytesPerSecond   | Bajty zápisu v síti za sekundu            | BytesPerSecond | NetworkDeviceId-ID zařízení<br>bajty-celkový počet odeslaných bajtů |
| Síť     | ReadBytesPerSecond    | Počet bajtů přečtených v síti za sekundu             | BytesPerSecond | networkDeviceId-ID zařízení<br>Bajty celkem přijatých bajtů |
| Procesor   | UtilizationPercentage | Procento využití procesoru          | Procento        | totalCpus – celkový počet procesorů |
| Logický disk | WritesPerSecond       | Zápisy logického disku za sekundu            | CountPerSecond | mountId – ID připojení zařízení |
| Logický disk | WriteLatencyMs        | Latence zápisu u logického disku milisekund    | Milisekund   | mountId – ID připojení zařízení |
| Logický disk | WriteBytesPerSecond   | Bajty zápisu na logický disk za sekundu       | BytesPerSecond | mountId – ID připojení zařízení |
| Logický disk | TransfersPerSecond    | Přenosy logických disků za sekundu         | CountPerSecond | mountId – ID připojení zařízení |
| Logický disk | TransferLatencyMs     | Latence přenosu logického disku milisekund | Milisekund   | mountId – ID připojení zařízení |
| Logický disk | ReadsPerSecond        | Čtení z logického disku za sekundu             | CountPerSecond | mountId – ID připojení zařízení |
| Logický disk | ReadLatencyMs         | Latence čtení v logickém disku milisekund     | Milisekund   | mountId – ID připojení zařízení |
| Logický disk | ReadBytesPerSecond    | Bajty čtení z logického disku za sekundu        | BytesPerSecond | mountId – ID připojení zařízení |
| Logický disk | FreeSpacePercentage   | Procento volného místa na logickém disku        | Procento        | mountId – ID připojení zařízení |
| Logický disk | FreeSpaceMB           | Bajty volného místa logického disku             | Megabajty      | mountId – ID připojení zařízení<br>diskSizeMB – celková velikost disku |
| Logický disk | BytesPerSecond        | Bajty logických disků za sekundu             | BytesPerSecond | mountId – ID připojení zařízení |


## <a name="next-steps"></a>Další kroky

* Pokud s psaním dotazů protokolu v Azure Monitor začínáte, přečtěte si téma [použití Log Analytics](../log-query/log-analytics-tutorial.md) v Azure Portal k zápisu dotazů protokolu.

* Přečtěte si informace o [zápisu vyhledávacích dotazů](../log-query/get-started-queries.md).