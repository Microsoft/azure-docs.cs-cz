---
title: Konfigurace existující virtuální sítě
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak nakonfigurovat existující virtuální síť a podsíť, kde můžete nasadit spravovanou instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 17838d4a652be3a1b50da56812731e0f634a0c08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323120"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Konfigurace stávající virtuální sítě pro službu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě](../../virtual-network/virtual-networks-overview.md) Azure a podsíť vyhrazená jenom pro spravované instance. Existující virtuální síť a podsíť můžete použít, pokud jsou nakonfigurované podle [požadavků virtuální sítě spravované instance SQL](connectivity-architecture-overview.md#network-requirements).

Pokud se vám na vás vztahuje jeden z následujících případů, můžete ověřit a upravit síť pomocí skriptu, který je vysvětlen v tomto článku:

- Máte novou podsíť, která pořád není nakonfigurovaná.
- Nejste si jistí, že je podsíť zarovnána s [požadavky](connectivity-architecture-overview.md#network-requirements).
- Chcete ověřit, že podsíť ještě splňuje [požadavky sítě](connectivity-architecture-overview.md#network-requirements) po provedení změn.

> [!Note]
> Spravovanou instanci můžete vytvořit pouze ve virtuálních sítích vytvořených pomocí modelu nasazení Azure Resource Manager. Virtuální sítě Azure vytvořené prostřednictvím modelu nasazení Classic se nepodporují. Vypočítejte velikost podsítě podle pokynů v článku [Určení velikosti podsítě pro SQL Managed instance](vnet-subnet-determine-size.md) . Po nasazení prostředků v rámci nelze změnit velikost podsítě.
>
> Po vytvoření spravované instance se přesunutí instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného nepodporuje.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Ověření a úprava existující virtuální sítě

Pokud chcete vytvořit spravovanou instanci v rámci existující podsítě, doporučujeme pro přípravu podsítě použít následující skript prostředí PowerShell:

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

Tento skript připraví podsíť ve třech krocích:

1. Ověřit: ověřuje vybranou virtuální síť a podsíť pro požadavky na síť spravované instance SQL.
2. Potvrdit: zobrazuje uživatele sady změn, které je třeba provést pro přípravu podsítě pro nasazení spravované instance SQL. Také si vyžádá souhlas.
3. Příprava: správně nakonfiguruje virtuální síť a podsíť.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Managed instance SQL?](sql-managed-instance-paas-overview.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](custom-dns-configure.md).
