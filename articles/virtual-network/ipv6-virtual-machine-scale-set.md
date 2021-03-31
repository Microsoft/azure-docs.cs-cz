---
title: Nasazení služby Virtual Machine Scale Sets pomocí protokolu IPv6 v Azure
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit službu Virtual Machine Scale Sets s protokolem IPv6 ve službě Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84709993"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Nasazení služby Virtual Machine Scale Sets pomocí protokolu IPv6 v Azure

V tomto článku se dozvíte, jak nasadit sadu škálování virtuálních počítačů s duálním zásobníkem (IPv4 + IPv6) pomocí duálního externího nástroje pro vyrovnávání zatížení ve službě Azure Virtual Network. Proces vytvoření sady škálování virtuálních počítačů s podporou protokolu IPv6 je téměř stejný jako proces vytváření jednotlivých virtuálních počítačů popsaných [tady](ipv6-configure-standard-load-balancer-template-json.md). Začnete s kroky, které jsou podobné těm, které jsou popsány pro jednotlivé virtuální počítače:
1.    Vytvořte veřejné IP adresy IPv4 a IPv6.
2.    Vytvořte duální Nástroj pro vyrovnávání zatížení.  
3.    Vytvořte pravidla skupiny zabezpečení sítě (NSG).  

Jediným krokem, který se liší od jednotlivých virtuálních počítačů, je vytvoření konfigurace síťového rozhraní (NIC), která používá prostředek sady škálování virtuálního počítače: networkProfile/networkInterfaceConfigurations. Struktura JSON je podobná jako objekt Microsoft. Network/networkInterfaces, který se používá pro jednotlivé virtuální počítače, přičemž jako primární rozhraní používá atribut **"Primary": true**  , jak je vidět v následujícím příkladu:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Ukázka JSON šablony sady škálování virtuálního počítače

Pokud chcete nasadit sadu škálování virtuálních počítačů duálního zásobníku (IPv4 + IPv6) s externím Load Balancerm duálního zásobníku a ukázkovou [šablonou](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)zobrazení virtuální sítě.
## <a name="next-steps"></a>Další kroky

Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [co je IPv6 pro Azure Virtual Network?](ipv6-overview.md).
