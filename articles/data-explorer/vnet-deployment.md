---
title: Nasazení Průzkumníka dat Azure do virtuální sítě
description: Přečtěte si, jak nasadit Azure Data Explorer do virtuální sítě.
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b0530ddada68cc9d07753a3b8ab30bff642e26dd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618661"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Nasazení clusteru Azure Data Explorer do virtuální sítě

Tento článek vysvětluje prostředky, které jsou k dispozici při nasazení clusteru Azure Data Explorer do vlastní virtuální sítě Azure. Tyto informace vám pomohou nasadit cluster do podsítě ve virtuální síti (Virtuální síť). Další informace o virtuálních sítích Azure najdete v tématu [Co je virtuální síť Azure?](/azure/virtual-network/virtual-networks-overview)

   ![Diagram virtuálnísítě](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer podporuje nasazení clusteru do podsítě ve vaší virtuální síti (Virtuální síť). Tato funkce umožňuje:

* Vynucujte pravidla [skupiny zabezpečení sítě](/azure/virtual-network/security-overview) (NSG) pro provoz clusteru Azure Data Explorer.
* Připojte místní síť k podsíti clusteru Azure Data Explorer.
* Zabezpečte zdroje datového připojení[(Event Hub](/azure/event-hubs/event-hubs-about) a [Event Grid)](/azure/event-grid/overview)pomocí [koncových bodů služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Přístup k clusteru Azure Data Explorer ve virtuální síti

Ke clusteru Azure Data Explorer u každé služby (služby správy motoru a dat) můžete přistupovat pomocí následujících IP adres:

* **Privátní IP:** Používá se pro přístup ke clusteru uvnitř virtuální sítě.
* **Veřejná IP adresa**: Používá se pro přístup ke clusteru mimo virtuální síť pro správu a monitorování a jako zdrojová adresa pro odchozí připojení spuštěná z clusteru.

Pro přístup ke službě jsou vytvořeny následující záznamy DNS: 

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (správa dat) jsou mapovány na veřejnou IP adresu pro každou službu. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (správa dat) jsou mapovány na privátní IP adresu pro každou službu.

## <a name="plan-subnet-size-in-your-vnet"></a>Plánování velikosti podsítě ve virtuální síti

Velikost podsítě používané k hostování clusteru Průzkumníka dat Azure nelze po nasazení podsítě změnit. Ve vaší virtuální síti Azure Data Explorer používá jednu privátní IP adresu pro každý virtuální počítač a dvě privátní IP adresy pro interní vyrovnávání zatížení (správa motoru a dat). Azure networking také používá pět IP adres pro každou podsíť. Azure Data Explorer zřisá dva virtuální počítače pro službu správy dat. Virtuální počítače služby motoru jsou zřízeny podle kapacity škálování konfigurace uživatele.

Celkový počet IP adres:

| Použití | Počet adres |
| --- | --- |
| Servis motorů | 1 na instanci |
| Služba správy dat | 2 |
| Interní nástroje pro vyrovnávání zatížení | 2 |
| Vyhrazené adresy Azure | 5 |
| **Celkem** | **#engine_instances + 9** |

> [!IMPORTANT]
> Velikost podsítě musí být naplánovaná předem, protože ji po nasazení Průzkumníka dat Azure nejde změnit. Proto rezerva potřebné velikosti podsítě odpovídajícím způsobem.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Koncové body služby pro připojení k Azure Data Exploreru

[Koncové body služby Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) umožňují zabezpečit prostředky Azure s více tenanty do vaší virtuální sítě.
Nasazení clusteru Azure Data Explorer do podsítě umožňuje nastavit datová připojení pomocí [Centra událostí](/azure/event-hubs/event-hubs-about) nebo [gridu událostí](/azure/event-grid/overview) a zároveň omezit podkladové prostředky pro podsíť Azure Data Explorer.

> [!NOTE]
> Při použití nastavení EventGrid s [úložištěm](/azure/storage/common/storage-introduction) a [Event Hub], účet úložiště použitý v předplatném lze uzamknout s koncovými body služby do podsítě Azure Data Explorer a zároveň povolit důvěryhodné služby platformy Azure v [konfiguraci brány firewall](/azure/storage/common/storage-network-security), ale Centrum událostí nemůže povolit Service Endpoint, protože nepodporuje důvěryhodné [služby platformy Azure](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>Závislosti pro nasazení virtuální sítě

### <a name="network-security-groups-configuration"></a>Konfigurace skupin zabezpečení sítě

[Skupiny zabezpečení sítě (NSG)](/azure/virtual-network/security-overview) poskytují možnost řídit přístup k síti v rámci virtuální sítě. K Průzkumníku dat Azure se dá přistupovat pomocí dvou koncových bodů: HTTPs (443) a TDS (1433). Následující pravidla skupiny nsg musí být nakonfigurována tak, aby umožňovala přístup k těmto koncovým bodům pro správu, monitorování a správnou funkci clusteru.

#### <a name="inbound-nsg-configuration"></a>Příchozí konfigurace služby NSG

| **Použití**   | **Od**   | **Akce**   | **Protokol**   |
| --- | --- | --- | --- |
| Správa  |[Adresy správy ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | Podsíť ADX:443  | TCP  |
| Monitorování stavu  | [Adresy pro monitorování stavu adx](#health-monitoring-addresses)  | Podsíť ADX:443  | TCP  |
| Interní komunikace ADX  | Podsíť ADX: Všechny porty  | Podsíť ADX:Všechny porty  | Všechny  |
| Povolit příchozí služby Azure balancer (sonda stavu)  | AzureLoadBalancer  | Podsíť ADX:80 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Odchozí konfigurace služby NSG

| **Použití**   | **Od**   | **Akce**   | **Protokol**   |
| --- | --- | --- | --- |
| Závislost na úložišti Azure  | Podsíť ADX  | Skladování:443  | TCP  |
| Závislost na Datovém jezeře Azure  | Podsíť ADX  | AzureDataLake:443  | TCP  |
| EventHub ingestování a sledování služeb  | Podsíť ADX  | EventHub:443,5671  | TCP  |
| Publikovat metriky  | Podsíť ADX  | AzureMonitor:443 | TCP  |
| Ke stažení konfigurace Azure Monitoru  | Podsíť ADX  | [Adresy koncových bodů konfigurace Azure Monitoru](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Služba Active Directory (je-li k dispozici) | Podsíť ADX | AzureActiveDirectory:443 | TCP |
| Certifikační autorita | Podsíť ADX | Internet:80 | TCP |
| Interní komunikace  | Podsíť ADX  | Podsíť ADX: všechny porty  | Všechny  |
| Porty, které `sql\_request` `http\_request` se používají pro a pluginy  | Podsíť ADX  | Internet:Vlastní  | TCP  |

### <a name="relevant-ip-addresses"></a>Příslušné IP adresy

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP adresy správy Průzkumníka dat Azure

| Region (Oblast) | Adresy |
| --- | --- |
| Austrálie – střed | 20.37.26.134 |
| Austrálie Central2 | 20.39.99.177 |
| Austrálie – východ | 40.82.217.84 |
| Austrálie – jihovýchod | 20.40.161.39 |
| BrazílieJižní | 191.233.25.183 |
| Střední Kanada | 40.82.188.208 |
| Kanada – východ | 40.80.255.12 |
| Indie – střed | 40.81.249.251, 104.211.98.159 |
| USA – střed | 40.67.188.68 |
| Centrální US EUAP | 40.89.56.69 |
| Východní Asie | 20.189.74.103 |
| USA – východ | 52.224.146.56 |
| USA – východ 2 | 52.232.230.201 |
| Východní US2 EUAP | 52.253.226.110 |
| Francie – střed | 40.66.57.91 |
| Francie – jih | 40.82.236.24 |
| Japonsko – východ | 20.43.89.90 |
| Japonsko – západ | 40.81.184.86 |
| Jižní Korea – střed | 40.82.156.149 |
| Jižní Korea – jih | 40.80.234.9 |
| USA – středosever | 40.81.45.254 |
| Severní Evropa | 52.142.91.221 |
| Jižní Afrika – sever | 102.133.129.138 |
| Jižní Afrika – západ | 102.133.0.97 |
| USA – středojih | 20.45.3.60 |
| Jihovýchodní Asie | 40.119.203.252 |
| Indie – jih | 40.81.72.110, 104.211.224.189 |
| Spojené království – jih | 40.81.154.254 |
| Spojené království – západ | 40.81.122.39 |
| USA – středozápad | 52.159.55.120 |
| Západní Evropa | 51.145.176.215 |
| Indie – západ | 40.81.88.112, 104.211.160.120 |
| USA – západ | 13.64.38.225 |
| USA – západ 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adresy pro monitorování stavu

| Region (Oblast) | Adresy |
| --- | --- |
| Austrálie – střed | 191.239.64.128 |
| Austrálie – střed 2 | 191.239.64.128 |
| Austrálie – východ | 191.239.64.128 |
| Austrálie – jihovýchod | 191.239.160.47 |
| Brazílie – jih | 23.98.145.105 |
| Střední Kanada | 168.61.212.201 |
| Kanada – východ | 168.61.212.201 |
| Indie – střed | 23.99.5.162 |
| USA – střed | 168.61.212.201 |
| Centrální US EUAP | 168.61.212.201 |
| Východní Asie | 168.63.212.33 |
| USA – východ | 137.116.81.189 |
| USA – východ 2 | 137.116.81.189 |
| Východní USA 2 EUAP | 137.116.81.189 |
| Francie – střed | 23.97.212.5 |
| Francie – jih | 23.97.212.5 |
| Japonsko – východ | 138.91.19.129 |
| Japonsko – západ | 138.91.19.129 |
| Jižní Korea – střed | 138.91.19.129 |
| Jižní Korea – jih | 138.91.19.129 |
| USA – středosever | 23.96.212.108 |
| Severní Evropa | 191.235.212.69 
| Jižní Afrika – sever | 104.211.224.189 |
| Jižní Afrika – západ | 104.211.224.189 |
| USA – středojih | 23.98.145.105 |
| Indie – jih | 23.99.5.162 |
| Jihovýchodní Asie | 168.63.173.234 |
| Spojené království – jih | 23.97.212.5 |
| Spojené království – západ | 23.97.212.5 |
| USA – středozápad | 168.61.212.201 |
| Západní Evropa | 23.97.212.5 |
| Indie – západ | 23.99.5.162 |
| USA – západ | 23.99.5.162 |
| USA – západ 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Adresy koncových bodů konfigurace Azure Monitoru

| Region (Oblast) | Adresy |
| --- | --- |
| Austrálie – střed | 52.148.86.165 |
| Austrálie – střed 2 | 52.148.86.165 |
| Austrálie – východ | 52.148.86.165 |
| Austrálie – jihovýchod | 52.148.86.165 |
| Brazílie – jih | 13.68.89.19 |
| Střední Kanada | 13.90.43.231 |
| Kanada – východ | 13.90.43.231 |
| Indie – střed | 13.71.25.187 |
| USA – střed | 52.173.95.68 |
| Centrální US EUAP | 13.90.43.231 |
| Východní Asie | 13.75.117.221 |
| USA – východ | 13.90.43.231 |
| USA – východ 2 | 13.68.89.19 |    
| Východní USA 2 EUAP | 13.68.89.19 |
| Francie – střed | 52.174.4.112 |
| Francie – jih | 52.174.4.112 |
| Japonsko – východ | 13.75.117.221 |
| Japonsko – západ | 13.75.117.221 |
| Jižní Korea – střed | 13.75.117.221 |
| Jižní Korea – jih | 13.75.117.221 |
| USA – středosever | 52.162.240.236 |
| Severní Evropa | 52.169.237.246 |
| Jižní Afrika – sever | 13.71.25.187 |
| Jižní Afrika – západ | 13.71.25.187 |
| USA – středojih | 13.84.173.99 |
| Indie – jih | 13.71.25.187 |
| Jihovýchodní Asie | 52.148.86.165 |
| Spojené království – jih | 52.174.4.112 |
| Spojené království – západ | 52.169.237.246 |
| USA – středozápad | 52.161.31.69 |
| Západní Evropa | 52.174.4.112 |
| Indie – západ | 13.71.25.187 |
| USA – západ | 40.78.70.148 |
| USA – západ 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Nastavení ExpressRoute

Pomocí ExpressRoute se můžete připojit k místní síti k virtuální síti Azure. Běžným nastavením je inzerování výchozí ho trasy (0.0.0.0/0) prostřednictvím relace protokolu BGP (Border Gateway Protocol). To vynutí přenosy přicházející z virtuální sítě, které mají být předány do místní sítě zákazníka, které mohou upustit provoz, což způsobí přerušení odchozích toků. Chcete-li překonat toto výchozí [nastavení, uživatelem definované trasy (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) lze nakonfigurovat a další směrování bude *Internet*. Vzhledem k tomu, že UDR má přednost před protokolem BGP, bude provoz určen k Internetu.

## <a name="securing-outbound-traffic-with-firewall"></a>Zabezpečení odchozího provozu pomocí brány firewall

Pokud chcete zabezpečit odchozí provoz pomocí [Azure Firewall](/azure/firewall/overview) nebo jakéhokoli virtuálního zařízení k omezení názvů domén, musí být v bráně firewall povoleny následující plně kvalifikované názvy domén (FQDN).

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Je také nutné definovat [směrovací tabulku](/azure/virtual-network/virtual-networks-udr-overview) v podsíti s [adresami správy](#azure-data-explorer-management-ip-addresses) a [adresami monitorování stavu](#health-monitoring-addresses) s dalším *směrováním internetu,* aby se zabránilo problémům s asymetrickými cestami.

Například pro oblast **Západní USA** musí být definovány následující UDR:

| Name (Název) | Předpona adresy | Další směrování |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Nasazení clusteru Azure Data Explorer do virtuální sítě pomocí šablony Azure Resource Manager

Pokud chcete nasadit cluster Azure Data Explorer do virtuální sítě, použijte [cluster Deploy Azure Data Explorer do](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) šablony Správce prostředků Virtuální sítě Azure.

Tato šablona vytvoří cluster, virtuální síť, podsíť, skupinu zabezpečení sítě a veřejné IP adresy.
