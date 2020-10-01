---
title: Vytvoření virtuální sítě
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak vytvořit virtuální síť nakonfigurovanou pro podporu nasazení spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617702"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Vytvoření virtuální sítě pro službu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak vytvořit platnou virtuální síť a podsíť, kde můžete nasadit spravovanou instanci Azure SQL.

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě](../../virtual-network/virtual-networks-overview.md)Azure. Toto nasazení umožňuje následující scénáře:

- Zabezpečená privátní IP adresa
- Připojení k spravované instanci SQL přímo z místní sítě
- Propojení spravované instance SQL s propojeným serverem nebo jiným místním úložištěm dat
- Připojování spravované instance SQL k prostředkům Azure  

> [!NOTE]
> Před nasazením první instance byste měli [určit velikost podsítě pro spravovanou instanci SQL](vnet-subnet-determine-size.md) . Po umístění prostředků do se nedá změnit velikost podsítě.
>
> Pokud plánujete použít stávající virtuální síť, musíte tuto konfiguraci sítě upravit tak, aby vyhovovala spravované instanci SQL. Další informace najdete v tématu [Úprava existující virtuální sítě pro spravovanou instanci SQL](vnet-existing-add-subnet.md).
>
> Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.  Přesunutí spravované instance do jiné podsítě se také nepodporuje.
>

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nejjednodušší způsob, jak vytvořit a nakonfigurovat virtuální síť, je použít šablonu nasazení Azure Resource Manager.

1. Přihlaste se k portálu Azure.

2. Vyberte tlačítko **nasadit do Azure** :

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí, ve kterém můžete nasadit spravovanou instanci SQL.

   > [!Note]
   > Tato šablona Azure Resource Manager nasadí virtuální síť se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances**je vyhrazena pro SPRAVOVANOU instanci SQL a má předem nakonfigurovanou směrovací tabulku. Druhá podsíť označovaná jako **výchozí**se používá pro další prostředky, které by měly přistupovat ke spravované instanci SQL (například Azure Virtual Machines).

3. Nakonfigurujte síťové prostředí. V následujícím formuláři můžete nakonfigurovat parametry síťového prostředí:

   ![Šablona Správce prostředků pro konfiguraci sítě Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Můžete změnit názvy virtuální sítě a podsítí a upravit rozsahy IP adres přidružené k síťovým prostředkům. Po výběru tlačítka **koupit** se v tomto formuláři vytvoří a nakonfiguruje vaše prostředí. Pokud nepotřebujete dvě podsítě, můžete odstranit výchozí.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Managed instance SQL?](sql-managed-instance-paas-overview.md).
- Seznamte [se s architekturou připojení ve spravované instanci SQL](connectivity-architecture-overview.md).
- Naučte se, jak [Upravit existující virtuální síť pro spravovanou instanci SQL](vnet-existing-add-subnet.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](custom-dns-configure.md).
