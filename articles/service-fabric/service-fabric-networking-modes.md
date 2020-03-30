---
title: Konfigurace síťových režimů pro kontejnerové služby
description: Zjistěte, jak nastavit různé síťové režimy, které jsou podporované Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639798"
---
# <a name="service-fabric-container-networking-modes"></a>Síťové režimy kontejnerů Service Fabric

Cluster Azure Service Fabric pro kontejnerové služby používá ve výchozím nastavení síťový režim **nat.** Pokud více než jeden kontejner služba naslouchá na stejném portu a nat režim se používá, může dojít k chybám nasazení. Pro podporu více kontejnerových služeb naslouchání na stejném portu nabízí Service Fabric **režim Open** networking (verze 5.7 a novější). V režimu Otevřít má každá služba kontejneru interní dynamicky přiřazenou adresu IP, která podporuje více služeb naslouchání na stejném portu.  

Pokud máte jednu službu kontejneru se statickým koncovým bodem v manifestu služby, můžete vytvořit a odstranit nové služby pomocí režimu Open bez chyb nasazení. Stejný soubor docker-compose.yml lze také použít s mapování statickým portem k vytvoření více služeb.

Když se služba kontejneru restartuje nebo přesune do jiného uzlu v clusteru, změní se adresa IP. Z tohoto důvodu nedoporučujeme používat dynamicky přiřazenou IP adresu ke zjišťování kontejnerových služeb. Pro zjišťování služby by měla být použita pouze služba Service Fabric Naming Service. 

>[!WARNING]
>Azure umožňuje celkem 65 356 IP služeb na virtuální síť. Součet počtu uzlů a počtu instancí služby kontejneru (které používají režim Otevřít) nesmí překročit 65 356 IP adresy v rámci virtuální sítě. Pro scénáře s vysokou hustotou doporučujeme režim sítě nat. Kromě toho další závislosti, jako je například vyrovnávání zatížení bude mít další [omezení, aby zvážila.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) V současné době bylo testováno až 50 IP adresy na uzel a ukázalo se, že je stabilní. 
>

## <a name="set-up-open-networking-mode"></a>Nastavení režimu otevřené sítě

1. Nastavte šablonu Azure Resource Manager. V části **fabricSettings** prostředku clusteru povolte službu DNS a zprostředkovatele IP: 

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
    
2. Nastavte část profilu sítě prostředku škálovací sady virtuálních strojů. To umožňuje nakonfigurovat více adres IP v každém uzlu clusteru. Následující příklad nastaví pět IP adres na uzel pro cluster Windows/Linux Service Fabric. Můžete mít pět instancí služby naslouchání na portu na každém uzlu. Chcete-li mít pět IP adres přístupné z Azure BalanceR, zaregistrujte pět IP adres ve fondu back-end adres Azure Balancer, jak je znázorněno níže.  Proměnné budete také muset přidat do horní části šablony v části proměnné.

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
    
    Přidejte tuto část do prostředku škálovací sady virtuálních strojů:

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
 
3. Pouze pro clustery Windows nastavte pravidlo skupiny zabezpečení sítě Azure (NSG), které otevře port UDP/53 pro virtuální síť s následujícími hodnotami:

   |Nastavení |Hodnota | |
   | --- | --- | --- |
   |Priorita |2000 | |
   |Name (Název) |Custom_Dns  | |
   |Zdroj |VirtualNetwork | |
   |Cíl | VirtualNetwork | |
   |Služba | DNS (UDP/53) | |
   |Akce | Povolit  | |
   | | |

4. Zadejte síťový režim v manifestu `<NetworkConfig NetworkType="Open">`aplikace pro každou službu: . **Otevřený** síťový režim má za následek, že služba získá vyhrazenou adresu IP. Pokud režim není zadán, služba je výchozí **pro režim nat.** V následujícím příkladu `NodeContainerServicePackage1` manifestu a `NodeContainerServicePackage2` služby mohou každý poslouchat na `Endpoint1`stejném portu (obě služby jsou naslouchání na ). Pokud je zadán režim `PortBinding` Otevřené sítě, nelze zadat konfigurace.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

    Můžete kombinovat různé síťové režimy napříč službami v rámci aplikace pro cluster Windows. Některé služby mohou používat režim Open, zatímco jiné používají režim nat. Pokud je služba nakonfigurována pro použití režimu nat, musí být port, na který služba naslouchá, jedinečný.

    >[!NOTE]
    >V clusterech Linux není podporováno směšování síťových režimů pro různé služby. 
    >

5. Když je vybrán režim **Otevřít,** definice **koncového bodu** v manifestu služby by měla explicitně přejděte na balíček kódu odpovídající koncovému bodu, i když balíček služby má pouze jeden balíček kódu v něm. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. V případě Windows restartování virtuálního počítače způsobí, že otevřené sítě znovu vytvořit. Toto je zmírnit základní problém v síťovézásobníku. Výchozí chování je znovu vytvořit síť. Pokud je třeba toto chování vypnout, lze použít následující konfiguraci následovanou upgradem konfigurace.

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
 
## <a name="next-steps"></a>Další kroky
* [Pochopení aplikačního modelu služby Service Fabric](service-fabric-application-model.md)
* [Další informace o prostředcích manifestu služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Nasazení kontejneru Windows do service fabricu na Windows Serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru do Service Fabric na Linuxu](service-fabric-get-started-containers-linux.md)
