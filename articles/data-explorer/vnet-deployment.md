---
title: Nasazení Azure Průzkumník dat do Virtual Network (Preview)
description: Přečtěte si, jak nasadit Azure Průzkumník dat do Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 8dec673408b706a92a29f418af3bef4cc05a8d2d
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668579"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Nasazení Azure Průzkumník dat do Virtual Network (Preview)

Tento článek popisuje prostředky, které jsou k dispozici při nasazení clusteru Azure Průzkumník dat do vlastního Virtual Network Azure. Tyto informace vám pomůžou nasadit cluster do podsítě ve vaší Virtual Network (virtuální síť). Další informace o virtuálních sítích Azure najdete v tématu [co je Azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![Diagram virtuální sítě](media/vnet-deployment/vnet-diagram.png)

Azure Průzkumník dat podporuje nasazení clusteru do podsítě v Virtual Network (VNet). Tato funkce umožňuje:

* Vynutili pravidla [skupiny zabezpečení sítě](/azure/virtual-network/security-overview) (NSG) v provozu clusteru Azure Průzkumník dat.
* Připojte svoji místní síť k podsíti Azure Průzkumník dat clusteru.
* Zabezpečte zdroje datových připojení ([centrum událostí](/azure/event-hubs/event-hubs-about) a [Event Grid](/azure/event-grid/overview)) pomocí [koncových bodů služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> Integrace Virtual Network a nasazení je v režimu náhledu. Pokud chcete tuto funkci povolit, otevřete [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Přístup ke clusteru Azure Průzkumník dat ve vaší virtuální síti

K vašemu clusteru Azure Průzkumník dat máte přístup pomocí následujících IP adres pro každou službu (služby pro správu a modul pro správu dat):

* **Privátní IP adresa**: používá se pro přístup ke clusteru uvnitř virtuální sítě.
* **Veřejná IP**adresa: používá se pro přístup ke clusteru mimo virtuální síť pro správu a monitorování a jako zdrojovou adresu pro odchozí připojení spuštěná z clusteru.

Pro přístup ke službě jsou vytvořeny následující záznamy DNS: 

* `[clustername].[geo-region].kusto.windows.net` (Engine) `ingest-[clustername].[geo-region].kusto.windows.net` (Správa dat) jsou namapovány na veřejnou IP adresu pro každou službu. 

* `private-[clustername].[geo-region].kusto.windows.net` (Engine) `private-ingest-[clustername].[geo-region].kusto.windows.net` (Správa dat) jsou namapovány na privátní IP adresu pro každou službu.

## <a name="plan-subnet-size-in-your-vnet"></a>Plánování velikosti podsítě ve virtuální síti

Velikost podsítě, která se používá k hostování clusteru Azure Průzkumník dat, se po nasazení podsítě nedá změnit. Ve vaší virtuální síti používá Azure Průzkumník dat jednu privátní IP adresu pro každý virtuální počítač a dvě privátní IP adresy pro interní nástroje pro vyrovnávání zatížení (jádro a správa dat). Sítě Azure také používá pět IP adres pro každou podsíť. Azure Průzkumník dat zřídí dva virtuální počítače pro službu pro správu dat. Virtuální počítače služby Engine se zřídí na kapacitu škálování konfigurace uživatele.

Celkový počet IP adres:

| Použití | Počet adres |
| --- | --- |
| Služba Engine | 1 na instanci |
| Služba správy dat | 2 |
| Interní nástroje pro vyrovnávání zatížení | 2 |
| Rezervované adresy Azure | 5 |
| **Čtení** | **#engine_instances + 9** |

> [!IMPORTANT]
> Velikost podsítě musí být plánována předem, protože po nasazení služby Azure Průzkumník dat není možné ji změnit. Proto odpovídajícím způsobem rezervujte potřebnou velikost podsítě.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Koncové body služby pro připojení k Azure Průzkumník dat

[Koncové body služby Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) umožňují zabezpečit vaše prostředky Azure pro více tenantů do vaší virtuální sítě.
Nasazení clusteru Azure Průzkumník dat do vaší podsítě vám umožní nastavit datová připojení pomocí [centra událostí](/azure/event-hubs/event-hubs-about) nebo [Event Grid](/azure/event-grid/overview) a zároveň omezit základní prostředky pro podsíť Azure Průzkumník dat.

> [!NOTE]
> Při použití instalačního programu EventGrid s [úložištěm](/azure/storage/common/storage-introduction) a [centra událostí] může být účet úložiště, který se používá v předplatném, uzamčený pomocí koncových bodů služby do podsítě Azure Průzkumník dat a přitom povoluje v [konfiguraci brány firewall](/azure/storage/common/storage-network-security)důvěryhodné služby platformy Azure, ale centrum událostí nemůže koncový bod služby povolit, protože nepodporuje důvěryhodné [služby platformy Azure](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>Závislosti pro nasazení virtuální sítě

### <a name="network-security-groups-configuration"></a>Konfigurace skupin zabezpečení sítě

[Skupiny zabezpečení sítě (NSG)](/azure/virtual-network/security-overview) poskytují možnost řídit přístup k síti v rámci virtuální sítě. K Azure Průzkumník dat lze přistupovat pomocí dvou koncových bodů: HTTPs (443) a TDS (1433). Následující pravidla NSG musí být nakonfigurována tak, aby povolovala přístup k těmto koncovým bodům pro účely správy, monitorování a správné fungování clusteru.

#### <a name="inbound-nsg-configuration"></a>Příchozí konfigurace NSG

| **Použití**   | **Výsledkem**   | **Komu**   | **Protokol**   |
| --- | --- | --- | --- |
| Správa  |[ADX Management addresss](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | ADX podsíť: 443  | TCP  |
| Sledování stavu  | [Adresy sledování stavu ADX](#health-monitoring-addresses)  | ADX podsíť: 443  | TCP  |
| Interní komunikace ADX  | ADX podsíť: všechny porty  | ADX podsíť: všechny porty  | Všechno  |
| Povolit příchozí službu Azure Load Balancer (sondu stavu)  | AzureLoadBalancer  | ADX podsíť: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Konfigurace odchozího NSG

| **Použití**   | **Výsledkem**   | **Komu**   | **Protokol**   |
| --- | --- | --- | --- |
| Závislost na Azure Storage  | ADX podsíť  | Úložiště: 443  | TCP  |
| Závislost na Azure Data Lake  | ADX podsíť  | AzureDataLake: 443  | TCP  |
| Monitorování příjmu a sledování služby EventHub  | ADX podsíť  | EventHub: 443, 5671  | TCP  |
| Publikování metrik  | ADX podsíť  | AzureMonitor: 443 | TCP  |
| Stažení konfigurace Azure Monitor  | ADX podsíť  | [Adresy koncových bodů konfigurace Azure monitor](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Služba Active Directory (Pokud je k dispozici) | ADX podsíť | Azureactivedirectory selhala: 443 | TCP |
| Certifikační autorita | ADX podsíť | Internet: 80 | TCP |
| Interní komunikace  | ADX podsíť  | ADX podsíť: všechny porty  | Všechno  |
| Porty používané pro `sql\_request` a moduly plug-in `http\_request`  | ADX podsíť  | Internet: vlastní  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevantní IP adresy

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP adresy pro správu služby Azure Průzkumník dat

| Oblast | Adresy |
| --- | --- |
| Austrálie – střed | 20.37.26.134 |
| Central2 Austrálie | 20.39.99.177 |
| Austrálie – východ | 40.82.217.84 |
| Austrálie – jihovýchod | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Kanada – střed | 40.82.188.208 |
| Kanada – východ | 40.80.255.12 |
| Střední Indie | 40.81.249.251 |
| Střední USA | 40.67.188.68 |
| Střed USA EUAP | 40.89.56.69 |
| Východní Asie | 20.189.74.103 |
| USA – východ | 52.224.146.56 |
| USA – východ 2 | 52.232.230.201 |
| EUAP pro východní USA 2 | 52.253.226.110 |
| Francie – střed | 40.66.57.91 |
| Francie – jih | 40.82.236.24 |
| Japonsko – východ | 20.43.89.90 |
| Japonsko – západ | 40.81.184.86 |
| Korea – střed | 40.82.156.149 |
| Korea – jih | 40.80.234.9 |
| Středoseverní USA | 40.81.45.254 |
| Severní Evropa | 52.142.91.221 |
| Jižní Afrika – sever | 102.133.129.138 |
| Jižní Afrika – západ | 102.133.0.97 |
| Středojižní USA | 20.45.3.60 |
| Jihovýchodní Asie | 40.119.203.252 |
| Jižní Indie | 40.81.72.110 |
| Velká Británie – jih | 40.81.154.254 |
| Velká Británie – západ | 40.81.122.39 |
| Středozápadní USA | 52.159.55.120 |
| Západní Evropa | 51.145.176.215 |
| Západní Indie | 40.81.88.112 |
| Západní USA | 13.64.38.225 |
| Západní USA 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adresy sledování stavu

| Oblast | Adresy |
| --- | --- |
| Austrálie – střed | 191.239.64.128 |
| Austrálie – střed 2 | 191.239.64.128 |
| Austrálie – východ | 191.239.64.128 |
| Austrálie – jihovýchod | 191.239.160.47 |
| Brazílie – jih | 23.98.145.105 |
| Kanada – střed | 168.61.212.201 |
| Kanada – východ | 168.61.212.201 |
| Střední Indie | 23.99.5.162 |
| Střední USA | 168.61.212.201 |
| Střed USA EUAP | 168.61.212.201 |
| Východní Asie | 168.63.212.33 |
| USA – východ | 137.116.81.189 |
| Východ USA 2 | 137.116.81.189 |
| Východní USA 2 EUAP | 137.116.81.189 |
| Francie – střed | 23.97.212.5 |
| Francie – jih | 23.97.212.5 |
| Japonsko – východ | 138.91.19.129 |
| Japonsko – západ | 138.91.19.129 |
| Korea – střed | 138.91.19.129 |
| Korea – jih | 138.91.19.129 |
| Středoseverní USA | 23.96.212.108 |
| Severní Evropa | 191.235.212.69 
| Jižní Afrika – sever | 104.211.224.189 |
| Jižní Afrika – západ | 104.211.224.189 |
| Středojižní USA | 23.98.145.105 |
| Jižní Indie | 23.99.5.162 |
| Jihovýchodní Asie | 168.63.173.234 |
| Velká Británie – jih | 23.97.212.5 |
| Velká Británie – západ | 23.97.212.5 |
| Středozápadní USA | 168.61.212.201 |
| Západní Evropa | 23.97.212.5 |
| Západní Indie | 23.99.5.162 |
| Západní USA | 23.99.5.162 |
| Západní USA 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Adresy koncových bodů konfigurace Azure Monitor

| Oblast | Adresy |
| --- | --- |
| Austrálie – střed | 52.148.86.165 |
| Austrálie – střed 2 | 52.148.86.165 |
| Austrálie – východ | 52.148.86.165 |
| Austrálie – jihovýchod | 52.148.86.165 |
| Brazílie – jih | 13.68.89.19 |
| Kanada – střed | 13.90.43.231 |
| Kanada – východ | 13.90.43.231 |
| Střed Indie | 13.71.25.187 |
| Střed USA | 52.173.95.68 |
| Střed USA EUAP | 13.90.43.231 |
| Východní Asie | 13.75.117.221 |
| Východní USA | 13.90.43.231 |
| Východní USA 2 | 13.68.89.19 | 
| Východní USA 2 EUAP | 13.68.89.19 |
| Francie – střed | 52.174.4.112 |
| Francie – jih | 52.174.4.112 |
| Japonsko – východ | 13.75.117.221 |
| Japonsko – západ | 13.75.117.221 |
| Korea – střed | 13.75.117.221 |
| Korea – jih | 13.75.117.221 |
| Střed USA – sever | 52.162.240.236 |
| Severní Evropa | 52.169.237.246 |
| Jižní Afrika – sever | 13.71.25.187 |
| Jižní Afrika – západ | 13.71.25.187 |
| Střed USA – jih | 13.84.173.99 |
| Jižní Indie | 13.71.25.187 |
| Jihovýchodní Asie | 52.148.86.165 |
| Velká Británie – jih | 52.174.4.112 |
| Velká Británie – západ | 52.169.237.246 |
| Středozápadní USA | 52.161.31.69 |
| Západní Evropa | 52.174.4.112 |
| Západní Indie | 13.71.25.187 |
| Západní USA | 40.78.70.148 |
| Západní USA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Nastavení ExpressRoute

Pomocí ExpressRoute se připojte k Virtual Network Azure k místní síti. Běžným nastavením je inzerování výchozí trasy (0.0.0.0/0) prostřednictvím relace Border Gateway Protocol (BGP). Tím se vynutí předání provozu z Virtual Network do místní sítě zákazníka, která může vyřadit provoz, což způsobí přerušení odchozích toků. K překonání tohoto výchozího nastavení se dá nakonfigurovat [trasa definovaná uživatelem (udr)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) a další segment směrování bude *Internet*. Vzhledem k tomu, že UDR má přednost před protokolem BGP, přenosy budou určené pro Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Zabezpečení odchozího provozu pomocí brány firewall

Pokud chcete zabezpečit odchozí přenosy pomocí [Azure firewall](/azure/firewall/overview) nebo libovolného virtuálního zařízení pro omezení názvů domén, musí být v bráně firewall povolené tyto plně kvalifikované názvy domény (FQDN).

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
production.diagnostics.monitoring.core.windows.net:443
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

Je také potřeba definovat [směrovací tabulku](/azure/virtual-network/virtual-networks-udr-overview) v podsíti s [adresami pro správu](#azure-data-explorer-management-ip-addresses) a [adresami monitorování stavu](#health-monitoring-addresses) s dalším segmentem směrování *Internetu* , aby se předešlo problémům s asymetrickými trasami.

Například pro **západní USA** oblast musí být definována následující udr:

| Name (Název) | Předpona adresy | Další segment směrování |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Nasazení clusteru Azure Průzkumník dat do virtuální sítě pomocí šablony Azure Resource Manager

Pokud chcete nasadit cluster Azure Průzkumník dat do vaší virtuální sítě, použijte [cluster nasazení azure Průzkumník dat do](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) šablony Azure Resource Manager VNET.

Tato šablona vytvoří cluster, virtuální síť, podsíť, skupinu zabezpečení sítě a veřejné IP adresy.
