---
title: Nejčastější dotazy týkající se privátního spojení Azure
description: Přečtěte si o Privátním spojení Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349942"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se privátního spojení Azure

## <a name="private-link"></a>Privátní propojení

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co je Azure Private Endpoint a Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)**: Azure Private Endpoint je síťové rozhraní, které vás soukromě a bezpečně propojuje se službou využívající Azure Private Link. Privátní koncové body můžete použít k připojení ke službě Azure PaaS, která podporuje private link nebo k vlastní službě Private Link.
- **[Služba Azure Private Link Service:](private-link-service-overview.md)** Služba Azure Private Link je služba vytvořená poskytovatelem služeb. V současné době může být služba Private Link připojena k frontendové konfiguraci IP standardního nástroje pro vyrovnávání zatížení. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Jak se odesílá provoz při použití private link?
Provoz je odesílán soukromě pomocí páteřní sítě společnosti Microsoft. Neprochází internetem.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Jaký je rozdíl mezi koncovými body služby a privátními koncovými body?
- Při použití privátní koncové body přístup k síti je udělena konkrétní prostředky za danou službu poskytující podrobné segmentace, také provoz může dosáhnout prostředku služby z místní bez použití veřejných koncových bodů.
- Koncový bod služby zůstává veřejně směrovatelnou IP adresou.  Privátní koncový bod je privátní IP adresa v adresním prostoru virtuální sítě, kde je nakonfigurován soukromý koncový bod.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaký je vztah mezi službou Private Link a private endpoint?
Privátní Koncový bod poskytuje přístup k více typů prostředků privátní propojení, včetně služeb Azure PaaS a vlastní služby Private Link. Je to vztah jeden na jednoho. Jedna služba Private Link může přijímat připojení z více soukromých koncových bodů. Na druhou stranu jeden soukromý koncový bod se může připojit pouze k jedné službě Private Link.    

## <a name="private-endpoint"></a>Privátní koncový bod 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Můžu ve stejné virtuální síti vytvořit víc soukromých koncových bodů? Mohou se připojit k různým službám? 
Ano. Můžete mít více soukromých koncových bodů ve stejné virtuální síti nebo podsíti. Mohou se připojit k různým službám.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Je nutné vyhrazenou podsíť pro soukromé koncové body? 
Ne. Nepotřebujete vyhrazenou podsíť pro soukromé koncové body. Můžete zvolit privátní endpoint IP z libovolné podsítě z virtuální sítě, kde se používá vaše služba.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Může se privátní koncový bod připojit ke službě Private Link napříč klienty Služby Azure Active Directory? 
Ano. Privátní koncové body se můžou připojit ke službám Private Link nebo Azure PaaS napříč klienty Služby AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Může se privátní koncový bod připojit k prostředkům Azure PaaS napříč oblastmi Azure?
Ano. Privátní koncové body se můžou připojit k prostředkům Azure PaaS napříč oblastmi Azure.

## <a name="private-link-service"></a>Služba privátního propojení
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jaké jsou předpoklady pro vytvoření služby Private Link? 
Back-endy služby by měly být ve virtuální síti a za standardní nástroj pro vyrovnávání zatížení.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak lze škálovat službu Private Link? 
Službu Private Link můžete škálovat několika různými způsoby: 
- Přidání back-endových virtuálních počítačů do fondu za standardním vyrovnáváním zatížení 
- Přidejte ip adresu do služby Private Link. Povolujeme až 8 IP adresy na službu Private Link.  
- Přidejte novou službu Private Link do standardního vykladače zatížení. Na vyvažovači zatížení povolujeme až osm služeb Private Link.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Můžu svou službu připojit k více privátním koncovým bodům?
Ano. Jedna služba Private Link může přijímat připojení z více soukromých koncových bodů. Jeden soukromý koncový bod se však může připojit pouze k jedné službě Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak mám kontrolovat expozici své služby Private Link?
Expozici můžete řídit pomocí konfigurace viditelnosti ve službě Private Link. Viditelnost podporuje tři nastavení:

- **Žádné** – službu mohou vyhledat pouze předplatná s přístupem RBAC. 
- **Omezující** – službu lze vyhledat pouze předplatnými, která jsou na seznamu povolených a s přístupem RBAC. 
- **Vše** - Každý může najít službu. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Mohu vytvořit službu Private Link se základním vyrovnáváním zatížení? 
Ne. Služba Private Link přes základní vyrovnávání zatížení není podporována.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Je vyhrazená podsíť vyžadována pro službu Private Link? 
Ne. Služba Private Link nevyžaduje vyhrazenou podsíť. Můžete zvolit libovolnou podsíť ve virtuální síti, kde se vaše služba nasazuje.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jsem poskytovatel služeb pomocí Azure Private Link. Musím se ujistit, že všichni moji zákazníci mají jedinečný IP prostor a nepřekrývají se s mým IP prostorem? 
Ne. Azure Private Link poskytuje tuto funkci pro vás. Proto není nutné mít nepřekrývající se adresní prostor s adresním prostorem zákazníka. 

##  <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](private-link-overview.md)
