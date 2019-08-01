---
title: Schéma analýzy provozu Azure | Microsoft Docs
description: Pochopení schématu Analýza provozu k analýze protokolů toku skupin zabezpečení sítě Azure
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: efa8a92ca9861c0280237ba07f4304b5c7dbbb88
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609988"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregace schématu a dat v Analýza provozu

Analýza provozu je cloudové řešení, které poskytuje přehled o aktivitách uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokoly toků Network Watcher skupiny zabezpečení sítě (NSG), které poskytují přehled o toku přenosů ve vašem cloudu Azure. Analýza provozu vám umožní:

- Vizualizujte síťovou aktivitu napříč předplatnými Azure a Identifikujte aktivní body.
- Identifikujte bezpečnostní hrozby a zabezpečte svou síť pomocí informací, jako jsou otevřené porty, aplikace pokoušející se o přístup k Internetu a virtuální počítače, které se připojují k neautorizovaným sítím.
- Pochopení toků toků provozu napříč oblastmi Azure a internetem k optimalizaci nasazení sítě pro výkon a kapacitu
- Pinpoint chybné konfigurace sítě, což vede k neúspěšným připojením ve vaší síti.
- Poznejte využití sítě v bajtech, paketech nebo tocích.

### <a name="data-aggregation"></a>Agregace dat

1. Všechny protokoly toků v NSG mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t" jsou zachyceny v jednom minutovém účtu úložiště jako objekty blob, aby je bylo možno zpracovávat pomocí Analýza provozu. 
2. Výchozí interval zpracování Analýza provozu je 60 minut. To znamená, že každých 60 minut Analýza provozu z úložiště vybírá objekty blob pro agregaci. Pokud je zvolený interval zpracování 10 minut, Analýza provozu po každých 10 minutách vybírat objekty BLOB z účtu úložiště.
3. Toky, které mají stejnou zdrojovou IP adresu, cílovou IP adresu, cílový port, název NSG, pravidlo NSG, směr toku a protokol Transport Layer (TCP nebo UDP) (Poznámka: Zdrojový port je vyloučený pro agregaci) jsou clubbed do jediného toku Analýza provozu
4. Tento jediný záznam je upraven (podrobnosti v níže uvedené části) a ingestuje Log Analytics Analýza provozu. Tento proces může trvat maximálně jednu hodinu.
5. FlowStartTime_t pole indikuje první výskyt tohoto agregovaného toku (stejné čtyři-řazené kolekce členů) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t".
6. U všech prostředků v poli jsou toky, které jsou uvedené v uživatelském rozhraní, celkovými toky, které NSG uvidí, ale v Log Analytics uživatel uvidí jenom jediný, snížený záznam. Pokud chcete zobrazit všechny toky, použijte pole blob_id, na které se dá odkazovat z úložiště. Celkový počet toků pro tento záznam bude odpovídat jednotlivým tokům, které jsou v objektu BLOB viditelné.

Níže uvedený dotaz vám pomůže v posledních 30 dnech sledovat všechny protokoly toku z místního umístění.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Chcete-li zobrazit cestu objektu BLOB pro toky ve výše uvedeném dotazu, použijte následující dotaz:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Výše uvedený dotaz vytvoří adresu URL pro přímý přístup k objektu BLOB. Adresa URL s držiteli místa je následující:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Pole používaná ve schématu Analýza provozu

Analýza provozu je postavená na Log Analytics, takže můžete spouštět vlastní dotazy na data dekorovaná Analýza provozu a nastavovat výstrahy na stejné úrovni.

Níže jsou uvedené pole ve schématu a co značí.

| Pole | Formát | Komentáře |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabulka pro data Analýza provozu
| SubType_s | FlowLog | Podtyp pro protokoly toku. Použijte pouze "FlowLog", jiné hodnoty SubType_s jsou pro interní práci s produktem. |
| FASchemaVersion_s |   1   | Verze schématu Nereflektuje verzi protokolu toku NSG. |
| TimeProcessed_t   | Datum a čas ve standardu UTC  | Čas, kdy Analýza provozu zpracoval protokoly nezpracovaných toků z účtu úložiště |
| FlowIntervalStartTime_t | Datum a čas ve standardu UTC |  Počáteční čas intervalu zpracování protokolu toku. Toto je čas, od kterého se měří interval toku. |
| FlowIntervalEndTime_t | Datum a čas ve standardu UTC | Čas ukončení intervalu zpracování protokolu toku |
| FlowStartTime_t | Datum a čas ve standardu UTC |  První výskyt toku (který se získá agregovaný) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". Tento tok se agreguje na základě logiky agregace. |
| FlowEndTime_t | Datum a čas ve standardu UTC | Poslední výskyt toku (který se získá agregovaný) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". V protokolu toku v2 toto pole obsahuje čas posledního toku se stejnou čtyřmi řazenými kolekcemi (označený jako B) v záznamu nezpracovaného toku. |
| FlowType_s |  * IntraVNet <br> * Mezi virtuálními sítěmi <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Neznámý privátní <br> * Neznámý | Definice v poznámce pod tabulkou |
| SrcIP_s | Zdrojová IP adresa | Bude v případě toků AzurePublic a ExternalPublic prázdné. |
| DestIP_s | Cílová IP adresa | Bude v případě toků AzurePublic a ExternalPublic prázdné. |
| VMIP_s | IP adresa virtuálního počítače | Používá se pro toky AzurePublic a ExternalPublic. |
| PublicIP_s | Veřejné IP adresy | Používá se pro toky AzurePublic a ExternalPublic. |
| DestPort_d | Cílový port | Port, na kterém je provoz příchozí |
| L4Protocol_s  | * T <br> * U  | Transportní protokol. T = TCP <br> U = UDP |
| L7Protocol_s  | Název protokolu | Odvozeno z cílového portu |
| FlowDirection_s | * I = příchozí<br> * O = odchozí | Směr toku v/v NSG podle protokolu na jeden tok |
| FlowStatus_s  | * A = povoleno pravidlem NSG <br> * D = zamítnuto podle NSG pravidla  | Stav toku povolených/nblockedch podle NSG na protokol toku |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Skupina zabezpečení sítě (NSG) přidružená k toku |
| NSGRules_s | \<Hodnota indexu 0) > < NSG_RULENAME >\<směr toku >\<stav toku >\<FlowCount ProcessedByRule > |  Pravidlo NSG, které povoluje nebo zakázalo tento tok |
| NSGRuleType_s | * Definováno uživatelem * výchozí |   Typ pravidla NSG používaného tokem |
| MACAddress_s | Adresa MAC | Adresa MAC síťového adaptéru, na kterém byl tok zachycen |
| Subscription_s | V tomto poli se naplní předplatné virtuální sítě Azure/síťové rozhraní/virtuální počítač. | Platí jenom pro typy toků FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate (typy toků, kde je jenom jedna strana Azure). |
| Subscription1_s | ID předplatného | ID předplatného virtuální sítě/síťového rozhraní/virtuálního počítače, do kterého patří zdrojová IP adresa v toku |
| Subscription2_s | ID předplatného | ID předplatného virtuální sítě/síťového rozhraní/virtuálního počítače, do kterého patří cílová IP adresa v toku |
| Region_s | Oblast Azure virtuální sítě/síťové rozhraní/virtuální počítač, ke kterému patří IP adresa v toku | Platí jenom pro typy toků FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate (typy toků, kde je jenom jedna strana Azure). |
| Region1_s | Oblast Azure | Oblast Azure virtuální sítě/síťové rozhraní/virtuální počítač, ke kterému patří zdrojová IP adresa v toku |
| Region2_s | Oblast Azure | Oblast Azure virtuální sítě, do které patří cílová IP adresa v toku |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Síťová karta přidružená k virtuálnímu počítači odesílajícího nebo přijímaného provozu |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Síťové rozhraní přidružené ke zdrojové IP adrese v toku |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Síťové rozhraní přidružené k cílové IP adrese v toku |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Virtuální počítač přidružený k NIC_s síťového rozhraní |
| VM1_s | < resourcegroup_Name >/\<VirtualMachineName > | Virtuální počítač přidružený ke zdrojové IP adrese v toku |
| VM2_s | < resourcegroup_Name >/\<VirtualMachineName > | Virtuální počítač přidružený k cílové IP adrese v toku |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsíť přidružená k NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsíť přidružená ke zdrojové IP adrese v toku |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Podsíť přidružená k cílové IP adrese v toku |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Aplikační brána přidružená ke zdrojové IP adrese v toku |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Application Gateway přidružená k cílové IP adrese v toku |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Nástroj pro vyrovnávání zatížení přidružený ke zdrojové IP adrese v toku |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Nástroj pro vyrovnávání zatížení přidružený k cílové IP adrese v toku |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brána místní sítě přidružená ke zdrojové IP adrese v toku |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brána místní sítě přidružená k cílové IP adrese v toku |
| ConnectionType_s | Možné hodnoty jsou VNetPeering, VpnGateway a ExpressRoute. |    Typ připojení |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Název připojení |
| ConnectingVNets_s | Prostor oddělený seznam názvů virtuálních sítí | V případě topologie centra a paprsků se tady naplní virtuální sítě rozbočovačů. |
| Country_s | Dvoumístné číslo země (ISO 3166-1 alpha-2) | Bylo vyplněno pro typ toku ExternalPublic. Všechny IP adresy v poli PublicIPs_s budou sdílet stejný kód země. |
| AzureRegion_s | Umístění oblastí Azure | Bylo vyplněno pro typ toku AzurePublic. Všechny IP adresy v poli PublicIPs_s budou sdílet oblast Azure. |
| AllowedInFlows_d | | Počet příchozích toků, které byly povoleny. Představuje počet toků, které sdílely stejnou čtyři řazené kolekce členů na síťové rozhraní, ve kterém byl tok zachycen. |
| DeniedInFlows_d |  | Počet příchozích toků, které byly zamítnuty. (Příchozí pro síťové rozhraní, ve kterém byl tok zachycen) |
| AllowedOutFlows_d | | Počet odchozích toků, které byly povoleny (odchozí pro síťové rozhraní, ve kterém byl tok zachycen) |
| DeniedOutFlows_d  | | Počet odchozích toků, které byly zamítnuté (odchozí na síťové rozhraní, ve kterém se tok zachytává) |
| FlowCount_d | Zastaralé. Celkový počet toků, které odpovídají stejné čtyř-řazené kolekci členů. V případě typů toků ExternalPublic a AzurePublic bude počet zahrnovat i toky z různých adresních adres.
| InboundPackets_d | Přijaté pakety zaznamenané na síťovém rozhraní, kde se použilo pravidlo NSG | Tento údaj se naplní jenom na verzi 2 schématu protokolu toku NSG. |
| OutboundPackets_d  | Pakety odeslané jako zaznamenané v síťovém rozhraní, kde bylo použito pravidlo NSG | Tento údaj se naplní jenom na verzi 2 schématu protokolu toku NSG. |
| InboundBytes_d |  Počet přijatých bajtů zaznamenaných v síťovém rozhraní, kde bylo použito pravidlo NSG | Tento údaj se naplní jenom na verzi 2 schématu protokolu toku NSG. |
| OutboundBytes_d | Počet odeslaných bajtů zaznamenaných v síťovém rozhraní, kde bylo použito pravidlo NSG | Tento údaj se naplní jenom na verzi 2 schématu protokolu toku NSG. |
| CompletedFlows_d  |  | Tato hodnota se vyplní nenulovou hodnotou jenom pro schéma protokolu NSG Flow verze 2. |
| PublicIPs_s | < PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_PACKETS >\|\<\<\| \<OUTBOUND_BYTES>\|INBOUND_BYTES>\< | Položky oddělené řádky |

### <a name="notes"></a>Poznámky

1. V případě toků AzurePublic a ExternalPublic se v poli VMIP_s naplní IP adresa virtuálního počítače Azure ve vlastnictví Azure, zatímco veřejné IP adresy se naplní v poli PublicIPs_s. Pro tyto dva typy toků doporučujeme použít VMIP_s a PublicIPs_s namísto polí SrcIP_s a DestIP_s. U adres AzurePublic a ExternalPublicIP se ještě agreguje, takže počet záznamů, které se ingestují do pracovního prostoru služby Log Analytics pro zákazníky, je minimální. (Toto pole bude brzy zastaralé a v závislosti na tom, zda byl virtuální počítač Azure zdrojem nebo cílem v toku, by se mělo používat SrcIP_ a DestIP_s.)
1. Podrobnosti o typech toků: V závislosti na IP adresách, které jsou součástí tohoto toku, zařadíme toky do následujících typů toků:
1. IntraVNet – IP adresy v toku se nacházejí ve stejné službě Azure Virtual Network.
1. Mezi virtuálními sítěmi – IP adresy v toku se nacházejí ve dvou různých virtuálních sítích Azure.
1. S2S – (site-to-site) jedna z IP adres patří do Azure Virtual Network a druhá IP adresa patří do sítě zákazníka připojené ke službě Azure Virtual Network prostřednictvím brány VPN nebo Express Route.
1. P2S-(Point-to-site) jedna z IP adres patří do Azure Virtual Network a druhá IP adresa patří do sítě zákazníka připojené ke službě Azure Virtual Network prostřednictvím brány VPN.
1. AzurePublic – jedna z IP adres patří do Azure Virtual Network a druhá IP adresa patří do interních veřejných IP adres Azure vlastněných Microsoftem. Veřejné IP adresy vlastněné zákazníkem nebudou součástí tohoto typu toku. Například každý virtuální počítač vlastněný zákazníkem odesílající provoz do služby Azure (koncový bod úložiště) by měl být zařazen do kategorie tohoto typu toku.
1. ExternalPublic – jedna z IP adres patří do Azure Virtual Network a druhá IP adresa je veřejná IP adresa, která není v Azure, není v informačních kanálech ASC nahlášena jako škodlivá, která Analýza provozu spotřebovává interval zpracování mezi " FlowIntervalStartTime_t "a" FlowIntervalEndTime_t ".
1. MaliciousFlow – jedna z IP adres patří do služby Azure Virtual Network, zatímco druhá IP adresa je veřejná IP adresa, která není v Azure, a je v informačních kanálech ASC nahlášená jako škodlivá, která Analýza provozu spotřebovává interval zpracování mezi. FlowIntervalStartTime_t "a" FlowIntervalEndTime_t ".
1. UnknownPrivate – jedna z IP adres patří do Azure Virtual Network a druhá IP adresa patří do rozsahu privátních IP adres, jak je definováno v dokumentu RFC 1918 a nelze ji namapovat pomocí Analýza provozu na web vlastněné zákazníkem nebo na Azure Virtual Network.
1. Neznámé – nepovedlo se namapovat jednu z IP adres v tocích s topologií zákazníka v Azure i v místním prostředí (Web).
1. K některým názvům polí se připojí _s nebo _D. Neoznačují zdroj a cíl.

### <a name="next-steps"></a>Další kroky
Odpovědi na nejčastější dotazy najdete v tématu [Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md) a zobrazení podrobností o funkcích najdete v tématu [dokumentace k analýze provozu](traffic-analytics.md) .
