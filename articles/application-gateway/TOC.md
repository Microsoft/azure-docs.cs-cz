# [Dokumentace ke službě Application Gateway](index.md)

# Přehled
## [Co je Application Gateway?](application-gateway-introduction.md)
## [Nejčastější dotazy k Application Gateway](application-gateway-faq.md)
## [Brána firewall webových aplikací](application-gateway-web-application-firewall-overview.md)
### [Základní sady pravidel a pravidla](application-gateway-crs-rulegroups-rules.md)
## [Monitorování stavu](application-gateway-probe-overview.md)
## [Směrování adres URL](application-gateway-url-route-overview.md)
## [Multi-Site](application-gateway-multi-site-overview.md)
## [Koncový protokol SSL](application-gateway-backend-ssl.md)
## [Zásady protokolu SSL](application-gateway-ssl-policy-overview.md)
## [Přesměrování](application-gateway-redirect-overview.md)
## [Podpora více tenantů](application-gateway-web-app-overview.md)
## [WebSocket](application-gateway-websocket.md)
## [Omezení služby](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits)
# Začínáme
## [portál Azure Portal](application-gateway-create-gateway-portal.md)
## [Azure PowerShell](application-gateway-create-gateway-arm.md)
## [Azure CLI](application-gateway-create-gateway-cli.md)
# Postup
## Vytvoření aplikační brány s VMSS
### [Azure PowerShell](tutorial-create-vmss-powershell.md)
### [Azure CLI](tutorial-create-vmss-cli.md)
## [Použití šablony](application-gateway-create-gateway-arm-template.md)
## Konfigurace firewallu webových aplikací
### [portál Azure Portal](application-gateway-web-application-firewall-portal.md)
### [Azure PowerShell](application-gateway-web-application-firewall-powershell.md)
### [Azure CLI](application-gateway-web-application-firewall-cli.md)
## Přizpůsobení pravidel firewallu webových aplikací
### [portál Azure Portal](application-gateway-customize-waf-rules-portal.md)
### [Azure CLI](application-gateway-customize-waf-rules-cli.md)
## Konfigurace SSL
### [portál Azure Portal](application-gateway-ssl-portal.md)
### [Azure PowerShell](application-gateway-ssl-arm.md)
### [Azure CLI](application-gateway-ssl-cli.md)
### [Zásady protokolu SSL](application-gateway-configure-ssl-policy-powershell.md)
## Konfigurace směrování na základě cest URL
### [portál Azure Portal](application-gateway-create-url-route-portal.md)
### [Azure PowerShell](application-gateway-create-url-route-arm-ps.md)
### [Azure CLI](application-gateway-create-url-route-cli.md)
## Konfigurace hostování několika webů
### [portál Azure Portal](application-gateway-create-multisite-portal.md)
### [Azure PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
### [Azure CLI](tutorial-multisite-cli.md)
## Konfigurace externího přesměrování
### [Azure PowerShell](tutorial-external-site-redirect-powershell.md)
### [Azure CLI](tutorial-external-site-redirect-cli.md)
## Konfigurace interního přesměrování
### [Azure PowerShell](tutorial-internal-site-redirect-powershell.md)
### [Azure CLI](tutorial-internal-site-redirect-cli.md)
## Konfigurace přesměrování HTTP na HTTPS
### [Azure PowerShell](tutorial-http-redirect-powershell.md)
### [Azure CLI](tutorial-http-redirect-cli.md)
## Konfigurace přesměrování URL
### [Azure PowerShell](tutorial-url-redirect-powershell.md)
### [Azure CLI](tutorial-url-redirect-cli.md)
## Konfigurace webových aplikací jako členů back-endového fondu
### [Azure PowerShell](application-gateway-web-app-powershell.md)
## Konfigurace sond stavu
### [portál Azure Portal](application-gateway-create-probe-portal.md)
### [Azure PowerShell](application-gateway-create-probe-ps.md)
## [Integrace služby Azure Security Center](application-gateway-integration-security-center.md)
## [Kombinace služeb pro vyrovnávání zatížení](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)
## [Konfigurace kompletního SSL](application-gateway-end-to-end-ssl-powershell.md)
## Řešení potíží
### [Řešení chyb ve službě Application Gateway](application-gateway-troubleshooting-502.md)
### [Metriky a protokolování diagnostiky](application-gateway-diagnostics.md)
### [Problémy se spřažením relace ve službě Application Gateway](https://support.microsoft.com/help/4033827/troubleshooting-azure-application-gateway-session-affinity-issues)
# Referenční informace
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](/cli/azure/network/application-gateway)
## [.NET](/dotnet/api)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/ApplicationGateways)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/0.8.0/Azure/ARM/Network/ApplicationGateways)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.ApplicationGatewaysOperations)
## [REST](https://docs.microsoft.com/rest/api/applicationgateway)
# Související
## [ExpressRoute](/azure/expressroute/)
## [Virtuální síť](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtual Machines](/azure/virtual-machines/)
## [Load Balancer](/azure/load-balancer/)
## [Traffic Manager](/azure/traffic-manager/)
# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=networking)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)
## [Ceny](https://azure.microsoft.com/pricing/details/application-gateway/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=application-gateway)
## [SLA](https://azure.microsoft.com/support/legal/sla/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-application-gateway)
