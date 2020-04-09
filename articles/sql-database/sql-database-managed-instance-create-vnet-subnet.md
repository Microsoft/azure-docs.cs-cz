---
title: Vytvoření virtuální sítě pro spravovanou instanci
description: Tento článek popisuje, jak vytvořit virtuální síť, kde můžete nasadit Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878846"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Vytvoření virtuální sítě pro spravovanou instanci Azure SQL Database

Tento článek vysvětluje, jak vytvořit platnou virtuální síť a podsíť, kde můžete nasadit azure SQL database managed instance.

Azure SQL Database Managed Instance musí být nasazená v rámci [virtuální sítě](../virtual-network/virtual-networks-overview.md)Azure . Toto nasazení umožňuje následující scénáře:

- Zabezpečená privátní IP adresa
- Připojení ke spravované instanci přímo z místní sítě
- Připojení spravované instance k propojenému serveru nebo jinému místnímu úložišti dat
- Připojení spravované instance k prostředkům Azure  

> [!NOTE]
> Před nasazením první instance byste měli [určit velikost podsítě pro spravovanou instanci.](sql-database-managed-instance-determine-size-vnet-subnet.md) Po umístití prostředků do sítě nelze změnit velikost podsítě.
>
> Pokud plánujete použít existující virtuální síť, je třeba upravit tuto konfiguraci sítě tak, aby vyhovovala spravované instanci. Další informace naleznete [v tématu Úprava existující virtuální sítě pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Po vytvoření spravované instance není přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného podporováno.  Přesunutí spravované instance do jiné podsítě také není podporováno.
>

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nejjednodušší způsob, jak vytvořit a nakonfigurovat virtuální síť, je použít šablonu nasazení Azure Resource Manageru.

1. Přihlaste se k portálu Azure.

2. Vyberte tlačítko **Nasadit do Azure:**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí, ve kterém můžete nasadit spravovanou instanci.

   > [!Note]
   > Tato šablona Azure Resource Manager nasadí virtuální síť se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances**je vyhrazena pro spravovanou instanci a má předem nakonfigurovanou směrovací tabulku. Druhá podsíť, nazvaná **Výchozí**, se používá pro jiné prostředky, které by měly přistupovat ke spravované instanci (například virtuální počítače Azure).

3. Konfigurace síťového prostředí. V následujícím formuláři můžete konfigurovat parametry síťového prostředí:

   ![Šablona Správce prostředků pro konfiguraci sítě Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Můžete změnit názvy virtuální sítě a podsítí a upravit rozsahy IP adresy přidružené k síťovým prostředkům. Po výběru tlačítka **Nákup** tento formulář vytvoří a nakonfiguruje vaše prostředí. Pokud nepotřebujete dvě podsítě, můžete odstranit výchozí.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Co je spravovaná instance?](sql-database-managed-instance.md).
- Informace o [architektuře připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Přečtěte si, jak [upravit existující virtuální síť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz, který ukazuje, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete [v tématu Vytvoření spravované instance Azure SQL Database .](sql-database-managed-instance-get-started.md)
- Problémy se službou DNS naleznete [v tématu Konfigurace vlastního dns](sql-database-managed-instance-custom-dns.md).
