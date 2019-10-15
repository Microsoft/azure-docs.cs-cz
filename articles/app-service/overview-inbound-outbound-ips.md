---
title: Příchozí/odchozí IP adresy – Azure App Service | Microsoft Docs
description: Popisuje, jak se v App Service používají příchozí a odchozí IP adresy a jak v nich najít informace pro vaši aplikaci.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: de98f972a43f3845d2a01b928d90283732ef4843
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329777"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adresy v Azure App Service

[Azure App Service](overview.md) je víceklientské služby s výjimkou [App Service prostředí](environment/intro.md). Aplikace, které nejsou v prostředí App Service (ne v [izolované vrstvě](https://azure.microsoft.com/pricing/details/app-service/)) sdílejí síťovou infrastrukturu s jinými aplikacemi. V důsledku toho může být příchozí a odchozí IP adresa aplikace odlišná a může se dokonce změnit v určitých situacích. 

[App Service prostředí](environment/intro.md) využívají vyhrazenou síťovou infrastrukturu, takže aplikace běžící v prostředí App Service získají statické a vyhrazené IP adresy pro příchozí i odchozí připojení.

## <a name="when-inbound-ip-changes"></a>Při změně příchozí IP adresy

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace jednu příchozí IP adresu. Příchozí IP adresa se může změnit, když provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků.
- Odstraní poslední aplikaci v kombinaci skupiny prostředků _a_ oblasti a znovu ji vytvoří.
- Odstraní existující vazbu SSL, například během obnovování certifikátu (viz [obnovení certifikátů](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>Najít příchozí IP adresu

Stačí spustit následující příkaz v místním terminálu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Získat statickou příchozí IP adresu

Někdy můžete chtít vyhrazenou statickou IP adresu pro vaši aplikaci. Pokud chcete získat statickou příchozí IP adresu, musíte nakonfigurovat [vazbu SSL založenou na protokolu IP](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain). Pokud nepotřebujete pro zabezpečení vaší aplikace funkci SSL, můžete dokonce pro tuto vazbu nahrát certifikát podepsaný svým držitelem. Ve vazbě protokolu SSL založeného na protokolu IP je certifikát vázán na vlastní IP adresu, takže App Service zřídí statickou IP adresu, aby k ní mohlo dojít. 

## <a name="when-outbound-ips-change"></a>Změna odchozích IP adres

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace v daném okamžiku nastavený počet odchozích IP adres. Jakékoli odchozí připojení z aplikace App Service, jako je například databáze back-end, používá jako zdrojovou IP adresu jednu z odchozích IP adres. Nemůžete předem znát, která IP adresa, kterou daná instance aplikace použije k vytvoření odchozího připojení, takže vaše back-end služba musí bránu firewall otevřít na všechny odchozí IP adresy vaší aplikace.

Sada odchozích IP adres vaší aplikace se mění při škálování aplikace mezi nižšími úrovněmi (**Basic**, **Standard**a **Premium**) a úrovní **Premium v2** .

Můžete najít sadu všech možných odchozích IP adres, které vaše aplikace může používat, a to bez ohledu na cenové úrovně, a to tak, že vyhledáte vlastnost `possibleOutboundIpAddresses` nebo v poli **Další odchozí IP adresy** v okně **vlastnosti** Azure Portal. Viz [Najít odchozí IP adresy](#find-outbound-ips).

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
