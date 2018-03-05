# [Dokumentace ke službě ExpressRoute](index.md)

# Přehled
## [Co je ExpressRoute?](expressroute-introduction.md)
## [ExpressRoute – nejčastější dotazy](expressroute-faqs.md)
## [Modely připojení](expressroute-connectivity-models.md)
## [O okruzích a doménách směrování](expressroute-circuit-peerings.md)
## [Umístění a partneři](expressroute-locations.md)
### [Poskytovatelé podle umístění](expressroute-locations-providers.md)
### [Umístění podle poskytovatele](expressroute-locations.md)
## [O branách virtuálních sítí pro ExpressRoute](expressroute-about-virtual-network-gateways.md)

# Začínáme
## [Požadavky](expressroute-prerequisites.md)
## [Pracovní postupy](expressroute-workflows.md)
## [Požadavky směrování](expressroute-routing.md)
## [Požadavky QoS](expressroute-qos.md)
## [Informace o přesunu okruhů z modelu nasazení Classic do Resource Manageru](expressroute-move.md)

# Postup
## Vytvoření a změny okruhu
### [portál Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-circuit-arm.md)
### [Azure CLI](howto-circuit-cli.md)
## Vytvoření a změny konfigurace partnerského vztahu
### [portál Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-routing-arm.md)
### [Azure CLI](howto-routing-cli.md)
## Propojení virtuální sítě k okruhu ExpressRoute
### [portál Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-linkvnet-arm.md)
### [Azure CLI](howto-linkvnet-cli.md)
## [Konfigurace sítě VPN typu Site-to-Site přes partnerský vztah Microsoftu](site-to-site-vpn-over-microsoft-peering.md)
## Konfigurace brány virtuální sítě pro ExpressRoute
### [portál Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-add-gateway-resource-manager.md)
## [Konfigurace společně používaných připojení typu Site-to-Site a ExpressRoute](expressroute-howto-coexist-resource-manager.md)
## Konfigurace filtrů směrování pro partnerský vztah Microsoftu
### [portál Azure Portal](how-to-routefilter-portal.md)
### [Azure PowerShell](how-to-routefilter-powershell.md)
### [Azure CLI](how-to-routefilter-cli.md)
## [Přechod od veřejného partnerského vztahu k partnerskému vztahu Microsoftu](how-to-move-peering.md)
## [Přesun okruhu z modelu nasazení Classic do Resource Manageru](expressroute-howto-move-arm.md)
## [Migrace přidružených virtuálních sítí z modelu nasazení Classic do Resource Manageru](expressroute-migration-classic-resource-manager.md)
## Konfigurace směrovače pro ExpressRoute
### [Konfigurace směrovače](expressroute-config-samples-routing.md)
### [Ukázky konfigurace směrovače pro překlad adres (NAT)](expressroute-config-samples-nat.md)
## [Konfigurace Network Performance Monitor pro ExpressRoute](how-to-npm.md)
## Články věnované modelu nasazení Classic
### [Úprava okruhu](expressroute-howto-circuit-classic.md)
### [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-classic.md)
### [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md)
### [Konfigurace společně používaných připojení typu S2S a ExpressRoute](expressroute-howto-coexist-classic.md)
### [Přidání brány do virtuální sítě](expressroute-howto-add-gateway-classic.md)

## Osvědčené postupy
### [Osvědčené postupy pro zabezpečení sítě a cloudové služby](../best-practices-network-security.md)
### [Optimalizace směrování](expressroute-optimize-routing.md)
### [Asymetrické směrování](expressroute-asymmetric-routing.md)
### [NAT pro ExpressRoute](expressroute-nat.md)

## Řešení potíží
### [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
### [Řešení problémů s výkonem sítě](expressroute-troubleshooting-network-performance.md)
### [Resetování okruhu, který selhal](reset-circuit.md)
### [Získání tabulek protokolu ARP](expressroute-troubleshooting-arp-resource-manager.md)
### [Získání tabulek protokolu ARP (Classic)](expressroute-troubleshooting-arp-classic.md)

# Referenční informace
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#expressroute)
## [Azure CLI](/cli/azure/network/express-route)
## [REST](https://msdn.microsoft.com/library/azure/mt586720)
## [REST (Classic)](https://msdn.microsoft.com/library/azure/dn606310)

# Související
## [Virtual Network](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtual Machines](/azure/virtual-machines/)
## [Load Balancer](/azure/load-balancer/)
## [Traffic Manager](/azure/traffic-manager/)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=networking)
## [Případové studie](https://customers.microsoft.com/Pages/advancedsearch.aspx?mrmcproducts=More%20Products)
## [Blog o sítích](https://azure.microsoft.com/blog/topics/networking/)
## [Ceny](https://azure.microsoft.com/pricing/details/expressroute/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=expressroute)
## [SLA](https://azure.microsoft.com/support/legal/sla/)
## [Předplatné a omezení služeb](../azure-subscription-service-limits.md?toc=%2fazure%2fexpressroute%2ftoc.json)
## [ExpressRoute pro poskytovatele Cloud Solution Provider](expressroute-for-cloud-solution-providers.md)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=expressroute)
### [Připojení brány virtuální sítě k okruhu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit/)
### [Vytvoření virtuální sítě pro ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-virtual-network/)
### [Vytvoření brány virtuální sítě pro ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network/)
### [Vytvoření okruhu ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit/)
### [Rozvíjení připojitelnosti vaší síťové infrastruktury](https://go.microsoft.com/fwlink/p/?LinkId=615124)
### [Jak nastavit privátní partnerské vztahy pro váš okruh](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit/)
### [Hybridní partnerství: Povolení místních scénářů](https://go.microsoft.com/fwlink/p/?LinkId=615125)
### [Nastavení partnerského vztahu Microsoftu pro váš okruh](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit/)
### [Nastavení veřejného partnerského vztahu pro váš okruh](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit/)
