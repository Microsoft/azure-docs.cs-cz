---
title: Načtení metadat služby Vyrovnávání zatížení pomocí Instance Metadata Service Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Začněte s učením, jak načíst metadata služby Vyrovnávání zatížení pomocí Instance Metadata Service Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739776"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Načtení metadat služby Vyrovnávání zatížení pomocí Instance Metadata Service Azure (IMDS)

## <a name="prerequisites"></a>Předpoklady

* Pro svůj požadavek použijte [nejnovější verzi rozhraní API](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) .

## <a name="sample-request-and-response"></a>Ukázková žádost a odpověď
> [!IMPORTANT]
> Tento příklad obchází proxy servery. Při dotazování na IMDS je **nutné** obejít proxy servery. Další informace najdete v tématu [proxy](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> Parametr-bez proxy byl představen v PowerShellu 6,0. Pokud používáte starší verzi PowerShellu, v textu žádosti odeberte-bez proxy serveru a ujistěte se, že při načítání IMDS informací nepoužíváte proxy server. Další informace najdete [tady](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Ukázková odpověď

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Další kroky
[Běžné kódy chyb a postup řešení potíží](troubleshoot-load-balancer-imds.md)

Další informace o [Azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Načíst všechna metadata pro instanci](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Nasazení standardního nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-portal.md)

