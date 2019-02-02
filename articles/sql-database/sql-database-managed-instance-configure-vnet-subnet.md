---
title: Konfigurace existující virtuální sítě pro Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat stávající virtuální síť a podsíť, kde můžete nasadit Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: c806357cfb5cbcc67185473e490ebc1f37a12838
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564268"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurace existující virtuální sítě pro Azure SQL Database Managed Instance

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální sítě](../virtual-network/virtual-networks-overview.md) a podsíť vyhrazené jenom pro Managed instance. Můžete použít existující virtuální síť a podsíť, pokud je nakonfigurovaný podle [požadavky na virtuální síť Managed Instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Pokud jeden z následujících případech se na vás vztahuje, můžete ověřovat a upravovat vaší sítě pomocí skriptů je popsáno v tomto článku:

* Máte novou podsíť, která ještě není nakonfigurovaná.
* Si nejste jistí, že podsíť je v souladu s [požadavky](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
* Chcete zkontrolovat, že podsíť stále splňuje [požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po provedení změny.


> [!Note]
> Managed Instance můžete vytvořit pouze v virtuálními sítěmi vytvořenými prostřednictvím modelu nasazení Azure Resource Manageru. Azure virtuálních sítí vytvořených prostřednictvím modelu nasazení classic nejsou podporovány. Vypočítá velikost podsítě podle pokynů v [určit velikost podsítě pro Managed instance](sql-database-managed-instance-determine-size-vnet-subnet.md) článku. Poté, co nasadíte prostředky v nejde jeho velikost změnit podsíť.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Ověřovat a upravovat stávající virtuální síť

Pokud chcete vytvořit uvnitř existující podsíť Managed Instance, doporučujeme následující skript prostředí PowerShell pro přípravu na podsíť:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skript připraví podsítě ve třech krocích:

1. Ověření: Ověřuje vybraná virtuální síť a podsíť pro Managed Instance požadavky na síť.
2. Potvrzení: Uživatel zobrazuje sadu změn, které je třeba provést při přípravě na podsíť Managed Instance nasazení. Také požádá ho o souhlas.
3. Příprava: Řádně nakonfiguruje virtuální síť a podsíť.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvořit Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
