---
title: Povolení konfigurace podsítě podporované službou pro spravovanou instanci Azure SQL Database
description: Povolení konfigurace podsítě podporované službou pro spravovanou instanci Azure SQL Database
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533262"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Povolení konfigurace podsítě podporované službou pro spravovanou instanci Azure SQL Database
Konfigurace podsítě podporovaná službou poskytuje automatizovanou správu konfigurace sítě pro podsítě hostující spravované instance. S konfigurací podsítě s podporou služeb uživatel zůstává plně pod kontrolou přístupu k datům (tds dopravní toky), zatímco spravované instance přebírá odpovědnost za zajištění nepřetržitého toku provozu správy za účelem splnění smlouvy SLA.

Automaticky nakonfigurované skupiny zabezpečení sítě a pravidla směrovací tabulky jsou viditelné pro zákazníka a jsou vybaveny předponou _Microsoft.Sql-managedInstances_UseOnly__.

Konfigurace podporovaná službou je povolena automaticky, jakmile `Microsoft.Sql/managedInstances` zapnete [delegování podsítě](../virtual-network/subnet-delegation-overview.md) pro poskytovatele prostředků.

> [!IMPORTANT] 
> Po zapnutí delegování podsítě jej nelze vypnout, dokud z podsítě neodeberete poslední virtuální cluster. Další podrobnosti o odstranění virtuálního clusteru naleznete v následujícím [článku](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Vzhledem k tomu, že konfigurace podsítě podporovaná službou je základní funkcí pro údržbu sla, od 1. 1. července 2020 budou všechny podsítě obsahující spravované instance automaticky delegovány na poskytovatele prostředků spravované instance. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Povolení delegování podsítě pro nová nasazení
Chcete-li nasadit spravovanou instanci `Microsoft.Sql/managedInstances` do prázdné podsítě, musíte ji delegovat na poskytovatele prostředků, jak je popsáno v následujícím [článku](../virtual-network/manage-subnet-delegation.md). _Upozorňujeme, že odkazovaný článek používá `Microsoft.DBforPostgreSQL/serversv2` například poskytovatele prostředků. Místo toho budete `Microsoft.Sql/managedInstances` muset použít poskytovatele prostředků._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Povolení delegování podsítě pro existující nasazení

Chcete-li povolit delegování podsítě pro stávající nasazení spravované instance, musíte zjistit podsíť virtuální sítě, kde je umístěna. 

Chcete-li se `Virtual network/subnet` to `Overview` dozvědět, můžete zkontrolovat na portálu blade spravované instance.

Jako alternativu můžete spustit následující příkazy prostředí PowerShell, abyste se to dozvěděli. Nahraďte **Id předplatného** id s ID předplatného. Také nahraďte **rg-name** skupinou prostředků pro spravovanou instanci a nahraďte **mi-name** názvem spravované instance.

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

Jakmile najdete podsíť spravované instance, `Microsoft.Sql/managedInstances` musíte ji delegovat na poskytovatele prostředků, jak je popsáno v následujícím [článku](../virtual-network/manage-subnet-delegation.md). _Upozorňujeme, že odkazovaný článek používá `Microsoft.DBforPostgreSQL/serversv2` například poskytovatele prostředků. Místo toho budete `Microsoft.Sql/managedInstances` muset použít poskytovatele prostředků._


> [!IMPORTANT]
> Povolení konfigurace podporované službou nezpůsobí převzetí služeb při selhání nebo přerušení připojení pro spravované instance, které jsou již v podsíti.
