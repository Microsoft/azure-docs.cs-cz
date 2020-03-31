---
title: IP adresy ve funkcích Azure
description: Přečtěte si, jak najít příchozí a odchozí IP adresy pro funkční aplikace a co způsobí jejich změnu.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a1c4174b8f1f2349cbd35c32cbee468ee5b4cd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276488"
---
# <a name="ip-addresses-in-azure-functions"></a>IP adresy ve funkcích Azure

Tento článek vysvětluje následující témata týkající se IP adres funkčních aplikací:

* Jak najít IP adresy, které právě používá aplikace funkce.
* Co způsobí, že ip adresy aplikace funkce se změní.
* Jak omezit IP adresy, které mají přístup k aplikaci funkce.
* Jak získat vyhrazené IP adresy pro funkční aplikaci.

IP adresy jsou spojeny s funkčními aplikacemi, ne s jednotlivými funkcemi. Příchozí požadavky HTTP nemohou používat příchozí IP adresu k volání jednotlivých funkcí. musí používat výchozí název domény (functionappname.azurewebsites.net) nebo vlastní název domény.

## <a name="function-app-inbound-ip-address"></a>Vstupní IP adresa aplikace pro funkci

Každá aplikace funkce má jednu příchozí IP adresu. Chcete-li najít tuto IP adresu:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte do aplikace funkce.
3. Vyberte **Funkce platformy**.
4. Vyberte **Vlastnosti**a příchozí adresa IP se zobrazí v části **Virtuální ADRESA IP**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Odchozí IP adresy aplikace pro funkce

Každá aplikace funkce má sadu dostupných odchozích IP adres. Jakékoli odchozí připojení z funkce, například do back-endové databáze, používá jako původní IP adresu jednu z dostupných odchozích IP adres. Nemůžete předem vědět, kterou IP adresu dané připojení použije. Z tohoto důvodu musí služba back-endotevřít bránu firewall pro všechny odchozí IP adresy aplikace funkce.

Vyhledání odchozích IP adres dostupných pro funkční aplikaci:

1. Přihlaste se k [Průzkumníku prostředků Azure](https://resources.azure.com).
2. Vyberte **předplatná > {vaše předplatné} > zprostředkovatelé > webech microsoft.web >**.
3. V panelu JSON najděte `id` web s vlastností, která končí názvem aplikace funkce.
4. Zobrazit `outboundIpAddresses` `possibleOutboundIpAddresses`a . 

Sada aplikace `outboundIpAddresses` je v současné době k dispozici pro aplikaci funkce. Sada obsahuje `possibleOutboundIpAddresses` IP adresy, které budou k dispozici pouze v případě, že aplikace funkce [se škáluje na jiné cenové úrovně](#outbound-ip-address-changes).

Alternativní způsob, jak najít dostupné odchozí IP adresy, je pomocí [prostředí Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Při škálování aplikace funkce, která běží v [plánu Spotřeba,](functions-scale.md#consumption-plan) může být přiřazen a nový rozsah odchozích IP adres. Při spuštění v plánu Spotřeba může být nutné zařadit do seznamu celé ho datového centra.

## <a name="data-center-outbound-ip-addresses"></a>Odchozí IP adresy datového centra

Pokud potřebujete zařadit odchozí IP adresy používané vašimi funkčními aplikacemi, další možností je zařazení datového centra aplikací funkcí (oblast Azure). Můžete [si stáhnout soubor JSON se seznamem IP adres pro všechna datová centra Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Pak najděte fragment JSON, který se vztahuje k oblasti, ve které je spuštěna vaše aplikace funkce.

Například, to je to, co západní Evropa JSON fragment může vypadat:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Informace o tom, kdy je tento soubor aktualizován a kdy se změní adresy IP, získáte v části **Podrobnosti** na [stránce Centrum pro stahování](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Změny příchozí adresy IP

Příchozí adresa IP **se může** změnit, pokud:

- Odstraňte aplikaci funkcí a znovu ji vytvořte v jiné skupině prostředků.
- Odstraňte poslední aplikaci funkcí v kombinaci skupiny prostředků a oblasti a znovu ji vytvořte.
- Odstraňte vazbu SSL, například během [obnovení certifikátu](../app-service/configure-ssl-certificate.md#renew-certificate).

Když vaše aplikace funkce běží v [plánu spotřeby](functions-scale.md#consumption-plan), příchozí IP adresa se může také změnit, i když jste neprovedli žádné akce, jako jsou ty [uvedené výše](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Změny odchozí ip adresy

Sada dostupných odchozích IP adres pro aplikaci funkcí se může změnit, když:

* Provádějte jakoukoli akci, která může změnit příchozí adresu IP.
* Změňte cenovou úroveň plánu služby App Service. Seznam všech možných odchozích IP adres, které vaše aplikace může používat `possibleOutboundIPAddresses` pro všechny cenové úrovně, je ve vlastnosti. Viz [Najít odchozí IP adresy](#find-outbound-ip-addresses).

Když vaše funkční aplikace běží v [plánu spotřeby](functions-scale.md#consumption-plan), může se odchozí IP adresa změnit i v případě, že jste neprovedli žádné akce, jako jsou ty, které [jsou uvedeny výše](#inbound-ip-address-changes).

Chcete-li záměrně vynutit změnu odchozí adresy IP:

1. Škálujte plán služby App Service mezi standardními a prémiovými úrovněmi v2 nahoru nebo dolů.
2. Počkejte 10 minut.
3. Vraťte se tam, kde jste začali.

## <a name="ip-address-restrictions"></a>omezení IP adresy

Můžete nakonfigurovat seznam adres IP, které chcete povolit nebo odepřít přístup k aplikaci funkce. Další informace najdete v [tématu Azure App Service Statická omezení IP](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Vyhrazené IP adresy

Pokud potřebujete statické, vyhrazené IP adresy, doporučujeme [prostředí služby App Service](../app-service/environment/intro.md) [(izolovaná úroveň](https://azure.microsoft.com/pricing/details/app-service/) plánů služby App Service). Další informace najdete v tématu [IP adresy prostředí služby App Service](../app-service/environment/network-info.md#ase-ip-addresses) a Jak řídit příchozí provoz do prostředí [služby App Service](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Jak zjistit, jestli vaše aplikace funkcí běží v prostředí služby App Service:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte do aplikace funkce.
3. Vyberte kartu **Přehled**.
4. Úroveň plánu služby App Service se zobrazí v části **Plán/cenová úroveň služby App Service**. Cenová úroveň Prostředí služby App Service je **izolovaná**.
 
Jako alternativu můžete použít [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Prostředí `sku` služby `Isolated`App Service je .

## <a name="next-steps"></a>Další kroky

Častou příčinou změn IP je změna měřítka aplikace funkce. [Přečtěte si další informace o škálování aplikací funkcí](functions-scale.md).
