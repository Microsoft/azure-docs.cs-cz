---
title: IP adresy služby Azure API Management | Microsoft Docs
description: Naučte se, jak načíst IP adresy služby Azure API Management, a když se změní.
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
ms.openlocfilehash: 6e12a16b221a8f31d82a002fbc93b090d6f0c577
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861246"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP adresy API Management Azure

V tomto článku popisujeme, jak načíst IP adresy služby Azure API Management. Pokud je služba ve virtuální síti, může být IP adresa veřejná nebo soukromá.

Pomocí IP adres můžete vytvořit pravidla brány firewall, filtrovat příchozí provoz na back-end služby nebo omezit odchozí přenosy.

## <a name="ip-addresses-of-api-management-service"></a>IP adresy služby API Management

Každá API Management instance služby v úrovni Developer, Basic, Standard nebo Premium má veřejné IP adresy, které jsou výhradně k této instanci služby (nejsou sdíleny s jinými prostředky). 

IP adresy můžete načíst z řídicího panelu přehled prostředku v Azure Portal.

![API Management IP adresa](media/api-management-howto-ip-addresses/public-ip.png)

Můžete je také načíst programově pomocí následujícího volání rozhraní API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Veřejné IP adresy budou součástí odpovědi:

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

V případě [nasazení s více oblastmi](api-management-howto-deploy-multi-region.md)má každé regionální nasazení jednu veřejnou IP adresu.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP adresy služby API Management ve virtuální síti

Pokud je vaše služba API Management ve virtuální síti, bude mít dva typy IP adres – Public a Private.

Veřejné IP adresy se používají pro interní komunikaci na portu `3443` – pro správu konfigurace (například prostřednictvím Azure Resource Manager). Kromě toho platí, že když je žádost odeslána z API Management k veřejnému (internetovému) back-endu, jako zdroj žádosti se zobrazí veřejná IP adresa.

Privátní virtuální IP adresy (VIP) se používají pro připojení ze sítě k API Management koncových bodů – bran, portálu pro vývojáře a roviny správy pro přímý přístup k rozhraní API. Můžete je použít k nastavení záznamů DNS v síti.

V Azure Portal se zobrazí adresy obou typů a v reakci na volání rozhraní API:

![API Management ve virtuální IP adrese](media/api-management-howto-ip-addresses/vnet-ip.png)


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

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP adresy služby API Management úrovně spotřeby

Pokud vaše služba API Management je služba na úrovni spotřeby, nemá vyhrazenou IP adresu. Služba na úrovni spotřeby běží na sdílené infrastruktuře a bez deterministické IP adresy. 

Pro účely omezení provozu můžete použít rozsah IP adres datových center Azure. Přesný postup najdete [v článku dokumentace k Azure Functions](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Změny IP adres

V API Management úrovně pro vývojáře, Basic, Standard a Premium jsou veřejné IP adresy (VIP) pro celou dobu životnosti služby statické, s následujícími výjimkami:

* Služba se odstraní a pak znovu vytvoří.
* Předplatné služby je pozastavené nebo se [varuje](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (například pro nedoplatk) a pak se znovu obnoví. [](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)
* Služba Azure Virtual Network je do služby přidána nebo odebrána.
* Služba API Management se přepnula mezi režimem nasazení externí a interní virtuální sítě.

Při [nasazení s více oblastmi](api-management-howto-deploy-multi-region.md)se místní IP adresa změní, pokud je oblast uvolněné a pak se znovu obnoví.
