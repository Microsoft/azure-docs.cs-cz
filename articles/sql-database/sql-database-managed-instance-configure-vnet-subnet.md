---
title: Konfigurace existující virtuální sítě pro spravovanou instanci
description: Tento článek popisuje, jak nakonfigurovat existující virtuální síť a podsíť, kde můžete nasadit azure SQL database managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476945"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurace stávající virtuální sítě pro spravovanou instanci Azure SQL Database

Správa databáze Azure SQL musí být nasazená v rámci [virtuální sítě](../virtual-network/virtual-networks-overview.md) Azure a podsítě vyhrazené jenom pro spravované instance. Existující virtuální síť a podsíť můžete použít, pokud je nakonfigurovaná podle [požadavků na virtuální síť spravované instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Pokud se vás týká jeden z následujících případů, můžete ověřit a upravit síť pomocí skriptu popsaného v tomto článku:

- Máte novou podsíť, která stále není nakonfigurovaná.
- Nejste si jisti, že podsíť je v souladu s [požadavky](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Chcete zkontrolovat, zda podsíť stále splňuje [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements) i po provedené mši.

> [!Note]
> Spravovanou instanci můžete vytvořit jenom ve virtuálních sítích vytvořených pomocí modelu nasazení Azure Resource Manageru. Virtuální sítě Azure vytvořené prostřednictvím klasického modelu nasazení nejsou podporované. Velikost podsítě vypočítejte podle pokynů v článku [Určení velikosti podsítě pro spravované instance.](sql-database-managed-instance-determine-size-vnet-subnet.md) Po nasazení prostředků uvnitř nelze změnit velikost podsítě.
>
> Po vytvoření spravované instance není přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného podporováno.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Ověření a úprava existující virtuální sítě

Pokud chcete vytvořit spravovanou instanci uvnitř existující podsítě, doporučujeme pro přípravu podsítě následující skript prostředí PowerShell:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skript připraví podsíť ve třech krocích:

1. Ověřit: Ověří vybranou virtuální síť a podsíť pro požadavky na síť spravované instance.
2. Potvrdit: Zobrazuje uživateli sadu změn, které je třeba provést k přípravě podsítě pro nasazení spravované instance. Rovněž žádá o souhlas.
3. Příprava: Správně nakonfiguruje virtuální síť a podsíť.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Co je spravovaná instance?](sql-database-managed-instance.md).
- Kurz, který ukazuje, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete [v tématu Vytvoření spravované instance Azure SQL Database .](sql-database-managed-instance-get-started.md)
- Problémy se službou DNS naleznete [v tématu Konfigurace vlastního dns](sql-database-managed-instance-custom-dns.md).
