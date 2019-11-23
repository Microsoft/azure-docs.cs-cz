---
title: Azure virtual network service endpoints
titlesuffix: Azure Virtual Network
description: Zjistěte, jak povolit přímý přístup k prostředkům Azure z virtuální sítě pomocí koncových bodů služby.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378693"
---
# <a name="virtual-network-service-endpoints"></a>Koncové body služby pro virtuální síť

Virtual Network (VNet) service endpoints extend your virtual network private address space. The endpoints also extend the identity of your VNet to the Azure services over a direct connection. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

This feature is available for the following Azure services and regions. The *Microsoft.\** resource is in parenthesis. Enable this resource from the subnet side while configuring service endpoints for your service:

**Obecná dostupnost**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Generally available in all Azure regions.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in all Azure regions.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Generally available in Azure regions where database service is available.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Generally available in all Azure regions.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Generally available in all Azure regions.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Generally available in all Azure regions.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Generally available in all Azure regions where App service is available.

**Public Preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Preview available in all Azure regions where Azure Container Registry is available.

Nejaktuálnější oznámení najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Klíčové výhody

Koncové body služby poskytují následující výhody:

- **Improved security for your Azure service resources**: VNet private address spaces can overlap. You can't use overlapping spaces to uniquely identify traffic that originates from your VNet. Service endpoints provide the ability to secure Azure service resources to your virtual network by extending VNet identity to the service. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network. The rule addition provides improved security by fully removing public internet access to resources and allowing traffic only from your virtual network.
- **Optimal routing for Azure service traffic from your virtual network**: Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Koncové body služby poskytují optimální směrování provozu Azure. 

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to mělo vliv na provoz služby. For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Snadné nastavení a méně režie na správu:** Ve virtuálních sítích už nepotřebujete vyhrazené veřejné IP adresy pro zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. There are no Network Address Translation (NAT) or gateway devices required to set up the service endpoints. You can configure service endpoints through a simple click on a subnet. There's no additional overhead to maintaining the endpoints.

## <a name="limitations"></a>Omezení

- Tato funkce je dostupná pouze pro virtuální sítě nasazené pomocí modelu nasazení Azure Resource Manager.
- Koncové body jsou povolené na podsítích nakonfigurovaných ve virtuálních sítích Azure. Endpoints can't be used for traffic from your premises to Azure services. For more information, see [Secure Azure service access from on-premises](#secure-azure-services-to-virtual-networks)
- Pro Azure SQL se koncový bod služby vztahuje jenom na provoz služeb Azure v rámci oblasti virtuální sítě. For Azure Storage, endpoints also extend to include paired regions where you deploy the virtual network to support Read-Access Geo-Redundant Storage (RA-GRS) and Geo-Redundant Storage (GRS) traffic. For more information, see [Azure paired regions](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- For Azure Data Lake Storage (ADLS) Gen 1, the VNet Integration capability is only available for virtual networks within the same region. Also note that virtual network integration for ADLS Gen1 uses the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD doesn't support service endpoints natively. For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Secure Azure services to virtual networks

- Koncový bod služby pro virtuální síť poskytuje službě Azure identitu vaší virtuální sítě. Once you enable service endpoints in your virtual network, you can add a virtual network rule to secure the Azure service resources to your virtual network.
- Provoz služby Azure z virtuální sítě v současné době používá jako zdrojové IP adresy veřejné IP adresy. S koncovými body služby přepne provoz služby při přístupu ke službě Azure z vaší virtuální sítě na používání privátních adres virtuální sítě jako zdrojových IP adres. Toto přepnutí umožňuje přistupovat ke službám bez potřeby vyhrazených veřejných IP adres, které se používají v branách firewall protokolu IP.

   >[!NOTE]
   > S koncovými body služby se zdrojové IP adresy virtuálních počítačů v podsíti pro obsluhu provozu přepnou z používání veřejných IPv4 adres na privátní IPv4 adresy. Stávající pravidla bran firewall služeb Azure používající veřejné IP adresy Azure přestanou okamžikem přepnutí fungovat. Před nastavováním koncových bodů služby se ujistěte, že pravidla bran firewall služeb Azure toto přepnutí podporují. Při konfiguraci koncových bodů služby může také dojít k dočasnému přerušení provozu služby z této podsítě. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Secure Azure service access from on-premises

  By default, Azure service resources secured to virtual networks aren't reachable from on-premises networks. If you want to allow traffic from on-premises, you must also allow public (typically, NAT) IP addresses from your on-premises or ExpressRoute. You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: If you're using [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) for public peering or Microsoft peering from your premises, you'll need to identify the NAT IP addresses that you're using. For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. For Microsoft peering, the NAT IP addresses are either customer provided or provided by the service provider. To allow access to your service resources, you must allow these public IP addresses in the resource IP firewall setting. To find your public peering ExpressRoute circuit IP addresses, [open a support ticket with ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via the Azure portal. For more information about NAT for ExpressRoute public and Microsoft peering, see [ExpressRoute NAT requirements](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Svázání služeb Azure s virtuálními sítěmi](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurace

- Configure service endpoints on a subnet in a virtual network. Koncové body fungují s jakýmkoli typem výpočetních instancí spuštěných v rámci této podsítě.
- You can configure multiple service endpoints for all supported Azure services (Azure Storage or Azure SQL Database, for example) on a subnet.
- Pro Azure SQL Database musí být virtuální sítě ve stejné oblasti jako prostředek služby Azure. Pokud používáte pro Azure Storage účty GRS a RA-GRS, primární účet musí být ve stejné oblasti jako virtuální síť. For all other services, you can secure Azure service resources to virtual networks in any region. 
- Virtuální síť, ve které je koncový bod nakonfigurovaný, může být ve stejném předplatném jako prostředek služby Azure nebo v jiném předplatném. Další informace o oprávněních požadovaných pro nastavení koncových bodů a zabezpečení služeb Azure najdete v části [Zřizování](#provisioning).
- U podporovaných služeb můžete pomocí koncových bodů služby svázat s virtuálními sítěmi nové nebo existující prostředky.

### <a name="considerations"></a>Požadavky

- After enabling a service endpoint, the source IP addresses of virtual machines in the subnet switch. The source IP addresses switch from using public IPv4 addresses to using their private IPv4 address when communicating with the service from that subnet. Během tohoto přepnutí se ukončí všechna existující otevřená připojení TCP ke službě. Při povolování nebo zakazování koncového bodu služby pro podsíť se ujistěte, že nejsou spuštěné žádné důležité úlohy. Také se ujistěte, že se vaše aplikace můžou po přepnutí IP adres automaticky připojit ke službám Azure.

  Přepnutí IP adres ovlivní pouze provoz služeb z vaší virtuální sítě. There's no impact to any other traffic addressed to or from the public IPv4 addresses assigned to your virtual machines. Pokud máte pro služby Azure existující pravidla brány firewall používající veřejné IP adresy Azure, tato pravidla s přepnutím na privátní adresy virtuální sítě přestanou fungovat.
- With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Skupiny zabezpečení sítě (NSG) s koncovými body služby:
  - By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. This traffic continues to work with service endpoints as is. 
  - If you want to deny all outbound internet traffic and allow only traffic to specific Azure services, you can do so using [service tags](security-overview.md#service-tags) in your NSGs. You can specify supported Azure services as destination in your NSG rules and Azure also provides the maintenance of IP addresses underlying each tag. Další informace najdete v tématu [Značky služeb Azure pro skupiny zabezpečení sítě](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě:** Pokud chcete svázat služby Azure s několika podsítěmi v rámci virtuální sítě nebo mezi několika virtuálními sítěmi, můžete koncové body služby povolit v každé z podsítí nezávisle na sobě a svázat tak prostředky služeb Azure se všemi podsítěmi.
- **Filtering outbound traffic from a virtual network to Azure services**: If you want to inspect or filter the traffic sent to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. Potom můžete na podsíť s nasazeným síťovým virtuálním zařízením použít koncové body služby a svázat prostředky služby Azure pouze s touto podsítí. This scenario might be helpful if you want use network virtual appliance filtering to restrict Azure service access from your virtual network only to specific Azure resources. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Securing Azure resources to services deployed directly into virtual networks**: You can directly deploy various Azure services into specific subnets in a virtual network. Prostředky služby Azure můžete svázat s podsítěmi [spravované služby](virtual-network-for-azure-services.md) nastavením koncového bodu služby v podsíti spravované služby.
- **Disk traffic from an Azure virtual machine**: Virtual Machine Disk traffic for managed and unmanaged disks isn't affected by service endpoints routing changes for Azure Storage. This traffic includes diskIO as well as mount and unmount. You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží

Once you configure service endpoints to a specific service, validate that the service endpoint route is in effect by: 
 
- Ověření zdrojové IP adresy každé žádosti o služby v diagnostice služby. U všech nových žádostí pomocí koncových bodů služby se jako zdrojová IP adresa žádosti zobrazí privátní IP adresa virtuální sítě, která je přiřazená klientovi provádějícímu žádost z vaší virtuální sítě. Bez koncového bodu je tato adresa veřejnou IP adresou Azure.
- Zobrazení efektivních tras na všech síťových rozhraních v podsíti. Trasa ke službě:
  - Ukazuje konkrétnější výchozí trasu k rozsahu předpon adresy každé služby.
  - Má jako typ dalšího segmentu směrování *VirtualNetworkServiceEndpoint*.
  - Indicates that a more direct connection to the service is in effect compared to any forced-tunneling routes

>[!NOTE]
> Trasy koncového bodu služby přepíší všechny trasy BGP nebo UDR pro shodu předpon adresy služby Azure. For more information, see [troubleshooting with effective routes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Zřizování

Service endpoints can be configured on virtual networks independently by a user with write access to a virtual network. To secure Azure service resources to a VNet, the user must have permission to *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* for the added subnets. The built-in service administrator roles include this permission by default. You can modify the permission by creating custom roles.

For more information about built-in roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny a omezení

There's no additional charge for using service endpoints. The current pricing model for Azure services (Azure Storage, Azure SQL Database, etc.) applies as-is today.

There's no limit on the total number of service endpoints in a virtual network.

Certain Azure services, such as Azure Storage Accounts, may enforce limits on the number of subnets used for securing the resource. Refer to the documentation for various services in the [Next steps](#next-steps) section for details.

## <a name="vnet-service-endpoint-policies"></a>VNet service endpoint policies 

VNet service endpoint policies allow you to filter virtual network traffic to Azure services. This filter allows only specific Azure service resources over service endpoints. Service endpoint policies provide granular access control for virtual network traffic to Azure services. For more information, see [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Nejčastější dotazy

For FAQs, see [Virtual Network Service Endpoint FAQs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Další kroky

- [Configure virtual network service endpoints](tutorial-restrict-network-access-to-resources.md)
- [Secure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Secure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Virtual Network Service Endpoint Policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Šablona Azure Resource Manageru](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

