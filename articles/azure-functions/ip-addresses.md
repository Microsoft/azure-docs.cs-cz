---
title: IP adresy v Azure Functions
description: Přečtěte si, jak najít příchozí a odchozí IP adresy pro aplikace Function App a jaké způsobují jejich změnu.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 291a1cda7b8a2acc5426ea255519e1c2e58a2d7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936715"
---
# <a name="ip-addresses-in-azure-functions"></a>IP adresy v Azure Functions

Tento článek vysvětluje následující témata týkající se IP adres aplikací Function App:

* Jak najít IP adresy, které aktuálně používá aplikace Function App.
* Co způsobí změnu IP adres aplikace Function App.
* Jak omezit IP adresy, které mají přístup k aplikaci Function App.
* Jak získat vyhrazené IP adresy pro aplikaci Function App.

IP adresy jsou spojené s aplikacemi Function App, ne s jednotlivými funkcemi. Příchozí požadavky HTTP nemohou použít příchozí IP adresu pro volání jednotlivých funkcí; musí používat výchozí název domény (functionappname.azurewebsites.net) nebo vlastní název domény.

## <a name="function-app-inbound-ip-address"></a>Příchozí IP adresa aplikace Function App

Každá aplikace Function App má jednu příchozí IP adresu. Zjištění této IP adresy:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do aplikace Function App.
3. Vyberte **funkce platformy**.
4. Vyberte **vlastnosti** a v části **virtuální IP adresa** se zobrazí příchozí IP adresa.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Odchozí IP adresy aplikace Function App

Každá aplikace Function App má sadu dostupných odchozích IP adres. Jakékoli odchozí připojení z funkce, jako je například databáze back-end, používá jako zdrojovou IP adresu jednu z dostupných odchozích IP adres. Nemůžete předem znát, která IP adresa dané připojení bude používat. Z tohoto důvodu musí vaše back-end služba otevřít bránu firewall pro všechny odchozí IP adresy aplikace Function App.

Vyhledání odchozích IP adres dostupných pro aplikaci Function App:

1. Přihlaste se k [Azure Resource Explorer](https://resources.azure.com).
2. Vyberte **předplatná > {vaše předplatné} > poskytovatelé > weby Microsoft. Web >**.
3. Na panelu JSON Najděte lokalitu s `id` vlastností, která končí názvem vaší aplikace Function App.
4. Viz `outboundIpAddresses` a `possibleOutboundIpAddresses` . 

Sada `outboundIpAddresses` je aktuálně dostupná pro aplikaci Function App. Sada `possibleOutboundIpAddresses` zahrnuje IP adresy, které budou dostupné jenom v případě, že se aplikace funkcí [škáluje na jiné cenové úrovně](#outbound-ip-address-changes).

K dispozici je alternativní způsob, jak najít dostupné odchozí IP adresy pomocí [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Když se škáluje aplikace Function App, která běží na [plánu spotřeby](consumption-plan.md) nebo [plánu Premium](functions-premium-plan.md) , může se přiřadit nový rozsah odchozích IP adres. Při spuštění v některé z těchto plánů může být nutné přidat celé datové centrum do seznamu povolených.

## <a name="data-center-outbound-ip-addresses"></a>Odchozí IP adresy datového centra

Pokud potřebujete přidat odchozí IP adresy používané vašimi aplikacemi Function App do seznamu povolených aplikací, je další možností přidání datového centra aplikace Function App (oblast Azure) do seznamu povolených. Můžete [si stáhnout soubor JSON se seznamem IP adres pro všechna datová centra Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Pak najděte fragment JSON, který se vztahuje na oblast, ve které běží vaše aplikace Function App.

Například to vypadá, že fragment JSON západní Evropa může vypadat takto:

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

 Informace o tom, kdy se tento soubor aktualizuje a kdy se změní IP adresy, najdete v části **Podrobnosti** [stránky Stažení softwaru](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Změny příchozích IP adres

Příchozí IP adresa se **může** změnit, když:

- Odstraňte aplikaci funkcí a znovu ji vytvořte v jiné skupině prostředků.
- Odstraňte poslední aplikaci Function App v kombinaci skupiny prostředků a oblasti a vytvořte ji znovu.
- Odstraňte vazbu TLS, například během [obnovování certifikátu](../app-service/configure-ssl-certificate.md#renew-certificate).

Když vaše aplikace Function App běží v [plánu spotřeby](consumption-plan.md) nebo v [plánu Premium](functions-premium-plan.md), může se příchozí IP adresa změnit i v případě, že jste neudělali žádné akce, jako jsou třeba [výše uvedené](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Změny odchozích IP adres

Sada dostupných odchozích IP adres pro aplikaci Function App se může změnit v těchto případech:

* Proveďte jakoukoli akci, která může změnit příchozí IP adresu.
* Změňte cenovou úroveň plánu App Service. Seznam všech možných odchozích IP adres, které vaše aplikace může používat, je pro všechny cenové úrovně ve `possibleOutboundIPAddresses` Vlastnosti. Viz [Najít odchozí IP adresy](#find-outbound-ip-addresses).

Když vaše aplikace Function App běží v [plánu spotřeby](consumption-plan.md) nebo v [plánu Premium](functions-premium-plan.md), může se odchozí IP adresa změnit i v případě, že jste neudělali žádné akce, jako jsou třeba [výše uvedené](#inbound-ip-address-changes).

K úmyslnému vynucení změny odchozí IP adresy:

1. Škálujte App Service Naplánujte směrem nahoru nebo dolů mezi cenovými úrovněmi Standard a Premium v2.
2. Počkejte 10 minut.
3. Škálujte zpátky na místo, kde jste začali.

## <a name="ip-address-restrictions"></a>omezení IP adresy

Můžete nakonfigurovat seznam IP adres, u kterých chcete povolit nebo odepřít přístup k aplikaci Function App. Další informace najdete v tématu [Azure App Service omezení statických IP adres](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Vyhrazené IP adresy

Pokud potřebujete statické a vyhrazené IP adresy, doporučujeme [App Service prostředí](../app-service/environment/intro.md) ( [izolovaná úroveň](https://azure.microsoft.com/pricing/details/app-service/) App Service plánů). Další informace najdete v tématu [App Service Environment IP adresy](../app-service/environment/network-info.md#ase-ip-addresses) a [postup řízení příchozího provozu do App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Pokud chcete zjistit, jestli vaše aplikace Function App běží na App Service Environment:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do aplikace Function App.
3. Vyberte kartu **Přehled**.
4. Úroveň plánu App Service se zobrazí v části **App Service plán/cenová úroveň**. Cenová úroveň App Service Environment je **izolovaná**.
 
Alternativně můžete použít [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Environment `sku` je `Isolated` .

## <a name="next-steps"></a>Další kroky

Běžnou příčinou změn IP adres je změna měřítka aplikace Function App. [Přečtěte si další informace o škálování aplikace Function App](functions-scale.md).
