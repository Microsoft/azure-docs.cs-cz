---
title: IP adresy služby Azure API Management | Dokumenty společnosti Microsoft
description: Zjistěte, jak načíst IP adresy služby Azure API Management a když se změní.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047745"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP adresy Azure API Management

V tomto článku popisujeme, jak načíst IP adresy služby Azure API Management. IP adresy mohou být veřejné nebo soukromé, pokud je služba ve virtuální síti.

Pomocí adres IP můžete vytvořit pravidla brány firewall, filtrovat příchozí provoz do back-endových služeb nebo omezit odchozí provoz.

## <a name="ip-addresses-of-api-management-service"></a>IP adresy služby API Management

Každá instance služby správy rozhraní API na úrovni Vývojář, Basic, Standard nebo Premium má veřejné IP adresy, které jsou výhradní pouze pro tuto instanci služby (nejsou sdíleny s jinými prostředky). 

IP adresy můžete načíst z přehledového řídicího panelu vašeho prostředku na webu Azure Portal.

![IP adresa pro správu rozhraní API](media/api-management-howto-ip-addresses/public-ip.png)

Můžete je také načíst programově pomocí následujícího volání rozhraní API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Součástí odpovědi budou veřejné IP adresy:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

V [nasazeních s více oblastmi](api-management-howto-deploy-multi-region.md)má každé regionální nasazení jednu veřejnou IP adresu.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP adresy služby API Management ve virtuální síti

Pokud je vaše služba api management uvnitř virtuální sítě, bude mít dva typy IP adres – veřejné a soukromé.

Veřejné IP adresy se používají pro `3443` interní komunikaci na portu – pro správu konfigurace (například prostřednictvím Správce prostředků Azure). V konfiguraci externí virtuální sítě se také používají pro provoz rozhraní API za běhu. Při odeslání požadavku ze správy rozhraní API do veřejného (internetového) back-endu bude veřejná IP adresa viditelná jako původ požadavku.

Privátní virtuální IP adresy (VIP), které jsou dostupné **jenom** v [režimu interní virtuální sítě](api-management-using-with-internal-vnet.md), se používají k připojení z rámci sítě ke koncovým bodům správy rozhraní API – brány, portál pro vývojáře a rovina správy pro přímý přístup k rozhraní API. Můžete je použít k nastavení záznamů DNS v síti.

Adresy obou typů se zobrazí na webu Azure Portal a v odpovědi na volání rozhraní API:

![Správa rozhraní API v IP adrese virtuální sítě](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

Správa rozhraní API používá veřejnou IP adresu pro připojení mimo virtuální síť a privátní IP adresu pro připojení v rámci virtuální sítě.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP adresy služby Správy rozhraní API úrovně spotřeby

Pokud je vaše služba api Management služba úroveň spotřeby, nemá vyhrazenou IP adresu. Služba úrovně spotřeby běží na sdílené infrastruktuře a bez deterministické IP adresy. 

Pro účely omezení provozu můžete použít rozsah IP adres datových center Azure. Přesné kroky najdete [v článku s dokumentací k funkcím Azure.](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)

## <a name="changes-to-the-ip-addresses"></a>Změny IP adres

V úrovních Developer, Basic, Standard a Premium pro správu rozhraní API jsou veřejné IP adresy (VIP) statické po celou dobu životnosti služby, s následujícími výjimkami:

* Služba je odstraněna a znovu vytvořena.
* Předplatné služby je [pozastaveno](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) nebo [upozorňováno](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (například pro neplacení) a poté obnoveno.
* Virtuální síť Azure se přidá do služby nebo se z ní obere.
* Služba API Management se přepíná mezi režimem externího a interního nasazení virtuální sítě.

V [nasazeních s více oblastmi](api-management-howto-deploy-multi-region.md)se regionální adresa IP změní, pokud je oblast uvolněna a obnovena.
