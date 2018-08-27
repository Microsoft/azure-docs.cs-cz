# Přehled
## [Informace o sítích Azure](networking-overview.md)
## Architektura
### [Virtuální datová centra](/azure/architecture/vdc/networking-virtual-datacenter)
### [Asymetrické směrování s několika síťovými cestami](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Zabezpečení návrhů sítí](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologie centrum – paprsky](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Osvědčené postupy zabezpečení sítě](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vysoce dostupná virtuální síťová zařízení](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Kombinace metod vyrovnávání zatížení](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Zotavení po havárii s využitím Azure DNS a Traffic Manageru](disaster-recovery-dns-traffic-manager.md)
## Plánování a návrh
### [Virtuální sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Připojení mezi místními sítěmi – síť VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Připojení mezi místními sítěmi – vyhrazené privátní](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Koncepty
### [Virtuální sítě](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vyrovnávání zatížení sítě](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vyrovnávání zatížení aplikace](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuce přenosu na základě DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Místní připojení k síti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Místní připojení k síti vyhrazené síti](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Začínáme
## [Vytvoření první virtuální sítě](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Postup
## Připojení k internetu
### [Veřejné servery vyrovnávání zatížení sítě](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Veřejné servery vyrovnávání zatížení aplikace](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ochrana webových aplikací](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuce přenosu mezi umístění](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Interní připojení
### [Privátní servery vyrovnávání zatížení sítě](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Privátní servery vyrovnávání zatížení aplikace](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Propojení virtuálních sítí (stejné umístění)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Propojení virtuálních sítí (různá umístění)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Připojení mezi místními sítěmi
### [Vytvoření připojení S2S VPN (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vytvoření připojení P2S VPN (SSTP s certifikáty)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Vytvoření vyhrazeného privátního připojení (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Správa
### [Přehled sledování sítě](network-monitoring-overview.md)
### [Kontrola využití prostředků na základě omezení Azure](check-usage-against-limits.md)
### [Zobrazení topologie sítě](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Ukázkové skripty
### [Azure CLI](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Kurzy
### [Vyrovnávání zatížení virtuálních počítačů](../virtual-machines/linux/tutorial-load-balancer.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Připojení počítače k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Referenční informace
## [Azure CLI](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Zdroje a prostředky
## [Tvorba šablon](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=networking)
## [Komunitní šablony](https://azure.microsoft.com/resources/templates/)
## [Blog o sítích](http://azure.microsoft.com/blog/topics/networking)
## [Ceny](https://azure.microsoft.com/pricing)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Regionální dostupnost](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Videa](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

