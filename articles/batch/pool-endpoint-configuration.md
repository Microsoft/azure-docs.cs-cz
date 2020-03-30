---
title: Konfigurace koncových bodů uzlů ve fondu dávek Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat nebo zakázat přístup k portům SSH nebo RDP na výpočetních uzlech ve fondu Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017220"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurace nebo zakázání vzdáleného přístupu k výpočetním uzlům ve fondu Azure Batch

Ve výchozím nastavení batch umožňuje [uživateli uzlu](/rest/api/batchservice/computenode/adduser) se síťovým připojením připojit externě k výpočetnímu uzlu ve fondu dávek. Uživatel se může například připojit pomocí vzdálené plochy (RDP) na portu 3389 k výpočetnímu uzlu ve fondu systému Windows. Podobně ve výchozím nastavení se uživatel může připojit pomocí zabezpečeného prostředí (SSH) na portu 22 k výpočetnímu uzlu ve fondu Linuxu. 

Ve vašem prostředí může být nutné omezit nebo zakázat tato výchozí nastavení externího přístupu. Tato nastavení můžete upravit pomocí dávkových api pro nastavení vlastnosti [PoolEndpointConfiguration.](/rest/api/batchservice/pool/add#poolendpointconfiguration) 

## <a name="about-the-pool-endpoint-configuration"></a>O konfiguraci koncového bodu fondu
Konfigurace koncového bodu se skládá z jednoho nebo více [fondů překladu síťových adres (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) front-end portů. (Nezaměňujte fond NAT s fondem batch výpočetních uzlů.) Každý fond NAT nastavíte tak, aby přepsal výchozí nastavení připojení na výpočetních uzlech fondu. 

Každá konfigurace fondu navštívení sítí obsahuje jedno nebo více [pravidel skupiny zabezpečení sítě (NSG).](/rest/api/batchservice/pool/add#networksecuritygrouprule) Každé pravidlo sítě zabezpečení sítě umožňuje nebo zakazuje určitý síťový provoz koncovému bodu. Můžete povolit nebo odepřít veškerý provoz, provoz identifikovaný [výrobním označením](../virtual-network/security-overview.md#service-tags) (například "Internet") nebo přenosy z konkrétních IP adres nebo podsítí.

### <a name="considerations"></a>Požadavky
* Konfigurace koncového bodu fondu je součástí [konfigurace sítě](/rest/api/batchservice/pool/add#networkconfiguration)fondu . Konfigurace sítě může volitelně obsahovat nastavení pro připojení fondu k [virtuální síti Azure](batch-virtual-network.md). Pokud nastavíte fond ve virtuální síti, můžete vytvořit pravidla skupiny zabezpečení sítě, která používají nastavení adresy ve virtuální síti.
* Při konfiguraci fondu NAT můžete nakonfigurovat více pravidel skupiny zabezpečení sítě. Pravidla jsou kontrolována v pořadí podle priority. Jakmile se pravidlo aplikuje, u dalších pravidel se už shoda nekontroluje.


## <a name="example-deny-all-rdp-traffic"></a>Příklad: Odepřít veškerý provoz RDP

Následující fragment jazyka C# ukazuje, jak nakonfigurovat koncový bod PROTOKOLU RDP na výpočetních uzlech ve fondu systému Windows, aby byl odepřen veškerý síťový provoz. Koncový bod používá front-endový fond portů v rozsahu *60000 - 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Příklad: Odepřít veškerý provoz SSH z internetu

Následující fragment Pythonu ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linuxu, aby se odepřel veškerý internetový provoz. Koncový bod používá front-endový fond portů v rozsahu *4000 - 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Příklad: Povolit provoz PROTOKOLU RDP z určité adresy IP

Následující fragment jazyka C# ukazuje, jak nakonfigurovat koncový bod PROTOKOLU RDP na výpočetních uzlech ve fondu systému Windows tak, aby umožňoval přístup k protokolu RDP pouze z adresy IP *198.51.100.7*. Druhé pravidlo nsg zakazuje provoz, který neodpovídá adrese IP.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Příklad: Povolit provoz SSH z určité podsítě

Následující fragment Pythonu ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linuxu tak, aby umožňoval přístup pouze z podsítě *192.168.1.0/24*. Druhé pravidlo skupiny síťových sítí odepře provoz, který neodpovídá podsíti.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
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

## <a name="next-steps"></a>Další kroky

- Další informace o pravidlech skupiny zabezpečení sítě v Azure najdete v [tématu Filtrování síťového provozu se skupinami zabezpečení sítě](../virtual-network/security-overview.md).

- Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).

