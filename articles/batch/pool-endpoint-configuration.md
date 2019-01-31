---
title: Konfigurace koncových bodů uzlu ve fondu Azure Batch | Dokumentace Microsoftu
description: Jak nastavit či vypnout přístup k portům SSH nebo RDP na výpočetních uzlech ve fondu služby Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457611"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Nastavit či vypnout vzdálený přístup k výpočetní uzlům ve fondu služby Azure Batch

Ve výchozím nastavení, Batch umožňuje [uzel uživatele](/rest/api/batchservice/computenode/adduser) s připojením k síti externí připojení k výpočetnímu uzlu ve fondu služby Batch. Uživatel například může připojit pomocí RDP (Remote Desktop) na portu 3389 na výpočetním uzlu ve fondu Windows. Podobně ve výchozím nastavení, může uživatel připojit pomocí Secure Shell (SSH) na portu 22 na výpočetním uzlu ve fondu s Linuxem. 

Ve vašem prostředí potřebujete omezit nebo zakázat tato výchozí nastavení pro externí přístup. Tato nastavení lze upravit pomocí rozhraní API služby Batch nastavit [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) vlastnost. 

## <a name="about-the-pool-endpoint-configuration"></a>Informace o konfiguraci koncového bodu fondu
Konfigurace koncového bodu se skládá z jedné nebo více [sítě překladu adres](/rest/api/batchservice/pool/add#inboundnatpool) portů front-endu. (Nezaměňujte fond NAT s fondu výpočetních uzlů služby Batch.) Každý fond NAT nastavíte přepíší výchozí nastavení připojení ve fondu výpočetních uzlů. 

Každá konfigurace fondu NAT zahrnuje jednu nebo více [pravidel zabezpečení skupiny (NSG) sítě](/rest/api/batchservice/pool/add#networksecuritygrouprule). Každé pravidlo skupiny zabezpečení sítě povoluje nebo odepírá provoz určité síti ke koncovému bodu. Můžete povolit nebo odepřít veškerý provoz, provoz identifikovaný [značka služby](../virtual-network/security-overview.md#service-tags) (jako je například "Internet"), nebo provoz z konkrétní IP adresy nebo podsítě.

### <a name="considerations"></a>Požadavky
* Konfigurace koncového bodu fondu je součástí fondu [konfigurace sítě](/rest/api/batchservice/pool/add#NetworkConfiguration). Konfigurace sítě může volitelně zahrnovat nastavení připojení fondu, aby [virtuální síť Azure](batch-virtual-network.md). Pokud jste nastavili fondu ve virtuální síti, můžete vytvořit pravidla NSG, které používají nastavení adresy ve virtuální síti.
* Při konfiguraci fondu NAT, můžete nakonfigurovat víc pravidel skupiny zabezpečení sítě. Pravidla se kontrolují v pořadí podle priority. Jakmile se pravidlo aplikuje, u dalších pravidel se už shoda nekontroluje.


## <a name="example-deny-all-rdp-traffic"></a>Příklad: Odepřít veškerý provoz protokolu RDP

Následující C# fragment kódu ukazuje, jak nakonfigurovat koncový bod protokolu RDP na výpočetních uzlech ve fondu Windows chcete odepřít veškerý přenos v síti. Koncový bod používá front-endový fond v rozsahu portů *60000 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Příklad: Odepřít veškerý provoz SSH z Internetu

Následující fragment kódu Python ukazuje, jak nakonfigurovat koncový bod SSH na výpočetní uzlech ve fondu s Linuxem chcete odepřít veškerý internetový provoz. Koncový bod používá front-endový fond v rozsahu portů *4000 4100*. 

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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Příklad: Povolit provoz protokolu RDP z konkrétní IP adresy

Následující C# fragment kódu ukazuje, jak nakonfigurovat koncový bod protokolu RDP na výpočetní uzlech ve fondu Windows povoluje přístup protokolu RDP pouze z IP adresy *198.51.100.7*. Druhé pravidlo NSG odepření provozu, který neodpovídá IP adrese.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Příklad: Přijímaly provoz SSH z konkrétní podsítě

Následující fragment kódu Python ukazuje, jak nakonfigurovat koncový bod SSH na výpočetní uzlech ve fondu s Linuxem pro povolení přístupu jenom z podsítě *192.168.1.0/24*. Druhé pravidlo NSG odepření provozu, který se neshoduje s podsítí.

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

- Další informace o pravidlech NSG v Azure najdete v tématu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/security-overview.md).

- Podrobný přehled služby Batch, najdete v části [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).

