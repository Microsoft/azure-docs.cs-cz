---
title: Nejčastější dotazy ke službě Azure Private Link
description: Přečtěte si o privátních odkazech Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496469"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Nejčastější dotazy ke službě Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co je privátní koncový bod Azure a služba privátního propojení Azure?

- **[Privátní koncový bod Azure](private-endpoint-overview.md)**: privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Pomocí privátních koncových bodů se můžete připojit ke službě Azure PaaS, která podporuje privátní odkazy nebo vlastní službu privátních odkazů.
- **[Služba privátního propojení Azure](private-link-service-overview.md)**: služba privátního propojení Azure je služba vytvořená poskytovatelem služeb. V současné době může být služba privátního propojení připojena k konfiguraci protokolu IP front-endu Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Jak se posílá provoz při použití privátního propojení?
Provoz se odesílá soukromě přes páteřní síť Microsoftu. Neprojde internetem. Privátní odkaz na Azure neukládá zákaznická data.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Jaký je rozdíl mezi koncovými body služby a soukromými koncovými body?
- Soukromé koncové body udělují síťovému přístupu specifickým prostředkům za danou službu, která poskytuje podrobné segmentace. Provoz se může spojit s prostředkem služby z místního prostředí bez použití veřejných koncových bodů.
- Koncový bod služby zůstane veřejně směrovatelné IP adresy.  Soukromý koncový bod je privátní IP adresa v adresním prostoru virtuální sítě, ve které je nakonfigurovaný privátní koncový bod.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaký je vztah mezi službou privátního propojení a soukromým koncovým bodem?
Více typů prostředků privátního propojení podporuje přístup prostřednictvím privátního koncového bodu. Mezi prostředky patří služby Azure PaaS a vaše vlastní služba privátních odkazů. Je to vztah 1: n. 

Služba privátního propojení přijímá připojení z více privátních koncových bodů. Privátní koncový bod se připojí k jedné službě privátního propojení.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Potřebuji zakázat zásady sítě pro privátní propojení
Ano. Privátní koncový bod i služba privátního propojení potřebují zakázat zásady sítě, aby fungovaly správně. Mají obě vlastnosti nezávisle na sobě.

## <a name="private-endpoint"></a>Privátní koncový bod 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Můžu ve stejné virtuální síti vytvořit více privátních koncových bodů? Můžou se připojovat k různým službám? 
Ano. Můžete mít několik privátních koncových bodů ve stejné virtuální síti nebo podsíti. Můžou se připojovat k různým službám.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Vyžadujem vyhrazenou podsíť pro privátní koncové body? 
No. U privátních koncových bodů nevyžadujete vyhrazenou podsíť. Můžete vybrat IP adresu privátního koncového bodu z jakékoli podsítě z virtuální sítě, ve které je vaše služba nasazená.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Může se privátní koncový bod připojit ke službám privátního propojení napříč klienty Azure Active Directory? 
Ano. Privátní koncové body se můžou připojovat ke službám privátního propojení nebo k Azure PaaS napříč klienty Azure Active Directory. Privátní koncové body, které se připojují přes klienty, vyžadují schválení ruční žádosti. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Může se privátní koncový bod připojit k prostředkům Azure PaaS napříč oblastmi Azure?
Ano. Privátní koncové body se můžou připojit k prostředkům Azure PaaS napříč oblastmi Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Můžu změnit své privátní síťové rozhraní (NIC)?
Po vytvoření privátního koncového bodu je přiřazena síťová karta jen pro čtení. Tuto informaci nejde upravit a zůstane pro životní cyklus privátního koncového bodu.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Návody dosáhnout dostupnosti při používání privátních koncových bodů v případě regionálních selhání?

Soukromé koncové body jsou vysoce dostupné prostředky s 99,99% SLA [[SLA pro privátní propojení Azure]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/). Ale vzhledem k tomu, že se jedná o regionální prostředky, může dopad na dostupnost všech oblastí Azure ovlivnit. Aby bylo možné dosáhnout dostupnosti v případě regionálních selhání, může být v různých oblastech nasazeno více verzí PEs připojených ke stejnému cílovému prostředku. Tímto způsobem můžete v případě, že dojde k výpadku jedné oblasti, směrovat provoz pro vaše scénáře obnovení prostřednictvím PE v různých oblastech pro přístup k cílovému prostředku. Informace o tom, jak se na straně cílové služby zpracovávají regionální selhání, najdete v dokumentaci ke službě pro převzetí služeb při selhání a obnovení. Přenosy privátních odkazů následují Azure DNS rozlišení cílového koncového bodu. 


## <a name="private-link-service"></a>Služba privátního propojení
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jaké jsou požadavky pro vytvoření služby privátního propojení? 
Back-endy služby by měly být ve virtuální síti a za Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak můžu škálovat službu privátního propojení? 
Službu privátního propojení můžete škálovat několika různými způsoby: 
- Přidání back-end virtuálních počítačů do fondu za vaším Standard Load Balancer 
- Přidejte IP adresu ke službě privátního propojení. Povolujeme až 8 IP adres na jednu službu privátního propojení.  
- Přidejte novou službu privátního propojení do Standard Load Balancer. Pro každý nástroj pro vyrovnávání zatížení povolujeme až osm služeb privátních odkazů.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Co je konfigurace protokolu IP NAT (překladu síťových adres) použitá ve službě soukromé vazby? Jak můžu škálovat z pohledu dostupných portů a připojení? 

Konfigurace protokolu IP NAT zajišťuje, aby nedošlo ke konfliktu IP adres mezi zdrojovým a cílovým adresním prostorem (poskytovatelem služeb) a poskytováním zdrojového překladu adres na přenosech privátních odkazů na straně cíle (na straně poskytovatele služeb). IP adresa překladu adres (NAT) se zobrazí jako zdrojová IP adresa pro všechny pakety přijaté službou a cílovou IP adresou pro všechny pakety odesílané vaší službou.  IP adresu NAT si můžete vybrat z libovolné podsítě ve virtuální síti poskytovatele služeb. 

Každá IP adresa NAT poskytuje pro každý virtuální počítač za Standard Load Balancer 64 000 připojení TCP (64 portů). Aby bylo možné škálovat a přidávat další připojení, můžete buď přidat nové IP adresy NAT, nebo přidat další virtuální počítače za Standard Load Balancer. Provedete to tak, že budete škálovat dostupnost portů a povolíte další připojení. Připojení se budou distribuovat napříč IP adresami NAT a virtuálními počítači za Standard Load Balancer.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Můžu připojit službu k více soukromým koncovým bodům?
Ano. Jedna služba privátního propojení může přijímat připojení z více privátních koncových bodů. Jeden privátní koncový bod se ale může připojit jenom k jedné službě privátního propojení.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak mám řídit expozici mé služby privátního propojení?
Můžete řídit expozici pomocí konfigurace viditelnosti ve službě privátního propojení. Viditelnost podporuje tři nastavení:

- **Žádná** – pouze předplatná s přístupem k Azure RBAC můžou službu najít. 
- Jenom **omezující** odběry, které jsou schválené a které mají přístup k Azure RBAC, můžou službu najít. 
- **Všechno** – může službu najít kdokoli. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Můžu vytvořit službu privátního propojení se základním nástrojem pro vyrovnávání zatížení? 
No. Služba privátního propojení přes základní nástroj pro vyrovnávání zatížení není podporována.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Vyžaduje se pro službu privátního propojení vyhrazená podsíť? 
No. Služba privátního propojení nevyžaduje vyhrazenou podsíť. Můžete zvolit libovolnou podsíť ve virtuální síti, kde je vaše služba nasazená.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jsem poskytovatelem služeb pomocí privátního propojení Azure. Musím se muset ujistit, že všichni moji zákazníci mají jedinečný adresní prostor IP adres a nepřekrývat ho s mým adresním prostorem? 
No. Pro vás tato funkce poskytuje soukromý odkaz Azure. Nemusíte mít překrývající se adresní prostor s adresním prostorem zákazníka. 

##  <a name="next-steps"></a>Další kroky

- Informace o [privátním propojení Azure](private-link-overview.md)
