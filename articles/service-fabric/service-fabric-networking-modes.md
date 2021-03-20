---
title: Konfigurace síťových režimů pro služby kontejneru
description: Naučte se nastavit různé síťové režimy, které Azure Service Fabric podporuje.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86241892"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric síťové režimy kontejneru

Cluster Azure Service Fabric pro služby kontejneru ve výchozím nastavení používá režim sítě **NAT** . Pokud se používá více než jedna služba kontejneru, která naslouchá na stejném portu a režim překladu adres (NAT), může dojít k chybám nasazení. Aby bylo možné podporovat více kontejnerových služeb naslouchácích na stejném portu, Service Fabric nabízí režim **otevřené** sítě (verze 5,7 a novější). V režimu Open má každá služba kontejneru interní, dynamicky přiřazenou IP adresu, která podporuje více služeb, které naslouchají na stejném portu.  

Pokud máte v manifestu služby jednu službu kontejneru se statickým koncovým bodem, můžete vytvořit a odstranit nové služby pomocí režimu otevření bez chyb nasazení. Stejný soubor Docker-Compose. yml lze také použít se statickým mapováním portů pro vytvoření více služeb.

Když se služba kontejneru restartuje nebo přesune na jiný uzel v clusteru, IP adresa se změní. Z tohoto důvodu nedoporučujeme používat dynamicky přiřazenou IP adresu ke zjišťování služeb kontejneru. Pro zjišťování služeb by se měla použít jenom Naming Service Service Fabric nebo služba DNS. 

>[!WARNING]
>Azure umožňuje celkem 65 356 IP adres na jednu virtuální síť. Součet počtu uzlů a počtu instancí služby kontejnerů (používajících režim otevření) nesmí v rámci virtuální sítě překročit 65 356 IP adres. U scénářů s vysokou hustotou doporučujeme použít režim sítě NAT. Další závislosti, jako je třeba nástroj pro vyrovnávání zatížení, budou mít i další [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md) , která je potřeba zvážit. V současné době až 50 IP adres na uzel byly testovány a prověřeny stabilní. 
>

## <a name="set-up-open-networking-mode"></a>Nastavení režimu otevřené sítě

1. Nastavte šablonu Azure Resource Manager. V části **fabricSettings** prostředku clusteru povolte službu DNS a poskytovatele IP adres: 

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
    
2. Nastavte oddíl profil sítě v prostředku sady škálování virtuálního počítače. To umožňuje nakonfigurovat více IP adres na každém uzlu clusteru. Následující příklad nastaví pět IP adres na uzel pro cluster Service Fabric systému Windows/Linux. Na portu na každém uzlu můžete mít naslouchat pět instancí služby. Aby bylo možné z Azure Load Balancer získat přístup k pěti IP adresám, zaregistrujte pět IP adres ve fondu back-end adres Azure Load Balancer, jak je uvedeno níže.  Také budete muset přidat proměnné do horní části šablony v části proměnné.

    Přidejte tuto část do proměnných:

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
    
    Přidejte tuto část do prostředku sady škálování virtuálních počítačů:

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
 
3. V případě clusterů Windows nastavte pravidlo skupiny zabezpečení sítě Azure (NSG), které otevírá port UDP/53 pro virtuální síť s následujícími hodnotami:

   |Nastavení |Hodnota |
   | --- | --- |
   |Priorita |2000 |
   |Name |Custom_Dns  |
   |Zdroj |VirtualNetwork |
   |Cíl | VirtualNetwork |
   |Služba | DNS (UDP/53) |
   |Akce | Povolit  |

4. Zadejte režim sítě v manifestu aplikace pro každou službu: `<NetworkConfig NetworkType="Open">` . V případě **otevřeného** síťového režimu dojde k získání vyhrazené IP adresy ve službě. Pokud není zadán režim, služba je výchozím režimem **překladu adres (NAT)** . V následujícím příkladu manifestu `NodeContainerServicePackage1` `NodeContainerServicePackage2` můžou služby a naslouchat na stejném portu (obě služby naslouchá na `Endpoint1` ). Je-li zadán režim otevření sítě, `PortBinding` nelze zadat konfigurace.

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

    V rámci aplikace pro cluster Windows můžete kombinovat a porovnat různé režimy sítě napříč službami. Některé služby můžou používat režim otevření, zatímco ostatní používají režim překladu adres (NAT). Když je služba nakonfigurovaná tak, aby používala režim překladu adres, port, na kterém naslouchá služba, musí být jedinečný.

    >[!NOTE]
    >V případě clusterů se systémem Linux není podporováno kombinování síťových režimů pro různé služby. 
    >

5. Když je vybraný režim **otevření** , definice **koncového bodu** v manifestu služby by měla explicitně ukazovat na balíček kódu odpovídající koncovému bodu, a to i v případě, že v něm balíček služby obsahuje jenom jeden balíček kódu. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. V případě systému Windows restartování virtuálního počítače způsobí, že se otevřená síť znovu vytvoří. Důvodem je zmírnění základního problému v zásobníku sítě. Výchozím chováním je znovu vytvořit síť. Pokud je nutné toto chování vypnout, bude možné použít následující konfiguraci, po které následuje upgrade konfigurace.

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
* [Další informace o prostředcích manifestu služby Service Fabric Service](./service-fabric-service-manifest-resources.md)
* [Nasazení kontejneru Windows pro Service Fabric v systému Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Docker pro Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)
