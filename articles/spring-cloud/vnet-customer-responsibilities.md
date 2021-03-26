---
title: Zodpovědnost za Zákazníky, kteří spouštějí Azure jaře Cloud ve virtuální síti
description: Tento článek popisuje zodpovědnosti zákazníků, které provozují Azure jaře Cloud ve virtuální síti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 32b41c1c4446ba34e3bfad52f1d3cbd7ed72096d
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108805"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti
Tento dokument obsahuje specifikace pro používání jarního cloudu Azure ve virtuální síti.

Když ve virtuální síti nasadíte jarní cloud Azure, má odchozí závislosti na službách mimo virtuální síť. Pro účely správy a provozu musí mít Azure jaře cloud přístup k určitým portům a plně kvalifikovaným názvům domén (FQDN). Tyto koncové body se vyžadují ke komunikaci s rovinou správy cloudu Azure a ke stažení a instalaci základních komponent clusteru Kubernetes a aktualizací zabezpečení.

Ve výchozím nastavení má jarní cloudová služba Azure neomezený odchozí (výstupní) přístup k Internetu. Tato úroveň přístupu k síti umožňuje aplikacím, které spouštíte pro přístup k externím prostředkům, podle potřeby. Pokud chcete omezit výstupní přenos dat, musí být k dispozici omezený počet portů a adres pro úlohy údržby. Nejjednodušším řešením pro zabezpečení odchozích adres je použití zařízení brány firewall, které umožňuje řídit odchozí přenosy na základě názvů domén. Azure Firewall například může omezit odchozí přenosy HTTP a HTTPS na základě plně kvalifikovaného názvu domény cílového umístění. Můžete taky nakonfigurovat upřednostňovanou bránu firewall a pravidla zabezpečení, abyste tyto požadované porty a adresy povolili.

## <a name="azure-spring-cloud-resource-requirements"></a>Požadavky na prostředky na jaře cloudu Azure 

Níže je uveden seznam požadavků na prostředky pro služby Azure jaře Cloud Services. Obecně platí, že byste neměli měnit skupiny prostředků, které vytvořila služba Azure jaře Cloud a základní síťové prostředky.
- Neměňte skupiny prostředků vytvořené a vlastněné pomocí Azure jaře cloudu.
  - Ve výchozím nastavení jsou tyto skupiny prostředků pojmenovány jako AP-svc-rt_ [SERVICE-INSTANCE-NAME]_[region] * a AP_[Service-instance-name] _ [oblast] *.
- Neměňte podsítě používané v Azure jaře cloudu.
- Nevytvářejte více než jednu instanci služby Azure jaře Cloud Service ve stejné podsíti.
- Při používání brány firewall pro řízení provozu Neblokujte následující odchozí přenosy do komponent Azure jaře *Cloud, které* provozují, udržují a podporují instanci služby.

## <a name="azure-spring-cloud-network-requirements"></a>Požadavky na síť cloudu Azure na jaře

  | Cílový koncový bod | Port | Použití | Poznámka |
  |------|------|------|
  | *: 1194 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | Základní Správa clusteru Kubernetes. | |
  | *: 443 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 443 | TCP: 443 | Správa služby jarní cloudová služba Azure | Informace o instanci služby "requiredTraffics" mohou být známy v datové části prostředků v části "networkProfile". |
  | *: 9000 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | Základní Správa clusteru Kubernetes. |
  | *: 123 *nebo* NTP.Ubuntu.com:123 | UDP: 123 | Synchronizace času NTP na uzlech se systémem Linux. | |
  | *. azure.io:443 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Může být nahrazena povolením  [koncového bodu služby Azure Container Registry ve virtuální síti](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. core.windows.net:443 a *. core.windows.net:445 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 a Storage: 445 | TCP: 443, TCP: 445 | Azure File Storage | Může být nahrazena povolením  [koncového bodu služby Azure Storage ve virtuální síti](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. servicebus.windows.net:443 *nebo* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – EventHub: 443 | TCP: 443 | Centrum událostí Azure. | Dá se nahradit povolením  [koncového bodu služby Azure Event Hubs ve virtuální síti](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Požadavky na plně kvalifikovaný název domény v cloudu Azure a pravidla pro aplikace

Azure Firewall poskytuje plně kvalifikovaný název domény (FQDN) **AzureKubernetesService** ke zjednodušení následujících konfigurací.

  | Cílový plně kvalifikovaný název domény | Port | Použití |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | Základní Správa clusteru Kubernetes. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | MCR úložiště zajištěné Azure CDN. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | MCR úložiště zajištěné Azure CDN. |
  | <i>management.azure.com</i> | HTTPS: 443 | Základní Správa clusteru Kubernetes. |
  | <i>* login.microsoftonline.com</i> | HTTPS: 443 | Ověřování Azure Active Directory. |
  | <i>* login.microsoft.com</i> | HTTPS: 443 | Ověřování Azure Active Directory. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Úložiště balíčků Microsoftu. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | Úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Požadované cesty řetězení certifikátů společnosti Microsoft. |
  | *crl.microsoft.com* | HTTPS: 80 | Požadované cesty řetězení certifikátů společnosti Microsoft. |
  | *crl3.digicert.com* | HTTPS: 80 | Cesty řetěz certifikátů SSL třetích stran. |

## <a name="see-also"></a>Viz také
* [Přístup k aplikaci v privátní síti](access-app-virtual-network.md)
* [Vystavení aplikací pomocí Application Gateway a Azure Firewall](expose-apps-gateway-azure-firewall.md)