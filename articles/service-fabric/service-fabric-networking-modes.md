---
title: Konfigurace síťové režimy kontejneru služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nastavit jiné síťové režimy, které jsou podporovány službou Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: twhitney, subramar
ms.openlocfilehash: 62812dd8f92bcace8f764a21aba608157815cec3
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093141"
---
# <a name="service-fabric-container-networking-modes"></a>Síťové režimy kontejneru Service Fabric

Cluster Azure Service Fabric pro kontejner služby používá **nat** sítě režimu ve výchozím nastavení. Když se používá režim překladu adres více než jeden kontejner služba naslouchá na stejném portu, může dojít k chybám nasazení. Pro podporu více služeb kontejneru naslouchá na stejném portu, Service Fabric nabízí **otevřít** sítě režimu (verze 5.7 nebo novější). V režimu otevření má každá služba kontejneru interní, dynamickou IP adresu, která podporuje několik služeb, které naslouchá na stejném portu.  

Pokud máte jeden container service pomocí statické koncový bod v manifestu služby, můžete vytvořit a odstranit nových služeb pomocí otevřít režimu bez chyby nasazení. Stejný soubor docker-compose.yml také lze pomocí statické mapování portů nevytvářejte více služeb.

Pokud služby container service restartuje nebo přesune do jiného uzlu v clusteru, změní se IP adresa. Z tohoto důvodu nedoporučujeme používat IP adresu dynamicky přiřazeného ke zjišťování služby kontejneru. Zjišťování služby by měla sloužit pouze služba pojmenování Service Fabric nebo službu DNS. 

>[!WARNING]
>Azure umožňuje celkem 4 096 IP adresy na virtuální síť. Součet počtu uzlů a počet instancí služby kontejneru, (, které používají režim otevření) může mít maximálně 4 096 IP adres ve virtuální síti. Pro scénáře s vysokou hustotou doporučujeme síťový režim překladu adres.
>

## <a name="set-up-open-networking-mode"></a>Nastavit režim otevření sítě

1. Nastavení šablony Azure Resource Manageru. V **nastavení fabricSettings** oddílu prostředku clusteru povolit služby DNS a IP zprostředkovatele: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Nastavení profilu sítě část prostředku Škálovací sady virtuálních počítačů. To umožňuje více IP adres musí nakonfigurovat na každém uzlu clusteru. Následující příklad nastaví až 5 IP adres na jeden uzel clusteru Service Fabric s Windows nebo Linuxem. Může mít pět instancí služby naslouchání na portu na každém uzlu. Jak je znázorněno níže, aby bylo možné mít pět IP adres, které jsou přístupné z nástroje pro vyrovnávání zatížení Azure, zaregistrujte pět IP adres ve fondu adres back-endu nástroje pro vyrovnávání zatížení Azure.  Také je potřeba k hornímu okraji šablonu v sekci proměnných přidejte proměnné.

    Přidejte tuto část do proměnné:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Přidejte tuto část do prostředku Škálovací sady virtuálních počítačů:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. V případě clusterů Windows nastavte pravidlo skupiny zabezpečení sítě Azure (NSG), které se otevře port UDP/53 virtuální sítě s použitím následujících hodnot:

   |Nastavení |Value | |
   | --- | --- | --- |
   |Priorita |2000 | |
   |Name |Custom_Dns  | |
   |Zdroj |VirtualNetwork | |
   |Cíl | VirtualNetwork | |
   |Služba | DNS (UDP/53) | |
   |Akce | Povolit  | |
   | | |

4. Zadejte síťový režim v manifestu aplikace pro každou službu: `<NetworkConfig NetworkType="Open">`. **Otevřít** sítě výsledky režimu ve službě získat vyhrazenou IP adresu. Je-li režim není zadán, výchozí služba **nat** režimu. V následujícím příkladu manifestu `NodeContainerServicePackage1` a `NodeContainerServicePackage2` služby může každý vlastností listenurimode nastavenou na stejném portu (obě služby naslouchají na `Endpoint1`). Pokud je zadán režim otevření sítě, `PortBinding` nejde zadat konfigurace.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Můžete kombinovat a párovat síťové různé režimy napříč službami v rámci aplikace pro Windows cluster. Některé služby můžete použít režim otevření, zatímco v jiných režimu překladu adres. Pokud služba je nakonfigurovaná pro použití režimu nat, musí být jedinečný port, na kterém služba naslouchá.

    >[!NOTE]
    >Na clusterech s Linuxem není podporované kombinace síťové režimy pro různé služby. 
    >

5. Když **otevřít** je vybraný režim, **koncový bod** definice v manifestu služby by měly explicitně odkazovat na balíček kódu, který odpovídá ke koncovému bodu, i v případě, že balíček služby má pouze jeden kód balíček v ní. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Pro Windows způsobí restartování virtuálního počítače, otevřete sítě znovu vytvořit. Toto je základní problém v sadu síťových protokolů se dá zmírnit. Výchozí chování je znovu vytvořit v síti. Pokud toto chování vypnout, následující konfiguraci je možné za nímž následuje konfigurace upgradu.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Další postup
* [Pochopení aplikačního modelu služby Service Fabric](service-fabric-application-model.md)
* [Další informace o prostředky manifestu služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Nasazení kontejneru Windows do Service Fabric na Windows serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru pro Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md)
