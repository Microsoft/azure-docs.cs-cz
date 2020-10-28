---
title: Příchozí/odchozí IP adresy
description: Přečtěte si, jak se příchozí a odchozí IP adresy používají v Azure App Service, když se mění a jak najít adresy pro vaši aplikaci.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e5b271cc5cd8cb52267b6ee44bc3965d0e4b0aab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746144"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adresy v Azure App Service

[Azure App Service](overview.md) je víceklientské služby s výjimkou [App Service prostředí](environment/intro.md). Aplikace, které nejsou v prostředí App Service (ne v [izolované vrstvě](https://azure.microsoft.com/pricing/details/app-service/)) sdílejí síťovou infrastrukturu s jinými aplikacemi. V důsledku toho může být příchozí a odchozí IP adresa aplikace odlišná a může se dokonce změnit v určitých situacích.

[App Service prostředí](environment/intro.md) využívají vyhrazenou síťovou infrastrukturu, takže aplikace běžící v prostředí App Service získají statické a vyhrazené IP adresy pro příchozí i odchozí připojení.

## <a name="how-ip-addresses-work-in-app-service"></a>Jak IP adresy fungují v App Service

App Service aplikace běží v plánu App Service a App Service plány se nasazují do jedné z jednotek nasazení v infrastruktuře Azure (interně označované jako webspace). Každé jednotce nasazení je přiřazena až pět virtuálních IP adres, což zahrnuje jednu veřejnou příchozí IP adresu a čtyři odchozí IP adresy. Všechny plány App Service ve stejné jednotce nasazení a spuštěné instance aplikace sdílejí stejnou sadu virtuálních IP adres. V případě App Service Environment (plán App Service na [izolovanou úroveň](https://azure.microsoft.com/pricing/details/app-service/)) je plán App Service sám se samotnou jednotkou nasazení, takže virtuální IP adresy jsou ve výsledku vyhrazené.

Vzhledem k tomu, že nepovolujete přesun plánu App Service mezi jednotkami nasazení, virtuální IP adresy přiřazené k vaší aplikaci obvykle zůstanou stejné, ale existují výjimky.

## <a name="when-inbound-ip-changes"></a>Při změně příchozí IP adresy

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace jednu příchozí IP adresu. Příchozí IP adresa se může změnit, když provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků (jednotka nasazení se může změnit).
- Odstraní poslední aplikaci v kombinaci skupiny prostředků _a_ oblasti a znovu ji vytvoří (jednotka nasazení se může změnit).
- Odstraňte existující vazbu TLS/SSL založenou na protokolu IP, například během obnovování certifikátu (viz [obnovení certifikátu](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Najít příchozí IP adresu

Stačí spustit následující příkaz v místním terminálu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Získat statickou příchozí IP adresu

Někdy můžete chtít vyhrazenou statickou IP adresu pro vaši aplikaci. Chcete-li získat statickou příchozí IP adresu, je nutné [zabezpečit vlastní doménu](configure-ssl-bindings.md#secure-a-custom-domain). Pokud nepotřebujete pro zabezpečení vaší aplikace funkci TLS, můžete dokonce pro tuto vazbu nahrát certifikát podepsaný svým držitelem. V případě vazby TLS založené na protokolu IP je certifikát vázán na vlastní IP adresu, takže App Service zřídí statickou IP adresu, aby k ní mohlo dojít. 

## <a name="when-outbound-ips-change"></a>Změna odchozích IP adres

Bez ohledu na počet instancí s horizontálním navýšení kapacity má každá aplikace v daném okamžiku nastavený počet odchozích IP adres. Jakékoli odchozí připojení z aplikace App Service, jako je například databáze back-end, používá jako zdrojovou IP adresu jednu z odchozích IP adres. IP adresa, která se má použít, se vybere náhodně za běhu, takže vaše back-end služba musí bránu firewall otevřít na všechny odchozí IP adresy vaší aplikace.

Sada odchozích IP adres vaší aplikace se změní, když provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků (jednotka nasazení se může změnit).
- Odstraní poslední aplikaci v kombinaci skupiny prostředků _a_ oblasti a znovu ji vytvoří (jednotka nasazení se může změnit).
- Škálujte svoji aplikaci mezi nižšími úrovněmi ( **Basic** , **Standard** a **Premium** ) a úrovní **Premium v2** (IP adresy se můžou přičíst nebo odvolávat ze sady).

Můžete najít sadu všech možných odchozích IP adres, které vaše aplikace může používat, a to bez ohledu na cenové úrovně, a to tak, že vyhledáte `possibleOutboundIpAddresses` vlastnost nebo v poli **Další odchozí IP adresy** v okně **vlastnosti** Azure Portal. Viz [Najít odchozí IP adresy](#find-outbound-ips).

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
