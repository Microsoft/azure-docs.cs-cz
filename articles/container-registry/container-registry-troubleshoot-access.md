---
title: Řešení potíží se sítí pomocí registru
description: Příznaky, příčiny a řešení běžných potíží při přístupu ke službě Azure Container Registry ve virtuální síti nebo za bránou firewall
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: dc2110405713791d11fb438565fc091da9c9dd5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780747"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Řešení potíží se sítí pomocí registru

Tento článek vám pomůže řešit problémy, se kterými se můžete setkat při přístupu ke službě Azure Container Registry ve virtuální síti nebo za bránou firewall nebo proxy server. 

## <a name="symptoms"></a>Příznaky

Může zahrnovat jednu nebo více z následujících možností:

* Nelze vyžádat nebo načíst obrázky a zobrazí se chybová zpráva. `dial tcp: lookup myregistry.azurecr.io`
* Nelze vyžádat nebo načíst obrázky a zobrazí se chybová zpráva. `Client.Timeout exceeded while awaiting headers`
* Nepovedlo se odeslat nebo načíst image a zobrazí se chyba rozhraní příkazového řádku Azure CLI. `Could not connect to the registry login server`
* Nepovedlo se načíst image z registru do služby Azure Kubernetes nebo jiné služby Azure.
* Nejde získat přístup k registru za proxy HTTPS a zobrazí `Error response from daemon: login attempt failed with status: 403 Forbidden` se chyba. `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Nepovedlo se nakonfigurovat nastavení virtuální sítě a zobrazí se chyba. `Failed to save firewall and virtual network settings for container registry`
* Nejde získat přístup k nastavení registru v Azure Portal nebo spravovat registr pomocí rozhraní příkazového řádku Azure CLI.
* Nelze přidat nebo změnit nastavení virtuální sítě ani pravidla veřejného přístupu.
* Úlohy ACR nemůžou vyžádat nebo načíst image.
* Azure Security Center nemůže skenovat obrázky v registru nebo se výsledky kontroly neprojeví v Azure Security Center
* Při `host is not reachable` pokusu o přístup k registru nakonfigurovanému pomocí privátního koncového bodu se zobrazí chyba.

## <a name="causes"></a>Příčiny

* Brána firewall nebo proxy server klienta brání v přístupu k [řešení](#configure-client-firewall-access)
* Pravidla přístupu k veřejné síti v registru zabraňující přístupu – [řešení](#configure-public-access-to-registry)
* Konfigurace virtuální sítě brání přístupu – [řešení](#configure-vnet-access)
* Pokoušíte se integrovat Azure Security Center nebo některé jiné služby Azure s registrem, který má privátní koncový bod, koncový bod služby nebo pravidla přístupu veřejných IP adres – [řešení](#configure-service-access)

## <a name="further-diagnosis"></a>Další Diagnostika 

Pokud chcete získat další informace o stavu prostředí registru a volitelně získat přístup k cílovému registru, spusťte příkaz [AZ ACR check-Health](/cli/azure/acr#az_acr_check_health) . Například Diagnostikujte určité síťové připojení nebo problémy s konfigurací. 

Příklady příkazů najdete v tématu o [kontrole stavu služby Azure Container Registry](container-registry-check-health.md) . Pokud dojde k chybám, přečtěte si [referenční informace o chybě](container-registry-health-error-reference.md) a v následujících oddílech, kde najdete doporučená řešení.

Pokud máte problémy s používáním služby Azure Kubernetes s integrovaným registrem, spusťte příkaz [AZ AKS check-ACR](/cli/azure/aks#az_aks_check_acr) , který ověří, jestli se cluster AKS může dostat do registru.

> [!NOTE]
> Při potížích s ověřováním v registru nebo autorizací může dojít k určitým potížím se síťovým připojením. Viz [Poradce při potížích s přihlášením do registru](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Možná řešení

### <a name="configure-client-firewall-access"></a>Konfigurace přístupu klienta k bráně firewall

Pokud chcete získat přístup k registru z za bránou firewall nebo proxy server klienta, nakonfigurujte pravidla brány firewall pro přístup k veřejným koncovým bodům REST a dat registru. Pokud jsou [vyhrazené koncové body dat](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) povolené, budete potřebovat pravidla pro přístup:

* Koncový bod REST: `<registryname>.azurecr.io`
* Datový koncový bod (y): `<registry-name>.<region>.data.azurecr.io`

V případě geograficky replikovaného registru nakonfigurujte přístup ke koncovému bodu dat pro každou místní repliku.

Za proxy serverem HTTPS zajistěte, aby byl klient Docker i démon Docker pro chování proxy nakonfigurován. Pokud změníte nastavení proxy pro démona Docker, nezapomeňte spustit démona znovu. 

Protokoly prostředků registru v tabulce ContainerRegistryLoginEvents mohou přispět k diagnostice pokusy o připojení, která je blokovaná.

Související odkazy:

* [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md)
* [Konfigurace proxy serveru HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geografické replicationin Azure Container Registry](container-registry-geo-replication.md)
* [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurace veřejného přístupu k registru

Pokud přistupujete k registru přes Internet, potvrďte, že registr umožňuje přístup k veřejné síti z vašeho klienta. Ve výchozím nastavení povoluje služba Azure Container Registry přístup k koncovým bodům veřejného registru ze všech sítí. Registr může omezit přístup k vybraným sítím nebo vybraným IP adresám. 

Pokud je registr nakonfigurovaný pro virtuální síť s koncovým bodem služby, zakázání přístupu k veřejné síti taky zakáže přístup přes koncový bod služby. Pokud je váš registr nakonfigurovaný pro virtuální síť s privátním propojením, pravidla sítě protokolu IP se nevztahují na soukromé koncové body registru. 

Související odkazy:

* [Konfigurace pravidel sítě veřejných IP adres](container-registry-access-selected-networks.md)
* [Připojení soukromě ke službě Azure Container Registry pomocí privátního odkazu Azure](container-registry-private-link.md)
* [Omezení přístupu k registru kontejneru pomocí koncového bodu služby ve službě Azure Virtual Network](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurace přístupu k virtuální síti

Potvrďte, že je virtuální síť nakonfigurovaná s privátním koncovým bodem pro privátní linku nebo koncový bod služby (Preview). V současné době není koncový bod Azure bastionu podporován.

Pokud je nakonfigurovaný privátní koncový bod, zkontrolujte, jestli DNS překládá veřejný plně kvalifikovaný název domény (FQDN) registru, například *myregistry.azurecr.IO* , na privátní IP adresu registru. Použijte síťový nástroj, například `dig` nebo `nslookup` pro vyhledávání DNS. Zajistěte, aby [byly záznamy DNS](container-registry-private-link.md#dns-configuration-options) pro plně kvalifikovaný název domény v registru a pro každý plně kvalifikovaný název domény koncového bodu dat.

Zkontrolujte pravidla NSG a značky služeb, které se používají k omezení provozu z jiných prostředků v síti do registru. 

Pokud je nakonfigurován koncový bod služby registru, zkontrolujte, že je do registru přidáno síťové pravidlo, které umožňuje přístup z této podsítě sítě. Koncový bod služby podporuje přístup jenom z virtuálních počítačů a clusterů AKS v síti.

Pokud chcete omezit přístup k registru pomocí virtuální sítě v jiném předplatném Azure, ujistěte se, že `Microsoft.ContainerRegistry` v tomto předplatném zaregistrujete poskytovatele prostředků. [Zaregistrujte poskytovatele prostředků](../azure-resource-manager/management/resource-providers-and-types.md) pro Azure Container Registry pomocí Azure Portal, rozhraní příkazového řádku Azure nebo dalších nástrojů Azure.

Pokud je v síti nakonfigurované Azure Firewall nebo podobné řešení, ověřte, že se pro přístup k koncovým bodům registru má povolit odchozí přenos dat z jiných prostředků, jako je cluster AKS.

Související odkazy:

* [Připojení soukromě ke službě Azure Container Registry pomocí privátního odkazu Azure](container-registry-private-link.md)
* [Řešení potíží s připojením k privátnímu koncovému bodu Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Omezení přístupu k registru kontejneru pomocí koncového bodu služby ve službě Azure Virtual Network](container-registry-vnet.md)
* [Požadovaná odchozí síťová pravidla a plně kvalifikované názvy domény pro clustery AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: ladění překladu DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurace přístupu ke službě

V současné době není přístup k registru kontejnerů s omezeními sítě povolený z několika služeb Azure:

* Azure Security Center nemůže provádět [kontrolu ohrožení zabezpečení imagí](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) v registru, který omezuje přístup k privátním koncovým bodům, vybraným podsítím nebo IP adresám. 
* Zdroje určitých služeb Azure nemají přístup k registru kontejneru s omezeními sítě, včetně Azure App Service a Azure Container Instances.

Pokud se vyžaduje přístup nebo integrace těchto služeb Azure s registrem kontejneru, odeberte omezení sítě. Můžete například odebrat soukromé koncové body registru nebo odebrat nebo upravit pravidla pro veřejný přístup registru.

Od ledna 2021 můžete nakonfigurovat registr s omezeným přístupem sítě tak, aby [povoloval přístup](allow-access-trusted-services.md) z vybraných důvěryhodných služeb.

Související odkazy:

* [Azure Container Registry skenování imagí Security Center](../security-center/defender-for-container-registries-introduction.md)
* Poskytnutí [zpětné vazby](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Povoluje důvěryhodným službám zabezpečený přístup k registru kontejneru omezeného na síť.](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Pokud je v registru povolená [kolekce protokolů prostředků](container-registry-diagnostics-audit-logs.md) , přečtěte si protokol ContainterRegistryLoginEvents. Tento protokol ukládá události a stav ověřování, včetně příchozí identity a IP adresy. Dotaz na protokol pro [selhání ověřování registru](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Související odkazy:

* [Protokoly pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)
* [Nejčastější dotazy k registru kontejneru](container-registry-faq.md)
* [Základní hodnoty zabezpečení Azure pro Azure Container Registry](security-baseline.md)
* [Osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Další kroky

Pokud se vám problém nevyřeší, přečtěte si následující možnosti.

* Mezi další témata týkající se řešení potíží registru patří:
  * [Řešení potíží s přihlášením k registru](container-registry-troubleshoot-login.md) 
  * [Řešení potíží s výkonem registru](container-registry-troubleshoot-performance.md)
* Možnosti [podpory komunity](https://azure.microsoft.com/support/community/)
* [Microsoft – otázky a odpovědi](/answers/products/)
* [Otevření lístku podpory](https://azure.microsoft.com/support/create-ticket/)