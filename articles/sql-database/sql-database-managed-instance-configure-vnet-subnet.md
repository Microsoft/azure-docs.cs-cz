---
title: Konfigurace existující virtuální sítě pro Azure SQL Database spravovanou instanci | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat existující virtuální síť a podsíť, kde můžete nasadit Azure SQL Database spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 168068094761fd35bf0386f476fbdd1262e9643f
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228309"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurace existující virtuální sítě pro Azure SQL Database spravovanou instanci

Azure SQL Database spravovaná instance musí být nasazená v rámci [virtuální sítě](../virtual-network/virtual-networks-overview.md) Azure a podsíť vyhrazená jenom pro spravované instance. Existující virtuální síť a podsíť můžete použít, pokud je nakonfigurovaná podle [požadavků virtuální sítě spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Pokud se vám na vás vztahuje jeden z následujících případů, můžete ověřit a upravit síť pomocí skriptu, který je vysvětlen v tomto článku:

- Máte novou podsíť, která pořád není nakonfigurovaná.
- Nejste si jistí, že je podsíť zarovnána s [požadavky](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Chcete ověřit, že podsíť ještě splňuje [požadavky sítě](sql-database-managed-instance-connectivity-architecture.md#network-requirements) po provedení změn.

> [!Note]
> Spravovanou instanci můžete vytvořit pouze ve virtuálních sítích vytvořených pomocí modelu nasazení Azure Resource Manager. Virtuální sítě Azure vytvořené prostřednictvím modelu nasazení Classic se nepodporují. Vypočítejte velikost podsítě podle pokynů uvedených v článku [Určení velikosti podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md) . Po nasazení prostředků v rámci nelze změnit velikost podsítě.
>
> Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Ověření a úprava existující virtuální sítě

Pokud chcete vytvořit spravovanou instanci v rámci existující podsítě, doporučujeme pro přípravu podsítě použít následující skript prostředí PowerShell:

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

Skript připraví podsíť ve třech krocích:

1. Oproti Ověřuje vybranou virtuální síť a podsíť pro požadavky na síť spravované instance.
2. Potvrdit Zobrazuje uživatele sadu změn, které je třeba provést pro přípravu podsítě pro nasazení spravované instance. Také si vyžádá souhlas.
3. Systém Správně nakonfiguruje virtuální síť a podsíť.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](sql-database-managed-instance-custom-dns.md).
