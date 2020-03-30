---
title: Schéma analýzy provozu Azure | Dokumenty společnosti Microsoft
description: Seznamte se se schématem Traffic Analytics a analyzujte protokoly toku skupiny zabezpečení sítě Azure.
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666371"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schéma a agregace dat v Traffic Analytics

Traffic Analytics je cloudové řešení, které poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Traffic Analytics analyzuje protokoly toku skupiny network watcher (NSG) a poskytuje přehled o toku provozu ve vašem cloudu Azure. S analýzou provozu můžete:

- Vizualizujte síťové aktivity napříč předplatnými Azure a identifikujte aktivní body.
- Identifikujte bezpečnostní hrozby pro síť a zabezpečte ji pomocí informací, jako jsou otevřené porty, aplikace, které se pokoušejí o přístup k internetu, a virtuální počítače (VM) připojující se k neautorizovaným sítím.
- Seznamte se se vzory toku provozu napříč oblastmi Azure a internetem a optimalizujte nasazení sítě z výkonu a kapacity.
- Určit chybné konfigurace sítě vedoucí k selhání připojení v síti.
- Znát využití sítě v bajtů, pakety nebo toky.

### <a name="data-aggregation"></a>Agregace dat

1. Všechny protokoly toku v souboru nsg mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t" jsou zachyceny v jednominutových intervalech v účtu úložiště jako objekty BLOB před zpracováním Traffic Analytics.
2. Výchozí interval zpracování traffic analytics je 60 minut. To znamená, že každých 60 minut Traffic Analytics vybere objekty BLOB z úložiště pro agregaci. Pokud je zvolený interval zpracování 10 minut, služba Traffic Analytics bude po každých 10 minutch vybírat objekty BLOB z účtu úložiště.
3. Toky, které mají stejnou zdrojovou IP adresu, cílovou IP adresu, cílový port, název skupiny zabezpečení sítě, pravidlo sítě nsg, směr toku a protokol transportní vrstvy (TCP nebo UDP) (Poznámka: Zdrojový port je vyloučen pro agregaci) jsou udeřeny do jednoho toku službou Traffic Analytics
4. Tento jediný záznam je dekorován (podrobnosti v části níže) a ingestovány v Log Analytics podle Traffic Analytics.Tento proces může trvat až 1 hodinu max.
5. FlowStartTime_t pole označuje první výskyt takového agregovaného toku (stejné čtyřčlenné řazené kolekce členů) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t".
6. Pro všechny prostředky v TA, toky uvedené v uživatelském rozhraní jsou celkové toky vidět nsg, ale v Log Analytics uživatel uvidí pouze jeden, snížený záznam. Chcete-li zobrazit všechny toky, použijte pole blob_id, na které lze odkazovat z úložiště. Celkový počet toků pro tento záznam bude odpovídat jednotlivé toky vidět v objektu blob.

Níže uvedený dotaz vám pomůže prohledávat všechny protokoly toku z místního prostředí za posledních 30 dní.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Chcete-li zobrazit cestu objektu blob pro toky ve výše uvedeném dotazu, použijte následující dotaz:

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

Výše uvedený dotaz vytvoří adresu URL pro přímý přístup k objektu blob. Adresa URL s zástupnými symboly je uvedená níže:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Pole používaná ve schématu Analýzy provozu
  > [!IMPORTANT]
  > 22. srpna 2019 bylo aktualizováno schéma dopravní analýzy. Nové schéma poskytuje zdrojové a cílové IP adresy samostatně odstranění potřeba analyzovat FlowDirection pole dělat dotazy jednodušší. </br>
  > FASchemaVersion_s aktualizováno od 1 do 2. </br>
  > Zastaralá pole: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s FlowCount_d </br>
  > Nová pole: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Zastaralá pole budou k dispozici do 22.11.2019.

Traffic Analytics je postavenna na Log Analytics, takže můžete spouštět vlastní dotazy na data dekorovaná Traffic Analytics a nastavit výstrahy na stejné.

Níže jsou uvedena pole ve schématu a to, co znamená

| Pole | Formát | Komentáře |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabulka dat analýzy provozu
| SubType_s | FlowLog | Podtyp pro protokoly toku. Používejte pouze "FlowLog", jiné hodnoty SubType_s jsou určeny pro vnitřní fungování produktu |
| FASchemaVersion_s |   2   | Verze schématu. Neodráží verzi protokolu toku nsg |
| TimeProcessed_t   | Datum a čas v utc  | Čas, kdy analýza provozu zpracovala protokoly nezpracovaných toků z účtu úložiště |
| FlowIntervalStartTime_t | Datum a čas v utc |  Počáteční čas intervalu zpracování protokolu toku. To je doba, od které se měří interval průtoku |
| FlowIntervalEndTime_t | Datum a čas v utc | Čas ukončení intervalu zpracování protokolu toku |
| FlowStartTime_t | Datum a čas v utc |  První výskyt toku (který bude agregován) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". Tento tok získá agregované na základě logiky agregace |
| FlowEndTime_t | Datum a čas v utc | Poslední výskyt toku (který bude agregován) v intervalu zpracování protokolu toku mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". Pokud jde o protokol toku v2, toto pole obsahuje čas, kdy byl spuštěn poslední tok se stejnou čtyřčlennou řazenou skupinou n-tice (označenjako "B" v záznamu hrubého toku) |
| FlowType_s |  * Intravnet <br> * Intervnet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExterníPublic <br> * ŠkodlivýFlow <br> * Neznámý Soukromé <br> * Neznámý | Definice v poznámkách pod tabulkou |
| SrcIP_s | Zdrojová IP adresa | Bude prázdné v případě toků AzurePublic a ExternalPublic |
| DestIP_s | Cílová IP adresa | Bude prázdné v případě toků AzurePublic a ExternalPublic |
| VMIP_s | IP adresa virtuálního počítačů | Používá se pro toky AzurePublic a ExternalPublic |
| PublicIP_s | Veřejné IP adresy | Používá se pro toky AzurePublic a ExternalPublic |
| DestPort_d | Cílový port | Port, ve kterém je příchozí provoz |
| L4Protocol_s  | * T <br> * U  | Transportní protokol. T = TCP <br> U = UDP |
| L7Protocol_s  | Název protokolu | Odvozeno z cílového portu |
| FlowDirection_s | * I = Příchozí<br> * O = Odchozí | Směr toku dovnitř/ven z nsg podle protokolu průtoku |
| FlowStatus_s  | * A = Povoleno pravidlem nsg <br> * D = Odepřeno pravidlem nsg  | Stav toku povolený/nblocked nSG podle protokolu toku |
| NSGList_s | \<RESOURCEGROUP_NAME> \/ NSG_NAME>\/<>< | Skupina zabezpečení sítě (NSG) spojená s tokem |
| NSGRules_s | \<Hodnota indexu \| \<0)>\| \<NSG_RULENAME \| \<>NSG_RULENAME \| \<směr toku>stav toku>FlowCount ProcessedByRule> |  Pravidlo souboru nSG, které tento tok povolilo nebo zamítlo |
| NSGRule_s | NSG_RULENAME |  Pravidlo souboru nSG, které tento tok povolilo nebo zamítlo |
| NSGRuleType_s | * Uživatel definován * Výchozí |   Typ pravidla skupiny nsg používaný tokem |
| MACAddress_s | Adresa MAC | MAC adresa nic, na které byl zachycen tok |
| Subscription_s | Předplatné virtuální sítě Azure/ síťové rozhraní/ virtuální počítač je naplněn v tomto poli | Použitelné jenom pro flowtype = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate typy toků (typy toků, kde je azure jepouze jedna strana) |
| Subscription1_s | ID předplatného | ID předplatného virtuální sítě/ síťového rozhraní/ virtuálního počítače, do kterého zdrojová IP adresa v toku patří |
| Subscription2_s | ID předplatného | ID předplatného virtuální sítě/ síťového rozhraní/ virtuálního počítače, do kterého cílová IP adresa v toku patří |
| Region_s | Oblast Azure virtuální sítě/ síťového rozhraní/ virtuálního počítače, do které ip adresa v toku patří | Použitelné jenom pro flowtype = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate typy toků (typy toků, kde je azure jepouze jedna strana) |
| Region1_s | Oblast Azure | Oblast Azure virtuální sítě/ síťového rozhraní/ virtuálního počítače, do které zdrojová IP adresa v toku patří |
| Region2_s | Oblast Azure | Oblast Azure virtuální sítě, do které cílová IP adresa v toku patří |
| NIC_s | \<resourcegroup_Name \/ \<>> NetworkInterfaceName |  Nic přidružené k odesílání nebo přijímání provozu virtuálního montovana |
| NIC1_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | Nic spojené se zdrojovou IP v toku |
| NIC2_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | Nic spojená s cílovou IP adresou v toku |
| VM_s | <> \/ \<resourcegroup_Name resourcegroup_Name>networkinterfacename | Virtuální počítač přidružený k NIC_s síťového rozhraní |
| VM1_s | <>\<resourcegroup_Name> resourcegroup_Name/ VirtualMachineName | Virtuální počítač přidružený ke zdrojové IP adrese v toku |
| VM2_s | <>\<resourcegroup_Name> resourcegroup_Name/ VirtualMachineName | Virtuální počítač přidružený k cílové IP adrese v toku |
| Subnet_s | <ResourceGroup_Name <>/VNET_Name>/<>/Název\<podsítě> | Podsíť přidružená k NIC_s |
| Subnet1_s | <ResourceGroup_Name <>/VNET_Name>/<>/Název\<podsítě> | Podsíť přidružená ke zdrojové IP adrese v toku |
| Subnet2_s | <ResourceGroup_Name <>/VNET_Name>/<>/Název\<podsítě>    | Podsíť přidružená k cílové IP adrese v toku |
| ApplicationGateway1_s | \<SubscriptionID>/ResourceGroupName\<\<>/ApplicationGatewayName> | Aplikační brána přidružená ke zdrojové IP adrese v toku |
| ApplicationGateway2_s | \<SubscriptionID>/ResourceGroupName\<\<>/ApplicationGatewayName> | Aplikační brána přidružená k cílové IP adrese v toku |
| LoadBalancer1_s | \<SubscriptionID>/ResourceGroupName\<\<>/LoadBalancerName> | Vyrovnávání zatížení přidružené ke zdrojové IP adrese v toku |
| LoadBalancer2_s | \<SubscriptionID>/ResourceGroupName\<\<>/LoadBalancerName> | Vyrovnávání zatížení přidružené k cílové IP adrese v toku |
| LocalNetworkGateway1_s | \<SubscriptionID>/ResourceGroupName\<\<>/LocalNetworkGatewayName> | Brána místní sítě přidružená ke zdrojové IP adrese v toku |
| LocalNetworkGateway2_s | \<SubscriptionID>/ResourceGroupName\<\<>/LocalNetworkGatewayName> | Brána místní sítě přidružená k cílové IP adrese v toku |
| ConnectionType_s | Možné hodnoty jsou VNetPeering, VpnGateway a ExpressRoute |    Typ připojení |
| ConnectionName_s | \<SubscriptionID>/ResourceGroupName\<\<>/ConnectionName> | Název připojení. Pro flowtype P2S to bude <gateway name>formátováno jako _<VPN Client IP> |
| ConnectingVNets_s | Seznam názvů virtuálních sítí oddělených místy | V případě topologie rozbočovače a paprsku budou zde naplněny virtuální sítě rozbočovačů |
| Country_s | Dvoupísmenný kód země (ISO 3166-1 alfa-2) | Naplněný pro typ toku ExternalPublic. Všechny IP adresy v PublicIPs_s poli budou sdílet stejný kód země. |
| AzureRegion_s | Umístění oblasti Azure | Naplněný pro typ toku AzurePublic. Všechny IP adresy v PublicIPs_s poli budou sdílet oblast Azure. |
| AllowedInFlows_d | | Počet příchozích toků, které byly povoleny. To představuje počet toků, které sdílely stejný příchozí čtyři řazené kolekce členů do síťového rozhraní, při kterém byl zachycen tok |
| DeniedInFlows_d |  | Počet příchozích toků, které byly odepřeny. (Příchozí do síťového rozhraní, při kterém byl zachycen tok) |
| AllowedOutFlows_d | | Počet odchozích toků, které byly povoleny (Odchozí do síťového rozhraní, při kterém byl tok zachycen) |
| DeniedOutFlows_d  | | Počet odchozích toků, které byly odepřeny (Odchozí do síťového rozhraní, při kterém byl tok zachycen) |
| FlowCount_d | Zastaralé Celkový počet toků, které odpovídaly stejné čtyřčlenné řazené kolekce členů. V případě typů toků ExternalPublic a AzurePublic bude počet zahrnovat také toky z různých adres PublicIP.
| InboundPackets_d | Pakety přijaté jako zachycené v síťovém rozhraní, kde bylo použito pravidlo sítě Zabezpečení sítě | To je naplněna pouze pro schéma protokolu toku skupiny NSG. |
| OutboundPackets_d  | Pakety odeslané jako zachycené v síťovém rozhraní, ve kterém bylo použito pravidlo sítě Zabezpečení sítě | To je naplněna pouze pro schéma protokolu toku skupiny NSG. |
| InboundBytes_d |  Bajty přijaté jako zachycené v síťovém rozhraní, kde bylo použito pravidlo sítě zabezpečení sítě | To je naplněna pouze pro schéma protokolu toku skupiny NSG. |
| OutboundBytes_d | Bajty odeslané jako zachycené v síťovém rozhraní, kde bylo použito pravidlo sítě Zabezpečení sítě | To je naplněna pouze pro schéma protokolu toku skupiny NSG. |
| CompletedFlows_d  |  | Tato hodnota je naplněna nenulovou hodnotou pouze pro schéma protokolu toku skupiny NSG verze 2. |
| PublicIPs_s | <>\| \<>\| \<>\| \<>\| \<>\| \<>\|>>>>>>>>>>> \<INBOUND_BYTES OUTBOUND_BYTES>INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT PUBLIC_IP>>>>. | Příspěvky oddělené podle pruhů |
| SrcPublicIPs_s | <INBOUND_PACKETS \| \<OUTBOUND_PACKETS \| \<OUTBOUND_PACKETS \| \<OUTBOUND_PACKETS \| \<OUTBOUND_PACKETS \| \<OUTBOUND_PACKETS \|OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS FLOW_STARTED_COUNT INBOUND_BYTES>\<OUTBOUND_BYTES>>>FLOW_ENDED_COUNT>>SOURCE_PUBLIC_IP FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT> FLOW_STARTED_COUNT | Příspěvky oddělené podle pruhů |
| DestPublicIPs_s | INBOUND_BYTES>\| \< \| \< \| \< \| \< \<OUTBOUND_BYTES>\|INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT><DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP DESTINATION_PUBLIC_IP> \| \< | Příspěvky oddělené podle pruhů |

### <a name="notes"></a>Poznámky

1. V případě toků AzurePublic a ExternalPublic se IP virtuálního počítače Azure vlastněný zákazníkem naplní v poli VMIP_s, zatímco veřejné IP adresy jsou naplněny v poli PublicIPs_s. Pro tyto dva typy toku bychom měli použít VMIP_s a PublicIPs_s místo SrcIP_s a DestIP_s polí. Pro adresy AzurePublic a ExternalPublicIP dále agregujeme tak, aby byl počet záznamů ingestovaných do pracovního prostoru analýzy protokolů zákazníků minimální. (Toto pole bude brzy zastaralé a měli bychom používat SrcIP_ a DestIP_s v závislosti na tom, zda byl azure VM zdrojem nebo cílem v toku)
1. Podrobnosti pro typy toků: Na základě IP adres zapojených do toku kategorizujeme toky do následujících typů toků:
1. IntraVNet – obě IP adresy v toku jsou umístěny ve stejné virtuální síti Azure.
1. InterVNet – IP adresy v toku jsou umístěny ve dvou různých virtuálních sítích Azure.
1. S2S – (site to site) Jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa patří do zákaznické sítě (Site) připojené k virtuální síti Azure prostřednictvím brány VPN nebo express route.
1. P2S – (point to site) Jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa patří do zákaznické sítě (Site) připojené k virtuální síti Azure prostřednictvím brány VPN.
1. AzurePublic – jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa patří interním ip adresám Azure vlastněným Microsoftem. Veřejné IP adresy vlastněné zákazníkem nebudou součástí tohoto typu toku. Například každý zákazník vlastněný virtuální počítač odesílání provozu do služby Azure (koncový bod úložiště) by být rozděleny do kategorií podle tohoto typu toku.
1. ExternalPublic – jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa je veřejná IP adresa, která není v Azure, není hlášena jako škodlivá v informačních kanálech ASC, které Traffic Analytics spotřebovává pro interval zpracování mezi " FlowIntervalStartTime_t" a "FlowIntervalEndTime_t".
1. MaliciousFlow – jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa je veřejná IP adresa, která není v Azure a je hlášena jako škodlivá v informačních kanálech ASC, které Traffic Analytics spotřebovává pro interval zpracování mezi " FlowIntervalStartTime_t" a "FlowIntervalEndTime_t".
1. NeznámýSoukromý – Jedna z IP adres patří do virtuální sítě Azure, zatímco druhá IP adresa patří do privátního rozsahu IP adres, jak je definováno v RFC 1918 a nelze ji mapovat službou Traffic Analytics na web vlastněný zákazníkem nebo virtuální síť Azure.
1. Neznámý – nelze mapovat jednu z IP adres v tocích s topologii zákazníka v Azure i v místním (site).
1. Některé názvy polí \_jsou \_připojeny s s nebo d. Neoznačují zdroj a cíl, ale označují řetězec datových typů a desetinné místo.

### <a name="next-steps"></a>Další kroky
Odpovědi na nejčastější dotazy najdete v [tématu Nejčastější dotazy k analýze provozu,](traffic-analytics-faq.md) kde najdete podrobnosti o [funkcích,](traffic-analytics.md) v dokumentaci k analýze provozu.
