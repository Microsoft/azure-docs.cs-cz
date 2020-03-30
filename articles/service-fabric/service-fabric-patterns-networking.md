---
title: Síťové vzory pro Azure Service Fabric
description: Popisuje běžné síťové vzory pro Service Fabric a jak vytvořit cluster pomocí síťových funkcí Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466293"
---
# <a name="service-fabric-networking-patterns"></a>Síťové vzory service fabric
Cluster Azure Service Fabric můžete integrovat s dalšími síťovými funkcemi Azure. V tomto článku vám ukážeme, jak vytvořit clustery, které používají následující funkce:

- [Existující virtuální síť nebo podsíť](#existingvnet)
- [Statická veřejná IP adresa](#staticpublicip)
- [Interní pouze systém vyrovnávání zatížení](#internallb)
- [Interní a externí systém vyrovnávání zatížení](#internalexternallb)

Service Fabric běží ve standardní škálovací sadě virtuálních strojů. Všechny funkce, které můžete použít ve škálovací sadě virtuálních strojů, můžete použít s clusterem Service Fabric. Síťové části šablon Azure Resource Manager pro škálovací sady virtuálních strojů a Service Fabric jsou identické. Po nasazení do existující virtuální sítě je snadné začlenit další síťové funkce, jako je Azure ExpressRoute, Azure VPN Gateway, skupina zabezpečení sítě a partnerský vztah virtuální sítě.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Povolení zprostředkovatele prostředků Service Fabric k dotazování clusteru

Service Fabric je jedinečný z jiných síťových funkcí v jednom aspektu. [Portál Azure](https://portal.azure.com) interně používá poskytovatele prostředků Service Fabric k volání clusteru k získání informací o uzlech a aplikacích. Poskytovatel prostředků Service Fabric vyžaduje veřejně přístupný příchozí přístup k portu brány HTTP (port 19080, ve výchozím nastavení) v koncovém bodě správy. [Aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) používá koncový bod správy ke správě clusteru. Poskytovatel prostředků Service Fabric také používá tento port k dotazování informací o vašem clusteru, k zobrazení na webu Azure Portal. 

Pokud port 19080 není přístupný od zprostředkovatele prostředků Service Fabric, zobrazí se na portálu zpráva jako *Uzly, které nebyly nalezeny,* a seznam uzlů a aplikací se zobrazí prázdný. Pokud chcete zobrazit svůj cluster na webu Azure Portal, musí váš balancer vystavit veřejnou IP adresu a vaše skupina zabezpečení sítě musí povolit příchozí port 19080 provoz. Pokud vaše nastavení tyto požadavky nesplňuje, portál Azure nezobrazuje stav vašeho clusteru.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Šablony

Všechny šablony Service Fabric jsou v [GitHubu](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Šablony byste měli nasadit tak, jak jsou, pomocí následujících příkazů prostředí PowerShell. Pokud nasazujete existující šablonu virtuální sítě Azure nebo statickou veřejnou šablonu IP, přečtěte si [nejprve](#initialsetup) část Počáteční nastavení tohoto článku.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Počáteční nastavení

### <a name="existing-virtual-network"></a>Existující virtuální síť

V následujícím příkladu začneme s existující virtuální síť s názvem ExistingRG-vnet, ve skupině prostředků **ExistingRG.** Podsíť má název výchozí. Tyto výchozí prostředky se vytvoří při použití portálu Azure k vytvoření standardního virtuálního počítače (VM). Virtuální síť a podsíť můžete vytvořit bez vytvoření virtuálního počítače, ale hlavním cílem přidání clusteru do existující virtuální sítě je zajistit připojení k síti k jiným virtuálním počítačům. Vytvoření virtuálního počítače poskytuje dobrý příklad, jak se obvykle používá existující virtuální síť. Pokud váš cluster Service Fabric používá pouze interní vyrovnávání zatížení, bez veřejné IP adresy, můžete použít virtuální počítač a jeho veřejnou IP adresu jako zabezpečené *pole pro přeskoče .*

### <a name="static-public-ip-address"></a>Statická veřejná IP adresa

Statická veřejná IP adresa je obecně vyhrazený prostředek, který se spravuje odděleně od virtuálních počítačů nebo virtuálních počítačů, ke kterým je přiřazený. Je zřízena ve skupině prostředků vyhrazené sítě (na rozdíl od v clusteru Service Fabric skupiny prostředků sám). Vytvořte statickou veřejnou IP adresu s názvem staticIP1 ve stejné skupině prostředků ExistingRG, buď na webu Azure Portal, nebo pomocí Prostředí PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

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

V příkladech v tomto článku používáme Service Fabric template.json. Před vytvořením clusteru můžete šablonu stáhnout z portálu pomocí průvodce standardním portálem. Můžete také použít jednu z [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), například [zabezpečený cluster Service Fabric s pěti uzlovými daty](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Existující virtuální síť nebo podsíť

1. Změňte parametr podsítě na název existující podsítě a přidejte dva nové parametry, které budou odkazovat na existující virtuální síť:

    ```json
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

2. `nicPrefixOverride` Zakomentujte `Microsoft.Compute/virtualMachineScaleSets`atribut aplikace , protože používáte existující podsíť a tuto proměnnou jste zakázali v kroku 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Změňte `vnetID` proměnnou tak, aby přectola na existující virtuální síť:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Odeberte `Microsoft.Network/virtualNetworks` je z prostředků, aby Azure nevytvořil novou virtuální síť:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
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

5. Zakomentujte virtuální `dependsOn` síť `Microsoft.Compute/virtualMachineScaleSets`z atributu , takže nezávisíte na vytvoření nové virtuální sítě:

    ```json
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
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po nasazení by vaše virtuální síť měla obsahovat nové virtuální počítače škálovací sady. Typ uzlu škálovací sady virtuálních strojů by měl zobrazovat existující virtuální síť a podsíť. Můžete také použít protokol RDP (RdP) pro přístup k virtuálnímu počítači, který byl již ve virtuální síti, a ping nové škálovací sady virtuálních počítačů:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Další příklad naleznete [v tématu, který není specifický pro service fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statická veřejná IP adresa

1. Přidejte parametry pro název existující statické skupiny prostředků IP, název a plně kvalifikovaný název domény (Plně kvalifikovaný název domény):

    ```json
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

2. Odeberte `dnsName` parametr. (Statická ADRESA IP již jednu má.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Přidejte proměnnou, která odkazuje na existující statickou adresu IP:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Odeberte `Microsoft.Network/publicIPAddresses` ze svých prostředků, aby Azure nevytvořil novou IP adresu:

    ```json
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

5. Komentář ip adresu z `dependsOn` atributu `Microsoft.Network/loadBalancers`, takže nemusíte záviset na vytvoření nové IP adresy:

    ```json
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

6. V `Microsoft.Network/loadBalancers` prostředku změňte `publicIPAddress` prvek `frontendIPConfigurations` odkazovat na existující statickou adresu IP namísto nově vytvořené adresy:

    ```json
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

7. V `Microsoft.ServiceFabric/clusters` prostředku změňte `managementEndpoint` na vykreslovaný název domény DNS statické adresy IP. Pokud používáte zabezpečený cluster, nezapomeňte změnit *http://* na *https://*. (Všimněte si, že tento krok platí pouze pro clustery Service Fabric. Pokud používáte škálovací sadu virtuálních strojů, tento krok přeskočte.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Nasazení šablony:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Po nasazení uvidíte, že váš balancer je vázán na veřejnou statickou IP adresu z jiné skupiny prostředků. Koncový bod připojení klienta Service Fabric a koncový bod [aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) odkazují na soubor DNS FQDN statické adresy IP.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Interní pouze systém vyrovnávání zatížení

Tento scénář nahradí externí vyrovnávání zatížení ve výchozí šabloně Service Fabric s interním pouze vyrovnávání zatížení. Viz [dříve v článku](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) důsledky pro portál Azure a pro poskytovatele prostředků Service Fabric.

1. Odeberte `dnsName` parametr. (Není to potřeba.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Volitelně pokud použijete statickou metodu přidělení, můžete přidat parametr statické adresy IP. Pokud používáte metodu dynamického přidělení, není nutné provést tento krok.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Odeberte `Microsoft.Network/publicIPAddresses` ze svých prostředků, aby Azure nevytvořil novou IP adresu:

    ```json
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

4. Odeberte `dependsOn` atribut `Microsoft.Network/loadBalancers`IP adresy , abyste nebyli závislí na vytvoření nové ADRESY IP. Přidejte atribut `dependsOn` virtuální sítě, protože nástroj pro vyrovnávání zatížení teď závisí na podsíti z virtuální sítě:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Změňte `frontendIPConfigurations` nastavení pro vyrovnávání zatížení `publicIPAddress`z použití aplikace `privateIPAddress`a na podsíť a . `privateIPAddress`používá předdefinovanou statickou interní IP adresu. Chcete-li použít dynamickou `privateIPAddress` adresu IP, `privateIPAllocationMethod` odeberte prvek a přepřejte na **Dynamic**.

    ```json
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

6. V `Microsoft.ServiceFabric/clusters` prostředku změňte `managementEndpoint` odkaz na adresu interního správce zatížení. Pokud používáte zabezpečený cluster, zkontrolujte, zda *http://* změnit na *https://*. (Všimněte si, že tento krok platí pouze pro clustery Service Fabric. Pokud používáte škálovací sadu virtuálních strojů, tento krok přeskočte.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Nasazení šablony:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Po nasazení používá váš systém vyrovnávání zatížení privátní statickou adresu IP 10.0.0.250. Pokud máte jiný počítač ve stejné virtuální síti, můžete přejít na koncový bod interní [aplikace Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md) Všimněte si, že se připojuje k jednomu z uzlů za vyrovnáváním zatížení.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interní a externí systém vyrovnávání zatížení

V tomto scénáři začnete s existujícím externím mechanismem vyrovnávání zatížení typu jeden uzel a přidáte interní systém vyrovnávání zatížení pro stejný typ uzlu. Port back-end připojený k fondu adres back-end lze přiřadit pouze k jednomu vyvykladaču zatížení. Zvolte, který systém vyrovnávání zatížení bude mít vaše porty aplikace a který systém vyrovnávání zatížení bude mít koncové body správy (porty 19000 a 19080). Pokud vložíte koncové body správy na interní vyrovnávání zatížení, mějte na paměti omezení zprostředkovatele prostředků Service Fabric popsané [dříve v článku](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). V příkladu, který používáme, zůstávají koncové body správy na externím vytápěči zatížení. Přidáte také port 80 aplikační port a umístěte jej na vnitřní vyrovnávání zatížení.

V clusteru typu dvou uzlů je jeden typ uzlu na externím vydělávacím stroji zatížení. Druhý typ uzlu je na vnitřní vyrovnávání zatížení. Chcete-li použít cluster typu dvou uzlů, v šabloně typu dvou uzlů vytvořené portálem (která je dodávána se dvěma vykladači zatížení), přepněte druhý vykladač zatížení na interní systém vyrovnávání zatížení. Další informace naleznete v části [Interní pouze vyrovnávání zatížení.](#internallb)

1. Přidejte statický parametr IP adresy internal balancer. (Poznámky související s použitím dynamické adresy IP naleznete v předchozích částech tohoto článku.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Přidejte parametr portu aplikace 80.

3. Chcete-li přidat interní verze existujících síťových proměnných, zkopírujte je a vložte a do názvu přidejte "-Int":

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Pokud začnete s portálem generovaná šablona, která používá port aplikace 80, výchozí šablona portálu přidá AppPort1 (port 80) na externí vyrovnávání zatížení. V takovém případě odeberte AppPort1 `loadBalancingRules` z externího systému vyrovnávání zatížení a sond, abyste ho mohli přidat do interního systému pro vyrovnávání zatížení:

    ```json
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

5. Přidejte `Microsoft.Network/loadBalancers` druhý prostředek. Vypadá podobně jako interní nástroj pro vyrovnávání zatížení vytvořený v části [Pouze interní nástroj pro vyrovnávání zatížení,](#internallb) ale používá proměnné nástroje pro vyrovnávání zatížení "Int" a implementuje pouze port aplikace 80. To také `inboundNatPools`odebere , chcete-li zachovat koncové body RDP na veřejný vytápěč zatížení. Pokud chcete rdp na interní mzda, přesunout `inboundNatPools` z externího vyrovnávání zatížení do tohoto interního vyrovnávání zatížení:

    ```json
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

6. V `networkProfile` pro `Microsoft.Compute/virtualMachineScaleSets` prostředek, přidejte interní fond adres back-end:

    ```json
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
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Po nasazení uvidíte dva vykladače zatížení ve skupině prostředků. Pokud procházíte vykladače zatížení, můžete zobrazit veřejné IP adresy a koncové body správy (porty 19000 a 19080) přiřazené k veřejné IP adrese. Můžete také zobrazit statickou interní IP adresu a koncový bod aplikace (port 80) přiřazený internímu systému vyrovnávání zatížení. Oba nástroje pro vyrovnávání zatížení používají stejný fond back-end škálování virtuálních strojů.

## <a name="notes-for-production-workloads"></a>Poznámky pro produkční úlohy

Výše uvedené šablony GitHub jsou navržené pro práci s výchozí skladovou položkou pro Azure Standard Load Balancer (SLB), základní skladovou položku. Tento SLB nemá žádnou sla, takže pro produkční úlohy standardní skladová položka by měla být použita. Další informace najdete v [přehledu nástroje pro vyrovnávání zatížení Azure Standard](/azure/load-balancer/load-balancer-standard-overview). Jakýkoli cluster Service Fabric, který používá standardní skladovou položku pro službu SLB, musí zajistit, aby každý typ uzlu měl pravidlo umožňující odchozí provoz na portu 443. To je nezbytné k dokončení instalace clusteru a jakékoli nasazení bez takového pravidla se nezdaří. Ve výše uvedeném příkladu "pouze interní" vyrovnávání zatížení musí být do šablony přidán další externí systém vyrovnávání zatížení s pravidlem umožňujícím odchozí provoz pro port 443.

## <a name="next-steps"></a>Další kroky
[Vytvoření clusteru](service-fabric-cluster-creation-via-arm.md)

Po nasazení uvidíte dva vykladače zatížení ve skupině prostředků. Pokud procházíte vykladače zatížení, můžete zobrazit veřejné IP adresy a koncové body správy (porty 19000 a 19080) přiřazené k veřejné IP adrese. Můžete také zobrazit statickou interní IP adresu a koncový bod aplikace (port 80) přiřazený internímu systému vyrovnávání zatížení. Oba nástroje pro vyrovnávání zatížení používají stejný fond back-end škálování virtuálních strojů.

