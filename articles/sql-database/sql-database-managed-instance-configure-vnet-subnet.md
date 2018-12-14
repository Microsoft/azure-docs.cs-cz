---
title: Azure SQL Database Managed Instance konfigurace existující virtuální síť/podsíť | Dokumentace Microsoftu
description: Toto téma popisuje postup konfigurace existující virtuální síť (VNet) a podsíti, kde můžete nasadit Azure SQL Database Managed Instance.
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
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346028"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Konfigurace stávající virtuální síť pro Azure SQL Database Managed Instance

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md) a podsíť vyhrazené jenom pro Managed instance. Můžete použít existující virtuální síť a podsíť, pokud je nakonfigurovaný podle [požadavky na spravované instanci virtuální síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

Pokud máte novou podsíť, která ještě není nakonfigurovaná, si nejste jisti je podsíť v souladu s [požadavky](sql-database-managed-instance-connectivity-architecture.md#network-requirements), nebo chcete provést kontrolu je podsíť i nadále dodržovalo [požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po Některé změny, které jste provedli, můžete ověřovat a upravovat pomocí skriptů je popsáno v této části. 

  > [!Note]
  > Managed Instance můžete vytvořit pouze ve virtuálním sítím Resource Manageru. Azure virtuální sítě nasazené pomocí modelu nasazení Classic nejsou zkonstruovat. Ujistěte se, že vypočítat velikost podsítě podle pokynů v [určit velikost podsítě pro Managed instance](#determine-the-size-of-subnet-for-managed-instances) oddílu, protože podsíť velikost nelze změnit po nasazení spolehlivým prostředkům.

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
Příprava podsítě se provádí ve třech jednoduchých krocích:

1. Ověření – pro Managed Instance, požadavky na síť ověřit vybranou virtuální síť a podsíť.
2. Potvrďte – uživatel je zobrazen sady změn, které je potřeba provedené při přípravě podsíť Managed Instance nasazení a zobrazí výzva ho o souhlas.
3. Příprava – správně nakonfigurována virtuální síť a podsíť.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
