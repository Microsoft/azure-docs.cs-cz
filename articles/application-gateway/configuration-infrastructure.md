---
title: Konfigurace infrastruktury Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat infrastrukturu Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: cd1dc953c35233010250bf7f959c94d1de50fe4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319788"
---
# <a name="application-gateway-infrastructure-configuration"></a>Konfigurace infrastruktury Application Gateway

Infrastruktura služby Application Gateway zahrnuje virtuální síť, podsítě, skupiny zabezpečení sítě a uživatelsky definované trasy.

## <a name="virtual-network-and-dedicated-subnet"></a>Virtuální síť a vyhrazená podsíť

Application Gateway je ve vaší virtuální síti vyhrazené nasazení. V rámci virtuální sítě se pro aplikační bránu vyžaduje vyhrazená podsíť. V podsíti můžete mít několik instancí daného nasazení aplikační brány. V podsíti můžete taky nasadit jiné aplikační brány. V podsíti aplikační brány ale nemůžete nasadit žádné další prostředky. Nemůžete kombinovat Standard_v2 a standardní Application Gateway Azure ve stejné podsíti.

> [!NOTE]
> [Zásady koncového bodu služby virtuální sítě](../virtual-network/virtual-network-service-endpoint-policies-overview.md) se v Application Gateway podsíti v tuto chvíli nepodporují.

### <a name="size-of-the-subnet"></a>Velikost podsítě

Application Gateway používá jednu privátní IP adresu na instanci a další privátní IP adresu, pokud je nakonfigurovaná privátní IP adresa front-endu.

Azure také rezervuje pět IP adres v každé podsíti pro interní použití: první čtyři a poslední IP adresy. Zvažte například 15 instancí aplikační brány bez soukromé front-endové IP adresy. Pro tuto podsíť potřebujete alespoň 20 IP adres: pět pro interní použití a 15 pro instance služby Application Gateway.

Vezměte v úvahu podsíť, která má 27 instancí aplikační brány a IP adresu pro soukromou front-end IP adresu. V takovém případě potřebujete 33 IP adres: 27 pro instance služby Application Gateway, jednu pro privátní front-end a pět pro interní použití.

SKU Application Gateway (Standard nebo WAF) může podporovat až 32 instancí (32 IP adres instancí + 1 privátní front-endové IP adresy + 5 rezervované v Azure) – Proto se doporučuje minimální velikost podsítě/26.

Application Gateway (Standard_v2 nebo WAF_v2 SKU) můžou podporovat až 125 instancí (125 IP adres instancí + 1 privátní front-endové IP adresy + 5 rezervované v Azure) – Proto se doporučuje minimální velikost podsítě/24.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

V Application Gateway jsou podporovány skupiny zabezpečení sítě (skupin zabezpečení sítě). Existují však určitá omezení:

- Je nutné, aby příchozí internetový provoz na portech TCP 65503-65534 pro SKU Application Gateway V1 a porty TCP 65200-65535 pro SKU verze 2 v2 s cílovou podsítí jako **s označením služby a jako** zdroj jako služba **GatewayManager** . Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (uzamčené) pomocí certifikátů Azure. Externí entity, včetně zákazníků těchto bran, nemůžou komunikovat na těchto koncových bodech.

- Odchozí připojení k Internetu nejde zablokovat. Výchozí odchozí pravidla v NSG umožňují připojení k Internetu. Doporučený postup:

  - Neodstraňujte Výchozí odchozí pravidla.
  - Nevytvářejte jiná odchozí pravidla, která odmítají jakékoli odchozí připojení.

- **Musí být** povolen provoz ze značky **AzureLoadBalancer** v cílové podsíti.

### <a name="allow-access-to-a-few-source-ips"></a>Povolení přístupu k několika zdrojovým IP adresám

V tomto scénáři použijte skupin zabezpečení sítě v podsíti Application Gateway. V této prioritě vložte následující omezení podsítě:

1. Povolí příchozí provoz ze zdrojové IP adresy nebo rozsahu IP adres s cílem jako celý rozsah adres podsítě Application Gateway a cílový port jako port pro příchozí přístup, například port 80 pro přístup HTTP.
2. Povolí příchozí požadavky ze zdroje jako **GatewayManager** Service Tag a Destination jako **libovolný** a cílový port jako 65503-65534 pro SKU Application Gateway V1 a porty 65200-65535 pro 2 SKU pro [komunikaci stavu back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (uzamčené) pomocí certifikátů Azure. Bez příslušných certifikátů nemohou externí entity zahájit změny těchto koncových bodů.
3. Povolit příchozí testy Azure Load Balancer (značka*AzureLoadBalancer* ) a příchozí provoz virtuální sítě (značka*VirtualNetwork* ) ve [skupině zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Zablokuje všechny ostatní příchozí přenosy pomocí pravidla odepřít – vše.
5. Povolí odchozí provoz do Internetu pro všechny cíle.

## <a name="supported-user-defined-routes"></a>Podporované uživatelsky definované trasy 

> [!IMPORTANT]
> Použití udr v podsíti Application Gateway může způsobit, že se stav v [zobrazení stavu back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) zobrazí jako **Neznámý**. Může to také způsobit selhání generování protokolů Application Gateway a metrik. Doporučujeme, abyste v Application Gateway podsíti nepoužívali udr, abyste mohli zobrazit stav back-endu, protokoly a metriky.

- **V1**

   V případě SKU v1 jsou uživatelsky definované trasy (udr) podporované v Application Gateway podsíti, pokud se nezmění na koncovou komunikaci mezi požadavky a odpověďmi. V Application Gateway podsíti můžete například nastavit UDR, aby odkazoval na zařízení brány firewall pro kontrolu paketů. Je ale nutné se ujistit, že paket se po kontrole může dostat k zamýšlenému cíli. V takovém případě může dojít k nesprávnému chování při testování stavu nebo směrování provozu. To zahrnuje naučené trasy nebo výchozí trasy 0.0.0.0/0, které šíří služby Azure ExpressRoute nebo VPN Gateway ve virtuální síti. Pro V1 se nepodporuje jakýkoli scénář, ve kterém musí být adresa 0.0.0.0/0 přesměrována místně (vynucené tunelování).

- **v2**

   Pro SKU v2 existují podporované a nepodporované scénáře:

   **verze V2 – podporované scénáře**
   > [!WARNING]
   > Nesprávná konfigurace směrovací tabulky by mohla vést k asymetrickému směrování ve Application Gateway v2. Zajistěte, aby se veškerý provoz na rovině správy a řízení odesílal přímo na Internet, a ne přes virtuální zařízení. Protokolování a metriky by taky mohly ovlivnit.


  **Scénář 1**: udr pro zákaz šíření tras Border Gateway Protocol (BGP) do Application Gateway podsítě

   Někdy je výchozí trasa brány (0.0.0.0/0) inzerována prostřednictvím bran ExpressRoute nebo VPN, které jsou přidružené k virtuální síti Application Gateway. Tím dojde k přerušení provozu roviny správy, což vyžaduje přímou cestu k Internetu. V takových scénářích se dá UDR použít k zakázání šíření tras protokolu BGP. 

   Chcete-li zakázat šíření tras protokolu BGP, použijte následující postup:

   1. Vytvořte prostředek směrovací tabulky v Azure.
   2. Zakažte parametr **šíření trasy brány virtuální sítě** . 
   3. Přidružte směrovací tabulku k příslušné podsíti. 

   Povolení UDR pro tento scénář by nemělo přerušit existující nastavení.

  **Scénář 2**: udr k přímému nasměrování 0.0.0.0/0 na Internet

   Můžete vytvořit UDR pro odeslání provozu 0.0.0.0/0 přímo na Internet. 

  **Scénář 3**: udr pro Azure Kubernetes Service s kubenet

  Pokud používáte kubenet se službou Azure Kubernetes Service (AKS) a Application Gateway řadič příchozího přenosu dat (AGIC), budete potřebovat směrovací tabulku, aby bylo možné provoz odeslaný do lusků z Application Gateway směrovat do správného uzlu. To nebude nutné v případě, že použijete Azure CNI. 

  Chcete-li pomocí směrovací tabulky kubenet pracovat, postupujte podle následujících kroků:

  1. Přejít do skupiny prostředků vytvořené pomocí AKS (název skupiny prostředků by měl začínat na "MC_")
  2. V této skupině prostředků Najděte směrovací tabulku vytvořenou AKS. Směrovací tabulka by měla naplnit následující informace:
     - Předpona adresy by měla být rozsahem IP adres lusků, které chcete oslovit v AKS. 
     - Typ dalšího segmentu směrování by měl být virtuální zařízení. 
     - Adresa dalšího směrování by měla být IP adresa uzlu hostujícího lusky.
  3. Přidruží tuto směrovací tabulku k podsíti Application Gateway. 
    
  **V2 – nepodporované scénáře**

  **Scénář 1**: udr pro virtuální zařízení

  Všechny scénáře, kdy musí být adresa 0.0.0.0/0 přesměrována prostřednictvím libovolného virtuálního zařízení, virtuální sítě typu hub/paprsek nebo místní (vynucené tunelové propojení), není pro v2 podporována.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si o konfiguraci front-ENDOVÉ IP adresy](configuration-front-end-ip.md).