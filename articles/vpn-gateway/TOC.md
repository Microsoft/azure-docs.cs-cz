# [Dokumentace ke službě VPN Gateway](index.md)

# Přehled
## [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md)
## [VPN Gateway – nejčastější dotazy](vpn-gateway-vpn-faq.md)
## [Předplatné a omezení služeb](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Začínáme
## [Plánování a návrh pro VPN Gateway](vpn-gateway-plan-design.md)
## [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md)
## [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md)
## [Informace o kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md)
## [BGP a VPN Gateway](vpn-gateway-bgp-overview.md)
## [Možnosti připojení s vysokou dostupností](vpn-gateway-highlyavailable.md)
## [Připojení typu Point-to-Site](point-to-site-about.md)

# Postup
## Konfigurace připojení typu Site-to-Site
### [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Stažení skriptů konfigurace zařízení VPN](vpn-gateway-download-vpndevicescript.md)

## Konfigurace připojení typu Point-to-Site – nativní ověřování certifikátů Azure
### Konfigurace P2S VPN
#### [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Generování certifikátů podepsaných svým držitelem
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Instalace klientských certifikátů](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Konfigurace připojení typu Point-to-Site – ověřování pomocí protokolu RADIUS
### Konfigurace P2S VPN
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-radius.md)
### [Integrace ověřování P2S VPN RADIUS se serverem NPS](vpn-gateway-radiuis-mfa-nsp.md)

## Konfigurace připojení typu VNet-to-VNet
### [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Konfigurace připojení typu VNet-to-VNet mezi modely nasazení
### [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Konfigurace společně používaných připojení typu Site-to-Site a ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Konfigurace několika připojení typu site-to-site
### [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (Classic)](vpn-gateway-multi-site.md)
## Připojení několika zařízení VPN na základě zásad
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Konfigurace zásad IPsec/IKE pro připojení
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Konfigurace vysoce dostupných připojení typu aktivní-aktivní
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Konfigurace BGP pro bránu VPN
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure CLI](bgp-how-to-cli.md)
## Konfigurace vynuceného tunelování
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (Classic)](vpn-gateway-about-forced-tunneling.md)
## Úprava nastavení místní síťové brány
### [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [Ověření připojení brány VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Resetování brány VPN](vpn-gateway-resetgw-classic.md)
## Odstranění brány VPN
### [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Konfigurace brány VPN (Classic)](vpn-gateway-configure-vpn-gateway-mp.md)
## [Skladové jednotky (SKU) brány](vpn-gateway-about-skus-legacy.md)
## Konfigurace zařízení VPN třetích stran
### [Přehled a konfigurace Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Ukázka: Zařízení Cisco ASA (IKEv2/bez BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migrace z modelu Classic do Resource Manageru](vpn-gateway-classic-resource-manager-migration.md)
## [Řešení problémů](vpn-gateway-troubleshoot.md)
### [Ověření propustnosti sítě VPN do virtuální sítě](vpn-gateway-validate-throughput-to-vnet.md)
### [Nastavení zařízení brány firewall nebo VPN navrhované komunitou](vpn-gateway-third-party-settings.md)
### [Problémy s připojením typu Point-to-Site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Problémy s připojením typu Point-to-Site – klient Mac OS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### [Problémy s připojením typu Site-to-Site](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Připojení typu Site-to-Site se přerušovaně odpojuje](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Konfigurace a ověření připojení VPN nebo virtuální sítě](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Referenční informace
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (Classic)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (Classic)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# Související
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
