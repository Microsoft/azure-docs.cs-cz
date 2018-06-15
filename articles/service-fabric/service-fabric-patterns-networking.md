---
title: Vzory sítě pro Azure Service Fabric | Microsoft Docs
description: Popisuje obecné sítě vzory pro Service Fabric a jak vytvořit cluster pomocí Azure síťových funkcí.
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
ms.openlocfilehash: b180e62804b875ca4547a9d09f19efff32ae0cd9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207219"
---
# <a name="service-fabric-networking-patterns"></a>Vzory sítě Service Fabric
Cluster Azure Service Fabric může integrovat další funkce Azure sítě. V tomto článku jsme ukazují, jak vytvořit clustery, které používají následující funkce:

- [Existující virtuální síť nebo podsíť](#existingvnet)
- [Statickou veřejnou IP adresu](#staticpublicip)
- [Pouze interní zátěže.](#internallb)
- [Nástroj pro vyrovnávání zatížení pro vnitřní a vnější](#internalexternallb)

Service Fabric se spustí ve škálovací sadě standardní virtuální počítač. Žádné funkce, které můžete použít v škálovací sadu virtuálních počítačů, můžete se cluster Service Fabric. Síťové oddíly šablon Azure Resource Manageru pro sady škálování virtuálního počítače a služby prostředků infrastruktury jsou identické. Po nasazení existující virtuální síť, je snadné obsahovat další funkce sítě, jako je Azure ExpressRoute, Azure VPN Gateway, skupinu zabezpečení sítě a partnerský vztah virtuální sítě.

Service Fabric se liší od dalších síťových funkcí v jeden aspekt. [Portál Azure](https://portal.azure.com) poskytovatele prostředků Service Fabric interně používá k volání do clusteru se získat informace o uzly a aplikace. Zprostředkovatel prostředků Service Fabric vyžaduje veřejně přístupná příchozí přístup k brány port HTTP (port 19080, ve výchozím nastavení) na koncový bod správy. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) používá koncový bod správy ke správě tohoto clusteru. Zprostředkovatel prostředků Service Fabric také používá tento port dotazů na informace o clusteru, pro zobrazení na portálu Azure. 

Pokud port 19080 není přístupný ze zprostředkovatele prostředků Service Fabric, jako například zprávu *uzly nebyl nalezen* se zobrazí na portálu, a seznamu uzlů a aplikace se zobrazí prázdné. Pokud chcete zobrazit cluster v portálu Azure, nástroj pro vyrovnávání zatížení musí vystavit veřejnou IP adresu a vaše skupina zabezpečení sítě musí umožňovat příchozí port 19080 provoz. Pokud vaše instalace těchto požadavků nesplňuje, portálu Azure nezobrazuje stav clusteru.

## <a name="templates"></a>Šablony

Všechny šablony Service Fabric se v [Githubu](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Nyní byste měli mít k nasazení šablony jako-pomocí následujících příkazů prostředí PowerShell. Pokud nasazujete existující šablonu Azure Virtual Network nebo šabloně statické veřejné IP, nejdřív přečíst [počáteční instalace](#initialsetup) tohoto článku.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Počáteční nastavení

### <a name="existing-virtual-network"></a>Existující virtuální síť

V následujícím příkladu jsme spustit s existující virtuální síť s názvem ExistingRG-vnet v **ExistingRG** skupinu prostředků. Podsíť je s názvem výchozí. Tyto výchozí prostředky jsou vytvořeny při použití portálu Azure vytvořit standardní virtuální počítač (VM). Můžete vytvořit virtuální síť a podsíť bez vytvoření virtuálního počítače, ale přidání clusteru pro existující virtuální síť hlavním cílem je zajistit připojení k síti na ostatních virtuálních počítačů. Vytvoření virtuálního počítače poskytuje dobrým příkladem jak se obvykle používá existující virtuální síť. Pokud váš cluster Service Fabric používá jenom k interním pro vyrovnávání zatížení, bez veřejnou IP adresu, můžete virtuální počítač a jeho veřejnou IP adresu jako zabezpečený *přejít pole*.

### <a name="static-public-ip-address"></a>Statickou veřejnou IP adresu

Statickou veřejnou IP adresu obecně je vyhrazený prostředek, který spravované samostatně z virtuálních počítačů nebo virtuální počítače, kterému je přiřazena. (Na rozdíl v prostředku clusteru Service Fabric skupiny sám sebe) je zřízený ve skupině prostředků vyhrazené sítě. Vytvořte statickou veřejnou IP adresu s názvem staticIP1 ve stejné ExistingRG skupině prostředků, na portálu Azure nebo pomocí prostředí PowerShell:

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

### <a name="service-fabric-template"></a>Šablona Service Fabric

V příkladech v tomto článku používáme template.json Service Fabric. Průvodce standardní portálu můžete stáhnout šablonu z portálu, před vytvořením clusteru. Také můžete použít jednu z [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), například [zabezpečení clusteru Service Fabric pěti uzly](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Existující virtuální síť nebo podsíť

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


2. Změna `vnetID` proměnné tak, aby odkazoval na stávající virtuální síť:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Odebrat `Microsoft.Network/virtualNetworks` z vašich prostředků, takže Azure nevytvoří nové virtuální sítě:

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

4. Komentář virtuální sítě z `dependsOn` atribut `Microsoft.Compute/virtualMachineScaleSets`, takže nemusíte závisí na vytvoření nové virtuální sítě:

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

5. Nasazení šablony:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po nasazení by měla obsahovat nové virtuální sítě sady škálování virtuálních počítačů. Typ uzlu sady škálování virtuálního počítače by měl zobrazit existující virtuální síť a podsíť. Také můžete použít protokol RDP (Remote Desktop) přístup k virtuálnímu počítači, který již byl ve virtuální síti, a na příkaz ping nové měřítka nastavte virtuální počítače:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Jiný příklad najdete v tématu [ten, který není specifické pro Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statickou veřejnou IP adresu

1. Přidání parametrů pro název existující skupiny prostředků, statické IP, název a plně kvalifikovaný název domény (FQDN):

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

2. Odeberte `dnsName` parametr. (Statická IP adresa už má jednu.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Přidejte proměnnou, která bude odkazovat na existující statická IP adresa:

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

5. Komentář IP adresu z `dependsOn` atribut `Microsoft.Network/loadBalancers`, takže nemusíte závisí na vytvoření novou IP adresu:

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

6. V `Microsoft.Network/loadBalancers` prostředků, změny `publicIPAddress` element `frontendIPConfigurations` Chcete-li existující statická IP adresa místo nově vytvořený jeden:

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

7. V `Microsoft.ServiceFabric/clusters` prostředků, změna `managementEndpoint` na plně kvalifikovaný název domény DNS statické IP adresy. Pokud používáte zabezpečení clusteru, ujistěte se, změníte *http://* k *https://*. (Všimněte si, že tento krok platí jenom pro clusterů Service Fabric. Pokud používáte škálovací sadu virtuálních počítačů, tento krok přeskočte.)

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

Po nasazení se zobrazí, že nástroj pro vyrovnávání zatížení je vázána na veřejnou statickou IP adresu z jiné skupiny prostředků. Koncový bod připojení klienta Service Fabric a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) koncového bodu statické IP adresy na plně kvalifikovaný název domény DNS.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Pouze interní zátěže.

Tento scénář nahrazuje externím vyrovnáváním zatížení v šabloně Service Fabric výchozí s nástrojem pro vyrovnávání zatížení pouze interní. Důsledky pro portál Azure a pro poskytovatele prostředků Service Fabric najdete v předchozí části.

1. Odeberte `dnsName` parametr. (Jej není nutné.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Pokud používáte metodu statického přidělování, Volitelně můžete přidat parametr statických adres IP. Pokud používáte metody dynamického přidělení, není potřeba tento krok.

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

4. IP adresu odeberte `dependsOn` atribut `Microsoft.Network/loadBalancers`, takže nemusíte závisí na vytvoření nové adresy IP. Přidat virtuální síť `dependsOn` atributů, protože nástroj pro vyrovnávání zatížení teď závisí na podsíť z virtuální sítě:

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

5. Změna nástroje pro vyrovnávání zatížení `frontendIPConfigurations` ze pomocí nastavení `publicIPAddress`, pomocí podsíť a `privateIPAddress`. `privateIPAddress` používá předdefinovanou statické interní IP adresu. Chcete-li použít dynamickou IP adresu, odeberte `privateIPAddress` element a poté změňte `privateIPAllocationMethod` k **dynamické**.

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

6. V `Microsoft.ServiceFabric/clusters` prostředků, změna `managementEndpoint` tak, aby odkazoval na adrese služby Vyrovnávání zatížení interní. Pokud používáte zabezpečení clusteru, ujistěte se, změníte *http://* k *https://*. (Všimněte si, že tento krok platí jenom pro clusterů Service Fabric. Pokud používáte škálovací sadu virtuálních počítačů, tento krok přeskočte.)

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

Po nasazení používá nástroj pro vyrovnávání zatížení privátní statické 10.0.0.250 IP adresa. Pokud máte jiný počítač ve stejné virtuální síti, můžete přejít na interní [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) koncový bod. Všimněte si, že se připojuje ke službě jednoho z uzlů za nástrojem pro vyrovnávání zatížení.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Nástroj pro vyrovnávání zatížení pro vnitřní a vnější

V tomto scénáři spustit s existující externím vyrovnáváním zatížení typ s jedním uzlem a přidejte interní nástroj pro stejný typ uzlu. Back-end port připojený k fondu adres back-end lze přiřadit pouze ke službě Vyrovnávání zatížení jednu. Zvolte, které nástroj pro vyrovnávání zatížení bude mít vaše aplikace porty a který nástroj pro vyrovnávání zatížení bude mít vaše koncové body správy (porty 19000 a 19080). Když vložíte koncové body správy v nástroji pro vyrovnávání zatížení pro vnitřní, mějte na paměti prostředků Service Fabric zprostředkovatele omezení popsané dříve v článku. V příkladu používáme, správu, které koncové body zůstat na externím vyrovnáváním zatížení. Také přidat na port 80 aplikace port a umístěte ji na nástroje pro vyrovnávání zatížení interní.

V typu dva uzlu clusteru je jeden typ uzlu na externím vyrovnáváním zatížení. Další typ uzlu je v nástroji pro vyrovnávání zatížení interní. Chcete použít typ dva uzlu clusteru, v šabloně typ uzlu dva portálu vytvořené, (který se dodává s nástroje pro vyrovnávání zatížení dvou), přepněte druhý pro vyrovnávání zatížení do interní nástroj. Další informace najdete v tématu [nástroj pro vyrovnávání zatížení pouze pro interní](#internallb) části.

1. Přidejte parametr statické interní služby load vyrovnávání IP adresu. (Poznámky související s použitím dynamickou IP adresu, naleznete v předchozích částech tohoto článku.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Přidáte parametr aplikace port 80.

3. Přidejte interní verzích stávající sítě proměnné, zkopírujte a vložte je a přidejte "-Int" k názvu:

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

4. Pokud spustíte pomocí šablony generovaný na portálu, který používá aplikace port 80, přidá výchozí šablony portálu AppPort1 (port 80) na externím vyrovnáváním zatížení. V takovém případě odeberte z nástroje pro vyrovnávání zatížení externí AppPort1 `loadBalancingRules` a sondy, můžete ho přidat do nástroje pro vyrovnávání zatížení pro vnitřní:

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

5. Přidejte druhý `Microsoft.Network/loadBalancers` prostředků. Vypadá podobně jako nástroj pro vyrovnávání zatížení interní vytvořené v [nástroj pro vyrovnávání zatížení pouze pro interní](#internallb) části, ale používá "-Int" proměnné nástroje pro vyrovnávání zatížení a implementuje pouze aplikace port 80. Tím se taky odeberou `inboundNatPools`, zachovat koncových bodů protokolu RDP na nástroje pro vyrovnávání zatížení veřejné. Pokud chcete v nástroji pro vyrovnávání zatížení pro vnitřní protokol RDP, přesuňte `inboundNatPools` z externí službu tento nástroj pro vyrovnávání zatížení pro vnitřní Vyrovnávání zatížení:

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

6. V `networkProfile` pro `Microsoft.Compute/virtualMachineScaleSets` prostředků, přidejte interní adresa back-end fondu:

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

Po nasazení se zobrazí dvě služby Vyrovnávání zatížení ve skupině prostředků. Pokud Vyrovnávání zatížení, uvidíte veřejné IP adresy a správu koncové body (porty 19000 a 19080) přiřazenou veřejnou IP adresu. Také můžete zobrazit statické interní IP adresu a aplikace koncového bodu (port 80) přiřazen ke službě Vyrovnávání zatížení interní. Obě služby Vyrovnávání zatížení používat stejný fond back-end škálování sady virtuálního počítače.

## <a name="next-steps"></a>Další postup
[Vytvoření clusteru](service-fabric-cluster-creation-via-arm.md)
