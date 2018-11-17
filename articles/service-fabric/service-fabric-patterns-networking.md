---
title: Vzory sítě pro Azure Service Fabric | Dokumentace Microsoftu
description: Popisuje běžné vzory sítě pro Service Fabric a jak vytvořit cluster s použitím Azure síťových funkcí.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 2fce90f971d13b94c73012d4089cca05739c5440
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853706"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric sítě vzory
Cluster Azure Service Fabric můžete integrovat další funkce Azure sítě. V tomto článku ukážeme, jak vytvářet clustery, které používají následující funkce:

- [Existující virtuální síť nebo podsítě](#existingvnet)
- [Statické veřejné IP adresy](#staticpublicip)
- [Nástroje pro vyrovnávání zatížení jenom pro interní](#internallb)
- [Nástroj pro vyrovnávání zatížení interní i externí](#internalexternallb)

Service Fabric funguje ve škálovací sadě standardní virtuální počítač. Všechny funkce, které můžete využít ve škálovací sadě virtuálních počítačů, můžete použít s clusterem Service Fabric. Sítě části šablony Azure Resource Manageru pro škálovací sady virtuálních počítačů a Service Fabric jsou identické. Po nasazení do existující virtuální sítě je snadno začlenit další funkce sítě, jako je Azure ExpressRoute, Azure VPN Gateway, skupiny zabezpečení sítě a partnerský vztah virtuální sítě.

Service Fabric se liší od jiných síťových funkcí v jeden aspekt. [Webu Azure portal](https://portal.azure.com) interně používá poskytovatele prostředků Service Fabric pro volání do clusteru a získat informace o uzlech a aplikace. Poskytovatel prostředků Service Fabric vyžaduje veřejně přístupné příchozí přístup k portu brány protokolu HTTP (port 19080, ve výchozím nastavení) na koncový bod správy. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) používá koncový bod správy ke správě clusteru. Tento port dotaz na informace o clusteru, poskytovatel prostředků Service Fabric také používá k zobrazení na webu Azure Portal. 

Pokud port 19080 není přístupný z poskytovatele prostředků Service Fabric, jako jsou zprávy *uzly nebyl nalezen* se zobrazí na portálu a seznamu uzlů a aplikace se zobrazí prázdné. Pokud chcete zobrazit váš cluster na webu Azure Portal, nástroj pro vyrovnávání zatížení musí vystavit veřejná IP adresa a skupiny zabezpečení sítě musí umožňovat příchozí provoz na portu 19080. Pokud nastavení těchto požadavků nesplňuje, na webu Azure portal nezobrazuje stav clusteru.

## <a name="templates"></a>Šablony

Všechny šablony Service Fabric jsou v [Githubu](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Byste měli nasadit šablony jako-pomocí následujících příkazů Powershellu. Pokud nasazujete existující šablonu Azure Virtual Network nebo statické veřejné IP šablony, nejdřív přečíst [počáteční instalace](#initialsetup) části tohoto článku.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Počáteční nastavení

### <a name="existing-virtual-network"></a>Existující virtuální sítě

V následujícím příkladu Začneme s existující virtuální síť s názvem ExistingRG virtuální sítě v **ExistingRG** skupinu prostředků. Podsíť se nazývá výchozí. Tyto výchozí materiály jsou vytvořeny při vytvoření standardního virtuálního počítače (VM) pomocí webu Azure portal. Můžete vytvořit virtuální síť a podsíť bez vytvoření virtuálního počítače, ale hlavním cílem přidávání clusteru do existující virtuální sítě je k zajištění síťové připojení k ostatním virtuálním počítačům. Vytváří se virtuální počítač poskytuje dobrý příklad, jak je obvykle používána existující virtuální sítě. Pokud váš cluster Service Fabric používá jenom interní nástroj pro vyrovnávání zatížení, bez veřejné IP adresy, můžete virtuální počítač a jeho veřejná IP adresa jako zabezpečený *jump box*.

### <a name="static-public-ip-address"></a>Statické veřejné IP adresy

Statickou veřejnou IP adresu obecně je zdroj vyhrazený, která se spravují odděleně od virtuálního počítače nebo virtuální počítače, který je přiřazen. Se zřizují ve skupině vyhrazených síťových prostředků (na rozdíl od v prostředku clusteru Service Fabric seskupení samotné). Vytvoření statické veřejné IP adresy s názvem staticIP1 ve stejném ExistingRG skupině prostředků, na webu Azure Portal nebo pomocí prostředí PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Šablony Service Fabric

V příkladech v tomto článku používáme template.json Service Fabric. Stáhnout šablonu z portálu, před vytvořením clusteru můžete použít Průvodce standardní portálu. Také můžete použít jednu z [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), třeba [zabezpečeného clusteru Service Fabric s pěti uzly](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Existující virtuální síť nebo podsítě

1. Změňte parametr podsítě na název existující podsíť a pak přidejte dva nové parametry tak, aby odkazovaly existující virtuální síť:

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Okomentujte `nicPrefixOverride` atribut `Microsoft.Compute/virtualMachineScaleSets`, protože používáte existující podsítě a jste zakázali této proměnné v kroku 1.

    ```
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Změnit `vnetID` proměnné tak, aby odkazovala na existující virtuální síť:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Odebrat `Microsoft.Network/virtualNetworks` z vašich prostředků, takže Azure nevytvoří novou virtuální síť:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Odkomentujte virtuálními sítěmi `dependsOn` atribut `Microsoft.Compute/virtualMachineScaleSets`, takže nejsou závislé na vytvoření nové virtuální sítě:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Nasazení šablony:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po nasazení, virtuální sítě by měl obsahovat novou škálovací sadu virtuálních počítačů. Typ uzlu sady škálování virtuálního počítače by se zobrazit existující virtuální síť a podsíť. Také můžete použít protokol RDP (Remote Desktop) pro přístup k virtuálnímu počítači, který už je ve virtuální síti, a na příkaz ping novou škálovací nastavte virtuální počítače:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Další příklad naleznete v tématu [, který není specifický pro Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statické veřejné IP adresy

1. Přidáte parametry pro název existující skupiny prostředků, statické IP, název a plně kvalifikovaný název domény (FQDN):

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Odeberte `dnsName` parametru. (Statická IP adresa už má jeden.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Přidejte proměnnou, která odkazují na existující statická IP adresa:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Odebrat `Microsoft.Network/publicIPAddresses` z vašich prostředků, takže Azure nevytvoří novou IP adresu:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Komentář na IP adresu z `dependsOn` atribut `Microsoft.Network/loadBalancers`, takže nejsou závislé na vytváření novou IP adresu:

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. V `Microsoft.Network/loadBalancers` prostředek, změny `publicIPAddress` prvek `frontendIPConfigurations` odkazovat existující statickou IP adresu místo si nově vytvořený odběr:

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. V `Microsoft.ServiceFabric/clusters` prostředků, změna `managementEndpoint` na plně kvalifikovaný název domény DNS statické IP adresy. Pokud používáte zabezpečeného clusteru, ujistěte se, že změníte *http://* k *https://*. (Všimněte si, že tento krok platí jenom pro clustery Service Fabric. Pokud použijete škálovací sady virtuálních počítačů, přeskočte tento krok.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Nasazení šablony:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Po nasazení uvidíte, že nástroj pro vyrovnávání zatížení je vázán na statickou veřejnou IP adresu z jiné skupiny prostředků. Koncový bod připojení klienta Service Fabric a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) koncového bodu pro plně kvalifikovaný název DNS statickou IP adresu.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Nástroje pro vyrovnávání zatížení jenom pro interní

Tento scénář nahradí externím vyrovnáváním zatížení v šabloně Service Fabric výchozí služby pouze interní load balancer. Důsledky pro na webu Azure portal a u poskytovatele prostředků Service Fabric najdete v předchozí části.

1. Odeberte `dnsName` parametru. (Není potřeba.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Volitelně můžete použít metodu statického přidělování, můžete přidat parametr statické IP adresy. Pokud používáte metody dynamického přidělení, není potřeba tento krok.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Odebrat `Microsoft.Network/publicIPAddresses` z vašich prostředků, takže Azure nevytvoří novou IP adresu:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Odeberte IP adresu `dependsOn` atribut `Microsoft.Network/loadBalancers`, takže nejsou závislé na vytváření novou IP adresu. Přidat virtuální síť `dependsOn` atribut, protože nástroj pro vyrovnávání zatížení teď závisí na podsíť z virtuální sítě:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Změna nástroje pro vyrovnávání zatížení `frontendIPConfigurations` ze pomocí nastavení `publicIPAddress`, na použití podsítě a `privateIPAddress`. `privateIPAddress` používá předdefinované statické interní IP adresu. Chcete-li použít dynamickou IP adresu, odeberte `privateIPAddress` element a pak změňte `privateIPAllocationMethod` k **dynamické**.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. V `Microsoft.ServiceFabric/clusters` prostředků, změna `managementEndpoint` tak, aby odkazoval na adresu interního nástroje pro vyrovnávání. Pokud používáte zabezpečeného clusteru, ujistěte se, že změníte *http://* k *https://*. (Všimněte si, že tento krok platí jenom pro clustery Service Fabric. Pokud použijete škálovací sady virtuálních počítačů, přeskočte tento krok.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Nasazení šablony:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Po nasazení se používá nástroj pro vyrovnávání zatížení 10.0.0.250 statické privátní IP adresu. Pokud máte jiného počítače ve stejné virtuální síti, můžete přejít k interní [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) koncového bodu. Všimněte si, že se připojuje k jednomu z uzlů za nástrojem pro vyrovnávání zatížení.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Nástroj pro vyrovnávání zatížení interní i externí

V tomto scénáři začínat stávající typ s jedním uzlem externí nástroj pro vyrovnávání zatížení a přidejte interního nástroje pro stejný typ uzlu. Back-end port připojený k fondu back endových adres lze přiřadit pouze k nástroji pro vyrovnávání zatížení. Určete, které nástroj pro vyrovnávání zatížení bude mít vaše aplikace porty a jaké nástroje pro vyrovnávání zatížení bude mít vaše koncové body správy (porty 19000 a 19080). Když vložíte koncových bodů správy v nástroji pro vyrovnávání zatížení interní, mějte na paměti prostředku Service Fabric poskytovatele omezení popsaná v následujícím článku. V tomto příkladu používáme, správy, které zůstávají koncových bodů v externím vyrovnáváním zatížení. Také přidat port 80 port aplikace a umístěte ho na interní služby load balancer.

V clusteru dvě typ uzlu jeden typ uzlu je v externím vyrovnáváním zatížení. Další typ uzlu je v interní služby load balancer. Pro použití dvou typ uzlu clusteru, v šabloně portál vytvořil dva typ uzlu, (která je součástí nástrojů pro vyrovnávání zatížení dvou), přepněte druhý nástroj pro vyrovnávání zatížení interního nástroje. Další informace najdete v tématu [nástroje pro vyrovnávání zatížení jenom pro interní](#internallb) oddílu.

1. Přidáte parametr statické interního nástroje pro vyrovnávání IP adresa. (Poznámky související s používáním dynamickou IP adresu, najdete v dřívějších částech tohoto článku.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Přidáte parametr port 80 aplikace.

3. Přidejte sítě proměnné, kopírovat a vkládat je interní verze stávajícího a přidejte "-Int" k názvu:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Pokud začnete se šablonou generovaný na portálu, který používá aplikace port 80, přidá výchozí šablony portálu AppPort1 (port 80) na externím vyrovnáváním zatížení. V takovém případě odeberte z nástroje pro vyrovnávání zatížení externí AppPort1 `loadBalancingRules` a testy, můžete ho přidat do interní služby load balancer:

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Přidejte druhý `Microsoft.Network/loadBalancers` prostředků. Vypadá podobně jako interního nástroje load balancer vytvořené v [nástroje pro vyrovnávání zatížení jenom pro interní](#internallb) oddílu, ale používá "-Int" proměnné nástroje pro vyrovnávání zatížení a implementuje pouze aplikace port 80. Tím se taky odeberou `inboundNatPools`, aby vaše koncové body protokolu RDP na nástroj pro vyrovnávání zatížení veřejnou. Pokud chcete protokol RDP na interní služby load balancer, přesuňte `inboundNatPools` z externí nástroj pro vyrovnávání pro tento interní nástroj pro vyrovnávání zatížení:

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. V `networkProfile` pro `Microsoft.Compute/virtualMachineScaleSets` prostředků, přidat interní back endovém fondu adres:

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Nasazení šablony:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Po nasazení zobrazí se dvě služby Vyrovnávání zatížení ve skupině prostředků. Pokud přejdete nástroje pro vyrovnávání zatížení, můžete zobrazit veřejné IP adresy a správa koncových bodů (porty 19000 a 19080) přiřazenou veřejnou IP adresu. Můžete zobrazit také statické interní IP adresu a aplikace koncový bod (port 80) přiřazená interní služby load balancer. Oba nástroje pro vyrovnávání zatížení používat stejný fond back-end škálovací sady virtuálního počítače.

## <a name="next-steps"></a>Další postup
[Vytvoření clusteru](service-fabric-cluster-creation-via-arm.md)
