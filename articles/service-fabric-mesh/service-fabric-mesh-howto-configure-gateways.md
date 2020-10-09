---
title: Konfigurace brány na požadavky směrování
description: Zjistěte, jak nakonfigurovat bránu, která zpracovává příchozí provoz pro vaše aplikace běžící na Service Fabric síti.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: aa3ac9d8835cd17387346bb29b3e7c30f286cd1f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839720"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurace prostředku brány na požadavky směrování

Prostředek brány se používá ke směrování příchozího provozu do sítě, ve které je umístěná vaše aplikace. Nakonfigurujte ji tak, aby určovala pravidla, pomocí kterých se požadavky přesměrují na konkrétní služby nebo koncové body na základě struktury žádosti. Další informace o sítích a branách v síti najdete v tématu [Úvod do sítě v Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md) . 

Prostředky brány musí být deklarovány jako součást šablony nasazení (JSON nebo YAML) a jsou závislé na síťovém prostředku. Tento dokument popisuje různé vlastnosti, které lze nastavit pro bránu, a popisuje vzorovou konfiguraci brány.

## <a name="options-for-configuring-your-gateway-resource"></a>Možnosti konfigurace prostředku brány

Vzhledem k tomu, že prostředek brány slouží jako most mezi sítí vaší aplikace a sítí základní infrastruktury ( `open` sítě). Měli byste jenom nakonfigurovat jenom jednu bránu (ve verzi Preview, což je limit jedné brány na aplikaci). Deklarace prostředku se skládá ze dvou hlavních částí: metadata prostředku a vlastnosti. 

### <a name="gateway-resource-metadata"></a>Metadata prostředku brány

Brána je deklarována s následujícími metadaty:
* `apiVersion` – musí být nastavené na "2018-09-01-Preview" (nebo novější), a to v budoucnu.
* `name` – název řetězce pro tuto bránu
* `type` – "Microsoft. ServiceFabricMesh/gateways"
* `location` – mělo by být nastavené na umístění vaší aplikace nebo sítě; obvykle bude odkaz na parametr Location v nasazení
* `dependsOn` – síť, pro kterou bude tato brána sloužit jako vstupní bod pro

Tady je postup, jak vypadá v šabloně nasazení Azure Resource Manager (JSON): 

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

Oddíl Properties (vlastnosti) slouží k definování sítí, mezi kterými brána leží, a pravidel pro požadavky směrování. 

#### <a name="source-and-destination-network"></a>Zdrojová a cílová síť 

Každá brána vyžaduje `sourceNetwork` a `destinationNetwork` . Zdrojová síť je definovaná jako síť, ze které vaše aplikace bude přijímat příchozí požadavky. Vlastnost Name by měla být vždycky nastavená na Open (otevřít). Cílová síť je síť, na kterou požadavky cílí. Hodnota tohoto názvu by měla být nastavená na název prostředku místní sítě vaší aplikace (musí obsahovat úplný odkaz na prostředek). Níže najdete ukázku konfigurace toho, co vypadá jako nasazení v síti s názvem "myNetwork".

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

Brána může mít několik pravidel směrování, která určují, jak se budou zpracovávat příchozí přenosy. Pravidlo směrování definuje vztah mezi naslouchajícím portem a cílovým koncovým bodem pro danou aplikaci. Pro pravidla směrování protokolu TCP existuje mapování 1:1 mezi portem: koncový bod. Pro pravidla směrování protokolu HTTP můžete nastavit složitější pravidla směrování, která prozkoumají cestu k žádosti a volitelně hlavičky pro rozhodování, jak bude požadavek směrován. 

Pravidla směrování se zadává na základě jednotlivých portů. Každý port příchozího přenosu dat má vlastní pole pravidel v části vlastnosti konfigurace brány. 

#### <a name="tcp-routing-rules"></a>Pravidla směrování TCP 

Pravidlo směrování TCP se skládá z následujících vlastností: 
* `name` – odkaz na pravidlo, které může být libovolný řetězec podle vašeho výběru 
* `port` – port, na kterém se má naslouchat příchozím žádostem 
* `destination` – specifikace koncového bodu, která zahrnuje `applicationName` , `serviceName` a `endpointName` , kde je potřeba směrovat požadavky na

Tady je příklad pravidla směrování TCP:

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
* `port` – port, na kterém se má naslouchat příchozím žádostem 
* `hosts` – pole zásad, které se vztahují na požadavky přicházející do různých hostitelů na výše uvedeném portu. Hostitelé jsou sada aplikací a služeb, které mohou být spuštěny v síti a mohou obsluhovat příchozí požadavky, tj. webovou aplikaci. Zásady hostitele jsou interpretovány v pořadí, takže byste měli vytvořit následující v sestupných úrovních specifičnosti.
    * `name` – název DNS hostitele, pro který jsou zadána následující pravidla směrování. Pomocí tohoto postupu můžete vytvořit pravidla směrování pro všechny hostitele.
    * `routes` – pole zásad pro tohoto konkrétního hostitele
        * `match` – specifikace příchozí struktury požadavků pro toto pravidlo, které se má použít, na základě `path`
            * `path` -obsahuje `value` (příchozí identifikátor URI) `rewrite` (jak chcete přesměrovat požadavek) a `type` (může aktuálně být jenom "prefix").
            * `header` – je volitelná hodnota pole hlaviček, která se má shodovat s hlavičkou požadavku, pokud požadavek odpovídá specifikaci cesty (výše).
              * Každá položka obsahuje `name` (název řetězce záhlaví, který se má shodovat), `value` (Řetězcová hodnota hlavičky v žádosti) a a `type` (aktuálně může být pouze "přesný").
        * `destination` – Pokud požadavek odpovídá, bude směrován do tohoto cíle, který je určen pomocí `applicationName` , `serviceName` a. `endpointName`

Tady je příklad pravidla směrování protokolu HTTP, které se vztahuje na žádosti přicházející na port 80 na všechny hostitele obsluhované aplikacemi v této síti. Má-li adresa URL požadavku strukturu, která odpovídá specifikaci cesty, tj., `<IPAddress>:80/pickme/<requestContent>` pak bude přesměrována do `myListener` koncového bodu.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Ukázková konfigurace prostředku brány 

Tady je postup, jak vypadá úplná konfigurace prostředku brány (Tato akce je přizpůsobená z ukázky příchozího přenosu dat v [úložišti vzorkování sítě](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

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

Tato brána je nakonfigurovaná pro aplikaci pro Linux, "meshAppLinux", která se skládá z aspoň dvou služeb, "helloWorldService" a "counterService", která naslouchá na portu 80. V závislosti na struktuře adresy URL příchozího požadavku bude požadavek směrovat na jednu z těchto služeb. 
* " \<IPAddress> : 80/helloWorld/ \<request\> " by způsobil přesměrování požadavku na "helloWorldListener" v helloWorldService. 
* " \<IPAddress> : 80/Counter/ \<request\> " by způsobilo, že bude požadavek směrován na "counterListener" v counterService. 

## <a name="next-steps"></a>Další kroky
* Nasazením [ukázky](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) příchozího přenosu dat zobrazíte brány v akci.
