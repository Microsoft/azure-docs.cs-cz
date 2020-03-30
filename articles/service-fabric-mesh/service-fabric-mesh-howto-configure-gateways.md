---
title: Konfigurace brány pro směrování požadavků
description: Zjistěte, jak nakonfigurovat bránu, která zpracovává příchozí provozy pro vaše aplikace spuštěné v síti Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461964"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurace prostředku brány pro směrování požadavků

Prostředek brány se používá k směrování příchozích přenosů do sítě, ve které je umístěna vaše aplikace. Nakonfigurujte jej tak, aby určoval pravidla, jejichž prostřednictvím jsou požadavky směrovány na konkrétní služby nebo koncové body na základě struktury požadavku. Další informace o sítích a branách v síti Service Fabric najdete v části Úvod do sítí v síti Service [Fabric.](service-fabric-mesh-networks-and-gateways.md) 

Prostředky brány musí být deklarovány jako součást šablony nasazení (JSON nebo yaml) a jsou závislé na síťovém prostředku. Tento dokument popisuje různé vlastnosti, které lze nastavit pro bránu a zahrnuje konfiguraci ukázkové brány.

## <a name="options-for-configuring-your-gateway-resource"></a>Možnosti konfigurace prostředku brány

Vzhledem k tomu, že prostředek brány slouží jako most mezi sítí `open` vaší aplikace a sítí základní infrastruktury (sítě). Měli byste jenom nakonfigurovat (v náhledu sítě je limit jedné brány na aplikaci). Deklarace pro prostředek se skládá ze dvou hlavních částí: metadata prostředku a vlastnosti. 

### <a name="gateway-resource-metadata"></a>Metadata prostředku brány

Brána je deklarována s následujícími metadaty:
* `apiVersion`- musí být nastavena na "2018-09-01-preview" (nebo později, v budoucnu)
* `name`- název řetězce pro tuto bránu
* `type`- "Microsoft.ServiceFabricMesh/brány"
* `location`- by měla být nastavena na umístění vaší aplikace / sítě; obvykle bude odkaz na parametr umístění ve vašem nasazení
* `dependsOn`- síť, pro kterou bude tato brána sloužit jako vstupní bod pro

Tady je postup, jak vypadá v šabloně nasazení Správce prostředků Azure (JSON): 

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

Část vlastností se používá k definování sítí, mezi nimiž brána leží, a pravidel pro požadavky na směrování. 

#### <a name="source-and-destination-network"></a>Zdrojová a cílová síť 

Každá brána `sourceNetwork` `destinationNetwork`vyžaduje a . Zdrojová síť je definována jako síť, ze které bude aplikace přijímat příchozí požadavky. Jeho název vlastnost by měla být vždy nastavena na "Otevřít". Cílová síť je síť, na kterou požadavky cílí. Hodnota názvu pro to by měla být nastavena na název prostředku místní sítě vaší aplikace (by měl obsahovat úplný odkaz na prostředek). Viz níže ukázkový konfigurační soubor toho, jak to vypadá pro nasazení v síti s názvem "myNetwork".

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

Brána může mít více směrovacích pravidel, která určují, jak bude zpracován příchozí provoz. Pravidlo směrování definuje vztah mezi naslouchacím portem a cílovým koncovým bodem pro danou aplikaci. Pro pravidla směrování TCP existuje mapování 1:1 mezi portem:koncovýbod. Pro pravidla směrování HTTP můžete nastavit složitější pravidla směrování, která zkoumají cestu požadavku a volitelně záhlaví, abyste rozhodli, jak bude požadavek směrován. 

Pravidla směrování jsou určena pro jeden port. Každý vstupní port má vlastní pole pravidel v rámci části vlastností konfigurace brány. 

#### <a name="tcp-routing-rules"></a>Pravidla směrování tcp 

Pravidlo směrování TCP se skládá z následujících vlastností: 
* `name`- odkaz na pravidlo, které může být libovolný řetězec dle vašeho výběru 
* `port`- port poslouchat pro příchozí požadavky 
* `destination`- specifikace koncového `applicationName` `serviceName`bodu, `endpointName`která zahrnuje , a , pokud je třeba žádosti směrovat do

Zde je příklad pravidla směrování TCP:

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


#### <a name="http-routing-rules"></a>Pravidla směrování HTTP 

Pravidlo směrování HTTP se skládá z následujících vlastností: 
* `name`- odkaz na pravidlo, které může být libovolný řetězec dle vašeho výběru 
* `port`- port poslouchat pro příchozí požadavky 
* `hosts`- řadu zásad, které se vztahují na požadavky přicházející do různých "hostitelů" na výše uvedeném portu. Hostitelé jsou sada aplikací a služeb, které mohou být spuštěny v síti a mohou obsluhovat příchozí požadavky, tj. Zásady hostitele jsou interpretovány v pořadí, takže byste měli vytvořit následující v sestupných úrovních specifičnosti
    * `name`- název DNS hostitele, pro kterého jsou určena následující pravidla směrování. Použití "*" zde by vytvořilo pravidla směrování pro všechny hostitele.
    * `routes`- řada zásad pro tohoto konkrétního hostitele
        * `match`- specifikace struktury příchozích požadavků, aby se toto pravidlo použilo na základě`path`
            * `path`- obsahuje `value` (příchozí URI), `rewrite` (jak chcete, aby byl požadavek předán) a `type` (v současné době může být pouze "Prefix")
            * `header`- je volitelné pole hodnot záhlaví, které odpovídá v hlavičce požadavku, které pokud požadavek odpovídá specifikaci cesty (výše).
              * každá položka `name` obsahuje (název řetězce záhlaví `value` tak, aby odpovídala), (hodnota `type` řetězce záhlaví v požadavku) a (může být v současné době pouze "Exact")
        * `destination`- pokud se požadavek shoduje, bude směrován na toto `applicationName` `serviceName`místo určení, které je určeno pomocí ,`endpointName`

Zde je příklad pravidla směrování HTTP, které by se vztahovalo na požadavky přicházející na portu 80, na všechny hostitele obsluhované aplikacemi v této síti. Pokud adresa URL požadavku má strukturu, která odpovídá specifikaci cesty, `<IPAddress>:80/pickme/<requestContent>` `myListener` tj.  

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

Zde je to, co úplné gateway prostředek config vypadá (to je upravena z ingress vzorku k dispozici v [mesh vzorky repo](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

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

Tato brána je nakonfigurovánpro linuxovou aplikaci "meshAppLinux", která se skládá alespoň ze dvou služeb, "helloWorldService" a "counterService", který poslouchá na portu 80. V závislosti na struktuře adresy URL příchozí žádosti bude směrovat požadavek na jednu z těchto služeb. 
* "\<IPAddress>:80/helloWorld/\<request\>" by mělo za následek požadavek přesměrován na "helloWorldListener" v helloWorldService. 
* "\<IPAddress>:80/counter/\<request\>" by mělo za následek požadavek přesměrován na "counterListener" v counterService. 

## <a name="next-steps"></a>Další kroky
* Nasazení [ingress ukázky](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) zobrazíte brány v akci
