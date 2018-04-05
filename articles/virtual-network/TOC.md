# [Dokumentace ke službě Virtual Network](index.md)

# Přehled
## [Virtuální sítě](virtual-networks-overview.md)
## [Směrování](virtual-networks-udr-overview.md)
## [Partnerské vztahy virtuálních sítí](virtual-network-peering-overview.md)
## [Koncové body služby virtuální sítě](virtual-network-service-endpoints-overview.md)
## [Virtuální síť pro služby Azure](virtual-network-for-azure-services.md)
## [Zabezpečení](security-overview.md)
## [Sítě kontejnerů](container-networking.md)
## [Kontinuita podnikových procesů](virtual-network-disaster-recovery-guidance.md)
## [Přidělování IP adres](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## [Nejčastější dotazy](virtual-networks-faq.md)
## Classic
### [Přidělování IP adres](virtual-network-ip-addresses-overview-classic.md)
### [Seznamy řízení přístupu](virtual-networks-acl.md)

# Začínáme
## [Vytvoření virtuální sítě – portál](quick-create-portal.md)
## [Vytvoření virtuální sítě – PowerShell](quick-create-powershell.md)
## [Vytvoření virtuální sítě – Azure CLI](quick-create-cli.md)

# Postup
## Plánování a návrh
### [Virtuální sítě](virtual-network-vnet-plan-design-arm.md)
### [Skupiny zabezpečení sítě](virtual-networks-nsg.md)

## Nasazení

### Skupiny zabezpečení sítě
#### [Azure PowerShell](tutorial-filter-network-traffic.md)
#### [Azure CLI](tutorial-filter-network-traffic-cli.md)
#### Bez skupin zabezpečení aplikací
##### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
##### [Šablona](virtual-networks-create-nsg-arm-template.md)
#### Classic
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### Směrovací tabulky
#### [Azure Portal](tutorial-create-route-table-portal.md)
#### [Azure PowerShell](tutorial-create-route-table-powershell.md)
#### [Azure CLI](tutorial-create-route-table-cli.md)
#### [Šablona](virtual-network-create-udr-arm-template.md)
#### Classic
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### Partnerské vztahy virtuálních sítí
#### Stejný model nasazení, stejné předplatné
##### [Azure Portal](tutorial-connect-virtual-networks-portal.md)
##### [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)
##### [Azure CLI](tutorial-connect-virtual-networks-cli.md)
#### [Stejný model nasazení, různá předplatná](create-peering-different-subscriptions.md)
#### [Různé modely nasazení, stejné předplatné](create-peering-different-deployment-models.md)
#### [Různé modely nasazení, různá předplatná](create-peering-different-deployment-models-subscriptions.md)

### Koncové body služby virtuální sítě
#### [Azure Portal](tutorial-restrict-network-access-to-resources.md)
#### [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)
#### [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md)

### Virtuální počítače
#### [Propustnost sítě virtuálních počítačů](virtual-machine-network-throughput.md)
#### Vytvoření virtuálního počítače se statickou veřejnou IP adresou
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Šablona](virtual-network-deploy-static-pip-arm-template.md)
##### Classic
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Vytvoření virtuálního počítače – statická privátní IP adresa
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Classic
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Vytvoření virtuálního počítače – několik síťových rozhraní
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Šablona](virtual-network-deploy-multinic-arm-template.md)

##### Classic
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Vytvoření virtuálního počítače – několik IP adres
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Šablona](virtual-network-multiple-ip-addresses-template.md)

#### Vytvoření virtuálního počítače – akcelerované síťové služby
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [Azure CLI](create-vm-accelerated-networking-cli.md)

### Scénáře připojení
#### [Virtuální síť k virtuální síti](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Virtuální síť (Resource Manager) k virtuální síti (Classic)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Virtuální síť k místní síti (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Virtuální síť k místní síti (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Vysoce dostupná hybridní síťová architektura](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Scénáře zabezpečení
#### [Zabezpečení sítí pomocí virtuálních zařízení](virtual-network-scenario-udr-gw-nva.md)
#### [DMZ mezi Azure a internetem](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Zabezpečení cloudové služby a sítě](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Vytvoření DMZ pomocí skupin zabezpečení sítě](virtual-networks-dmz-nsg.md)
##### [Vytvoření DMZ pomocí skupin zabezpečení sítě (Classic)](virtual-networks-dmz-nsg-asm.md)
##### [Vytvoření DMZ pomocí brány firewall a skupin zabezpečení sítě (Classic)](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ pomocí brány firewall, směrování definovaného uživatelem a skupin zabezpečení sítě (Classic)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Ukázková aplikace](virtual-networks-sample-app.md)

### Classic
#### [Virtuální síť](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [Zadání nastavení DNS v konfiguračním souboru virtuální sítě](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Zadání nastavení DNS v konfiguračním souboru služby](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Konfigurace
### Virtuální počítače
#### [Přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md)
#### [Překlad názvů pro virtuální počítače a cloudové služby](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Použití dynamického DNS k registraci názvů hostitelů na vlastním serveru DNS](virtual-networks-name-resolution-ddns.md)
#### [Optimalizace propustnosti sítě](virtual-network-optimize-network-bandwidth.md)
#### [Zobrazení a úpravy názvů hostitelů](virtual-networks-viewing-and-modifying-hostnames.md)
#### Classic
##### Statické IP adresy
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [Rozhraní příkazového řádku](virtual-networks-static-private-ip-classic-cli.md)
##### [Veřejná IP adresa na úrovni instance](virtual-networks-instance-level-public-ip.md)

### Classic
#### Seznamy řízení přístupu
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Spravovat
### [Virtuální sítě](manage-virtual-network.md)
#### [Podsítě](virtual-network-manage-subnet.md)
#### [Partnerské vztahy](virtual-network-manage-peering.md)
#### Classic
##### [Konfigurační soubory sítě](virtual-networks-using-network-configuration-file.md)
##### [Migrace ze skupiny vztahů do oblasti](virtual-networks-migrate-to-regional-vnet.md)
### Skupiny zabezpečení sítě
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

#### [Protokoly](virtual-network-nsg-manage-log.md)
### [Směrovací tabulky](manage-route-table.md)
### Síťová rozhraní (NIC)
#### [Vytvoření, změna nebo odstranění síťových rozhraní](virtual-network-network-interface.md)
#### [Přidání, změna nebo odstranění IP adresy](virtual-network-network-interface-addresses.md)
### Virtuální počítače
#### [Přesun virtuálního počítače do jiné podsítě](virtual-networks-move-vm-role-to-subnet.md)
### [Veřejné IP adresy](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Řešení potíží
### Skupiny zabezpečení sítě
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Trasy
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Testování propustnosti](virtual-network-bandwidth-testing.md)
### [Nelze odstranit virtuální sítě](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Problémy propojení virtuálních počítačů](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [Konfigurace PTR pro kontrolu bannerů protokolu SMTP](create-ptr-for-smtp-service.md)

## Ukázkové skripty
### [Azure CLI](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

# Referenční informace
## [Ukázky kódu](https://azure.microsoft.com/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (Classic)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (Classic)](https://msdn.microsoft.com/library/jj157182.aspx)


# Související
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog o sítích](http://azure.microsoft.com/blog/topics/networking)
## [Fórum o sítích](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Ceny](https://azure.microsoft.com/pricing/details/virtual-network)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Poskytovatel síťových prostředků](resource-groups-networking.md)
