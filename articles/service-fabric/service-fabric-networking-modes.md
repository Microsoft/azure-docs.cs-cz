---
title: Konfigurace sítě režimy služby Azure Service Fabric kontejneru | Microsoft Docs
description: Zjistěte, jak nastavit různé režimy sítě, které podporuje Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f831c046bcf8f633841f9dc4a0fce6d1e419e6c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-container-networking-modes"></a>Režimy sítě kontejneru Service Fabric

Cluster služby Azure Service Fabric pro kontejner služeb používá **nat** sítě režimu ve výchozím nastavení. Když více než jedna služba kontejneru naslouchá na stejném portu a nat režim je používán, může dojít k chybám nasazení. Pro podporu více služby kontejneru naslouchá na stejném portu, Service Fabric nabízí **otevřete** sítě režimu (verze 5.7 a novější). V otevřené režim má každý kontejner služby interní, dynamicky přiřadit IP adresu, která podporuje více služeb naslouchá na stejném portu.  

Pokud máte jeden kontejner služby s statického koncového bodu v manifestu služby, můžete vytvořit a odstranit pomocí režim otevření bez chyby nasazení nové služby. Stejný soubor docker-compose.yml lze také u mapování statický port pro vytvoření více služeb.

Pokud službu kontejneru restartuje nebo přesune do jiného uzlu v clusteru, změní IP adresu. Z tohoto důvodu není doporučeno použít ke zjištění služby kontejneru dynamicky přiřazenou IP adresu. Pro zjišťování služby by měl použít pouze služby prostředků infrastruktury služby DNS nebo služba DNS. 

>[!WARNING]
>Azure umožňuje celkem 4 096 IP adres na jednu virtuální síť. Součet počet uzlů a počet instancí kontejneru služby, (které používají režim otevření) nesmí přesáhnout 4 096 IP adresy v rámci virtuální sítě. Pro scénáře s vysokou hustotou doporučujeme nat sítě režimu.
>

## <a name="set-up-open-networking-mode"></a>Nastavit režim otevření sítě

1. Nastavte šablony Azure Resource Manageru. V **fabricSettings** povolte službu DNS a IP zprostředkovatele: 

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

2. Nastavte části profil sítě a povolit více IP adres nakonfigurovat na každém uzlu clusteru. Následující příklad nastaví pět IP adres na uzel pro cluster Service Fabric Windows nebo Linuxem. Můžete mít pět instancí služby naslouchání na portu na každém uzlu.

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
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
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
 
3. Pro clustery Windows nastavte pravidlo skupina zabezpečení sítě Azure (NSG), které se otevře port UDP/53 pro virtuální síť s následujícími hodnotami:

   |Nastavení |Hodnota | |
   | --- | --- | --- |
   |Priorita |2000 | |
   |Název |Custom_Dns  | |
   |Zdroj |VirtualNetwork | |
   |Cíl | VirtualNetwork | |
   |Služba | DNS (UDP/53) | |
   |Akce | Povolit  | |
   | | |

4. Zadejte režim sítě v manifestu aplikace pro každou službu: `<NetworkConfig NetworkType="Open">`. **Otevřete** sítě režimu výsledky v rámci služby získávání vyhrazenou IP adresu. Pokud není zadán režim, službu výchozí **nat** režimu. V následujícím příkladu manifestu `NodeContainerServicePackage1` a `NodeContainerServicePackage2` služby můžete každý naslouchání na stejném portu (obě služby jsou naslouchá na `Endpoint1`). Pokud je zadána režim otevření sítí, `PortBinding` konfigurace nelze zadat.

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

    Můžete kombinovat a párovat síťové různé režimy ve službách v rámci aplikace pro Windows cluster. Některé služby můžete použít režim otevření, jiné zase nat režimu. Pokud služba je nakonfigurována pro použití režimu nat, port, který služba naslouchá na musí být jedinečný.

    >[!NOTE]
    >Kombinování sítě režimy pro různé služby se nepodporuje v clusterech Linux. 
    >

5. Když **otevřete** režimu je vybraná, **koncový bod** definice v service manifest by měla explicitně odkazovat na odpovídající ke koncovému bodu, balíček kódu i v případě, že balíček služby má jenom jeden kód balíček v ní. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```

## <a name="next-steps"></a>Další postup
* [Pochopení aplikačního modelu služby Service Fabric](service-fabric-application-model.md)
* [Další informace o prostředky manifestu služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Nasazení kontejneru systému Windows pro Service Fabric na Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasadit kontejner Docker do Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)
