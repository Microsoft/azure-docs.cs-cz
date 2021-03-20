---
title: Modely sítě pro Azure Service Fabric
description: Popisuje běžné síťové vzory pro Service Fabric a vytváření clusterů pomocí síťových funkcí Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87421463"
---
# <a name="service-fabric-networking-patterns"></a>Modely Service Fabric sítě
Svůj cluster Azure Service Fabric můžete integrovat s dalšími síťovými funkcemi Azure. V tomto článku vám ukážeme, jak vytvořit clustery, které používají následující funkce:

- [Existující virtuální síť nebo podsíť](#existingvnet)
- [Statická veřejná IP adresa](#staticpublicip)
- [Jenom interní nástroj pro vyrovnávání zatížení](#internallb)
- [Interní a externí nástroj pro vyrovnávání zatížení](#internalexternallb)

Service Fabric běží ve standardní sadě škálování virtuálního počítače. Všechny funkce, které můžete použít v sadě škálování virtuálního počítače, můžete použít s clusterem Service Fabric. Síťové oddíly šablon Azure Resource Manager pro Virtual Machine Scale Sets a Service Fabric jsou identické. Po nasazení do existující virtuální sítě je snadné začlenit další síťové funkce, jako je Azure ExpressRoute, Azure VPN Gateway, skupina zabezpečení sítě a partnerský vztah virtuálních sítí.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Povolení Service Fabricho poskytovatele prostředků pro dotazování clusteru

Service Fabric je jedinečný z dalších síťových funkcí v jednom aspektu. [Azure Portal](https://portal.azure.com) interně používá poskytovatele prostředků Service Fabric k volání clusteru k získání informací o uzlech a aplikacích. Poskytovatel prostředků Service Fabric vyžaduje veřejně přístupný příchozí přístup k portu služby HTTP Gateway (ve výchozím nastavení je ve výchozím nastavení port 19080) na koncovém bodu správy. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) používá ke správě clusteru koncový bod správy. Poskytovatel prostředků Service Fabric používá tento port také k dotazování na informace o clusteru, aby se zobrazily v Azure Portal. 

Pokud port 19080 není dostupný od poskytovatele prostředků Service Fabric, zobrazí se na portálu zpráva jako *uzly nenalezené* a seznam uzlů a aplikací se zobrazí jako prázdný. Pokud chcete cluster zobrazit v Azure Portal, musí vystavit Nástroj pro vyrovnávání zatížení veřejnou IP adresu a vaše skupina zabezpečení sítě musí umožňovat příchozí přenos portu 19080. Pokud vaše instalace tyto požadavky nesplňuje, Azure Portal nezobrazuje stav vašeho clusteru.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Šablony

Všechny šablony Service Fabric jsou na [GitHubu](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Šablony byste měli být schopní nasadit pomocí následujících příkazů PowerShellu, jak je to možné. Pokud nasazujete stávající šablonu Azure Virtual Network nebo statickou veřejnou IP adresu, přečtěte si část [Úvodní nastavení](#initialsetup) tohoto článku.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Počáteční nastavení

### <a name="existing-virtual-network"></a>Existující virtuální síť

V následujícím příkladu Začínáme se stávající virtuální sítí s názvem ExistingRG-VNet ve skupině prostředků **ExistingRG** . Podsíť se nazývá výchozí. Tyto výchozí prostředky se vytvoří, když použijete Azure Portal k vytvoření standardního virtuálního počítače (VM). Virtuální síť a podsíť byste mohli vytvořit bez vytvoření virtuálního počítače, ale hlavním cílem přidání clusteru do existující virtuální sítě je poskytnout síťové připojení k ostatním virtuálním počítačům. Vytvoření virtuálního počítače nabízí dobrý příklad toho, jak se používá existující virtuální síť. Pokud váš Service Fabric cluster používá jenom interní nástroj pro vyrovnávání zatížení, bez veřejné IP adresy, můžete virtuální počítač a jeho veřejnou IP adresu použít jako zabezpečené *pole s odkazem*.

### <a name="static-public-ip-address"></a>Statická veřejná IP adresa

Statická veřejná IP adresa je obecně vyhrazený prostředek, který je spravovaný samostatně z virtuálního počítače nebo virtuálních počítačů, ke kterým je přiřazený. Zřizuje se ve vyhrazené skupině síťových prostředků (na rozdíl od Service Fabric samotné skupiny prostředků clusteru). Vytvořte statickou veřejnou IP adresu s názvem staticIP1 ve stejné skupině prostředků ExistingRG, a to buď v Azure Portal, nebo pomocí prostředí PowerShell:

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

V příkladech v tomto článku používáme Service Fabric template.jsna. K stažení šablony z portálu před vytvořením clusteru můžete použít Průvodce standardním portálem. Můžete použít také jednu z [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), jako je například [zabezpečený Service Fabric cluster s pěti uzly](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Existující virtuální síť nebo podsíť

1. Změňte parametr Subnet na název existující podsítě a pak přidejte dva nové parametry, které odkazují na stávající virtuální síť:

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

   Můžete také zadat komentář k parametru s názvem "virtualNetworkName", takže se nezobrazí výzva k zadání názvu virtuální sítě dvakrát v okně nasazení clusteru v Azure Portal.

2. Odkomentujte `nicPrefixOverride` atribut `Microsoft.Compute/virtualMachineScaleSets` , protože používáte existující podsíť a tuto proměnnou jste v kroku 1 zakázali.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Změňte `vnetID` proměnnou tak, aby odkazovala na existující virtuální síť:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Odeberte `Microsoft.Network/virtualNetworks` z vašich prostředků, takže Azure nevytvoří novou virtuální síť:

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

5. Odkomentujte virtuální síť od `dependsOn` atributu `Microsoft.Compute/virtualMachineScaleSets` , takže nezáleží na tom, jak vytvořit novou virtuální síť:

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

    Po nasazení by měla virtuální síť zahrnovat nové virtuální počítače sady škálování. Typ uzlu sady škálování virtuálního počítače by měl Ukázat existující virtuální síť a podsíť. K přístupu k virtuálnímu počítači, který už je ve virtuální síti, můžete použít taky protokol RDP (Remote Desktop Protocol) (RDP), a k otestování virtuálního počítače s novým škálováním na více virtuálních počítačích:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Další příklad naleznete v tématu [, které není specifické pro Service Fabric](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statická veřejná IP adresa

1. Přidejte parametry pro název existující skupiny prostředků statických IP adres, názvu a plně kvalifikovaného názvu domény (FQDN):

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

2. Odeberte `dnsName` parametr. (Statická IP adresa už jednu obsahuje.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Přidejte proměnnou, která bude odkazovat na existující statickou IP adresu:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Odeberte `Microsoft.Network/publicIPAddresses` z vašich prostředků, takže Azure nevytvoří novou IP adresu:

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

5. Odkomentujte IP adresu z `dependsOn` atributu `Microsoft.Network/loadBalancers` , takže nezáleží na vytvoření nové IP adresy:

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

6. V `Microsoft.Network/loadBalancers` prostředku změňte `publicIPAddress` element `frontendIPConfigurations` na odkaz na existující statickou IP adresu namísto nově vytvořené:

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

7. V `Microsoft.ServiceFabric/clusters` prostředku přejděte `managementEndpoint` do plně kvalifikovaného názvu domény DNS statické IP adresy. Pokud používáte zabezpečený cluster, nezapomeňte změnit *http://* na *https://*. (Upozorňujeme, že tento krok platí jenom pro Service Fabric clustery. Pokud používáte sadu škálování virtuálního počítače, přeskočte tento krok.)

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

Po nasazení vidíte, že je váš nástroj pro vyrovnávání zatížení vázaný na veřejnou statickou IP adresu z jiné skupiny prostředků. Koncový bod připojení klienta Service Fabric a koncový bod [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) odkazuje na plně kvalifikovaný název domény DNS statické IP adresy.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Jenom interní nástroj pro vyrovnávání zatížení

Tento scénář nahrazuje externí nástroj pro vyrovnávání zatížení ve výchozí šabloně Service Fabric s interním nástrojem pro vyrovnávání zatížení. Důsledky pro Azure Portal a pro poskytovatele prostředků Service Fabric najdete [v části v článku](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) .

1. Odeberte `dnsName` parametr. (Není nutné.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Případně, pokud použijete statickou metodu přidělení, můžete přidat parametr statických IP adres. Pokud používáte metodu dynamického přidělení, nemusíte tento krok provádět.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Odeberte `Microsoft.Network/publicIPAddresses` z vašich prostředků, takže Azure nevytvoří novou IP adresu:

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

4. Odeberte atribut IP adresy `dependsOn` `Microsoft.Network/loadBalancers` , takže nezáleží na tom, jak vytvořit novou IP adresu. Přidejte atribut virtuální síť, `dependsOn` protože nástroj pro vyrovnávání zatížení je teď závislý na podsíti z virtuální sítě:

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

5. Změňte nastavení nástroje pro vyrovnávání zatížení `frontendIPConfigurations` z použití `publicIPAddress` , aby bylo možné použít podsíť a `privateIPAddress` . `privateIPAddress` používá předdefinovanou statickou interní IP adresu. Chcete-li použít dynamickou IP adresu, odeberte `privateIPAddress` prvek a pak změňte hodnotu `privateIPAllocationMethod` na **Dynamic**.

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

6. V `Microsoft.ServiceFabric/clusters` prostředku změňte tak, `managementEndpoint` aby odkazoval na adresu interního nástroje pro vyrovnávání zatížení. Pokud používáte zabezpečený cluster, nezapomeňte změnit *http://* na *https://*. (Upozorňujeme, že tento krok platí jenom pro Service Fabric clustery. Pokud používáte sadu škálování virtuálního počítače, přeskočte tento krok.)

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

Po nasazení použije nástroj pro vyrovnávání zatížení privátní statickou IP adresu 10.0.0.250. Pokud máte jiný počítač ve stejné virtuální síti, můžete přejít na koncový bod interního [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) . Všimněte si, že se připojí k jednomu z uzlů za nástrojem pro vyrovnávání zatížení.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Interní a externí nástroj pro vyrovnávání zatížení

V tomto scénáři začínáte s existujícím typem externího nástroje pro vyrovnávání zatížení s jedním uzlem a přidáte interní nástroj pro vyrovnávání zatížení pro stejný typ uzlu. Back-endové porty připojené k fondu back-end adres lze přiřadit pouze k jednomu nástroji pro vyrovnávání zatížení. Vyberte, který nástroj pro vyrovnávání zatížení bude mít porty vaší aplikace a který nástroj pro vyrovnávání zatížení bude mít koncové body správy (porty 19000 a 19080). Pokud umístíte koncové body správy do interního nástroje pro vyrovnávání zatížení, pamatujte na Service Fabric omezení poskytovatele prostředků popsaná [výše v článku](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). V příkladu, který používáme, zůstávají koncové body správy v externím nástroji pro vyrovnávání zatížení. Můžete také přidat port aplikace portu 80 a umístit ho do interního nástroje pro vyrovnávání zatížení.

V clusteru typu se dvěma uzly je jeden typ uzlu v externím nástroji pro vyrovnávání zatížení. Druhý typ uzlu je na interním nástroji pro vyrovnávání zatížení. Pokud chcete použít cluster se dvěma uzly, vytvořte na portálu šablonu typu se dvěma uzly, která se dodává se dvěma nástroji pro vyrovnávání zatížení, a druhý nástroj pro vyrovnávání zatížení přepněte do interního nástroje pro vyrovnávání zatížení. Další informace najdete v části [Nástroj pro vyrovnávání zatížení s interním vyrovnáváním zatížení](#internallb) .

1. Přidejte parametr statické IP adresy interního nástroje pro vyrovnávání zatížení. (Poznámky související s používáním dynamické IP adresy najdete v předchozích částech tohoto článku.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Přidejte parametr port aplikace 80.

3. Pokud chcete přidat interní verze existujících proměnných sítě, zkopírujte je a vložte a přidejte do názvu "-int":

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

4. Pokud začnete s šablonou generovanou na portálu, která používá port aplikace 80, výchozí šablona portálu přidá AppPort1 (port 80) na externí nástroj pro vyrovnávání zatížení. V takovém případě odeberte AppPort1 z externího nástroje pro vyrovnávání zatížení `loadBalancingRules` a sondy, abyste je mohli přidat do interního nástroje pro vyrovnávání zatížení:

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

5. Přidejte druhý `Microsoft.Network/loadBalancers` prostředek. Vypadá podobně jako interní nástroj pro vyrovnávání zatížení vytvořený v oddílu [Nástroje pro vyrovnávání zatížení](#internallb) , ale používá proměnné nástroje pro vyrovnávání zatížení "-int" a implementuje pouze port aplikace 80. Tím se také odstraní `inboundNatPools` , aby zůstaly koncové body RDP ve veřejném nástroji pro vyrovnávání zatížení. Pokud chcete protokol RDP na interním nástroji pro vyrovnávání zatížení, přejděte z externího nástroje pro vyrovnávání zatížení `inboundNatPools` do tohoto interního nástroje pro vyrovnávání zatížení:

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

6. V `networkProfile` části pro `Microsoft.Compute/virtualMachineScaleSets` prostředek přidejte interní fond back-end adres:

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

Po nasazení můžete ve skupině prostředků zobrazit dvě nástroje pro vyrovnávání zatížení. Pokud procházíte nástroji pro vyrovnávání zatížení, můžete zobrazit veřejné IP adresy a koncové body správy (porty 19000 a 19080) přiřazené k veřejné IP adrese. Také se můžete podívat na statickou interní IP adresu a koncový bod aplikace (port 80) přiřazený internímu nástroji pro vyrovnávání zatížení. Obě služby Vyrovnávání zatížení používají stejný fond back-end sady škálování virtuálních počítačů.

## <a name="notes-for-production-workloads"></a>Poznámky pro produkční úlohy

Výše uvedené šablony GitHubu jsou navržené tak, aby fungovaly s výchozí SKU pro Azure Standard Load Balancer (SLB), základní SKU. Tato SLB nemá žádnou smlouvu SLA, takže pro produkční úlohy by se měla použít standardní SKU. Další informace najdete v tématu [Přehled Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md). Všechny Service Fabric clustery, které používají standardní SKU pro SLB, musí zajistit, aby každý typ uzlu měl pravidlo umožňující odchozí přenosy na portu 443. To je nezbytné pro dokončení instalace clusteru a jakékoli nasazení bez takového pravidla selže. Ve výše uvedeném příkladu nástroje pro vyrovnávání zatížení musí být do šablony přidán další externí nástroj pro vyrovnávání zatížení s pravidlem, které povoluje odchozí přenosy pro port 443.

## <a name="next-steps"></a>Další kroky
[Vytvoření clusteru](service-fabric-cluster-creation-via-arm.md)

Po nasazení můžete ve skupině prostředků zobrazit dvě nástroje pro vyrovnávání zatížení. Pokud procházíte nástroji pro vyrovnávání zatížení, můžete zobrazit veřejné IP adresy a koncové body správy (porty 19000 a 19080) přiřazené k veřejné IP adrese. Také se můžete podívat na statickou interní IP adresu a koncový bod aplikace (port 80) přiřazený internímu nástroji pro vyrovnávání zatížení. Obě služby Vyrovnávání zatížení používají stejný fond back-end sady škálování virtuálních počítačů.
