---
title: Příchozí/odchozí IP adresy
description: Přečtěte si, jak se příchozí a odchozí IP adresy používají v Azure App Service, když se mění a jak najít adresy pro vaši aplikaci.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671607"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adresy v Azure App Service

[Azure App Service](overview.md) je víceklientské služby s výjimkou [App Service prostředí](environment/intro.md). Aplikace, které nejsou v prostředí App Service (ne v [izolované vrstvě](https://azure.microsoft.com/pricing/details/app-service/)) sdílejí síťovou infrastrukturu s jinými aplikacemi. V důsledku toho může být příchozí a odchozí IP adresa aplikace odlišná a může se dokonce změnit v určitých situacích. 

[App Service prostředí](environment/intro.md) využívají vyhrazenou síťovou infrastrukturu, takže aplikace běžící v prostředí App Service získají statické a vyhrazené IP adresy pro příchozí i odchozí připojení.

## <a name="when-inbound-ip-changes"></a>Při změně příchozí IP adresy

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace jednu příchozí IP adresu. Příchozí IP adresa se může změnit, když provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků.
- Odstraní poslední aplikaci v kombinaci skupiny prostředků _a_ oblasti a znovu ji vytvoří.
- Odstraní existující vazbu SSL, například během obnovování certifikátu (viz [obnovení certifikátu](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Najít příchozí IP adresu

Stačí spustit následující příkaz v místním terminálu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Získat statickou příchozí IP adresu

Někdy můžete chtít vyhrazenou statickou IP adresu pro vaši aplikaci. Pokud chcete získat statickou příchozí IP adresu, musíte nakonfigurovat [vazbu SSL založenou na protokolu IP](configure-ssl-bindings.md#secure-a-custom-domain). Pokud nepotřebujete pro zabezpečení vaší aplikace funkci SSL, můžete dokonce pro tuto vazbu nahrát certifikát podepsaný svým držitelem. Ve vazbě protokolu SSL založeného na protokolu IP je certifikát vázán na vlastní IP adresu, takže App Service zřídí statickou IP adresu, aby k ní mohlo dojít. 

## <a name="when-outbound-ips-change"></a>Změna odchozích IP adres

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace v daném okamžiku nastavený počet odchozích IP adres. Jakékoli odchozí připojení z aplikace App Service, jako je například databáze back-end, používá jako zdrojovou IP adresu jednu z odchozích IP adres. Nemůžete předem znát, která IP adresa, kterou daná instance aplikace použije k vytvoření odchozího připojení, takže vaše back-end služba musí bránu firewall otevřít na všechny odchozí IP adresy vaší aplikace.

Sada odchozích IP adres vaší aplikace se mění při škálování aplikace mezi nižšími úrovněmi (**Basic**, **Standard**a **Premium**) a úrovní **Premium v2** .

Můžete najít sadu všech možných odchozích IP adres, které vaše aplikace může používat, bez ohledu na cenové úrovně, a to tak, že vyhledáte vlastnost `possibleOutboundIpAddresses` nebo v poli **Další odchozí IP adresy** v okně **vlastnosti** Azure Portal. Viz [Najít odchozí IP adresy](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Najít odchozí IP adresy

Pokud chcete najít odchozí IP adresy, které vaše aplikace aktuálně používá v Azure Portal, klikněte na **vlastnosti** v levém navigačním panelu vaší aplikace. Jsou uvedeny v poli **odchozí IP adresy** .

Stejné informace můžete najít spuštěním následujícího příkazu v [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Pokud chcete najít _všechny_ možné odchozí IP adresy pro vaši aplikaci bez ohledu na cenové úrovně, klikněte na **vlastnosti** v levém navigačním panelu aplikace. Jsou uvedeny v poli **Další odchozí IP adresy** .

Stejné informace můžete najít spuštěním následujícího příkazu v [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak omezit příchozí provoz na základě IP adres zdrojů.

> [!div class="nextstepaction"]
> [Omezení statických IP adres](app-service-ip-restrictions.md)
