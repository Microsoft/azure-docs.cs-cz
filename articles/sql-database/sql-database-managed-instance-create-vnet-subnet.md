---
title: Spravovaná Instance Azure SQL Database vytvořte virtuální síť | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvořit virtuální síť (VNet), kde můžete nasadit Azure SQL Database Managed Instance.
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
ms.openlocfilehash: ebdfc80d3802ad8eb7da6fb7f152efdaee8d777d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345962"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurace virtuální sítě pro spravovanou instanci Azure SQL Database

Toto téma vysvětluje, jak vytvořit platný virtuální síť a podsíť, kde můžete nasadit Azure SQL Database Managed instance.

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Toto nasazení umožňuje následující scénáře: 
- Zabezpečené privátní IP adresu.
- Připojení k Managed Instance přímo z místní sítě 
- Připojení Managed Instance pro odkazovaný server nebo další místní úložiště dat 
- Připojování k prostředkům Azure Managed Instance  

  > [!Note]
  > Měli byste [určit velikost podsítě pro Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md) před nasazením první instance vzhledem k tomu, sunet nelze změnit velikost, jakmile stačí vložit prostředky uvnitř.
  > Pokud plánujete použití existující virtuální sítě, budete muset upravit tuto konfiguraci sítě tak, aby vyhovovaly Managed Instance. Další informace najdete v tématu [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md). 

## <a name="create-a-new-virtual-network"></a>Vytvořit novou virtuální síť

Nejjednodušší způsob, jak vytvořit a konfigurovat virtuální sítě je použití šablony nasazení Azure Resource Manageru.

1. Přihlaste se k portálu Azure.

2. Použití **nasadit do Azure** tlačítko k nasazení virtuální sítě v cloudu Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Toto tlačítko otevře formulář, který můžete použít ke konfiguraci síťového prostředí kde můžete nasadit Managed Instance.

  > [!Note]
  > Tuto šablonu Azure Resource Manageru nasadí virtuální síť se dvěma podsítěmi. Jedna podsíť s názvem **ManagedInstances** je vyhrazen pro Managed instance a má předem nakonfigurované směrovací tabulku, zatímco jiné podsíti nazývají **výchozí** slouží k jiným prostředkům, které by měl přístup ke spravované Instance (například virtuální počítače Azure). Můžete odebrat **výchozí** podsítě, pokud už nepotřebujete.

3. Konfigurace prostředí sítě. V následujícím formátu můžete nakonfigurovat parametry vaše síťové prostředí:

![Konfigurace sítě azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Můžete změnit názvy virtuální sítě a podsítě a upravit rozsahy IP adres přidružené k síťové prostředky. Po stisknutí tlačítka "Koupit", bude tento formulář vytvoření a konfiguraci prostředí. Pokud už nebudete potřebovat dvě podsítě, můžete odstranit výchozí hodnotu. 

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md).
- Další informace o [architektura připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Vymazat jak [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md)
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v článku [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md)
