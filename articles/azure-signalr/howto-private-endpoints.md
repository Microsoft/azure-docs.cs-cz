---
title: Použití privátních koncových bodů
titleSuffix: Azure SignalR Service
description: Přehled privátních koncových bodů pro zabezpečený přístup ke službě Azure Signal Service z virtuálních sítí.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682626"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Použití privátních koncových bodů pro službu Azure Signal Service

Pro službu signalizace Azure můžete použít [privátní koncové body](../private-link/private-endpoint-overview.md) , které umožní klientům ve virtuální síti (VNET) zabezpečený přístup k datům prostřednictvím [privátního propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro vaši službu Azure Signal. Síťový provoz mezi klienty virtuální sítě a službou signalizace Azure prochází privátním odkazem na páteřní síti Microsoftu a odstraňuje expozici veřejného Internetu.

Použití privátních koncových bodů pro službu signalizace Azure vám umožní:

- Zabezpečte službu Azure Signal pomocí řízení přístupu k síti k blokování všech připojení ve veřejném koncovém bodu služby Azure Signal.
- Zvyšte zabezpečení virtuální sítě tak, že povolíte blokování exfiltrace dat z virtuální sítě.
- Bezpečně se připojovat ke službám Azure Signal z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

## <a name="conceptual-overview"></a>Základní přehled

![Přehled privátních koncových bodů pro službu Azure Signal Service](media/howto-private-endpoints/private-endpoint-overview.png)

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší [Virtual Network](../virtual-network/virtual-networks-overview.md) (virtuální síť). Při vytváření privátního koncového bodu pro službu Azure Signal Service je zajištěno zabezpečené připojení mezi klienty ve vaší virtuální síti a vaší službou. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou Azure Signal Service používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou bez problémů připojit ke službě Azure Signal Service přes soukromý koncový bod **pomocí stejných připojovacích řetězců a autorizačních mechanismů, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými službou Azure Signal, včetně REST API.

Když vytvoříte privátní koncový bod pro službu Azure Signal Service ve vaší virtuální síti, pošle se žádost o souhlas ke schválení vlastníkem služby signalizace Azure. Pokud je uživatel požadující vytvoření privátního koncového bodu také vlastníkem služby Azure Signaler, je tato žádost o přijetí souhlasu automaticky schválena.

Vlastníci služby signálu v Azure mohou spravovat žádosti o souhlas a soukromé koncové body prostřednictvím karty *privátní koncové body* pro službu Azure Signal Service v [Azure Portal](https://portal.azure.com).

> [!TIP]
> Pokud chcete omezit přístup k vaší službě Azure Signal pomocí pouze privátního koncového bodu, [nakonfigurujte Access Control sítě](howto-network-access-control.md#managing-network-access-control) tak, aby odepřela nebo řídit přístup prostřednictvím veřejného koncového bodu.

### <a name="connecting-to-private-endpoints"></a>Připojování k soukromým koncovým bodům

Klienti ve virtuální síti s použitím privátního koncového bodu by měli použít stejný připojovací řetězec služby Azure Signal, jako klienti připojení k veřejnému koncovému bodu. Na překladu DNS spoléháme na Automatické směrování připojení z virtuální sítě do služby Azure Signal Service prostřednictvím privátního odkazu.

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení ke službě Azure Signal Service pomocí privátních koncových bodů, jak byste to využívali jinak. Nepřipojujte se ke službě Azure Signal Service pomocí `privatelink` adresy URL své subdomény.

Ve výchozím nastavení vytvoříme [privátní ZÓNU DNS](../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnými aktualizacemi pro privátní koncové body. Pokud ale používáte vlastní server DNS, možná budete muset provést další změny v konfiguraci DNS. Oddíl o [změnách DNS](#dns-changes-for-private-endpoints) níže popisuje aktualizace vyžadované pro soukromé koncové body.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro vaši službu Azure Signale aktualizuje na alias v subdoméně s předponou `privatelink` . Ve výchozím nastavení vytvoříme také [soukromou ZÓNU DNS](../dns/private-dns-overview.md), která odpovídá `privatelink` subdoméně, a záznamy prostředků DNS pro privátní koncové body.

Při překladu názvu domény služby signalizace Azure z oblasti mimo virtuální síť s privátním koncovým bodem se přeloží na veřejný koncový bod služby Azure Signal. Při vyřešení z virtuální sítě hostující soukromý koncový bod se název domény přeloží na IP adresu privátního koncového bodu.

Pro znázorněný příklad uvedený výše budou záznamy o prostředcích DNS pro službu Azure Signaler "panel" po vyřešení mimo virtuální síť hostující soukromý koncový bod:

| Název                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Jak už jsme uvedli, můžete odepřít nebo řídit přístup pro klienty mimo virtuální síť prostřednictvím veřejného koncového bodu pomocí řízení přístupu k síti.

Záznamy o prostředcích DNS pro ' panel ', pokud jsou vyřešeny klientem ve virtuální síti, která je hostitelem privátního koncového bodu, bude:

| Název                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | zákazníka 10.1.1.5                                              |

Tento přístup umožňuje přístup ke službě Azure Signal Service **pomocí stejného připojovacího řetězce** pro klienty ve virtuální síti, která je hostitelem privátních koncových bodů, i klientů mimo virtuální síť.

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény pro koncový bod služby signálů Azure na IP adresu privátního koncového bodu. Server DNS byste měli nakonfigurovat tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo aby záznamy A pro `foobar.privatelink.service.signalr.net` IP adresu privátního koncového bodu byly nakonfigurované.

> [!TIP]
> Pokud používáte vlastní nebo místní server DNS, měli byste server DNS nakonfigurovat tak, aby přeložil název služby signálu Azure v `privatelink` subdoméně na IP adresu privátního koncového bodu. Můžete to udělat tak `privatelink` , že subdoménu delegujete do privátní zóny DNS virtuální sítě nebo nakonfigurujete ZÓNU DNS na serveru DNS a přidáte záznamy DNS.

Doporučený název zóny DNS pro privátní koncové body služby Azure Signal je: `privatelink.service.signalr.net` .

Další informace o konfiguraci vlastního serveru DNS pro podporu privátních koncových bodů najdete v následujících článcích:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro privátní koncové body](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Vytvořte privátní koncový bod spolu s novou službou Azure Signal Service v Azure Portal

1. Při vytváření nové služby signalizace Azure vyberte kartu **síť** . Jako metodu připojení vyberte **privátní koncový bod** .

    ![Vytvoření služby signalizace Azure – karta síť](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Klikněte na **Přidat**. Vyplňte pole předplatné, skupina prostředků, umístění a název nového privátního koncového bodu. Vyberte virtuální síť a podsíť.

    ![Vytvoření služby signalizace Azure – přidání privátního koncového bodu](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Klikněte na **Zkontrolovat a vytvořit**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Vytvoření privátního koncového bodu pro existující službu Azure Signal Service v Azure Portal

1. Přejít na službu Azure Signal Service.

1. Klikněte na nabídku nastavení s názvem **připojení privátního koncového bodu**.

1. V horní části klikněte na tlačítko **+ soukromý koncový bod** .

    ![Okno připojení privátního koncového bodu](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Vyplňte v předplatném, skupině prostředků, názvu prostředku a oblasti nový soukromý koncový bod.
    
    ![Vytvoření základních informací o základech koncového bodu](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Vyberte cílový prostředek služby Azure Signal.

    ![Vytvoření privátního koncového bodu – prostředek](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Zvolit cílovou virtuální síť

    ![Vytvoření privátního koncového bodu – konfigurace](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Klikněte na **Zkontrolovat a vytvořit**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Vytvoření privátního koncového bodu pomocí Azure CLI

1. Přihlášení do Azure CLI
    ```azurecli
    az login
    ```
1. Vyberte své předplatné Azure.
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Vytvoření nové skupiny prostředků
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Registrovat Microsoft. SignalRService jako poskytovatele
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Vytvořit novou službu Azure Signal Service
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Vytvoření virtuální sítě
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Přidání podsítě
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Zakázat zásady Virtual Network
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Přidat zónu Privátní DNS
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Propojit zónu Privátní DNS s Virtual Network
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Vytvoření privátního koncového bodu (automaticky schvalovat)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Vytvoření privátního koncového bodu (žádost o schválení ručně)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Zobrazit stav připojení
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Známé problémy

Pamatujte na následující známé problémy týkající se privátních koncových bodů služby Azure Signal

### <a name="free-tier"></a>Úroveň Free

Nemůžete vytvořit žádný privátní koncový bod pro službu Azure Signal Service úrovně Free.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Omezení přístupu pro klienty v virtuální sítě s privátními koncovými body

Klienti v virtuální sítě se stávajícími omezeními tváře privátních koncových bodů při přístupu k jiným instancím služby signalizace Azure, které mají privátní koncové body. Předpokládejme například, že virtuální síť N1 má privátní koncový bod pro instanci služby signalizace Azure S1. Pokud má služba Azure Signaler ve virtuálním N2 privátní koncový bod, pak klienti ve virtuální síti N1 mají přístup také ke službě Azure Signal Service S2 pomocí privátního koncového bodu. Pokud služba signálů Azure S2 nemá žádné privátní koncové body, klienti v síti VNet N1 můžou ke službě Azure Signal Service přistupovat v tomto účtu bez privátního koncového bodu.

Toto omezení je výsledkem změn DNS, které provedou, když služba Azure Signal Service S2 vytvoří privátní koncový bod.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Pravidla skupin zabezpečení sítě pro podsítě s privátními koncovými body

V současné době nemůžete konfigurovat pravidla [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md) (NSG) a trasy definované uživatelem pro privátní koncové body. Pravidla NSG použitá pro podsíť hostující soukromý koncový bod se aplikují na soukromý koncový bod. Omezené řešení tohoto problému je implementace pravidel přístupu pro privátní koncové body ve zdrojových podsítích, i když tento přístup může vyžadovat vyšší režijní náklady na správu.

## <a name="next-steps"></a>Další kroky

- [Konfigurace Access Control sítě](howto-network-access-control.md)