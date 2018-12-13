---
title: Konfigurace brány pro směrování požadavků | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat bránu, která zpracovává příchozí provoz pro vaše aplikace běží na služby prostředků infrastruktury sítě.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 928ba5074577fda05162dd4f113572fa66d7847f
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892168"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurace prostředku brány pro směrování požadavků

Prostředek brány se používá ke směrování příchozího provozu v síti, ke které jsou uloženy vaše aplikace. Nakonfigurujte lze určit pravidla, pomocí kterých jsou žádosti směrované na konkrétní služby nebo koncové body na základě struktury požadavku. Zobrazit [Úvod do vytváření sítí v Service Fabric mřížky](service-fabric-mesh-networks-and-gateways.md) Další informace o sítě a brány v síti. 

Prostředky brány musí být deklarován jako součást šablony nasazení (yaml nebo JSON) a jsou závislé na síťových prostředků. Tento dokument popisuje různé vlastnosti, které lze nastavit pro bránu a zahrnuje Ukázková konfigurace brány.

## <a name="options-for-configuring-your-gateway-resource"></a>Možnosti konfigurace vašeho prostředku brány

Vzhledem k tomu, že prostředek brány slouží jako most mezi vaší aplikací a základní infrastrukturu sítí ( `open` sítě). By měl pouze musíte nakonfigurovat jednu (ve verzi preview síť je omezena na jednu bránu na aplikaci). Deklarace pro prostředek se skládá ze dvou hlavních částí: metadat prostředků a vlastnosti. 

### <a name="gateway-resource-metadata"></a>Metadata prostředku brány

Brána je deklarována pomocí následující metadata:
* `apiVersion` – musí být nastavena na "2018-09-01-preview" (nebo později, v budoucnu)
* `name` -název řetězce pro tuto bránu
* `type` -"Microsoft.ServiceFabricMesh/gateways"
* `location` – musí být nastavena na umístění aplikace / network; obvykle bude odkaz na parametr umístění ve vašem nasazení
* `dependsOn` -sítě, pro kterou tato brána bude sloužit jako bod příchozího přenosu dat pro

Zde je, jak vypadá v šabloně nasazení Azure Resource Manageru (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [  
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Vlastnosti brány

Oddíl properties se používá k definování sítě, mezi kterými je brána a pravidla pro směrování požadavků. 

#### <a name="source-and-destination-network"></a>Zdrojové a cílové sítě 

Každá brána vyžaduje `sourceNetwork` a `destinationNetwork`. Zdrojová síť je definován jako sítě, ze kterého vaše aplikace bude přijímat příchozí požadavky. Vlastnost jeho název by měl být vždy nastavená na "Otevřít". Cílovou sítí je síť, která cílí na požadavky. Hodnota názvu pro tuto by měl nastavena na název prostředku aplikace místní sítě (úplný odkaz na prostředek by měl obsahovat). Ukázková konfigurace z jak to vypadá v síť s názvem "myNetwork" pro nasazení najdete níže.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Pravidla 

Brány můžou mít více směrování se zpracuje pravidla, které určují, jak příchozí provoz. Pravidlo směrování definuje vztahy mezi naslouchající port a cílový koncový bod pro danou aplikaci. Pravidla směrování TCP existuje mapování 1:1 mezi Port: koncový bod. Pro pravidla směrování protokolu HTTP můžete nastavit složitější pravidla směrování, zkontrolujte cestu požadavku, a volitelně záhlaví, a rozhodnout, jak se budou směrovat žádosti. 

Pravidla směrování zadávají se na základě na port. Každý z portů příchozího přenosu dat má svůj vlastní pole pravidla v rámci části vlastností v konfiguraci brány. 

#### <a name="tcp-routing-rules"></a>Pravidla směrování protokolu TCP 

Pravidlo směrování TCP se skládá z následujících vlastností: 
* `name` – odkaz na pravidlo, které může být libovolný řetězec podle vašeho výběru 
* `port` – port pro naslouchání pro příchozí požadavky 
* `destination` -koncový bod specifikace, která zahrnuje `applicationName`, `serviceName`, a `endpointName`, pro které je potřeba směrovat do žádosti

Tady je pravidlo směrování TCP příkladu:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Pravidla směrování protokolu HTTP 

Pravidlo směrování protokolu HTTP se skládá z následujících vlastností: 
* `name` – odkaz na pravidlo, které může být libovolný řetězec podle vašeho výběru 
* `port` – port pro naslouchání pro příchozí požadavky 
* `hosts` -pole zásad, které platí pro požadavky přicházející na portu výše uvedené různé "hostitelům". Hostitelé jsou sadu aplikací a služeb, které možná běží v síti a může sloužit příchozích požadavků, například webové aplikace. Zásady hostitele jsou interpretovány v pořadí, takže byste měli vytvořit následující sestupně podle úrovně přesnosti
    * `name` – název DNS hostitele, pro který jsou zadány následující pravidla směrování. Pomocí "*" zde bude vytvářet pravidla směrování pro všechny hostitele.
    * `routes` -pole zásad pro tento konkrétní hostitel
        * `match` – na základě specifikace struktury příchozí žádosti pro toto pravidlo použít, `path`
            * `path` -obsahuje `value` (příchozí identifikátor URI), `rewrite` (jak chcete žádost o předat dál) a `type` (aktuálně může být pouze "Předpony")
            * `header` -je volitelné pole hodnot záhlaví tak, aby odpovídaly v hlavičce požadavku, že pokud požadavek odpovídá specifikaci cesty (viz výše).
              * Každá položka obsahuje `name` (název hlavičky k porovnání shody řetězce) `value` (řetězcová hodnota záhlaví v žádosti) a `type` (aktuálně může být pouze "Stejné")
        * `destination` -Pokud požadavek odpovídá, budou směrovat do tohoto cílového místa, který je určen pomocí `applicationName`, `serviceName`, a `endpointName`

Tady je příklad HTTP pravidlo směrování pro požadavky přicházející na portu 80 a pro všechny hostitele obsluhuje aplikace v této síti. Pokud adresu URL požadavku obsahuje strukturu, která odpovídá specifikaci cesty, například `<IPAddress>:80/pickme/<requestContent>`, pak budete přesměrováni na `myListener` koncového bodu.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Ukázková konfigurace pro prostředek brány 

Tady je úplný konfiguraci prostředků brány, která bude vypadat jako (jde přizpůsobit z ukázky příchozího přenosu dat k dispozici v [Mesh vzorků úložiště](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{  
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [  
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {  
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {  
      "name": "Open"
    },
    "destinationNetwork": {  
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [  
      {  
        "name": "web",
        "port": 80,
        "hosts": [  
          {  
            "name": "*",
            "routes": [  
              {  
                "match": {  
                  "path": {  
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {  
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {  
                "match": {  
                  "path": {  
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {  
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Tato brána je nakonfigurovaná pro aplikaci systému Linux, "meshAppLinux", který se skládá z alespoň dvě služby, "helloWorldService" a "counterService", která naslouchá na portu 80. V závislosti na struktuře adresy URL příchozích požadavků se bude směrovat žádosti na jednu z těchto služeb. 
* "<IPAddress>: 80/helloWorld/\<požadavek\>" výsledkem by byla žádost o nasměrování na "helloWorldListener" v helloWorldService. 
* "<IPAddress>: 80/čítač/\<požadavek\>" výsledkem by byla žádost o nasměrování na "counterListener" v counterService. 

## <a name="next-steps"></a>Další postup
* Nasazení [příchozího přenosu dat ukázka](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) zobrazíte brány v akci
