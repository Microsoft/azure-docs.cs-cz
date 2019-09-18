---
title: Nejčastější dotazy týkající se privátního propojení Azure
description: Přečtěte si o privátních odkazech Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 7e08c91636089abf25e46e1303097fc27b2d75b1
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037513"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se privátního propojení Azure

## <a name="private-link"></a>Privátní propojení

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co je privátní koncový bod Azure a služba privátního propojení Azure?

- **[Privátní koncový bod Azure](private-endpoint-overview.md)** : Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Pomocí privátních koncových bodů se můžete připojit ke službě Azure PaaS, která podporuje privátní odkazy nebo vlastní službu privátních odkazů.
- **[Služba privátního propojení Azure](private-link-service-overview.md)** : Služba privátního propojení Azure je služba vytvořená poskytovatelem služeb. V současné době může být služba privátního propojení připojena k konfiguraci protokolu IP front-endu Standard Load Balancer. 

### <a name="how-is-traffic-being-send-when-using-private-link"></a>Jak se posílá provoz při použití privátního propojení?
Provoz se odesílá soukromě přes páteřní síť Microsoftu. Neprojde internetem.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Jaký je rozdíl mezi koncovými body služby a soukromými koncovými body?
- Při použití privátních koncových bodů se k přístupu k síti přidělí konkrétní prostředky za danou službou, která poskytuje podrobné segmentování, provoz se taky může spojit s prostředkem služby z místního prostředí bez použití veřejných koncových bodů.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaký je vztah mezi službou privátního propojení a soukromým koncovým bodem?
Soukromý koncový bod poskytuje přístup k několika typům prostředků privátního propojení, včetně služeb Azure PaaS a vlastní služby privátního propojení. Jedná se o vztah 1: n. Jedna služba privátního propojení může přijímat připojení z více privátních koncových bodů. Na druhé straně se jeden privátní koncový bod může připojit jenom k jedné službě privátního propojení.    

## <a name="private-endpoint"></a>Soukromý koncový bod 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Můžu ve stejné virtuální síti vytvořit více privátních koncových bodů? Můžou se připojovat k různým službám? 
Ano. Můžete mít několik privátních koncových bodů ve stejné virtuální síti nebo podsíti. Můžou se připojovat k různým službám.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Vyžadujem vyhrazenou podsíť pro privátní koncové body? 
Ne. U privátních koncových bodů nevyžadujete vyhrazenou podsíť. Můžete vybrat IP adresu privátního koncového bodu z jakékoli podsítě z virtuální sítě, ve které je vaše služba nasazená.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Může se privátní koncový bod připojit ke službě privátního připojení napříč klienty Azure Active Directory? 
Ano. Privátní koncové body se můžou připojovat ke službám privátního propojení nebo Azure PaaS napříč klienty služby AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Může se privátní koncový bod připojit k prostředkům Azure PaaS napříč oblastmi Azure?
Ano. Privátní koncové body se můžou připojit k prostředkům Azure PaaS napříč oblastmi Azure.

## <a name="private-link-service"></a>Služba privátního propojení
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jaké jsou požadavky pro vytvoření služby privátního propojení? 
Back-endy služby by měly být ve virtuální síti a za Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak můžu škálovat službu privátního propojení? 
Službu privátního propojení můžete škálovat několika různými způsoby: 
- Přidání back-end virtuálních počítačů do fondu za vaším Standard Load Balancer 
- Přidejte IP adresu ke službě privátního propojení. Povolujeme až 8 IP adres na jednu službu privátního propojení.  
- Přidejte novou službu privátního propojení do Standard Load Balancer. Pro každý nástroj pro vyrovnávání zatížení povolujeme až osm služeb privátních odkazů.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Můžu připojit službu k více soukromým koncovým bodům?
Ano. Jedna služba privátního propojení může přijímat připojení z více privátních koncových bodů. Jeden privátní koncový bod se ale může připojit jenom k jedné službě privátního propojení.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak mám řídit expozici mé služby privátního propojení?
Můžete řídit expozici pomocí konfigurace viditelnosti ve službě privátního propojení. Viditelnost podporuje tři nastavení:

- **Žádné** – pouze odběry s přístupem RBAC můžou službu najít. 
- Jenom **omezující** odběry, které jsou na seznamu povolených a které mají přístup RBAC, můžou službu najít. 
- **Všechno** – může službu najít kdokoli. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Můžu vytvořit službu privátního propojení se základní Load Balancer? 
Ne. Služba privátního propojení přes základní Load Balancer není podporována.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Vyžaduje se pro službu privátního propojení vyhrazená podsíť? 
Ne. Služba privátního propojení nevyžaduje vyhrazenou podsíť. Můžete zvolit libovolnou podsíť ve virtuální síti, kde je vaše služba nasazená.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jsem poskytovatelem služeb pomocí privátního propojení Azure. Musím se muset ujistit, že všichni moji zákazníci mají jedinečný adresní prostor IP adres a nepřekrývat ho s mým adresním prostorem? 
Ne. Pro vás tato funkce poskytuje soukromý odkaz Azure. Proto nemusíte mít překrývající se adresní prostor s adresním prostorem zákazníka. 

##  <a name="next-steps"></a>Další kroky

- Informace o [privátním propojení Azure](private-link-overview.md)
