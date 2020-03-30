---
title: Nasazení škálovacích sad virtuálních strojů pomocí IPv6 v Azure
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit škálovací sady virtuálních strojů s IPv6 ve virtuální síti Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164988"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Nasazení škálovacích sad virtuálních strojů pomocí IPv6 v Azure (Preview)

Tento článek ukazuje, jak nasadit sadu škálování virtuálních strojů (IPv4 + IPv6) s externím nástrojem pro vyrovnávání zatížení ve virtuální síti Azure. Proces vytvoření škálovací sady virtuálních strojů podporující chod IPv6 je téměř totožný s procesem vytváření jednotlivých virtuálních počítačů popsaných [zde](ipv6-configure-standard-load-balancer-template-json.md). Začnete s kroky, které jsou podobné těm, které jsou popsány pro jednotlivé virtuální chodů:
1.  Vytvořte veřejné IP adresy IPv4 a IPv6.
2.  Vytvořte dvouvrstvý systém pro vyrovnávání zatížení.  
3.  Vytvořte pravidla skupiny zabezpečení sítě (NSG).  

Jediným krokem, který se liší od jednotlivých virtuálních počítačů, je vytvoření konfigurace síťového rozhraní (NIC), která používá prostředek škálovací sady virtuálních počítačů: networkProfile/networkInterfaceConfigurations. Struktura JSON je podobná jako u objektu Microsoft.Network/networkInterfaces používaného pro jednotlivé virtuální počítače s přidáním síťové koryty a konfigurace IpConfiguration IPv4 jako primárního rozhraní pomocí **atributu "primary": true,** jak je vidět v následujícím příkladu:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Ukázka šablony škálovací sady virtuálních strojů JSON

Chcete-li nasadit duální zásobník (IPv4 + IPv6) Škálovací sada virtuálních strojů s externím nástrojem pro vyrovnávání zatížení s dvěma [zásobníky](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)a ukázkovou šablonou zobrazení virtuální sítě zde .
## <a name="next-steps"></a>Další kroky

Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md).
