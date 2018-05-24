---
title: Nakonfigurovat koncové body uzlu ve fondu Azure Batch | Microsoft Docs
description: Jak nakonfigurovat nebo zakázat přístup k portům SSH nebo protokolu RDP na výpočetní uzlech ve fondu Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: 5898206761e5029f94b6d1f1b48223481ae2ca13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Nastavit či vypnout vzdáleného přístupu na výpočetní uzly ve fondu Azure Batch

Ve výchozím nastavení, umožňuje Batch [uzlu uživatele](/rest/api/batchservice/computenode/adduser) s připojením k síti připojit externě do výpočetního uzlu ve fondu služby Batch. Uživatel například může připojit pomocí RDP (Remote Desktop) na portu 3389 do výpočetního uzlu ve fondu systému Windows. Podobně ve výchozím nastavení, uživatel se nemůže připojit pomocí Secure Shell (SSH) na port 22 do výpočetního uzlu ve fondu Linux. 

V prostředí může být nutné omezit nebo zakázat tato výchozí nastavení externí přístup. Tato nastavení můžete upravit pomocí rozhraní API služby Batch nastavit [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) vlastnost. 

## <a name="about-the-pool-endpoint-configuration"></a>O konfiguraci fondu koncového bodu
Konfigurace koncového bodu se skládá z jedné nebo více [sítě fondy adres (NAT) překlad](/rest/api/batchservice/pool/add#inboundnatpool) portů front-endu. (Nezaměňujte fond NAT s fondu Batch výpočetních uzlů.) Každý fond NAT nastavíte přepíší výchozí nastavení připojení na výpočetních uzlech fondu. 

Každá konfigurace fond NAT zahrnuje jednu nebo více [sítě (NSG) skupiny pravidel zabezpečení](/rest/api/batchservice/pool/add#networksecuritygrouprule). Každé pravidlo NSG povoluje nebo zakazuje určité přenosy v síti ke koncovému bodu. Můžete povolit nebo odepřít veškerý provoz, provoz identifikovaný [služby značky](../virtual-network/security-overview.md#service-tags) (například "Internet"), nebo provoz z konkrétní IP adresy nebo podsítě.

### <a name="considerations"></a>Požadavky
* Konfigurace koncového bodu fond je součástí fondu [konfigurace sítě](/rest/api/batchservice/pool/add#NetworkConfiguration). Konfigurace sítě může volitelně obsahovat nastavení fondu se mají připojit [virtuální síť Azure](batch-virtual-network.md). Pokud jste nastavili fondu ve virtuální síti, můžete vytvořit pravidla NSG, které používají nastavení adresy ve virtuální síti.
* Když konfigurujete fond NAT, můžete nakonfigurovat víc pravidel NSG. Pravidla se kontrolují v pořadí podle priority. Jakmile se pravidlo aplikuje, u dalších pravidel se už shoda nekontroluje.


## <a name="example-deny-all-rdp-traffic"></a>Příklad: Odepřít veškerý provoz protokolu RDP

Následující fragment C# ukazuje postup konfigurace koncového bodu protokolu RDP na výpočetních uzlech ve fondu Windows tak, aby odepřel veškerý přenos v síti. Koncový bod používá fond front-endu porty v rozsahu *60000 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Příklad: Zakážou všechny SSH provoz z Internetu

Následující fragment kódu Python ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linux tak, aby odepřel všechny přenosy z Internetu. Koncový bod používá fond front-endu porty v rozsahu *4000 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Příklad: Povolit provoz protokolu RDP z konkrétní IP adresu

Následující fragment C# ukazuje postup konfigurace koncového bodu protokolu RDP na výpočetních uzlech ve fondu Windows povolit RDP přístup pouze z IP adresy *198.51.100.7*. Druhého pravidla NSG se odepře, provoz, který neodpovídá IP adresu.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Příklad: Povolit SSH provoz z určité podsíti

Následující fragment kódu Python ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linux pro povolení přístupu jenom z podsítě *192.168.1.0/24*. Druhého pravidla NSG se odepře, provoz, který neodpovídá podsíť.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Další postup

- Další informace o pravidla NSG v Azure najdete v tématu [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](../virtual-network/security-overview.md).

- Podrobnější přehled služby Batch, najdete v tématu [rozsáhlé paralelní vývoj výpočetní řešení pomocí služby Batch](batch-api-basics.md).

