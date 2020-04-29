---
title: Povoluje se konfigurace podsítě pro službu Azure SQL Database Managed instance.
description: Povoluje se konfigurace podsítě pro službu Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533262"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Povoluje se konfigurace podsítě pro službu Azure SQL Database Managed instance.
Konfigurace podsítě s podporou služby poskytuje automatizovanou správu konfigurace sítě pro podsítě hostující spravované instance. Uživatel s konfigurací podsítě s podporou služeb zůstává v plné kontrole přístupu k datům (toky přenosu dat TDS), zatímco spravovaná instance vezme zodpovědnost za zajištění nepřerušovaného toku provozu správy za účelem splnění smlouvy SLA.

Automaticky konfigurované skupiny zabezpečení sítě a pravidla směrovací tabulky je možné zobrazit zákazníkům a opatřit poznámkami předponou _Microsoft. SQL-managedInstances_UseOnly__.

Konfigurace podporující službu je povolená automaticky, když zapnete [delegování podsítě](../virtual-network/subnet-delegation-overview.md) pro `Microsoft.Sql/managedInstances` poskytovatele prostředků.

> [!IMPORTANT] 
> Pokud je delegování podsítě zapnuté, nemůžete ho vypnout, dokud neodeberete poslední virtuální cluster z podsítě. Další podrobnosti o tom, jak odstranit virtuální cluster, najdete v následujícím [článku](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Vzhledem k tomu, že konfigurace podsítě podporující službu je základní funkcí pro údržbu smlouvy SLA od 1. května 2020, nebude možné nasadit spravované instance v podsítích, které nejsou delegované pro poskytovatele prostředků spravované instance. 1. července 2020 všechny podsítě obsahující spravované instance budou automaticky delegovány na poskytovatele prostředků spravované instance. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Povolení delegování podsítí pro nová nasazení
Chcete-li nasadit spravovanou instanci v nástroji do prázdné podsítě, je nutné `Microsoft.Sql/managedInstances` ji delegovat na poskytovatele prostředků, jak je popsáno v následujícím [článku](../virtual-network/manage-subnet-delegation.md). _Upozorňujeme, že odkazovaný článek používá `Microsoft.DBforPostgreSQL/serversv2` poskytovatele prostředků. Místo toho budete muset použít `Microsoft.Sql/managedInstances` poskytovatele prostředků._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Povolení delegování podsítí pro existující nasazení

Aby bylo možné povolit delegování podsítě pro existující nasazení spravované instance, je třeba zjistit podsíť virtuální sítě, ve které je umístěna. 

Pokud se chcete dozvědět, jak `Virtual network/subnet` to zjistit `Overview` , můžete se podívat na okno portálu spravované instance.

Jako alternativu můžete použít následující příkazy PowerShellu, které se dozvíte. Nahraďte ID **předplatného** ID vašeho předplatného. Také nahraďte **RG-Name** skupinou prostředků vaší spravované instance a nahraďte **mi** název názvem vaší spravované instance.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Jakmile vyhledáte podsíť spravované instance, budete ji muset delegovat `Microsoft.Sql/managedInstances` na poskytovatele prostředků, jak je popsáno v následujícím [článku](../virtual-network/manage-subnet-delegation.md). _Upozorňujeme, že odkazovaný článek používá `Microsoft.DBforPostgreSQL/serversv2` poskytovatele prostředků. Místo toho budete muset použít `Microsoft.Sql/managedInstances` poskytovatele prostředků._


> [!IMPORTANT]
> Povolení konfigurace pro službu nezpůsobí převzetí služeb při selhání nebo přerušení připojení pro spravované instance, které už jsou v podsíti.
