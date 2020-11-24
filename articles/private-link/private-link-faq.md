---
title: Nejčastější dotazy týkající se privátního propojení Azure
description: Přečtěte si o privátních odkazech Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 2e5edb0ccf20ab0dba14864487f2b2ddef7d57e7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544203"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se privátního propojení Azure

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co je privátní koncový bod Azure a služba privátního propojení Azure?

- **[Privátní koncový bod Azure](private-endpoint-overview.md)**: privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Pomocí privátních koncových bodů se můžete připojit ke službě Azure PaaS, která podporuje privátní odkazy nebo vlastní službu privátních odkazů.
- **[Služba privátního propojení Azure](private-link-service-overview.md)**: služba privátního propojení Azure je služba vytvořená poskytovatelem služeb. V současné době může být služba privátního propojení připojena k konfiguraci protokolu IP front-endu Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Jak se posílá provoz při použití privátního propojení?
Provoz se odesílá soukromě přes páteřní síť Microsoftu. Neprojde internetem. Privátní odkaz na Azure neukládá zákaznická data.
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Jaký je rozdíl mezi koncovými body služby a soukromými koncovými body?
- Soukromé koncové body udělují síťovému přístupu specifickým prostředkům za danou službu, která poskytuje podrobné segmentace. Provoz se může spojit s prostředkem služby z místního prostředí bez použití veřejných koncových bodů.
- Koncový bod služby zůstane veřejně směrovatelné IP adresy.  Soukromý koncový bod je privátní IP adresa v adresním prostoru virtuální sítě, ve které je nakonfigurovaný privátní koncový bod.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaký je vztah mezi službou privátního propojení a soukromým koncovým bodem?
Více typů prostředků privátního propojení podporuje přístup prostřednictvím privátního koncového bodu. Mezi prostředky patří služby Azure PaaS a vaše vlastní služba privátních odkazů. Je to vztah 1: n. 

Služba privátního propojení přijímá připojení z více privátních koncových bodů. Privátní koncový bod se připojí k jedné službě privátního propojení.    

## <a name="private-endpoint"></a>Privátní koncový bod 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Můžu ve stejné virtuální síti vytvořit více privátních koncových bodů? Můžou se připojovat k různým službám? 
Ano. Můžete mít několik privátních koncových bodů ve stejné virtuální síti nebo podsíti. Můžou se připojovat k různým službám.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Vyžadujem vyhrazenou podsíť pro privátní koncové body? 
Ne. U privátních koncových bodů nevyžadujete vyhrazenou podsíť. Můžete vybrat IP adresu privátního koncového bodu z jakékoli podsítě z virtuální sítě, ve které je vaše služba nasazená.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Může se privátní koncový bod připojit ke službě privátního připojení napříč klienty Azure Active Directory? 
Ano. Privátní koncové body se můžou připojovat ke službám privátního propojení nebo Azure PaaS napříč klienty služby AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Může se privátní koncový bod připojit k prostředkům Azure PaaS napříč oblastmi Azure?
Ano. Privátní koncové body se můžou připojit k prostředkům Azure PaaS napříč oblastmi Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Můžu změnit své privátní síťové rozhraní (NIC)?
Po vytvoření privátního koncového bodu je přiřazena síťová karta jen pro čtení. Tuto informaci nejde upravit a zůstane pro životní cyklus privátního koncového bodu.

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

- **Žádná** – pouze předplatná s přístupem k Azure RBAC můžou službu najít. 
- Jenom **omezující** odběry, které jsou schválené a které mají přístup k Azure RBAC, můžou službu najít. 
- **Všechno** – může službu najít kdokoli. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Můžu vytvořit službu privátního propojení se základním nástrojem pro vyrovnávání zatížení? 
Ne. Služba privátního propojení přes základní nástroj pro vyrovnávání zatížení není podporována.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Vyžaduje se pro službu privátního propojení vyhrazená podsíť? 
Ne. Služba privátního propojení nevyžaduje vyhrazenou podsíť. Můžete zvolit libovolnou podsíť ve virtuální síti, kde je vaše služba nasazená.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jsem poskytovatelem služeb pomocí privátního propojení Azure. Musím se muset ujistit, že všichni moji zákazníci mají jedinečný adresní prostor IP adres a nepřekrývat ho s mým adresním prostorem? 
Ne. Pro vás tato funkce poskytuje soukromý odkaz Azure. Nemusíte mít překrývající se adresní prostor s adresním prostorem zákazníka. 

##  <a name="next-steps"></a>Další kroky

- Informace o [privátním propojení Azure](private-link-overview.md)
