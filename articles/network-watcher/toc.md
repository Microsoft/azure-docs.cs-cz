# [Dokumentace ke službě Azure Network Watcher](index.md)

# Přehled
## [Co je Network Watcher?](network-watcher-monitoring-overview.md)
## [Topologie](network-watcher-topology-overview.md)
## [Proměnné zachytávání paketů](network-watcher-packet-capture-overview.md)
## [Protokolování toků skupin zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md)
## [Ověření IP toku](network-watcher-ip-flow-verify-overview.md)
## [Zobrazení skupin zabezpečení](network-watcher-security-group-view-overview.md)
## [Další směrování](network-watcher-next-hop-overview.md)
## [Řešení potíží s připojením VPN](network-watcher-troubleshoot-overview.md)
## [Řešení potíží s připojení](network-watcher-connectivity-overview.md)
## [Omezení služby](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-watcher-limits)

# Začínáme
## [Konfigurace Network Watcheru](network-watcher-create.md)
## Správa zachytávání paketů
### [portál Azure Portal](network-watcher-packet-capture-manage-portal.md)
### [Azure PowerShell](network-watcher-packet-capture-manage-powershell.md)
### [Azure CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
### [Azure CLI](network-watcher-packet-capture-manage-cli.md)
### [REST](network-watcher-packet-capture-manage-rest.md)
## Ověření IP toků z a do virtuálního počítače
### [portál Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
### [Azure PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
### [Azure CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
### [Azure CLI](network-watcher-check-ip-flow-verify-cli.md)
### [REST](network-watcher-check-ip-flow-verify-rest.md)
## Analýza dalšího směrování
### [portál Azure Portal](network-watcher-check-next-hop-portal.md)
### [Azure PowerShell](network-watcher-check-next-hop-powershell.md)
### [Azure CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
### [Azure CLI](network-watcher-check-next-hop-cli.md)
### [REST](network-watcher-check-next-hop-rest.md)
## Zobrazení topologie vaší sítě
### [Azure PowerShell](network-watcher-topology-powershell.md)
### [Azure CLI 1.0](network-watcher-topology-cli-nodejs.md)
### [Azure CLI](network-watcher-topology-cli.md)
### [REST](network-watcher-topology-rest.md)
## Řešení potíží s připojeními a VPN Gateway
### [portál Azure Portal](network-watcher-troubleshoot-manage-portal.md)
### [Azure PowerShell](network-watcher-troubleshoot-manage-powershell.md)
### [Azure CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
### [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
### [REST](network-watcher-troubleshoot-manage-rest.md)
## Zobrazení analýzy skupin zabezpečení sítě
### [Azure PowerShell](network-watcher-security-group-view-powershell.md)
### [Azure CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
### [Azure CLI](network-watcher-security-group-view-cli.md)
### [REST](network-watcher-security-group-view-rest.md)
## Řešení potíží s připojeními
### [portál Azure Portal](network-watcher-connectivity-portal.md)
### [Azure PowerShell](network-watcher-connectivity-powershell.md)
### [Azure CLI](network-watcher-connectivity-cli.md)
### [REST](network-watcher-connectivity-rest.md)
## Konfigurace protokolů toku NSG
### [portál Azure Portal](network-watcher-nsg-flow-logging-portal.md)
### [Azure PowerShell](network-watcher-nsg-flow-logging-powershell.md)
### [Azure CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
### [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
### [REST](network-watcher-nsg-flow-logging-rest.md)

# Postup
## [Monitorování VPN Gateway s využitím Azure Automation](network-watcher-monitor-with-azure-automation.md)
## [Diagnostika místního připojení prostřednictvím VPN Gateway](network-watcher-diagnose-on-premises-connectivity.md)
## [Sledování síťových připojení](connection-monitor.md)
## [Audit a kontrola dodržování předpisů ve vaší síti pomocí PowerShellu](network-watcher-nsg-auditing-powershell.md)
## [Analýza protokolů Application Gateway v Azure Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
## Zachytávání paketů
### [Vyhledání anomálií](network-watcher-deep-packet-inspection.md)
### [Proaktivní monitorování sítě s využitím Azure Functions](network-watcher-alert-triggered-packet-capture.md)
### [detekce neoprávněných vniknutí pomocí opensourcových nástrojů](network-watcher-intrusion-detection-open-source-tools.md)
### [Vizualizace provozu sítě s využitím opensourcových nástrojů](network-watcher-using-open-source-tools.md)
## Analýza protokolů toku NSG
### [Čtení protokolů toku NSG](network-watcher-read-nsg-flow-logs.md)
### [Použití analýzy provozu](traffic-analytics.md)
### [Použití Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
### [Použití Elastic Stack](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
### [Použití Grafana](network-watcher-nsg-grafana.md)
### [Použití Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md)
## [Určení relativní latence mezi umístěním a oblastí Azure](view-relative-latencies.md)

# Referenční informace
## [Azure CLI](/cli/azure/network/watcher)
## [Azure PowerShell](/powershell/module/azurerm.network/#network_watcher)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/Network/Mgmt/V2016_09_01/Models/NetworkWatcher) 
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.html#azure.mgmt.network.NetworkManagementClient.network_watchers)

## [.NET](/dotnet/api)
## [REST](/rest/api/networkwatcher/)
# Související
## [Virtuální síť](/azure/virtual-network/)
## [Virtual Machines](/azure/virtual-machines/)
## [VPN Gateway](/azure/vpn-gateway/)
# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)
## [Ceny](https://azure.microsoft.com/pricing/details/network-watcher/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=network-watcher)
## [SLA](https://azure.microsoft.com/support/legal/sla/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/network-watcher)
