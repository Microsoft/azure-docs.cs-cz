---
title: Azure traffic analytics schématu | Dokumentace Microsoftu
description: Pochopení schématu Traffic Analytics analyzovat protokoly toků skupin zabezpečení sítě Azure.
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
ms.openlocfilehash: 922e01c26a2cfe24c8b8a32bb8037d9b3b3384c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109116"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregace schéma a data a analýzy provozu

Analýza provozu je cloudové řešení, která poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu analyzuje protokoly toku Network Watcher sítě zabezpečení skupiny (NSG) poskytnout přehled o toku provozu ve vašem cloudu Azure. Analýza provozu vám umožňuje:

- Vizualizace aktivity sítě ve vašich předplatných Azure a identifikace hotspotů.
- Identifikovat ohrožení zabezpečení a zabezpečte svoji síť, informace, jako je otevření portů, když se aplikace pokoušejí přístup k Internetu a připojení k podvodné sítě virtuálních počítačů (VM).
- Pochopit vzory v toku provozu mezi oblastmi Azure a Internetem pro optimalizaci vašeho nasazení sítě pro výkon a kapacitu.
- Odhalte chybné konfigurace sítě, což vede k selhání připojení ve vaší síti.
- Vědět, využití sítě v bajtech, paketů nebo toky.

### <a name="data-aggregation"></a>Agregace dat

1. Všechny protokoly toku NSG mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t" jsou zachyceny v minutových intervalech v účtu úložiště jako objekty BLOB před zpracováním analýzu provozu. 
2. Výchozí interval zpracování analýzy provozu je 60 minut. To znamená, že každých 60 minut, které analýzu provozu vybere objekty BLOB ze služby storage pro agregaci.
3. Toky, které mají stejné Zdrojová IP adresa, cílová IP adresa, cílový port, název skupiny zabezpečení sítě, pravidlo NSG, směr toku a Transport layer protocol (TCP nebo UDP) (Poznámka: Zdrojový port je vyloučit z agregace) jsou spouštěný do jediného toku podle analýzy provozu
4. Tento jeden záznam je upravena (podrobnosti v části níže) a přijaté v Log Analytics pomocí analýzy provozu.
5. Pole FlowStartTime_t označuje první výskyt takové agregované tok, který (stejné čtyři-řazené kolekce členů) v protokolu toku zpracování interval mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". 
6. Pro libovolné prostředky v TA toky uvedené v uživatelském rozhraní jsou celkový počet toků vidět skupiny zabezpečení sítě, ale v protokolu Anlaytics uživateli se zobrazí pouze jeden, snížené záznam. Pokud chcete zobrazit všechny toky, použijte blob_id pole, které lze odkazovat z úložiště. Celkový tok pro Spolehněte se, že záznam bude odpovídat jednotlivých toků v objektu blob.


### <a name="fields-used-in-traffic-analytics-schema"></a>Pole používá ve schématu analýzy provozu

Analýza provozu je nástavbou Log Analytics, takže můžete spouštět vlastní dotazy na data upravena podle analýzy provozu a nastavit výstrahy na stejné.

Tady je polí na schéma a jejich místo

| Pole | Formát | Komentáře | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabulka Anlaytics přenosy dat
| SubType_s | FlowLog | Podtyp pro protokoly toků |
| FASchemaVersion_s |   1   | Scehma verze. Neodráží verze protokolů toku NSG |
| TimeProcessed_t   | Datum a čas ve standardu UTC  | Čas zpracování analýzy provozu nezpracovaná tok protokoly z účtu úložiště |
| FlowIntervalStartTime_t | Datum a čas ve standardu UTC |  Počáteční čas interval zpracování protokolu toku. To je čas, ze kterého se měří interval toku |
| FlowIntervalEndTime_t | Datum a čas ve standardu UTC | Koncový čas interval zpracování protokolů toku |
| FlowStartTime_t | Datum a čas ve standardu UTC |  První výskyt toku (to se získání agregované) v interval zpracování protokolu tok mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". Tento tok získá agregovat podle logiky agregace |
| FlowEndTime_t | Datum a čas ve standardu UTC | Poslední výskyt tok (což se získání agregované) v interval zpracování protokolu tok mezi "FlowIntervalStartTime_t" a "FlowIntervalEndTime_t". Z hlediska v2 protokolu toku toto pole obsahuje čas zahájení poslední toku pomocí stejné čtyři-řazené kolekce členů (označené jako "B" v záznamu nezpracovaná toku) |
| FlowType_s |  * IntraVNet <br> * Mezi virtuálními sítěmi <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Privátní neznámý <br> * Neznámý | Definice v níže uvedené tabulce poznámky |
| SrcIP_s | Zdrojová IP adresa | V případě AzurePublic prázdné a ExternalPublic toky |
| DestIP_s | Cílová IP adresa | V případě AzurePublic prázdné a ExternalPublic toky |
| VMIP_s | IP adresu virtuálního počítače | Používá pro AzurePublic a ExternalPublic toků |
| PublicIP_S | Veřejné IP adresy | Používá pro AzurePublic a ExternalPublic toků |
| DestPort_d | Cílový port | Port, na kterém je příchozí provoz | 
| L4Protocol_s  | * T <br> * U  | Přenosový protokol. T = TCP <br> U = UDP | 
| L7Protocol_s  | Název protokolu | Odvozený od cílový port |
| FlowDirection_s | * Můžu = příchozí<br> * O = odchozí | Směr toku do/z NSG podle protokolu toku | 
| FlowStatus_s  | * S = povolen pravidlem NSG <br> * D = odepřen pravidlem NSG  | Stav toku povolený/nblocked pomocí skupiny zabezpečení sítě podle protokolu toku |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Skupina zabezpečení sítě (NSG) přidružené k toku |
| NSGRules_s | \<Index hodnoty 0) >< NSG_RULENAME >\<směr toku >\<tok Stav >\<FlowCount ProcessedByRule > |  Pravidlo skupiny zabezpečení sítě, která povolí nebo zakáže tento tok |
| NSGRuleType_s | * Uživatelem definované * výchozí |   Typ pravidla skupiny zabezpečení sítě používá tok |
| MACAddress_s | Adresa MAC | Adresa MAC síťové karty, na které se zaznamenala v toku |
| Subscription_s | Předplatné virtuální sítě Azure / síťové rozhraní / virtuální počítač se vyplní v tomto poli | Vztahuje se jenom typ toku = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate toku typy (ve kterém je jenom jedna strana azure tok typy) |
| Subscription1_s | ID předplatného | ID předplatného virtuální síť / síťové rozhraní nebo virtuální počítač, ke kterému patří Zdrojová IP adresa v toku |
| Subscription2_s | ID předplatného | ID předplatného virtuální síť / síťové rozhraní nebo virtuální počítač, ke kterému patří cílová IP adresa v toku |
| Region_s | Oblast Azure z virtuální sítě nebo síťové rozhraní / virtuální počítač, ke kterému patří IP toku | Vztahuje se jenom typ toku = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow a UnknownPrivate toku typy (ve kterém je jenom jedna strana azure tok typy) |
| Region1_s | Oblast Azure | Oblast Azure z virtuální sítě nebo síťové rozhraní nebo virtuální počítač, ke kterému patří Zdrojová IP adresa v toku |
| Region2_s | Oblast Azure | Oblast Azure z virtuální sítě, ke kterému patří cílová IP adresa v toku |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NIC přidruženou k virtuálnímu počítači, odesílání a příjem provozu |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Síťové rozhraní přidružené k Zdrojová IP adresa v toku |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Síťové rozhraní přidružené k cílová IP adresa v toku |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Virtuální počítač přidružený k síťovému rozhraní NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuální počítač přidružený Zdrojová IP adresa v toku |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuální počítač přidružený cílová IP adresa v toku |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsíť přidružená NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsíť přidružená Zdrojová IP adresa v toku |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Podsíť přidružená cílová IP adresa v toku |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Služba Application gateway, které jsou přidružené k Zdrojová IP adresa v toku | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Služba Application gateway, které jsou přidružené k cílová IP adresa v toku |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Nástroj pro vyrovnávání zatížení související s Zdrojová IP adresa v toku |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Nástroj pro vyrovnávání zatížení související s Cílová IP adresa v toku |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brána místní sítě, které jsou přidružené k Zdrojová IP adresa v toku |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brána místní sítě, které jsou přidružené k cílová IP adresa v toku |
| ConnectionType_s | Možné hodnoty jsou VNetPeering, brána VPN a ExpressRoute |    Typ připojení |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Název připojení |
| ConnectingVNets_s | Místo oddělený seznam názvů virtuálních sítí | V případě hvězdicovou topologii virtuální sítě centra naplní se tady |
| Country_s | Dvoupísmenná směrové číslo země (ISO, alfa 3166-1-2) | Vyplní pro typ toku ExternalPublic. Všechny IP adresy v poli PublicIPs_s bude sdílet stejný kód země |
| AzureRegion_s | Umístění oblasti Azure | Vyplní pro typ toku AzurePublic. Všechny IP adresy v poli PublicIPs_s podělí o oblasti Azure |
| AllowedInFlows_d | | Počet příchozích toků, které byly povoleny. To představuje počet toků, které sdílejí stejnou čtyři-n-tice příchozí netweork rozhraní, ve kterém se zaznamenala v toku | 
| DeniedInFlows_d |  | Počet příchozích toků, které se zamítla. (Příchozí k síťovému rozhraní, ve kterém se zaznamenala v toku) |
| AllowedOutFlows_d | | Počet odchozích toků, které byly povoleny (odchozí k síťovému rozhraní, ve kterém se zaznamenala v toku) |
| DeniedOutFlows_d  | | Počet odchozích toků, které se zamítla (odchozí k síťovému rozhraní, ve kterém se zaznamenala v toku) |
| FlowCount_d | Zastaralé. Celkový počet toků, které odpovídají stejným čtyři-řazené kolekce členů. V případě typech toků ExternalPublic a AzurePublic bude obsahovat počet toků z různých adres veřejné IP adresy i.
| InboundPackets_d | Pakety jako zachytí na síťové rozhraní, ve kterém byla použita pravidlo skupiny zabezpečení sítě | To je vyplněný pouze pro schéma verze 2 NSG flow protokolu |
| OutboundPackets_d  | Pakety odeslané jako zachytí na síťové rozhraní, ve kterém byla použita pravidlo skupiny zabezpečení sítě | To je vyplněný pouze pro schéma verze 2 NSG flow protokolu |
| InboundBytes_d |  Počet bajtů přijatých jako zachytí na síťové rozhraní, ve kterém byla použita pravidlo skupiny zabezpečení sítě | To je vyplněný pouze pro schéma verze 2 NSG flow protokolu |
| OutboundBytes_d | Bajtů odeslaných jako zachytí na síťové rozhraní, ve kterém byla použita pravidlo skupiny zabezpečení sítě | To je vyplněný pouze pro schéma verze 2 NSG flow protokolu |
| CompletedFlows_d  |  | Tím se načtou nenulovou hodnotu pouze pro schéma verze 2 NSG flow protokolu |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Položky oddělených pruhy |
    
### <a name="notes"></a>Poznámky
    
1. V případě AzurePublic a ExternalPublic toky zákazník, který vlastní že IP adresa virtuálního počítače Azure jsou v VMIP_s pole vyplněné, zatímco veřejné IP adresy se zatím připravují PublicIPs_s pole. Pro tyto typy dvě toku jsme používali VMIP_s a PublicIPs_s místo SrcIP_s a DestIP_s pole. Pro AzurePublic a ExternalPublicIP adresy jsme agregovat dále tak, aby byla minimální počet záznamů přijaté do pracovního prostoru log analytics zákazníka. (Toto pole bude brzy přestanou používat a jsme měli použít SrcIP_ a DestIP_s v závislosti na tom, jestli byl virtuální počítač azure zdroji nebo cíli v toku) 
1. Podrobnosti o typech toků: Podle IP adresy používané v toku, jsme kategorizace toků v následující typy toku: 
1. IntraVNet – obě IP adresy v toku se nachází ve stejné virtuální síti Azure. 
1. Mezi virtuálními sítěmi – IP adresy v toku se nachází ve dvou různých virtuálních sítí Azure. 
1. S2S – jeden (S2s) IP adres patří do virtuální sítě Azure při dalších IP adres patří do sítě zákazníka (lokalita) připojené k Azure Virtual Network prostřednictvím brány VPN nebo Express Route. 
1. P2S - (bod do lokality) některou z IP adres patří do virtuální sítě Azure při dalších IP adres patří do sítě zákazníka (lokalita) připojené k virtuální síti Azure přes bránu VPN.
1. AzurePublic - některou z IP adres patří do virtuální sítě Azure při dalších IP adres patří do Azure interní veřejných IP adres ve vlastnictví společnosti Microsoft. Zákazník vlastní veřejné IP adresy nebude součástí tento typ toku. Například každý zákazník, který vlastní odesílání provozu do služby Azure (koncový bod služby Storage) virtuálního počítače by zařazených do kategorií podle typu tohoto toku. 
1. ExternalPublic - některou z IP adres patří do virtuální sítě Azure IP adresa je veřejná IP adresa, která není v Azure, nebude hlášena jako škodlivou v ASC informační kanály, které využívá analýzy provozu pro zpracování interval mezi " FlowIntervalStartTime_t"a"FlowIntervalEndTime_t". 
1. MaliciousFlow - některou z IP adres patří do virtuální sítě azure IP adresa je veřejná IP adresa, která není v Azure a hlásí jako škodlivou v ASC informační kanály, které využívá analýzy provozu pro zpracování interval mezi" FlowIntervalStartTime_t"a"FlowIntervalEndTime_t". 
1. UnknownPrivate - některou z IP adres patří do virtuální sítě Azure a dalších IP adres patří do rozsah privátních IP adres, jak jsou definovány v dokumentu RFC 1918 nemohly být namapovány analýzu provozu pro vlastní web nebo Azure Virtual Network zákazníka.
1. Neznámé – nelze mapovat buď IP adres v toků s topologií zákazníků v Azure stejně jako místní (lokalita).

### <a name="next-steps"></a>Další kroky
Pokud chcete získat odpovědi na nejčastější dotazy, naleznete v tématu [nejčastější dotazy k analýze provozu](traffic-analytics-faq.md) podrobnosti o funkcích najdete v tématu [dokumentace k analýze provozu](traffic-analytics.md)
    


    


