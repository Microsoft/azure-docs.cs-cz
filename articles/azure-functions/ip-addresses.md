---
title: IP adresy ve službě Azure Functions
description: Zjistěte, jak najít příchozí a odchozí IP adresy pro aplikace function App a co způsobí, že je změnit.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: glenga
ms.openlocfilehash: ce520c5972bf27c30ecb175d58b6b6071bf804db
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818288"
---
# <a name="ip-addresses-in-azure-functions"></a>IP adresy ve službě Azure Functions

Tento článek vysvětluje následující témata související s aplikací function App IP adresy:

* Jak chcete zjistit IP adresy aktuálně používá v aplikaci function app.
* Co způsobí, že funkce vaší aplikace IP adresy, které se změnit.
* Jak můžete omezit IP adresy, které můžete přístup k aplikaci function app.
* Jak získat vyhrazené IP adresy pro aplikaci function app.

IP adresy jsou spojeny s aplikací function App, nikoli jednotlivých funkcí. Příchozí požadavky HTTP nelze použít IP adresu příchozího volat jednotlivé funkce. Uživatel musí použít výchozí název domény (functionappname.azurewebsites.net) nebo vlastního názvu domény.

## <a name="function-app-inbound-ip-address"></a>Aplikace Function app příchozí IP adresu

Každá aplikace function app má jednu příchozí IP adresu. Chcete zjistit tuto IP adresu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do aplikace function app.
3. Vyberte **funkce platformy**.
4. Vyberte **vlastnosti**, a příchozí IP adresa se zobrazí v části **virtuální IP adresa**.

## <a name="find-outbound-ip-addresses"></a>Odchozí IP adresy aplikace – funkce

Každá aplikace function app obsahuje sadu k dispozici odchozí IP adresy. Všechny odchozí připojení z funkce, třeba k back-end databáze, používá jednu z dostupných odchozí IP adresy jako IP adresa původu. Nemůže vědět předem bude používat jedno připojení která IP adresa. Z tohoto důvodu musí back-end služby otevřete jeho bránu firewall, aby všechny aplikace function app odchozí IP adresy.

Chcete-li zjistit odchozí IP adres k dispozici pro aplikaci function app:

1. Přihlaste se k [Azure Resource Exploreru](https://resources.azure.com).
2. Vyberte **předplatná > {vaše předplatné} > poskytovatelů > Microsoft.Web > lokality**.
3. V panelu JSON najdete na webu `id` vlastnost, která končí název vaší aplikace function app.
4. Zobrazit `outboundIpAddresses` a `possibleOutboundIpAddresses`. 

Sada `outboundIpAddresses` je momentálně dostupný pro aplikace function app. Sada `possibleOutboundIpAddresses` obsahuje IP adresy, které bude k dispozici pouze tehdy, pokud aplikace function app [škáluje, aby jiné cenové úrovně](#outbound-ip-address-changes).

Najít odchozí IP adresy k dispozici alternativní způsob je pomocí [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="data-center-outbound-ip-addresses"></a>Datové centrum odchozí IP adresy

Pokud je potřeba povolit odchozí IP adresy používané vaší aplikace function App, Další možností je přidat na seznam povolených aplikací function App datového centra (oblast Azure). Je možné [stáhnout soubor XML, který zobrazí seznam IP adres pro všechny datových center Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Vyhledejte element XML, který se vztahuje na oblasti, která spustí vaši aplikaci function app v.

Například je element západní Evropa XML může vypadat:

```
  <Region Name="europewest">
    <IpRange Subnet="13.69.0.0/17" />
    <IpRange Subnet="13.73.128.0/18" />
    <!-- Some IP addresses not shown here -->
    <IpRange Subnet="213.199.180.192/27" />
    <IpRange Subnet="213.199.183.0/24" />
  </Region>
```

 Informace o při aktualizaci tohoto souboru a při změně IP adres, rozbalte **podrobnosti** část [webu služby Stažení softwaru](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="inbound-ip-address-changes"></a>Příchozí změny IP adresy

 Příchozí IP adresu **může** změnit, když jste:

- Aplikace function app odstranit a znovu ho vytvořte v jiné skupině prostředků.
- Odstranit poslední aplikace function app v kombinaci prostředků skupiny a oblast a znovu vytvořit.
- Odstranění vazby SSL, například během [prodloužení platnosti certifikátu](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

Příchozí IP adresa může po ještě nevstoupilo všechny akce, například těch, které jsou uvedené také změnit.

## <a name="outbound-ip-address-changes"></a>Změny odchozí IP adresy

Sada k dispozici odchozí IP adresy pro aplikaci function app může změnit, když jste:

* Provést žádnou akci, která může měnit příchozí IP adresu.
* Změňte plán služby App Service s cenovou úroveň. Seznam všech možných odchozí IP adresy vaší aplikace můžete použít pro všechny cenové úrovně, `possibleOutboundIPAddresses` vlastnost. Zobrazit [najít odchozí IP adresy](#find-outbound-ip-addresses).

Příchozí IP adresa může po ještě nevstoupilo všechny akce, například těch, které jsou uvedené také změnit.

Chcete-li vynutit záměrně změnu odchozí IP adresy:

1. Plán služby App Service horizontální i vertikální škálování mezi Standard a Premium v2 cenové úrovně.
2. Počkejte 10 minut.
3. Zpět na škálování, ve kterém jste spustili.

## <a name="ip-address-restrictions"></a>Omezení podle IP adresy

Můžete nakonfigurovat seznam IP adres, které chcete povolit nebo odepřít přístup k aplikaci function app. Další informace najdete v tématu [Azure App Service statické omezení IP adres](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Vyhrazené IP adresy

Pokud potřebujete statickou a vyhrazená IP adresa, doporučujeme [App Service Environment](../app-service/environment/intro.md) ( [izolované úroveň](https://azure.microsoft.com/pricing/details/app-service/) plánů služby App Service). Další informace najdete v tématu [App Service prostředí IP adresy](../app-service/environment/network-info.md#ase-ip-addresses) a [řízení příchozího provozu do služby App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Chcete-li zjistit, pokud vaše function app běží ve službě App Service Environment:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do aplikace function app.
3. Vyberte **přehled** kartu.
4. Úroveň plánu služby App Service se zobrazí v části **plán/cenová úroveň služby App Service**. Cenová úroveň služby App Service Environment je **izolované**.
 
Jako alternativu můžete použít [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Služba App Service Environment `sku` je `Isolated`.

## <a name="next-steps"></a>Další postup

Běžnou příčinou změny IP se změny škálovací funkce aplikace. [Další informace o škálování pro funkce aplikace](functions-scale.md).
