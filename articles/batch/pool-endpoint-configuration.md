---
title: Konfigurace koncových bodů uzlu ve fondu Azure Batch | Microsoft Docs
description: Jak nakonfigurovat nebo zakázat přístup k portům SSH nebo RDP na výpočetních uzlech ve fondu Azure Batch.
services: batch
author: ju-shim
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: jushiman
ms.openlocfilehash: 1ac4c7647125cd6164235e98a4a828f6b072cbee
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029462"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurace nebo zakázání vzdáleného přístupu k výpočetním uzlům ve fondu Azure Batch

Ve výchozím nastavení služba Batch umožňuje, aby se [uživatel uzlu](/rest/api/batchservice/computenode/adduser) s připojením k síti připojoval externě k výpočetnímu uzlu ve fondu služby Batch. Uživatel se například může připojit pomocí vzdálené plochy (RDP) na portu 3389 k výpočetnímu uzlu ve fondu Windows. Podobně se ve výchozím nastavení může uživatel připojit pomocí Secure Shell (SSH) na portu 22 k výpočetnímu uzlu ve fondu Linux. 

V prostředí může být nutné omezit nebo zakázat tato výchozí nastavení externího přístupu. Tato nastavení můžete změnit pomocí rozhraní API pro dávkování pro nastavení vlastnosti [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) . 

## <a name="about-the-pool-endpoint-configuration"></a>O konfiguraci koncového bodu fondu
Konfigurace koncového bodu se skládá z jednoho nebo více [fondů překládání adres (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) portů front-endu. (Nepleťte si fond NAT s fondem dávkových uzlů služby Compute.) Nastavíte všechny fondy NAT, abyste přepsali výchozí nastavení připojení na výpočetních uzlech fondu. 

Každá konfigurace fondu NAT zahrnuje jedno nebo více [pravidel skupiny zabezpečení sítě (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Každé pravidlo NSG povoluje nebo odmítá určitý síťový provoz do koncového bodu. Můžete zvolit, že chcete povolit nebo zakázat veškerý provoz, provoz identifikovaný [značkou služby](../virtual-network/security-overview.md#service-tags) (například Internet) nebo provoz z konkrétních IP adres nebo podsítí.

### <a name="considerations"></a>Požadavky
* Konfigurace koncového bodu fondu je součástí [Konfigurace sítě](/rest/api/batchservice/pool/add#networkconfiguration)fondu. Konfigurace sítě může volitelně zahrnovat nastavení pro připojení fondu ke [službě Azure Virtual Network](batch-virtual-network.md). Pokud nastavíte fond ve virtuální síti, můžete vytvořit pravidla NSG, která používají nastavení adres ve virtuální síti.
* Při konfiguraci fondu NAT můžete nakonfigurovat několik pravidel NSG. Pravidla jsou kontrolována v pořadí podle priority. Jakmile se pravidlo aplikuje, u dalších pravidel se už shoda nekontroluje.


## <a name="example-deny-all-rdp-traffic"></a>Příklad: zamítnutí všech přenosů RDP

Následující C# fragment kódu ukazuje, jak nakonfigurovat koncový bod RDP na výpočetních uzlech ve fondu Windows tak, aby odepřel veškerý síťový provoz. Koncový bod používá front-end fond portů v rozsahu *60000 – 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Příklad: zamítnutí všech přenosů SSH z Internetu

Následující fragment kódu Pythonu ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linux pro odepření veškerého internetového provozu. Koncový bod používá front-end fond portů v rozsahu *4000 – 4100*. 

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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Příklad: povolení provozu protokolu RDP z konkrétní IP adresy

Následující C# fragment kódu ukazuje, jak nakonfigurovat koncový bod RDP na výpočetních uzlech ve fondu Windows tak, aby přístup k protokolu RDP povoloval jenom z IP adresy *198.51.100.7*. Druhé pravidlo NSG zakazuje provoz, který se neshoduje s IP adresou.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Příklad: povolení provozu SSH z konkrétní podsítě

Následující fragment kódu Pythonu ukazuje, jak nakonfigurovat koncový bod SSH na výpočetních uzlech ve fondu Linux, aby povoloval přístup jenom z podsítě *192.168.1.0/24*. Druhé pravidlo NSG zakazuje provoz, který se neshoduje s podsítí.

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

- Další informace o pravidlech NSG v Azure najdete v tématu [filtrování síťového provozu pomocí skupin zabezpečení sítě](../virtual-network/security-overview.md).

- Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).

