---
title: Vytvoření virtuální sítě pro spravovanou instanci
description: Tento článek popisuje, jak vytvořit virtuální síť, ve které můžete nasadit Azure SQL Database spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: cece07cccf00548a62c17cb59e8cf873ba106a3c
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773546"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Vytvoření virtuální sítě pro Azure SQL Database spravovanou instanci

Tento článek vysvětluje, jak vytvořit platnou virtuální síť a podsíť, kde můžete nasadit Azure SQL Database spravovanou instanci.

Azure SQL Database spravovaná instance musí být nasazená v rámci [virtuální sítě](../virtual-network/virtual-networks-overview.md)Azure. Toto nasazení umožňuje následující scénáře:

- Zabezpečená privátní IP adresa
- Připojení ke spravované instanci přímo z místní sítě
- Připojení spravované instance k odkazovanému serveru nebo jinému místnímu úložišti dat
- Připojení spravované instance k prostředkům Azure  

> [!NOTE]
> Před nasazením první instance byste měli [určit velikost podsítě pro spravovanou instanci](sql-database-managed-instance-determine-size-vnet-subnet.md) . Po umístění prostředků do se nedá změnit velikost podsítě.
>
> Pokud plánujete použít stávající virtuální síť, musíte tuto konfiguraci sítě upravit tak, aby vyhovovala vaší spravované instanci. Další informace najdete v tématu [Úprava existující virtuální sítě pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.  Přesunutí spravované instance do jiné podsítě se také nepodporuje.
>

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nejjednodušší způsob, jak vytvořit a nakonfigurovat virtuální síť, je použít šablonu nasazení Azure Resource Manager.

1. Přihlaste se k portálu Azure.

2. Vyberte tlačítko **nasadit do Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí, ve kterém můžete nasadit spravovanou instanci.

   > [!Note]
   > Tato šablona Azure Resource Manager nasadí virtuální síť se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances**je vyhrazena pro spravovanou instanci a má předem nakonfigurovanou směrovací tabulku. Druhá podsíť, která se označuje jako **výchozí**, se používá pro další prostředky, které by měly přistupovat ke spravované instanci (například Azure Virtual Machines).

3. Nakonfigurujte síťové prostředí. V následujícím formuláři můžete nakonfigurovat parametry síťového prostředí:

   ![Šablona Správce prostředků pro konfiguraci sítě Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Můžete změnit názvy virtuální sítě a podsítí a upravit rozsahy IP adres přidružené k síťovým prostředkům. Po výběru tlačítka **koupit** se v tomto formuláři vytvoří a nakonfiguruje vaše prostředí. Pokud nepotřebujete dvě podsítě, můžete odstranit výchozí.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Přečtěte si o [architektuře připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Naučte se, jak [Upravit existující virtuální síť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](sql-database-managed-instance-custom-dns.md).
